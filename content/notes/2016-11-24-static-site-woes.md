---
title: 😢 Static Site Woes 😡
date: '2016-11-24'
tags:
  - static-sites
slug: static-site-woes
---
Let's say you have a personal website and you want to host it somewhere. Maybe
it's statically generated via Hugo or Jekyll, or maybe it's a simple Single Page
App (SPA). Most people choose one of two options; Github Pages or a combination
of AWS S3 with CloudFront.

GitHub Pages is easy to setup for a very simple Jekyll based blog, but once you
start requiring things like custom domain SSL or using a static site generator
that's not Jekyll you begin hacking to try work around GitHub Pages (I mean just
look at [this](https://gohugo.io/tutorials/github-pages-blog/) long and messy
tutorial to get Hugo working with gh pages). Another frustrating thing is that
there is only support for [specific versions](https://pages.github.com/versions/)
of gems to build your site.

AWS and S3 is a very cheap and cost-effective service (I pay on average $0.50 per
month to host this site). You can easily use a custom domain with SSL, you're not
limited to specific dependencies, and you can host literally any static site. The
downside however is that it requires a certain level of technical know-how that
most people don't have the time to learn. I remember being a Jr. Dev and spending
almost an entire day trying to set up my personal blog just right.

Trying to host a static site today is needlessly complicated, and there's a big gap
between the simplicity (or over-simplicity) of GitHub Pages and the
complexity of self-hosting. It should be easier. I want it to be easier. I would
like to do this by creating a SaaS for static site hosting that has the following
features:

```
- support for all major static site generators (hugo/jekyll)
- pay only for what you use
- custom domain SSL
- as fast as GitHub Pages
- commit-to-deploy type deploys (same as gh pages)
- password protected sites
- easy redirect rules
- no dependency locking on jekyll gems
- only slightly more expensive than S3/CloudFront
```

- Is there anything else you think would make a static site SaaS awesome?
- Would you use a static site SaaS with these features?

Let me know in the comments!
