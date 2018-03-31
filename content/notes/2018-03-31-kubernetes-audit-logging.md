---
title: "Kubernetes Audit Logging Tutorial"
date: '2018-03-31'
tags:
  - kubernetes
  - kops
  - devops
slug: kubernetes-audit-logging-tutorial
---

Kubernetes Auditing is part of the `kube-apiserver`, and will log all requests that the API Server processes for audit purposes.

This is what an audit log looks like:

```
{
  "kind":"Event”,
  "apiVersion":"audit.k8s.io/v1beta1”,
  "metadata":{ "creationTimestamp":"2018-03-21T21:47:07Z" },
  "level":"Metadata”,
  "timestamp":"2018-03-21T21:47:07Z”,
  "auditID":"20ac14d3-1214-42b8-af3c-31454f6d7dfb”,
  "stage":"RequestReceived”,
  "requestURI":"/api/v1/namespaces/default/persistentvolumeclaims”,
  "verb":"list”,
  "user": {
    "username":"benjamin.visser@example.org",
    "groups":[ "system:authenticated" ]
  },
  "sourceIPs":[ "172.20.66.233" ],
  "objectRef": {
    "resource":"persistentvolumeclaims",
    "namespace":"default",
    "apiVersion":"v1"
  },
  "requestReceivedTimestamp":"2018-03-21T21:47:07.603214Z”,
  "stageTimestamp":"2018-03-21T21:47:07.603214Z”
}
```

These logs can give very useful information about what is happening in your cluster, and can even be required for compliance purposes.

This is what a basic audit logging policy looks like:

```
---
apiVersion: audit.k8s.io/v1beta1
kind: Policy
rules:
  - level: RequestResponse
    omitStages:
      - RequestReceived
    resources:
      - group: ""
```

level? omitStages? What the heck. Let's explain what those terms mean!

### Stages

- _RequestReceived_ - The stage for events generated as soon as the audit handler receives the request.
- _ResponseStarted_ - Once the response headers are sent, but before the response body is sent. This stage is only generated for         long-running requests (e.g. watch).
- _ResponseComplete_ - Once the response body has been completed.
- _Panic_ - Events generated when a panic occurred.

### Levels

- _None_ - don’t log events that match this rule.
- _Metadata_ - log request metadata (requesting user, timestamp, resource, verb, etc.) but not request or response body.
- _Request_ - log event metadata and request body but not response body.
- _RequestResponse_ - log event metadata, request and response bodies.

## Configuration

Auditing is configurable at two levels:

- _Policy_ - What is recorded.
- _Backends_ - How are records persisted and broadcast.


This is a basic policy that would log everything at the Metadata level.

```
---
apiVersion: audit.k8s.io/v1beta1
kind: Policy
rules:
  - level: Metadata
    omitStages:
      - RequestReceived
```

The policy below was [created for GCE](https://github.com/kubernetes/kubernetes/blob/master/cluster/gce/gci/configure-helper.sh#L735),
but it's a great starting point for an audit policy. It is much less verbose
than the simple audit policy above, which can be costly if you're using a SaaS
centralized logging solution.

Something to note about audit policies is that when an event is processed it is compared
against the audit policy rules in order, and the first matching rule sets the audit
level of the event. It's pretty weird. It would make a lot more sense to read the
entire policy and apply rules according to most restrictive; like is done with
Kubernetes RBAC policies.

```
---
apiVersion: audit.k8s.io/v1beta1
kind: Policy
rules:
  - level: None
    resources:
      - group: ""
        resources:
          - endpoints
          - services
          - services/status
    users:
      - 'system:kube-proxy'
    verbs:
      - watch

  - level: None
    resources:
      - group: ""
        resources:
          - nodes
          - nodes/status
    userGroups:
      - 'system:nodes'
    verbs:
      - get

  - level: None
    namespaces:
      - kube-system
    resources:
      - group: ""
        resources:
          - endpoints
    users:
      - 'system:kube-controller-manager'
      - 'system:kube-scheduler'
      - 'system:serviceaccount:kube-system:endpoint-controller'
    verbs:
      - get
      - update

  - level: None
    resources:
      - group: ""
        resources:
          - namespaces
          - namespaces/status
          - namespaces/finalize
    users:
      - 'system:apiserver'
    verbs:
      - get

  - level: None
    resources:
      - group: metrics.k8s.io
    users:
      - 'system:kube-controller-manager'
    verbs:
      - get
      - list

  - level: None
    nonResourceURLs:
      - '/healthz*'
      - /version
      - '/swagger*'

  - level: None
    resources:
      - group: ""
        resources:
          - events

  - level: Request
    omitStages:
      - RequestReceived
    resources:
      - group: ""
        resources:
          - nodes/status
          - pods/status
    users:
      - kubelet
      - 'system:node-problem-detector'
      - 'system:serviceaccount:kube-system:node-problem-detector'
    verbs:
      - update
      - patch

  - level: Request
    omitStages:
      - RequestReceived
    resources:
      - group: ""
        resources:
          - nodes/status
          - pods/status
    userGroups:
      - 'system:nodes'
    verbs:
      - update
      - patch

  - level: Request
    omitStages:
      - RequestReceived
    users:
      - 'system:serviceaccount:kube-system:namespace-controller'
    verbs:
      - deletecollection

  - level: Metadata
    omitStages:
      - RequestReceived
    resources:
      - group: ""
        resources:
          - secrets
          - configmaps
      - group: authentication.k8s.io
        resources:
          - tokenreviews

  - level: Request
    omitStages:
      - RequestReceived
    resources:
      - group: ""
      - group: admissionregistration.k8s.io
      - group: apiextensions.k8s.io
      - group: apiregistration.k8s.io
      - group: apps
      - group: authentication.k8s.io
      - group: authorization.k8s.io
      - group: autoscaling
      - group: batch
      - group: certificates.k8s.io
      - group: extensions
      - group: metrics.k8s.io
      - group: networking.k8s.io
      - group: policy
      - group: rbac.authorization.k8s.io
      - group: scheduling.k8s.io
      - group: settings.k8s.io
      - group: storage.k8s.io
    verbs:
      - get
      - list
      - watch

  - level: RequestResponse
    omitStages:
      - RequestReceived
    resources:
      - group: ""
      - group: admissionregistration.k8s.io
      - group: apiextensions.k8s.io
      - group: apiregistration.k8s.io
      - group: apps
      - group: authentication.k8s.io
      - group: authorization.k8s.io
      - group: autoscaling
      - group: batch
      - group: certificates.k8s.io
      - group: extensions
      - group: metrics.k8s.io
      - group: networking.k8s.io
      - group: policy
      - group: rbac.authorization.k8s.io
      - group: scheduling.k8s.io
      - group: settings.k8s.io
      - group: storage.k8s.io

  - level: Metadata
    omitStages:
      - RequestReceived
```

Once we've defined a policy like above, we need to apply it to the Kubernetes API Server.

For Kops, we can apply the changes with `kops edit cluster <cluster>`.

```
spec:
  fileAssets:
  - name: kubernetes-audit
    path: /srv/kubernetes/audit.yaml
    # which type of instances to appy the file
    roles: [Master]
    content: |
      <audit policy here>
  kubeAPIServer:
    auditLogPath: /var/log/kube-apiserver-audit.log
    auditLogMaxAge: 10 # num days
    auditLogMaxBackups: 1 # the num of audit logs to retain
    auditLogMaxSize: 100 # the max size in MB to retain
    auditPolicyFile: /srv/kubernetes/audit.yaml
```

**NOTE**: If you're using a cluster management tool other than Kops, you'll need to find some way to get the audit policy on the Kubernetes master node.

In the kube configuration above, we're using a log backend, to read about log backends and webhook backends, check the [official Kubernetes documentation](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/#audit-backends).

OK! Once we've rotated our master node with the new configuration, we can pickup
the audit logs from the `auditLogPath` using some kid of log exporter and send them to a centralized logging solution. My preference is [LogDNA](https://github.com/logdna/logdna-agent#kubernetes-logging).

You should be getting some pretty audit logs now. Have fun.
