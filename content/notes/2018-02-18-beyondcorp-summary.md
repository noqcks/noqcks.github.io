---
title: "BeyondCorp: Enterprise Security At Google"
date: '2018-02-18'
tags:
  - google
  - security
slug: beyondcorp-summary
---

A Summary of the BeyordCorp paper.

[BeyondCorp: A New Approach to Enterprise Security](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43231.pdf). Ward & Beyer, USENIX DEC 2014

This paper challenges the idea of [perimeter security](https://en.wikipedia.org/wiki/Perimeter_Security) for corporate networks, and describes the implementation of BeyondCorp as an alternative.

> "Virtually every company today uses firewalls to enforce perimeter security.”

The problem here is that everyone inside the perimeter is trusted and everyone outside is not! This is false on two fronts; You can have an intruder internally that is not trusted, and you can have a valid employee working from a coffee shop for example.

> "The perimeter security model works well enough when all employees work exclusively in buildings owned by an enterprise."

The growth of a mobile workforce and cloud services have challenged this perimeter assumption: apps are in public datacenters, and users are working outside the office!

BeyondCorp is a model that only depends on `device` and `user` credentials. Location does not matter.

### The Components of BeyondCorp

This is a diagram of BeyondCorp.

<p><img src="/img/beyondcorp-model.png" alt="Screenshot showing beyondcorp"></p>


There are 5 major components of BeyondCorp.

#### 1. Identify Device

All devices are stored in a database and then identified using a certificate.

**Device Inventory Database**

Each "managed device" is listed here.

**Device Identity**

Each device has a certificate locally that can authenticate itself against the device inventory database.

#### 2. Identify User

All users and groups are stored in a database, identified using traditional credentials, and then given a SSO token to access some services.

**User/Group Database**

All users/groups are stored here.

**SSO System**

An externalized service that gives SSO tokens to authenticated users.

#### 3. De-Trusting Internal Network

**Unprivileged Network**

"An unprivileged network that very closely resembles
an external network, although within a private address space" is deployed so that internal users do not have special access to services.

**802.1.x Authentication**

Wired and Wireless networks are authenticated w/ 802.1x using the device certificate used for device identity.

#### 4. Externalizing Apps

**Internet Facing Proxy**

All enterprise apps are exposed externally via a proxy that allows access after access control checks.

**Public DNS**

All enterprise apps are exposed externally via CNAME.

#### 5. Inventory-Based Access Control

**Trust Inference for User/Device**

The level of access for a user/device is determined dynamically over time based on device type, location, etc.

**Access Control Engine**


At the proxy level, this engine "provides service-level authorization on a per-request basis". This can also enforce location-based access if needed.

----

This is a much saner policy for a mobile workflow in my opinion. Thanks Google!
