ansible-supervisor
==================

[![Build Status](https://travis-ci.org/vvkuznetsov/ansible-supervisor.svg?branch=master)](https://travis-ci.org/vvkuznetsov/ansible-supervisor) [![Ansible Galaxy](http://img.shields.io/badge/galaxy-vvkuznetsov.supervisor-660198.svg?style=flat)](https://galaxy.ansible.com/list#/roles/4260)

A role for creating supervisor tasks.


- Ensures that supervisor is installed (using `apt`);
- Ensures that **supervisord** is running and enabled for autostart;
- Creates a task in the supervisor conf directory;
- Run created task.


Installation
------------

This role requires at least Ansible `v1.7.0`. To install it, run:

    ansible-galaxy install vvkuznetsov.supervisor


Usage
-----

### Simple use

```
roles:
  - role: vvkuznetsov.supervisor
    name: webserver
    command: python -m SimpleHTTPServer
    directory: /opt/web
    user: ubuntu
    stopsignal: HUP
```

### Non standart way

If you want to use this role as a dependence to your own role and you have strict sequence of steps and you have to use supervisor in the middle/end of them. Then I can only suggest you to separate your role in mini-roles and connect them as dependences like that:

```
roles:
  - base
  - web
  ...
```

`Web` role that makes:
  - download code from git;
  - configured virtualenv;
  - copied configuration files for nginx and gunicorn;
  - configured supervisor.

Even if `web` role makes only configuration of preinstalled programs it is too hard to use outer role in this scenario. You can easily use it in playbook right after role `web`. But it's ugly to use `web` role specific vars outside of role that setted them.
Another way to split web role in meaningful steps like this:

```
roles:
  - base
  - code
  - nginx
  - web
    - defaults
      - main.yml
    - meta
      - main.yml
    - vars
      - main.yml
  ...
```

`Web` role will be like group of roles, that only set variables and connect roles as dependencies:

```
# web/meta/main.yml

---

dependencies:
  - role: code
  - role: supervisor
    name: "{{ application_name }}"
    command: "{{ virtualenv_path }}/bin/gunicorn_start"
    user: "{{ gunicorn_user }}"
    stdout_logfile: "{{ application_log_file }}"
    tags: supervisor
  - role: nginx
```


It still no good it will be better if we cat call role inside another role. But for me it's better than first way.


Role Variables
--------------

**Escape warning**: most options need "%" escaped as "%%", as supervisord uses %(var_name)s for variable substitution.

### 1. Required


    name: No default

The name is used within client applications that control the processes that are created as a result of this configuration. It is an error to create a `program` section that does not have a name. The name must not include a colon character or a bracket character. The value of the name is used as the value for the `%(program_name)s` string expression expansion within other values where specified.


    command: No default

The command to run.


### 2. Optional


    restart_task: false

If `true`, supervisor will restart your app even if configuration file did not changed.


    supervisor_inet_http_server: 9001

Set `false` if don't need a web gui. A TCP host:port value or (e.g. `127.0.0.1:9001`) on which supervisor will listen for HTTP/XML-RPC requests. **supervisorctl** will use XML-RPC to communicate with **supervisord** over this port. To listen on all interfaces in the machine, use `:9001` or `*:9001`.


    directory: No default

A file path representing a directory to which **supervisord** should temporarily chdir before exec’ing the child.


    supervisor_dir: "/etc/supervisor"

The folder for **supervisord** config will be placed.


    supervisor_config_dir: "{{ supervisor_dir }}/conf.d"

The folder where all apps configs will be placed.


    supervisorctl_command: "supervisorctl"

The command that is using to manage supervised services.


    redirect_stderr: "true"

If `true`, cause the process’ stderr output to be sent back to **supervisord** on its stdout file descriptor (in UNIX shell terms, this is the equivalent of executing `/the/program 2>&1`).


    autorestart: "true"

May be one of `false`, `unexpected`, or `true`. If `false`, the process will never be autorestarted. If `unexpected`, the process will be restart when the program exits with an exit code that is not one of the exit codes associated with this process’ configuration (see `exitcodes`). If `true`, the process will be unconditionally restarted when it exits, without regard to its exit code.


    autostart: "true"

If `true`, this program will start automatically when **supervisord** is started.


    user: "root"

If **supervisord** is run as the root user, switch users to this UNIX user account before doing any meaningful processing. This value has no effect if **supervisord** is not run as root.


    env_vars: {}

Environment dict that will be placed in the child process’ environment.


    numprocs: 1

Supervisor will start as many instances of this program as named by numprocs. Note that if `numprocs > 1`, the `process_name` expression must include `%(process_num)s` (or any other valid Python string expression that includes `process_num`) within it.


    process_name: "%(process_num)02d"

A Python string expression that is used to compose the supervisor process name for this process. You usually don’t need to worry about setting this unless you change `numprocs`. The string expression is evaluated against a dictionary that includes `group_name`, `host_node_name`, `process_num`, `program_name`, and here (the directory of the **supervisord** config file).


    socket: No default, optional

Socket to listen on, on behalf of this program.
Setting this will generate an fcgi-program:{{ name }} instead of program:{{ name }} section.


Additional notes
----------------

This role will not restart your app every time it's called if you don't set variable `restart_task: true`


Authors and license
-------------------

`supervisor` role was written by:

- Valentin Kuznetsov | [e-mail](mailto:aimestereo@gmail.com)
- sujaymansingh | [github](https://github.com/sujaymansingh)

License: MIT
