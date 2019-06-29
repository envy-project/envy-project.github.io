---
layout: content
title: ENVYfile Reference
description: Detailed guide to ENVyfile syntax and contents.
---

```
environment:
  base:
    image: ruby:stretch
  system-packages:
    - recipe: python3.7
      version: 3.7.1-1~18.04
    - recipe: docker
  setup-steps:
    - name: bundle
      type: script
      run:
        - "bundle install"
      triggers:
        files:
          - Gemfile
          - Gemfile.lock
actions:
  - name: lint
    script: 'rubocop'
    help: 'lint the project'
  - name: run
    script: 'ruby hello.rb'
    help: 'run the project'
  - name: bundle
    script: 'bundle'
    help: 'run bundler'
compose-file: docker-compose.yml
```