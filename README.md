ansible-supervisor
==================

[![Build Status](https://travis-ci.org/vvkuznetsov/ansible-supervisor.svg?branch=master)](https://travis-ci.org/vvkuznetsov/ansible-supervisor) [![Ansible Galaxy](http://img.shields.io/badge/galaxy-vvkuznetsov.supervisor-660198.svg?style=flat)](https://galaxy.ansible.com/list#/roles/4260)

A role for creating supervisor tasks.


### Actions

- Ensures that supervisor is installed (using `apt`);
- Ensures that supervisor is running and enabled for autostart;
- Creates a task in the supervisor conf directory;
- Run created task.


### Installation

This role requires at least Ansible `v1.7.0`. To install it, run:

    ansible-galaxy install vvkuznetsov.supervisor


### Usage:
```
  roles:
    - role: vvkuznetsov.supervisor
      name: webserver
      command: python -m SimpleHTTPServer
      directory: /opt/web
      user: ubuntu
      stopsignal: HUP
```

### Authors and license

`supervisor` role was written by:
- Valentin Kuznetsov | [e-mail](mailto:aimestereo@gmail.com)

License: MIT
