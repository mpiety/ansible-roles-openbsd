# openntpd

## Description

The ntpd daemon implements the Simple Network Time Protocol version 4 as
described in RFC 2030 and the Network Time Protocol version 3 as described in
RFC 1305. It can synchronize the local clock to one or more remote NTP servers
and act as NTP server itself, redistributing the local time.

For more information on the usage and available configuration options,
consult the following sections.

## Usage

### Install

```
- hosts: all
  roles:
    - role: openntpd
  vars:
    openntpd_state: 'install'
```

### Enable

```
- hosts: all
  roles:
    - role: openntpd
  vars:
    openntpd_state: 'enable'
    openntpd_server:
      - {address: "{{ansible_default_ipv4.gateway}}", comment: 'Default IPv4 gateway'}
```

### Disable

```
- hosts: all
  roles:
    - role: openntpd
  vars:
    openntpd_state: 'disable'
    openntpd_server:
      - {address: "{{ansible_default_ipv4.gateway}}", comment: 'Default IPv4 gateway'}
```

### Remove

```
- hosts: all
  roles:
    - role: openntpd
  vars:
    openntpd_state: 'remove'
```

### Inactive

```
- hosts: all
  roles:
    - role: openntpd
  vars:
    openntpd_state: 'inactive'
```

## Parameters

### Role

`openntpd_state`

    Description: Control the state of the role.
    Required   : False
    Value      : Predetermined
    Type       : String
    Default    : 'enable'
    Options    :
      Install : 'true' | 'yes' | 'install'
      Enable  : 'start' | 'on' | 'enable'
      Disable : 'stop' | 'off' | 'disable'
      Remove  : 'false' | 'no' | 'remove'
      Inactive: 'quiesce' | 'inactive'

`openntpd_check_time_day`

    Description: Define the 'openntpd_check_time_day' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : '*'
    Options    :
      Examples: '1' | '*/2'

`openntpd_check_time_hour`

    Description: Define the 'openntpd_check_time_hour' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : '*'
    Options    :
      Examples: '5' | '*/6'

`openntpd_check_time_minute`

    Description: Define the 'openntpd_check_time_minute' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : '*/10'
    Options    :
      Examples: '42' | '*/30'

`openntpd_check_time_month`

    Description: Define the 'openntpd_check_time_month' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : '*'
    Options    :
      Examples: '10' | '*/1'

`openntpd_check_time_weekday`

    Description: Define the 'openntpd_check_time_weekday' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : '*'
    Options    :
      Examples: '6' | 'Saturday'

`openntpd_check_time_offset_limit`

    Description: Set the 'openntpd_check_time_offset_limit' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer
    Default    : 1000
    Options    :
      Examples: 200 | 500 | 1000 | 1500

`openntpd_check_time_state`

    Description: Control the 'openntpd_check_time_state' option.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : True
    Options    : True | False

`openntpd_listen`

    Description: Define the 'openntpd_listen' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : []
    Options    :
      Examples: [{address: '*', comment: 'All interfaces'}] |
                [{address: '127.0.0.1', comment: IPv4 localhost'},
                 {address: '::1', comment: 'IPv6 localhost'}] |
                [{address: '10.0.0.10', comment: 'IPv4 interface'}]
      None    : []

`openntpd_monitor_mail_from_address`

    Description: Define the 'openntpd_monitor_mail_from_address' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : "root@{{ansible_domain}}"
    Options    :
      Examples: 'root@domain.tld' | 'admin@domain.tld' | 'user@domain.tld'

`openntpd_monitor_mail_to_address`

    Description: Define the 'openntpd_monitor_mail_to_address' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array
    Default    : ["admin@{{ansible_domain}}"]
    Options    :
      Examples: ['root@domain.tld'] | ['root@domain.tld', 'admin@domain.tld']
                ['root@domain.tld', 'admin@domain.tld', 'user@domain.tld']

`openntpd_monitor_mail_state`

    Description: Control the 'openntpd_monitor_mail_state' option.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : False
    Options    : True | False

`openntpd_monitor_monit_state`

    Description: Control the 'openntpd_monitor_monit_state' option.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : False
    Options    : True | False

`openntpd_monitor_prom_state`

    Description: Control the 'openntpd_monitor_prom_state' option.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : False
    Options    : True | False

`openntpd_monitor_prom_textfile_collector`

    Description: Define the 'openntpd_monitor_prom_textfile_collector' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '/var/node_exporter/textfile_collector'
    Options    :
      Examples: '/var/node_exporter/textfile_collector'

`openntpd_pf_filters`

    Description: Define the 'openntpd_pf_filters' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : |
      pass in inet proto udp from { 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 } to port 123 # ntp from internal private addresses
      pass in inet6 proto udp from fc00::/7 to port 123 # ntp from unique local addresses
      pass out inet proto udp to any port 123 # ntp to any
      pass out inet6 proto udp to any port 123 # ntp to any
    Options    :
      Examples: |
        pass out inet proto udp to 10.0.0.0/8 port 123 # ntp to internal-networks

`openntpd_pf_state`

    Description: Control the 'openntpd_pf_state' option.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : False
    Options    : True | False

`openntpd_server`

    Description: Define the 'openntpd_server' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : [{address: "ntp.{{ansible_domain}}", weight: 1, comment: 'Default NTP server'}]
    Options    :
      Examples: [{address: '195.176.26.204', weight: 1, comment: 'Federal Institute of Metrology METAS'},
                 {address: '195.176.26.205', weight: 2, comment: 'Federal Institute of Metrology METAS'}] |
                [{address: '195.176.26.206', comment: 'Federal Institute of Metrology METAS'},
                 {address: '192.33.96.101', comment: 'Swiss Federal Institute of Technology Zurich'},
                 {address: '192.33.96.102', comment: 'Swiss Federal Institute of Technology Zurich'}]
      None    : []

`openntpd_servers`

    Description: Define the 'openntpd_servers' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : []
    Options    :
      Examples: [{address: '195.176.26.204', weight: 1, comment: 'Federal Institute of Metrology METAS'},
                 {address: '195.176.26.205', weight: 2, comment: 'Federal Institute of Metrology METAS'}] |
                [{address: '195.176.26.206', comment: 'Federal Institute of Metrology METAS'},
                 {address: '192.33.96.101', comment: 'Swiss Federal Institute of Technology Zurich'},
                 {address: '192.33.96.102', comment: 'Swiss Federal Institute of Technology Zurich'}]
      None    : []

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
