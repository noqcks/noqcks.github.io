---
title: "Get Notified On New GitHub Issues w/ Labels"
date: '2018-03-31'
tags:
  - kubernetes
  - devops
  - open-source
slug: notifications-for-help-wanted-gh-issues
---

I like to contribute to some open source projects in my spare time, and for
some hotly contested projects (*cough* kubernetes *cough*) it can be hard to
find interesting tasks to pick up!

I found a way on Zapier to get alerts via SMS whenever an issue is created in GitHub
with a `help wanted` label. But you can use this for any kind of label you can
imagine.

This is the flow:
<p><img src="/img/zapier-flow.png" width="400px" alt=""></p>

Hookup GitHub and pull issues:
<p><img src="/img/zapier-gh-issue.png" alt=""></p>

Setup filtering on `help wanted`:
<p><img src="/img/zapier-filter.png" alt=""></p>

Send SMS when a new issue arrives using Twilio:
<p><img src="/img/zapier-send-sms.png" alt=""></p>
