---
title: "Prometheus: Setup A Dead Man's Switch!"
date: '2018-01-29'
tags:
  - prometheus
  - alertmanager
  - alerting
slug: prometheus-alertmanager-deadmansswitch
---

A [Dead Man's Switch](https://en.wikipedia.org/wiki/Dead_man%27s_switch) is an
alert that allows us to trigger an alert when our Prometheus cluster is no
longer functioning correctly. This is important, because it would be a disaster if
our monitoring pipeline went down and critical alerts weren't being triggered!

In Prometheus, we need to [define an alerting rule](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)
that continuously triggers/alerts, so that if it no longer triggers, we know.

```
groups:
- name: meta
  rules:
    - alert: DeadMansSwitch
      expr: vector(1)
      labels:
        severity: critical
      annotations:
        description: This is a DeadMansSwitch meant to ensure that the entire Alerting
          pipeline is functional.
        summary: Alerting DeadMansSwitch
```

Once we have the Alert working in Prometheus, we can configure Alertmanager. For this
config I will be using email notification to a service called [DeadMansSnitch](https://deadmanssnitch.com)
that handles DeadMansSwitch alerting and has a [nice integration with pagerduty](https://www.pagerduty.com/docs/guides/dead-mans-snitch-integration-guide/).

In DeadMansSnitch, we need to configure our time interval to 15mins. This will alert
us if our DeadMansSwitch alert in Alertmanager has been silent 3 times, and allows us to sidestep
the unreliability of email, which we'll use from AlertManager to DeadMansSnitch
in want of a better option.

<p><img src="https://www.noqcks.io/img/deadmanssnitch.png" alt="Screenshot showing deadmanssnitch"></p>

In AlertManager, this will be our config. You will need to replace the `to:` email
to the DeadMansSnitch email for your account.

alertmanager.yml
```
global:
    smtp_smarthost: "smtp.sendgrid.net:587"
    smtp_from: "Alertmanager <alertmanager@yourcorp.com>"
    smtp_auth_username: apikey
    smtp_auth_password: "<api key here>"
route:
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: deadmansswitch
  email_configs:
  - to: "youraccount@nosnch.in"
```

You now have Prometheus/Alertmanager triggering DeadMansSnitch!

<p><img src="https://www.noqcks.io/img/deadmanssnitch-green.png" alt="Screenshot showing deadmanssnitch"></p>

You can now setup a [Pagerduty integration](https://www.pagerduty.com/docs/guides/dead-mans-snitch-integration-guide/)
to page you when the DeadMansSwitch fails.

Enjoy.
