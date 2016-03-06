---
layout: post
title: HTTP/2 and ECDSA Cipher Suites
category: note
---

If you're using an ECC certificate with HTTP/2, you may get the following error from Google Chrome:

```
ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY
```

If you googled about it you will finally find the following HTTP/2 requirements:

> A deployment of HTTP/2 over TLS 1.2 SHOULD NOT use any of the cipher suites that are listed in the cipher suite black list ([Appendix A](https://http2.github.io/http2-spec/#BadCipherSuites)).

and…

> The black list includes the cipher suite that TLS 1.2 makes mandatory, which means that TLS 1.2 deployments could have non-intersecting sets of permitted cipher suites. To avoid this problem causing TLS handshake failures, deployments of HTTP/2 that use TLS 1.2 MUST support `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` [[TLS-ECDHE](https://http2.github.io/http2-spec/#TLS-ECDHE)] with the P-256 elliptic curve [[FIPS186](https://http2.github.io/http2-spec/#FIPS186)].

So you need the following cipher suite at the beginning of your suites:

```nginx
ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256
```

In my case if you're using a ECC certificate you will need:

```nginx
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256
```

According to the specs:

> Note that clients might advertise support of cipher suites that are on the black list in order to allow for connection to servers that do not support HTTP/2. This allows servers to select HTTP/1.1 with a cipher suite that is on the HTTP/2 black list. However, this can result in HTTP/2 being negotiated with a black-listed cipher suite if the application protocol and cipher suite are independently selected.

You can use black-listed cipher suites for backward compatibility.
