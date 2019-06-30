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
</style>

Anatomy of an Envyfile
---
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
      <span style="color: teal" class="tooltip">run:<span class="tooltiptext">Commands to run to complete this step</span></span>
        - <span style="color: teal" class="tooltip">"bundle install"<span class="tooltiptext">A command passed to the Bash shell - can contain Bash special characters.</span></span>
      <span style="color: purple" class="tooltip">triggers:<span class="tooltiptext">List of things that can cause this step to be run (in addition to the first envy up)</span></span>
        <span style="color: purple" class="tooltip">files:<span class="tooltiptext">Files that are watched for changes and should cause this step to run</span></span>
          - <span style="color: purple" class="tooltip">Gemfile<span class="tooltiptext">A Ruby gemfile, specifying Ruby packages to be installed</span></span>
          - Gemfile.lock
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
<span style="color: tomato" class="tooltip">compose-file: docker-compose.yml<span class="tooltiptext">An optional docker-compose file, started and stopped with the ENVy environment</span></span>
</code>
</pre></div></div>
