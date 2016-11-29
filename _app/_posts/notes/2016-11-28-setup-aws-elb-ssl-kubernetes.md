---
layout: post
comments: true
title: SSL for AWS ELBs with Kubernetes
category: note
---

**NOTE**: This is a beta feature of Kubernetes. Use at your own risk.


I want to setup HTTP on port 80 and SSL on port 443 and then route both on TCP
to my backend servers in AWS, like so:

<p><img src="{{ site.file }}/k8s-aws-elb.png" alt="Screenshot showing aws elb"></p>

To do this with Kubernetes, I need the following service definition:

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-ssl-cert: "arn:aws:iam::723765766667:server-certificate/certificate-name"
    service.beta.kubernetes.io/aws-load-balancer-ssl-ports: "443"
  name: myproject
  labels:
    run: myproject
spec:
  type: LoadBalancer
  ports:
    - name: "http"
      port: 80
      targetPort: 80
      protocol: "TCP"
    - name: "https"
      port: 443
      targetPort: 80
      protocol: "TCP"
```

- `service.beta.kubernetes.io/aws-load-balancer-ssl-cert` is used to specify the
SSL certificate you would like to use. You can find of list of you server
certificates by querying AWS: `aws iam list-server-certificates`.

- `service.beta.kubernetes.io/aws-load-balancer-ssl-ports` specifies the ports
you would like these SSL certificates to be used on. The default is `*` (ALL).


Hooray!
