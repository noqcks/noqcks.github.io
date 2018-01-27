---
title: Ansible Role Testing
date: '2016-01-29'
categories:
  - immutable-infra
tags:
  - ansible
slug: ansible-role-testing
---

Repos:

- [Ansible fish role](https://github.com/noqcks/ansible-fish)

  The role we're going to test.

- [Test Kitchen example repo](https://github.com/noqcks/ansible-test-kitchen-exmaple)

  A bootstrap repo for testing ansible roles.

I've noticed that documentation on how to use test kitchen with ansible is sparse. It took me a while to get it set up with my ansible roles; having to figure out how test-kitchen and serverspec worked together was a huge pain. I'm hoping to lower the barrier of entry for new people trying to test their infrastructure.

First let's start with an ansible role to test. `ansible-fish` is simple enough to explain. It's just a simple `apt-get` install of `fish`.

There are three parts to this testing strategy:

##### 1. [Test Kitchen](#test-kitchen)

  This is what we use to spin up a consistent virtual environment to run our `ansible-fish` role on.

##### 2. [Serverspec](#serverspec)

  The test framework we're using to actually test that `ansible-fish` does what we expect.

##### 3. [Travis-ci](#travis-ci)

  A hosted continuous integration server that is free for open source projects. This will give us immediate feeback on every commit.

## Test Kitchen

[Test kitchen](http://kitchen.ci/) is a "test harness tool to execute your configured code on one or more platforms in isolation." It was originally created for running tests against chef cookbooks, but is now available for CM tools like ansible and salt.

There are 4 important components of test kitchen:

#### 1. Drivers

  Drivers allow you to run your tests on cloud providers and virtualization technologies. Drivers are available for docker, vagrant, azure, aws, and more.

#### 2. Platforms

  Platforms are the operating system that you run your tests on. Can be windows or linux.

#### 3. Provisioners

  A provisioner is the configuration management tool used to provision the environment. Can be Ansible, Chef, Puppet, or Salt.

#### 4. Test Suites

  A test suite are the tests you would like to verify against the environment. Can be Bats, Cucumber, Rspec, Serverspec.


There are a few files we need to get test kitchen working.

- .kitchen.yml

~~~~~~~~
---
driver:
  # were using docker here as our driver
  name: docker
  privileged: true

provisioner:
  # use an ansible playbook to provision our server
  name: ansible_playbook
  # name of the host
  hosts: test-kitchen
  # list ansible galaxy requirements from meta/main.yml
  requirements_path: requirements.yml


platforms:
  # running our tests on ubuntu 14.04
  - name: ubuntu-14.04

suites:
  # suites found at /test/integration/$test-name
  - name: default
~~~~~~~~

- test/integration/default/default.yml

This was specified in `suites` -> `name` in our .kitchen.yml file

~~~~~~~~
---
  # host to test against
- hosts: test-kitchen
  remote_user: root

  roles:
    # name of role to test
    - role: ansible-fish
~~~~~~~~

And finally

- requirements.yml

~~~~~~~~
---
# dependencies found in meta/main.yml of the ansible role
- src: telusdigital.apt-repository

~~~~~~~~

## Serverspec

[Serverspec](http://serverspec.org/) is basically like rspec, but for infrastructure.

Wanna check if a package is installed? BINGO

~~~~~~~~
describe package('httpd') do
  it { should be_installed }
end
~~~~~~~~

Wanna check that nginx is listening on port 80? BINGO x2

~~~~~~~~
describe port(80) do
  it { should be_listening }
end
~~~~~~~~

To get this set up we need a few files as well

- test/integration/default/serverspec/spec_helper.rb

~~~~~~~~
require 'serverspec'

# :backend can be either :exec or :ssh
# since we are running local we use :exec
set :backend, :exec
~~~~~~~~

- test/integration/default/serverspec/localhost/default_spec.rb

~~~~~~~~
require 'spec_helper'

describe 'ansible-fish::default' do

  describe package('fish') do
    it { should be_installed.by('apt') }
  end

end

~~~~~~~~

And that's it. The test in `default_spec.rb` will be used to test that our fish role installed the package `fish` correctly.


## Travis-ci

[Travis-ci](https://travis-ci.org/) is a hosted continuous integration platform that is free for all open-source projects. It's fast, free, and has many integrations with other services like github and slack.

Travis was originally a pain-in-the-ass for me. It's a little weird, but essentially we're going to be running docker inside docker. 0.o. Rnuning a test kitchen instance (docker) inside a travis-ci instance (docker). One thing to note, I have not been able to get vagrant working inside travis-ci. If you can, leave a comment, I would love to know how.

We only need one file for this

- .travis.yml

~~~~~~~~
---
sudo: required
# we're using docker for test kitchen, so we need to require it here
services: docker

install:
  # install all the test kitchen gems from the Gemfile
  - bundle install

script:
  # run kitchen tests (destroy, create, converge, setup, verify and destroy)
  - bundle exec kitchen test

~~~~~~~~

And that's it, you have just tested an ansible role. Enjoy!
