---
layout: content
title: ENVYfile Reference
description: Detailed guide to ENVyfile syntax and contents.
---

<style>
.tooltip {
  position: relative;
  display: inline-block;
  border-bottom: 1px dotted black;
}

.tooltip .tooltiptext {
  visibility: hidden;
  background-color: #555;
  color: #fff;
  padding-left: 10px;
  padding-right: 10px;
  text-align: center;
  border-radius: 6px;
  padding-top: 5px;
  padding-bottom: 5px;
  position: absolute;
  z-index: 1;
  bottom: 125%;
  left: 50%;
  margin-left: -60px;
  opacity: 0;
  transition: opacity 0.3s;
}

.tooltip .tooltiptext::after {
  content: "";
  position: absolute;
  top: 100%;
  left: 50%;
  margin-left: -5px;
  border-width: 5px;
  border-style: solid;
  border-color: #555 transparent transparent transparent;
}

.tooltip:hover .tooltiptext {
  visibility: visible;
  opacity: 1;
}
h1:not(#logo) {
  font-size: 2.5em;
  line-height: 1.25em;
  font-weight: bold;
}

.indentThis {
  margin-left: 50px;
}
</style>

# Anatomy of an Envyfile

This is an overview of what an Envyfile can look like. Hover over the underlined text to see an explanation of the line
<br />
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child" style="color: black; background-color: #eee;"><pre class="highlight">
<code>environment:
  base:
    <span style="color: maroon" class="tooltip">image: ruby:stretch<span class="tooltiptext">The Docker image to use as a base for this environment</span></span>
  <span style="color: blue" class="tooltip">system-packages:<span class="tooltiptext">List of APT packages to be installed in the environment</span></span>
    - <span style="color: darkblue" class="tooltip">recipe: python3.7<span class="tooltiptext">APT package name</span></span>
      <span style="color: darkblue" class="tooltip">version: 3.7.1-1~18.04<span class="tooltiptext">APT package version</span></span>
    - recipe: docker
  <span style="color: green" class="tooltip">setup-steps:<span class="tooltiptext">Additional setup steps not included in APT packages</span></span>
    - <span style="color: darkgreen" class="tooltip">name: bundle<span class="tooltiptext">The name of the script</span></span>
      <span style="color: magenta" class="tooltip">label: "Installing Ruby Dependencies"<span class="tooltiptext">The step label that will be displayed when envy runs this step</span></span>
      <span style="color: teal" class="tooltip">run:<span class="tooltiptext">Commands to run to complete this step</span></span>
        - <span style="color: teal" class="tooltip">"bundle install"<span class="tooltiptext">A command passed to the Bash shell - can contain Bash special characters.</span></span>
      <span style="color: purple" class="tooltip">triggers:<span class="tooltiptext">List of things that can cause this step to be run (in addition to the first envy up)</span></span>
        <span style="color: purple" class="tooltip">files:<span class="tooltiptext">Files that are watched for changes and should cause this step to run</span></span>
          - <span style="color: purple" class="tooltip">Gemfile<span class="tooltiptext">A Ruby gemfile, specifying Ruby packages to be installed</span></span>
          - Gemfile.lock
  <span style="color: red" class="tooltip">x-forward: True<span class="tooltiptext">Enable X-forwarding for the project. Optional, defaults to False.</span></span>
<span style="color: goldenrod" class="tooltip">actions:<span class="tooltiptext">Actions are scripts or commands contributors are likely to use often</span></span>
  - <span style="color: peru" class="tooltip">name: lint<span class="tooltiptext">The name of the command, used to invoke it</span></span>
    <span style="color: chocolate" class="tooltip">script: 'rubocop'<span class="tooltiptext">The Bash invocation to be run upon invoking the command</span></span>
    <span style="color: saddlebrown" class="tooltip">help: 'lint the project'<span class="tooltiptext">The help text displayed when envy --help is used</span></span>
  - name: run
    script: 'ruby hello.rb'
    help: 'run the project'
  - name: bundle
    script: 'bundle'
    help: 'run bundler'
services:
  <span style="color: tomato" class="tooltip">compose-file: docker-compose.yml<span class="tooltiptext">An _optional_ docker-compose file, started and stopped with the ENVy environment</span></span>
<span style="color: navy" class="tooltip">network:<span class="tooltiptext">The docker network configuration for envy. Specificy "host" to use docker's host networking (not fully implemented on Docker for Mac), or provide a dictionary with the options below</span></span>
  <span style="color: orange" class="tooltip">name: my-docker-network<span class="tooltiptext">The name of a specific docker network for the envy container to run on. Usually only useful in conjuction with a docker-compose file</span></span>
  <span style="color: orchid" class="tooltip">ports: <span class="tooltiptext">The list of ports to map from the environment to your host network. Useful for accessing web servers and similar that are run inside envy.</span></span>
    - 43
    - 80:8080
</code>
</pre></div></div>
<br />
{::options parse_block_html="true" /}

# Environment
<div class="indentThis" markdown="1">
The Environment object describes the development environment that will be used to work on the project. This object contains `base`, `system-packages`, and `setup-steps`.


`base`
---
<div class="indentThis" markdown="1">
An object (_optional_).

This object describes the base container. Normally, this container is barebones, with the utilities actually being used installed by the system packages and setup steps sections.

This will contain exactly one key, `image`, an _optional_ string. This is the Docker image used as the starting point for the environment. (optional, default is ubuntu:18.04). Note that for the `system-packages` configuration to work, this image will need to support the `apt` package manager.

```
environment:
  base:
    image: ubuntu:18.10
```
<br />
<br />
</div>

`project-dir`
---
<div class="indentThis" markdown="1">
A string (_optional_)

This string describes the project directory mount path within the ENVy environment. If not specified, it defaults to /project. This can be useful for certain projects (e.g. Golang) where the project must exist and a particular path format. This path must be specified as an _absolute path_.

```
environment:
  project-dir: '/anotherDirectory'
```
<br />
<br />
</div>

`system-packages`
---
<div class="indentThis" markdown="1">
An array (_optional_)

This array contains a list of packages that will be installed on the system. Each package is specified by a `recipe` (mandatory), and an optional `version`. The recipe refers to an exact apt package name, and the version refers to a version available in the repository. The [Ubuntu package repository](https://packages.ubuntu.com) is a good place to search for recipes and versions.

```
environment:
  system-packages:
    - recipe: docker
    - recipe: python3.7
      version: version: 3.7.1-1~18.04
```
<br />
<br />
</div>

`setup-steps`
---
<div class="indentThis" markdown="1">
An array (_optional_).

This array contains a list of setup steps that will be performed after packages are installed during the initial `envy up`, and, optionally, at points in the future when `envy up` is invoked.

Each step has a `name` (mandatory, string) to describe the step, a `run` (mandatory, array of strings)  to list the commands that must be run to complete the step, and some have `triggers` (optional, object) defined.

Ideally, setup steps are written in such a way that they are _idempotent_, and as such can be run multiple times without causing changes (or at least without causing damage) to the development environment. This allows for more flexibility when deciding on when to trigger setup steps.

Note that by default, steps are run with the same UID/GID as the user invoking ENVy. This helps avoid mangling file permissions in your repository. If this causes setup steps to fail, add the `as_user: false` flag to your setup step.

Triggers
---
<div style="margin-top: 1em" class="indentThis" markdown="1">
Triggers require some more in-depth explanation. Sometimes, it makes sense to run setup steps again after the initial `envy up`. This can be simple, such as an updated `package.json` file causing `npm install` to be re-run, or more complex, like triggering every time a specific other step has been run. **Many projects do not require triggers at all**.

Note that triggers do not set up 'watches' on files or conditions specified - **steps will only ever be run when `envy up` is called**

There are three types of Triggers:

- `system-packages`: Run this step any time one of the listed system packages has updated or been reinstalled. Useful for reinstalling non-native packages that are likely to depend on native packages, like Python modules.
- `files`: Run this step any time one of the listed files within the project directory has changed. Useful for reinstalling non-native dependencies, with tools like `pip` or `npm`.
- `steps`: Run this step any time of of the named steps are run. This can be useful for finalizing installation of another step, or to create dependency chains of steps.
- `always`: Run this step every time `envy up` is run.
</div>
```
environment:
  setup-steps:
    - name 'Seed sqlite database'
      as_user: false
      run:
        - 'sqlite3 maindata.db < create.sql'
    - name: 'install npm packages'
      run:
        - 'npm install --dev'
      triggers:
        files:
          - package.json
```
<br />
<br />
</div>
</div>
# Actions
<div class="indentThis" markdown="1">
Actions are common tasks for contributors to run. This often includes tasks such as linting, running tests, building a release artifact, or simply running the application itself. This section allows maintainers to pre-define the commands contributors are likely to need.

Actions are invokable by using their name directly after `envy` on the command line - so an action called "lint" is invoked by running `envy lint`.

Users can see the available actions in a project by running `envy --help` anywhere in the project directory.

All actions, by default, are run from the current working directory. If this is not desirable, then set the `disable_relpath` option for the action.

Actions contain three required fields:
  - `name` (mandatory, string) that contributors can call this action using
  - `script` (mandatory, string) the command passed to Bash (can contain Bash special characters like pipes or ampersands) that executes this action.
  - `help` (mandatory, string) the help text presented to users when they run `envy --help`

as well as one optional field:

  - `disable_relpath` (mandatory, boolean) to disable the default behaviour of scripts being run from the current working directory. If this is set to true, then all scripts will start from the project root.

```
actions:
  - name: lint
    script: 'rubocop'
    help: 'lint the project'
  - name: run
    script: 'ruby hello.rb'
    help: 'run the project'
```
<br />
<br />
</div>

# Services

<div class="indentThis" markdown="1">
Many projects need accompanying services to run at the same time in order to have a complete development environment. ENVy supports this through the concept of _services_ (sometimes called "sidecar services"). These are started at the same time `envy up` is run, and stopped when `envy down` or `envy nuke` is run.

At the moment, only `docker-compose` based service files are supported. Please open [an issue](https://github.com/envy-project/envy/issues) if you'd like to see more.

You can specify the docker-compose file you'd like ENVy to manage state for using the `compose-file` key:

```
services:
  compose-file: docker-compose.yml
```
</div>
<br />
<br />
</div>

# Network

<div class="indentThis" markdown="1">
Envy is intended to operate transparently inside its own environment. However, sometimes it is necessary to be able to interact with services from the envy environment, or access the environment from your host machine. ENVy supports this through _network_ configuration.

By default, ENVy's underlying docker container uses an arbitrarily assigned network, making it difficult to connect to or from the environment. By specificying a network type of `host` ENVy will use docker's host networking to make the experience closer to running directly:
```
network: "host"
```
Unfortunately, this feature is not fully implemented within Docker for Mac, so if the project is expected to be developed on Mac then host networking must be used with caution.

ENVy can also support picking the docker network to communicate on, as well as expose ports to the host by providing named keys to the `network` key instead of a string:
```
network:
  name: docker-network-name
  ports:
    - 443
    - 80:8080
```
</div>
