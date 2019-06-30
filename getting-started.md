---
layout: content
title: Getting Started
description: How to install and use ENVy
---

Installing ENVY
---

**Prerequisites**: You should have Docker installed and running (i.e. `docker ps` doesn't give you an error), as well as `docker-compose`, needed for some projects.

You can install ENVy in a few different ways:

*pip (recommended)*:
   - The recommended installation method is to use Pip: `pip3 install envy-project`.

*from release (or master)*:
   - Download a tar.gz release from [ENVy's Github release page](https://github.com/envy-project/envy/releases).
   - Extract the archive (`tar -xzvf envy-*.tgz`)
   - Install the package (`sudo setup.py install`)

*AUR (Arch Linux)*:
   - Arch Linux suggests installing Python packages using Pacman instead of pip. There is an [AUR package available](nowhere yet)

Using ENVy
---

ENVy is a very simple tool to use. You can see all available commands by running `envy --help`. When you're working with a project, this help text will also contain all of the project-specific commands you can use (build, lint, etc.)

ENVy itself also defines a few commands:

- `envy up`: Create a development environment
{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
When run inside of a project directory, this will create a development environment according to the maintainer's specifications. You will see progress output as the process continues. It will mount the project directory inside of the environment. You can leave this container running as long as you'd like - it consumes little to no resources.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>~ $ cd golang-hello-world
golang-hello-world $ envy up
Detected change in config environment.
Creating ENVy environment image.
Creating ENVy container
ENVy environment is ready!
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}

- `envy down`: Pause a development environment

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>~ $ golang-hello-world $ envy down
ENVy environment stopped.
</code>
</pre></div></div>
</section>
<section class="col-6 col-12-narrower">
<p>
When run inside of a project directory, this will pause the development environment without deleting any data. You may want to run this when you're finished working on a project for a little while.
</p>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}


- `envy nuke`: Delete a development environment

{::options parse_block_html="true" /}
<div class="wrapper-smallpad">
<section class="container">
<div class="row">
<section class="col-6 col-12-narrower">
<p>
When run inside of a project directory, this will remove the environment created by ENVy. This will not affect the project directory itself - you will not lose any work. This frees up storage space on your host computer. Note that depending on the project configuration, this could delete temporary data - such as a database in use for development.
</p>
</section>
<section class="col-6 col-12-narrower">
<div class="highlighter-rouge big-wrapper"><div class="highlight big-child"><pre class="highlight">
<code>~ $ golang-hello-world $ envy nuke
ENVy environment destroyed.
</code>
</pre></div></div>
</section>
</div>
</section>
</div>
{::options parse_block_html="false" /}


