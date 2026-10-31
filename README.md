# supervisor

Role supervisor installs and configures Supervisor process control system and manages its programs

## Table of contents

- [Requirements](#requirements)
- [Default Variables](#default-variables)
  - [supervisor_actions](#supervisor_actions)
  - [supervisor_config](#supervisor_config)
  - [supervisor_config_dir](#supervisor_config_dir)
  - [supervisor_programs](#supervisor_programs)
  - [supervisor_state_action](#supervisor_state_action)
  - [supervisor_version](#supervisor_version)
- [Dependencies](#dependencies)
- [License](#license)
- [Author](#author)

---

## Requirements

- Minimum Ansible version: `2.20`

## Default Variables

### supervisor_actions

List of actions the role does, accepts one or more actions.
Use comma without spaces as a delimiter for multiple actions.

**_Required:_** `true`<br />
**_Type:_** String<br />

#### Example usage

```YAML
  supervisor_actions: install
  supervisor_actions: install,deploy_config,deploy_programs,state_control
```

### supervisor_config

Content of `supervisord.conf`, rendered into `{{ supervisor_config_dir }}/supervisord.conf`.
Keys are section names, values are dicts of the section's settings.
The `include` section must point to `conf.d/*.conf`, otherwise programs deployed
by `deploy_programs` are not loaded.

**_Required:_** `true`, only in case `supervisor_actions: deploy_config`<br />
**_Type:_** Dict<br />

#### Example usage

```YAML
supervisor_config:
  unix_http_server:
    file: /var/run/supervisor.sock
    chmod: '0700'
  supervisord:
    logfile: /var/log/supervisor/supervisord.log
    pidfile: /var/run/supervisord.pid
    childlogdir: /var/log/supervisor
  rpcinterface:supervisor:
    supervisor.rpcinterface_factory: supervisor.rpcinterface:make_main_rpcinterface
  supervisorctl:
    serverurl: unix:///var/run/supervisor.sock
  include:
    files: /etc/supervisor/conf.d/*.conf
```

### supervisor_config_dir

Supervisor configuration directory

**_Type:_** String<br />

#### Default value

```YAML
supervisor_config_dir: /etc/supervisor
```

### supervisor_programs

Programs managed by Supervisor, all rendered into `{{ supervisor_config_dir }}/conf.d/ansible_managed.conf`.
Keys are program names (rendered as `[program:<name>]`), values are dicts of the program's settings.

**_Required:_** `true`, only in case `supervisor_actions: deploy_programs`<br />
**_Type:_** Dict<br />

#### Example usage

```YAML
supervisor_programs:
  queue_worker:
    command: /usr/bin/php /var/www/app/artisan queue:work
    user: www-data
    numprocs: 4
    process_name: '%(program_name)s_%(process_num)02d'
    autostart: true
    autorestart: true
    stdout_logfile: /var/log/supervisor/queue_worker.log
```

### supervisor_state_action

Controls state of Supervisor daemon

**_Required:_** `true`, only in case `supervisor_actions: state_control`<br />
**_Type:_** String<br />

#### Example usage

```YAML
  supervisor_state_action: started
  supervisor_state_action: stopped
  supervisor_state_action: restarted
  supervisor_state_action: reloaded
```

### supervisor_version

Supervisor version to install. Defaults to `latest` which installs the newest available package.
Set to a specific version string to pin the installation.

**_Type:_** String<br />

#### Default value

```YAML
supervisor_version: latest
```

#### Example usage

```YAML
  supervisor_version: latest
  supervisor_version: 4.2.5-1
```

## Dependencies

None.

## License

MIT

## Author

freedform
