---
layout: post
title: CloudFront Cost Control
category: note
---

If you're using CloudFront to serve your website, here's some tips that can help reduce the cost.

Here's a output sample of my deploy script:

```sh
$ make
[info] Deploying /Sites/sparanoid.com/* to sparanoid.com
[succ] Updated lab/amsf/assets/css/app.css
[succ] Updated lab/amsf/assets/js/custom.js
[succ] Updated lab/amsf/assets/themes/curtana/js/intense.js
[succ] Invalidated 3 items on CloudFront
[info] Summary: Updated 3 files. Transferred 15.2 kB, 8.0 kB/s.
```

The invalidation price will be high if you like me, deploy sites via git hooks so every time you deploy sites to CloudFront the invalidation triggers. So here's a workaround way to avoid invalidation but also refresh caches for users.

> File hashes to the rescue.

Here's another output sample of optimized deploy script:

```sh
$ make
[info] Deploying /Sites/sparanoid.com/* to sparanoid.com
[succ] Deleted lab/amsf/assets/css/app.css
[succ] Deleted lab/amsf/assets/js/custom.js
[succ] Deleted lab/amsf/assets/themes/curtana/js/intense.js
[succ] Created lab/amsf/assets/css/app.17b3a8ad.css
[succ] Created lab/amsf/assets/js/custom.53609257.js
[succ] Created lab/amsf/assets/themes/curtana/js/intense.5797aa35.js
[info] Summary: Created 3 files. Deleted 3 files. Transferred 15.2 kB, 8.2 kB/s.
```

There's no invalidation anymore, The cost of invalidation requests is first 1,000 paths free, then $0.005 per path. For example if you deploy your sites for client preview, let's say 20 there're 10 deploys per day with an average of 20 files (CSS, scripts and images) per deploy, all done automatically via git hooks. You will have 200 invalidations per day. 1000 for five days then you'll run out of free tier. you have to pay $5 per 1000 request if you continue requesting invalidations.

So in my [current setup](http://sparanoid.com/lab/amsf/), I use [grunt-cache-bust](https://github.com/hollandben/grunt-cache-bust) to hash files. All file references will be updated automatically via Grunt task.
