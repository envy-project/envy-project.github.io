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
*Note: A more complete guide is available in the [user guide](user-guide.html), this serves as a quick introduction.*

ENVy is a very simple tool to use. You can see all available commands by running `envy help`. When you're working with a project, this help text will also contain all of the project-specific commands you can use (build, lint, etc.)

ENVy itself also defines a few commands:

- `envy up`: Create a development environment

When run inside of a project directory, this will create a development environment according to the maintainer's specifications. 


- Running ENVy up the first time
- Running ENVy nuke
- Running ENVy help

