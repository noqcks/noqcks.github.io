---
layout: post
comments: true
title: Dead Simple GitHub Releases for Golang Projects
category: note
---

This is a method I use personally to easily deploy releases to GitHub for a golang project I maintain; you can check out the source [here](https://github.com/noqcks/gucci). It uses travis-ci to do the heavy lifting and binary building and then releases the binaries to GitHub!

It's really pretty simple.

**Steps**:

**1.** run your tests

**2.** build the golang binaries for various systems (amd64, i686, darwin, linux, etc)

**3.** upload these binaries to GitHub on each tagged commit


First, we will set a version in our `VERSION` file

`VERSION`

```
0.0.4
```

The full `.travis.yml` file is below and it explained by comments in the file.

```
sudo: false
language: go

go:
- 1.5
- 1.6
- tip

env:
  global:
    # getting our version from the VERSION file
    - VERSION=$(cat VERSION)

before_script:
  - go get

script:
  - go test

# before we deploy, we go build for all operating systems we would like to support
before_deploy:
  - mkdir -p release
  - "GOOS=linux  GOARCH=amd64 go build -o release/gucci-v$VERSION-linux-amd64"
  - "GOOS=darwin GOARCH=amd64 go build -o release/gucci-v$VERSION-darwin-amd64"

# this tells travis-ci to create a github release and deploy artifacts to
# github _only_ when there is a tag associated with a commit. That is what the tags: true means.
# You can setup this section really easily by doing `$ travis setup releases`
deploy:
  provider: releases
  api_key:
    secure: yGwDXUYPbpUFZk0csGWmMBJQQoNeCiWjXKQsZS8bcignCOQyDb5qvYJAJ58pBAJApGP2w/+vFIhb5z4EHJzZZMoaygykdcdBunpIeMcFlFxIj0ziMu5iYH92wevkuuUs5+Eb35+MjD5txhh4ndyvH4lAlnAktqF+c71V+a/thmfu71reCkT86FE+OroN1X51wtTtO2Y+w65oab5LdXU40yizLumHMXDgQ8mQ73gt6OZvZmCF9P9Toc0ps2C444tA+mul/WZcRVYAD3aKi1XyhNixO18/p7tAaCoRpzgZO3oGIgVRL+0OdWCcCvyLfi9vMvj7BZneZ/KMHemi3xyPq6s+aTL4KVVU7uCfobG3/rnqak1t3G3V2GHMsXPYbvD8SM3X8KY+P3bPPlYcD0WXF4R1ryAd9kVDF287fTxwanlMV4ReVac117rDHqwTPc+aEewGIF+4mkYqTpBiKlfKqyf4ow4bDtAAWPNxSEjEuFWmVouMwaYFbbVzDnVn6iKYiT0ZcplIyXDG6JvkLSNqoJ9c6/6MOZ4My7ZccPKuXfI6qzIjKAjPJlhgox/LFTbAeOYlvNseSYPX1z7B/bEwDXTa6jYuPnOs+LiEUgoVRxyQkAfJG/D9zDqBvDyonRl6ksc50yv6Ss1GG7DOjiKf/wVzRbsTeIBdqmU2qblS3s0=
  file:
    - "release/gucci-v$VERSION-linux-amd64"
    - "release/gucci-v$VERSION-darwin-amd64"
  skip_cleanup: true
  on:
    tags: true
```

That's it! Now, whenever you are ready to deploy a new version of your golang app, you can just create a git tag


```
git tag -a v0.0.4 -m "Your release message"
```

and then push your tags to GitHub. Travis-ci will build and deploy your application to GitHub Releases!

```
git push --tags origin master
```

