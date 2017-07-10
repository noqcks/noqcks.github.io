---
layout: post
comments: true
title: Jenkins Installed Plugins List
excerpt: How to programatically list all installed Jenkins plugins
category: note
---


I need a way to get a list of plugins so that I can use them with [docker jenkins](https://hub.docker.com/_/jenkins/)
in the format `<plugin>: <version>`

# 1. Get the jenkins cli.

The jenkins CLI will allow us to interact with our jenkins server from the command line. We can get it with a simple curl call.

```
curl 'localhost:8080/jnlpJars/jenkins-cli.jar' > jenkins-cli.jar
```

# 2. Create a groovy script.

We need to create a groovy script to parse the information we receive from the jenkins API. This will output each plugin with its version. Save the following as `plugins.groovy`.

```
def plugins = jenkins.model.Jenkins.instance.getPluginManager().getPlugins()
plugins.each {println "${it.getShortName()}: ${it.getVersion()}"}
```

# 3. Call the Jenkins API.

Use jenkins-cli.jar to call the jenkins server where it is located (localhost:8080) in this case with the username and password you use for login and then output the results to plugins.txt

```
java -jar jenkins-cli.jar -s http://localhost:8080 groovy --username "admin" --password "admin" = < plugins.groovy > plugins.txt
```

The output looks like this

```
ace-editor: 1.1
ant: 1.5
antisamy-markup-formatter: 1.5
authentication-tokens: 1.3
blueocean-autofavorite: 1.0.0
blueocean-commons: 1.1.4
blueocean-config: 1.1.4
blueocean-dashboard: 1.1.4
blueocean-display-url: 2.0
blueocean-events: 1.1.4
blueocean-git-pipeline: 1.1.4
blueocean-github-pipeline: 1.1.4
blueocean-i18n: 1.1.4
blueocean-jwt: 1.1.4
blueocean-personalization: 1.1.4
blueocean-pipeline-api-impl: 1.1.4
blueocean-pipeline-editor: 0.2.0
blueocean-pipeline-scm-api: 1.1.4
blueocean-rest-impl: 1.1.4
```

Enjoy!
