Ansible Unicorn/rbenv setup
=========================

This Ansible role installs a service for Unicorn, with rbenv and multiple application support

Requirements
------------

Expects rbenv to be installed system-wide


Role Variables
--------------

-   `rbenv_root` defaults to `/usr/local/rbenv`
-   `rbenv_exe`  defaults to `/usr/local/rbenv/bin/rbenv`
-   `rails_apps` defaults to `[]`

    Each rails_apps entry is a dict with the following options

    -   `name` (eg `my_app`, required)
    -   `ruby_version` (eg `ruby-2.1.1`, required)
    -   `env` defaults to `production`
    -   `root` defaults to `/var/www/{{ name }}/current` (Capistrano
        compatible)
    -   `config` is used for `db` and `secrets`, defaults to
        `{{ root }}/config` if root is specified, otherwise to
        `/var/www/{{ name }}/shared/config`
    -   `db` is a dict for generating a `database.yml` file, with
        options:

        -   `adapter` defaults to `postgresql`
        -   `host` defaults to `localhost`
        -   `database` defaults to app name
        -   `username` defaults to app name
        -   `password` defaults to app name
        -   `pool` defaults to `5`
        -   `timeout` defaults to `5000`
    -   `secrets` is a dict and all its keys are converted to
        secrets.yml

Example Playbook
----------------

The role could be included in a playbook as follows:

```yaml
---
-hosts: application
  roles:
    - role: unicorn-rbenv
      rails_apps:
        - { name: 'my_app1', ruby_version: 'ruby-1.9.3' }
        - { name: 'my_app2', ruby_version: 'ruby-2.1.1', root: '/var/test_apps/app2', env: staging }
        - name: 'my_app3'
          ruby_version: 'ruby-2.1.1'
          db:
            password: topsecret
          secrets:
            secret_key_base: SuperSecretHexString
```

If the init script is called without any config parameters,
it will attempt to run the init command for all unicorn configurations listed in /etc/unicorn/_*_.conf

`/etc/init.d/unicorn start` # starts all unicorns

If you specify a particular config, it will only operate on that one

`/etc/init.d/unicorn start my_app`

Notes
-----

This role does not deploy the actual application, it assumes that this
will be done in another role or using a deployment tool such as
[Capistrano](https://github.com/capistrano/capistrano).

If you use this role to generate database.yml and secrets.yml, it is advised
to store the keys in an ansible vault file

License
-------

MIT

