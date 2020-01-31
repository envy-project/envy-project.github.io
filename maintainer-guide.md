---
layout: content
title: Setting up a project with ENVy
description: A step-by-step guide for setting up ENVy to work with your project.
---

This is a step-by-step guide for adding ENVy support to your project. This guide is intended for first-time setup on a project -  if you are looking to modify a project that already has ENVy set up, see the `.envyfile` [reference document](envyfile-reference.html). You will also need to have ENVy itself installed, see the [quick start guide](getting-started.html). ENVyfiles are written in YAML, which is quick to learn and use. If you want a more in-depth guide, you can read a [tutorial on YAML syntax here](https://gettaurus.org/docs/YAMLTutorial/).

A number of example ENVyfiles are available for a variety of languages and frameworks, see our [examples repository](http://github.com/envy-project/examples). You may still get some value out of following this guide to help you customize ENVy to better suit your progress.

Creating the Configuration File
---

ENVy uses a single configuration file within your project. Create an empty file named `.envyfile` at the root of your project directory. This directory will be mounted inside of the development environment, so avoid creating the ENVyfile in an unused sub-folder.

ENVy will now detect your project as ENVy compatible, though an empty configuration file isn't particularly useful. The next sections will walk you through filling out the configuration file.

Setting up the Environment
---

The first major part of the ENVyfile is configuring the environment your project will run in.

Choosing your Base Image
---

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
ENVy manages it's environments using Docker, which means when creating an ENVy environment you can give any Docker base image to start from. (Note: ENVy currently only supports Ubuntu-based images). If this key is not specified, ENVy will use `ubuntu:18.04`, a barebones Ubuntu image. This image contains only a very basic system, commands such as `curl` aren't present. If there is a more suitable base image for your project, use that here. If not, you can customize your  environment further in the rest of the configuration.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>environment:
  base:
    image: ubuntu:18.04
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}

Declaring System Packages
---
At this point, we have the starting point for our environment, but it doesn't contain all of the dependencies we need. We need to further customize the environment. The first step to this customization is adding system packages, specifically `apt` packages on Ubuntu. For each package, you will need to specify the package name as the 'recipe', and you can optionally use a specific version with the 'version' key.

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
For envy-project, we need `python3.7`, `python3-pip`, `git` and `docker`.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>environment:
  base:
    image: ubuntu:18.04
  system-packages:
    - recipe: docker
      version: 1.5-1build1
    - recipe: git
    - recipe: python3.7
      version: 3.7.1-1~18.04
    - recipe: python3-pip
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}

Adding Environment Setup Steps
---

After selecting system packages, there are usually several more steps to getting the environment working for your project. ENVy supports this through the use of "setup" steps. For a frontend system, this might be running `npm install`; for a low-level C project, that might be downloading libraries from Conan. Each of these steps should be added to the envyfile under the ‘setup-steps:’ key.

**Simple Steps**

In order to define a step, we need two values: a name for the step and a list of Bash commands to execute for it. While not required, its recommended to name your steps like a variable: human-readable but without whitespace (you may want to reference the step by name later!). Steps will be run in order.

Note that steps should be written in such a way that they are *idempotent* - running them multiple times should not cause any problems.

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
For envy-project, we have a few steps to do. First, installing the `python3.7` package doesn’t cause it to be installed as the default Python, so we need a step to set that up. Next, we use pipenv, so we need to install it. Finally, we need to install all our python dependencies into the pipenv environment.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>environment:
  base:
    image: ubuntu:18.04
  system-packages:
...
  setup-steps:
    - name: python3-symlink
      run:
        - "rm /usr/bin/python3"
        - "ln -s /usr/bin/python3.7 /usr/bin/python3"
    - name: pipenv
      label: "Installing pipenv"
      run:
        - "pip3 install pipenv"
    - name: python-deps
      label: "Installing python dependencies"
      run:
        - "pipenv install -d"
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}

**Adding Triggers for steps**:

Sometimes you would like steps to be re-run in certain cases. The (optional) 'triggers' key allows for this behaviour. By default, ENVy doesn’t know how your steps are related, or when they might need to re-run due to a change. So, without setting a trigger, your setup step will run once in a given environment. If you wanted it to run again, you would need to `envy nuke` the entire environment and rebuild it from scratch. In order to avoid this, you can set triggers a given step, telling it to re-run under certain conditions. As an example, you may want to re-run your `pipenv install` command if the `Pipfile` or `Pipfile.lock` changes.

Triggers are the most complex part of writing an Envyfile, though in most cases you'll only need to set up one or two. For a more in-depth discussion of available trigger types, see the [ENVyfile reference document](envyfile-reference.html)

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
For Envy, we will add triggers for system packages (causing steps to be re-run when packages are updated), as well as files (so that we re-install our dependencies when they change), and steps (so that when certain steps re-run, their dependant steps are also run).
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>environment:
  base:
    image: ubuntu:18.04
  system-packages:
...
  setup-steps:
    - name: python3-symlink
      run:
        - "rm /usr/bin/python3"
        - "ln -s /usr/bin/python3.7 /usr/bin/python3"
    - name: pipenv
      run:
        - "pip3 install pipenv"
      triggers:
        system-packages:
          - python3.7
          - python3-pip
    - name: python-deps
      run:
        - "pipenv install -d"
      triggers:
        files:
          - Pipfile
          - Pipfile.lock
        steps:
          - pipenv
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}


Adding Actions
---

The ENVyfile now reflects the desired state of the development environment. At this point, we will define *actions* that contributors regularly use.

Actions are composed of three keys, 'name', 'script', and 'help'. Each action requires a 'name', which is used to invoke it on the command line. The 'script' key is the one-line Bash command that is run inside the environment when the subcommand is invoked. The 'help' key is the information the contributor will see in the `envy --help` message. Normally it describes what the action does and when a contributor would use it.

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
Envy-project has three common workflow commands typified as actions: linting the whole project, and executing one of `pylint` or `black` independently.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>environment:
  base:
    image: ubuntu:18.04
  system-packages:
...
  setup-steps:
...
actions:
  - name: lint
    script: 'pipenv run black . ; pipenv run pylint envy'
    help: 'lint the project'
  - name: pylint
    script: 'cd envy ; pylint envy'
    help: 'run pylint alone'
  - name: black
    script: 'black .'
    help: 'run black alone'
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}

Adding Services
---

Many projects require secondary "sidecar" services to have a complete development environment. These include database daemons, caches, or proxy servers. The recommended way to organize this with ENVy is to use the `docker-compose` tool to organize such services.

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
You can specify a docker-compose configuration file using the `compose-file` key. The services specified in this file will be started when `envy up` is run, and stopped when `envy down` is run.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>environment:
  base:
    image: ubuntu:18.04
  system-packages:
...
  setup-steps:
...
actions:
  ...
services:
  compose-file: docker-compose.yml
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}
