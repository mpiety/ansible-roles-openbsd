# motd

## Description

The contents of '/etc/motd' are displayed by login after a successful login but
just before it executes the login shell.

For more information on the usage and available configuration options,
consult the following sections.

## Usage

### Install

```
- hosts: all
  roles:
    - role: motd
  vars:
    motd_state: 'install'
    motd_env_name: 'Testing'
    motd_host_description: 'Test Host'
```

### Inactive

```
- hosts: all
  roles:
    - role: motd
  vars:
    motd_state: 'inactive'
```

## Parameters

### Role

`motd_state`

    Description: Control the state of the role.
                 'motd' is a core file and can therefore not be removed.
    Required   : False
    Value      : Predetermined
    Type       : String
    Default    : 'install'
    Options    :
      Install : 'true' | 'yes' | 'install'
      Inactive: 'quiesce' | 'inactive'

`motd_env_name`

    Description: Define the 'motd_env_name' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : 'Production'
    Options    :
      Examples: 'Production' | 'Integration' | 'Testing' | 'Development'
      None    : ''

`motd_host_description`

    Description: Define the 'motd_host_description' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : "{{group_names|last}}"
    Options    :
      Examples: 'Backup Server' | 'Database Server' | 'Mail Server' | 'Web Server'
      None    : ''

`motd_host_ip_address`

    Description: Define the 'motd_host_ip_address' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : "{{ansible_default_ipv4.address}}"
    Options    :
      Examples: '192.168.0.10' | '172.16.0.10' | '10.0.0.10'
      None    : ''

`motd_motd_template`

    Description: Define the template source for '/etc/motd'.
                 Any value other than 'default' or 'empty' requires a Jinja2
                 file in the templates folder.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : 'default'
    Options    :
      Examples: 'empty' | 'environment' | 'hostname'

## Conflicts

## Dependencies

## Requirements

### Control Node

`ansible`

    Version: >= 2.15.0

### Managed Node

`python`

    Version: >= 3.10.0

## Support

### Operating Systems

`openbsd`

    Version: 7.6
    Version: 7.7
