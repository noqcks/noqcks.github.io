---
layout: post
comments: true
title: Static Site on Google Cloud
category: note
---

**Forewarn**: Google Cloud currently does not offer any way to enable SSL for sites
hosted on their Storage Engine. Either through the hack of using a Load Balancer,
or from the asset bucket directly.

I have been hosting this site on S3 for a while now, and I wanted to get more
familiar with Google Services, so I decided to see how much faster I could get
site while also learning about the Google Cloud! This site is using Jekyll to
compile static content, but any static site generator will do.

This guide assumes that you have enabled billing in your Google Cloud account,
and also have the `gcloud` cli installed.

First things first you need to create the bucket you will host your assets. We
will be using the `gsutil` command to interact with Cloud Storage (which comes
with the `gcloud` SDK). The name of the bucket should be the exact name of the
website you want to serve. In this case `www.noqcks.io`.

```
gsutil mb gs://www.noqcks.io
```

Upload your static content:

```
gsutil -m rsync -r _site/ gs://www.noqcks.io
```

Then we will need to set an ACL on the contents in this bucket. We will be setting
the property of `Read` (R) for `allUsers`

```
gsutil -m acl ch -r -u AllUsers:R gs://www.noqcks.io
```

The `ch` here stands for change. The dash `-u` is to specify a user, in this
case `AllUsers`.

Then we can set the index for the website.

```
gsutil web set -m index.html gs://www.noqcks.io
```

Now at this point, we can easily serve our website by just pointing our website
to `c.storage.googleapis.com`.

```
NAME              TYPE      DATA
www.noqcks.io     CNAME     c.storage.googleapis.com
```

Or if we wish, we can set up [fastly](https://www.fastly.com/) as a CDN to get
those sweet sweet sub 100ms page load speeds for simple static sites.

We can go [here](https://app.fastly.com/#quickstart) to set up the fastly config
for our site. Fill it out according to your site like so:


<p><img src="{{ site.file }}/fastly-setup.png" alt="Screenshot showing fastly setup"></p>

And we're done! You can now point your DNS name to use the fastly CDN

```
NAME              TYPE       DATA
www.noqcks.io     CNAME      global.prod.fastly.net
```

I took some screenshots of the performance before and after using fastly. The
performance gained is crazy...

This is before:
<p><img src="{{ site.file }}/www.noqcks.io-page-speed-before.png" alt="Screenshot showing page speed before fastly"></p>


And this is after:
<p><img src="{{ site.file }}/www.noqcks.io-page-speed-after.png" alt="Screenshot showing page speed after fastly"></p>


