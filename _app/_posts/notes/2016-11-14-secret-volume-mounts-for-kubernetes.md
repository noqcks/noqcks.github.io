---
layout: post
comments: true
title: Secret Volume Mounts in Kubernetes
category: note
---

This is how you mount secret volumes in Kubernets. In this example, it is an Nginx SSL cert/key, but this will work for any secret you want to mount. This is the Kubernetes deployment yaml:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: $PROJECT_NAME
spec:
  replicas: 2
  template:
    metadata:
      labels:
        run: $PROJECT_NAME
    spec:
      containers:
      - name: $PROJECT_NAME
        image: $IMAGE_REPO
        volumeMounts:
          - name: ssl
            readOnly: true
            mountPath: /etc/nginx/ssl
      volumes:
        - name: ssl
          secret:
            secretName: ssl
```

and the Kubernetes secret yaml looks like:

```
apiVersion: v1
kind: Secret
metadata:
  name: ssl
type: Opaque
data:
  server.crt: < base64 cert here >
  server.key: < base64 key here >
```

You can create these secrets from a file containing your SSL cert/key like so:

```
openssl base64 -in server.key -out server.key.b64
openssl base64 -in server.crt -out server.crt.b64
```

Just remember to strip newlines after encoding to base64. Kubernetes secrets doesn't play nice with newlines.

And of course, all of the above assumes that you have Nginx SSL directives that look like this:

```
server {
  ssl_certificate /etc/nginx/ssl/server.crt;
  ssl_certificate_key /etc/nginx/ssl/server.key;
}
```
