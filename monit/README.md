# monit

## Description

Monit is a utility for managing and monitoring processes, files, directories,
devices and network services on a Unix system. Monit conducts automatic
maintenance and repair and can execute meaningful causal actions in error
situations.

monit supports:
- Daemon mode - poll services at a specified interval
- Group and manage groups of services, service dependencies
- Logging - syslog or own logfile
- Alert, start, stop and restart of services based on it's characteristics
- MD5 and SHA1 checksums
- Runtime Unix socket and TCP/IP port checking (tcp and udp)
- Process status, timeout, memory and cpu usage, etc.
- Device usage monitoring (inodes and space)
- File monitoring (timestamp, checksum, permission, owner, etc.)
- Directory monitoring (timestamp, permission, owner, etc.)
- Remote network services monitoring (ping, response time, protocol, etc.)
- System load average monitoring
- Flexible and customizable email alert messages and notifications
- Protocol verification such as HTTP, FTP, SMTP, POP, IMAP, NNTP, NTP, etc.
- A HTTP interface with XML output option

For more information on the usage and available configuration options,
consult the following sections.

## Usage

### Install

```
- hosts: all
  roles:
    - role: monit
  vars:
    monit_state: 'install'
```

### Enable

```
- hosts: all
  roles:
    - role: monit
  vars:
    monit_state: 'enable'
    monit_mail_server:
      - {address: 'mail01.domain.tld', port: '465'}
      - {address: 'mail02.domain.tld', port: '587'}
    monit_mail_to:
      - {address: 'admin@domain.tld'}
      - {address: 'secops@domain.tld', filter: 'only', events: ['checksum', 'permission', 'uid', 'gid']}
      - {address: 'itops@domain.tld', filter: 'not on', events: ['instance', 'action']}
```

### Disable

```
- hosts: all
  roles:
     - role: monit
  vars:
    monit_state: 'disable'
    monit_mail_server:
      - {address: 'mail01.domain.tld', port: '465'}
      - {address: 'mail02.domain.tld', port: '587'}
    monit_mail_to:
      - {address: 'admin@domain.tld'}
      - {address: 'secops@domain.tld', filter: 'only', events: ['checksum', 'permission', 'uid', 'gid']}
      - {address: 'itops@domain.tld', filter: 'not on', events: ['instance', 'action']}
```

### Remove

```
- hosts: all
  roles:
     - role: monit
  vars:
    monit_state: 'remove'
```

### Inactive

```
- hosts: all
  roles:
     - role: monit
  vars:
    monit_state: 'inactive'
```

### Config

```
vars:
  monit_config_all:
    - name: 'system'
      state: True
      comment: 'system'
      config: |
        check system {{ansible_hostname}}
          if cpu usage (user) > 80% for 5 cycles then alert
          if cpu usage (system) > 20% for 5 cycles then alert
          if cpu usage (wait) > 20% for 5 cycles then alert
          if loadavg (1min) > 6 for 5 cycles then alert
          if loadavg (5min) > 3 for 5 cycles then alert
          if loadavg (15min) > 2 for 5 cycles then alert
          if memory usage > 90% for 5 cycles then alert
          if swap usage > 30% for 5 cycles then alert
          if swap usage > 30% for 5 cycles then alert

    - name: 'storage'
      state: True
      comment: 'boot'
      config: |
        check filesystem "boot" with path /boot
          if space usage > 80% for 5 cycles then alert

        check filesystem "root" with path /
          if space usage > 80% for 5 cycles then alert

        check filesystem "data" with path /data
          if space usage > 80% for 5 cycles then alert
          if does not exist then unmonitor

        check filesystem "backup" with path /backup
          if space usage > 80% for 5 cycles then alert
          if does not exist then unmonitor

    - name: 'ntpd'
      state: True
      comment: 'ntpd'
      config: |
        check process ntpd matching "/usr/sbin/ntpd.*"
          start program = "/sbin/service ntpd start"
          stop program = "/sbin/service ntpd stop"
          if failed host 127.0.0.1 port 123 type udp then alert

    - name: 'openssh'
      state: True
      comment: 'openssh'
      config: |
        check process openssh with pidfile "/run/sshd.pid"
          start program = "/sbin/service sshd start"
          stop program = "/sbin/service sshd stop"
          if failed host 127.0.0.1 port 22 type tcp protocol ssh with timeout 10 seconds then restart
          if 5 restarts within 5 cycles then timeout

  monit_config_group:
    - name: 'nsd'
      state: True
      comment: 'nsd'
      config: |
        check process nsd with pidfile "/run/nsd/nsd.pid"
          start program = "/sbin/service nsd start"
          stop program = "/sbin/service nsd stop"
          if failed host 127.0.0.1 port 53 type tcp protocol dns with timeout 10 seconds then restart
          if failed host 127.0.0.1 port 53 type udp protocol dns with timeout 10 seconds then restart
          if 5 restarts within 5 cycles then timeout
```

## Parameters

### Config

`state`

    Description: Control the 'state' of the monit file.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : True
    Options    : True | False

`name`

    Description: Define the 'name' of the monit file.
                 For more details see 'man monit' or 'monit --help'.
    Required   : True
    Value      : Arbitrary
    Type       : String
    Default    : ''
    Options    :
      Examples: 'openssh' | 'openntpd' | 'opensmtpd' | 'apache' | 'mariadb'

`comment`

    Description: Define a 'comment' for the monit file.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : ''
    Options    :
      Examples: 'OpenSSH' | 'OpenNTPD' | 'OpenSMTP' | 'Apache HTTP' | 'MariaDB'
      None    : ''

`config`

    Description: Define the 'config' entry/entries.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : ''
    Options    :
      Examples: config: |
                  check process apache with pidfile "/run/apache2.pid"
                    group web
                    start program = "/sbin/service apache2 start"
                    stop program = "/sbin/service apache2 stop"
                    if cpu > 60% for 2 cycles then alert
                    if cpu > 80% for 5 cycles then restart
                    if totalmem > 512.0 MB for 2 cycles then alert
                    if totalmem > 512.0 MB for 5 cycles then restart
                    if failed host 127.0.0.1 port 80 type tcp protocol http with timeout 10 seconds then restart
                    if 5 restarts within 5 cycles then timeout
      None    : ''

### Role

`monit_state`

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

`monit_config_all`

    Description: Define the 'monit_config_all' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : []
    Options    :
      Examples: name: 'openssh'
                state: True
                comment: 'OpenSSH'
                config: |
                  check process openssh with pidfile "/run/sshd.pid"
                    group system
                    start program = "/sbin/service sshd start"
                    stop program = "/sbin/service sshd stop"
                    if cpu > 60% for 2 cycles then alert
                    if cpu > 80% for 5 cycles then restart
                    if totalmem > 256.0 MB for 2 cycles then alert
                    if totalmem > 256.0 MB for 5 cycles then restart
                    if failed host 127.0.0.1 port 22 type tcp protocol ssh with timeout 10 seconds then restart
                    if 5 restarts within 5 cycles then timeout
      None    : []

`monit_config_group`

    Description: Define the 'monit_config_group' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : []
    Options    :
      Examples: name: 'apache'
                state: True
                comment: 'Apache HTTP'
                config: |
                  check process apache with pidfile "/run/apache2.pid"
                    group web
                    start program = "/sbin/service apache2 start"
                    stop program = "/sbin/service apache2 stop"
                    if cpu > 60% for 2 cycles then alert
                    if cpu > 80% for 5 cycles then restart
                    if totalmem > 512.0 MB for 2 cycles then alert
                    if totalmem > 512.0 MB for 5 cycles then restart
                    if failed host 127.0.0.1 port 80 type tcp protocol http with timeout 10 seconds then restart
                    if 5 restarts within 5 cycles then timeout
      None    : []

`monit_config_host`

    Description: Define the 'monit_config_host' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : []
    Options    :
      Examples: name: 'apache'
                state: True
                comment: 'Apache HTTP'
                config: |
                  check process apache with pidfile "/run/apache2.pid"
                    group web
                    start program = "/sbin/service apache2 start"
                    stop program = "/sbin/service apache2 stop"
                    if cpu > 60% for 2 cycles then alert
                    if cpu > 80% for 5 cycles then restart
                    if totalmem > 512.0 MB for 2 cycles then alert
                    if totalmem > 512.0 MB for 5 cycles then restart
                    if failed host 127.0.0.1 port 80 type tcp protocol http with timeout 10 seconds then restart
                    if 5 restarts within 5 cycles then timeout
      None    : []

`monit_daemon_delay`

    Description: Set the 'monit_daemon_delay' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : 3
    Options    :
      Examples: 3 | 5 | 10 | 15 | 20

`monit_daemon_interval`

    Description: Set the 'monit_daemon_interval' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : 60
    Options    :
      Examples: 30 | 60 | 120 | 180 | 240 | 300
      None    : ''

`monit_include`

    Description: Define the 'monit_include' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '/etc/monit.d/*.cfg'
    Options    :
      Examples: '/etc/monit.d/*.cfg' | '/usr/local/etc/monit.d/*.cfg'

`monit_mail_from`

    Description: Define the 'monit_mail_from' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : "monit@{{ansible_domain}}"
    Options    :
      Examples: 'root@domain.tld' | 'admin@domain.tld' | 'user@domain.tld'

`monit_mail_message_action`

    Description: Define the 'monit_mail_message_action' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '$ACTION'
    Options    :
      Examples: '$ACTION'

`monit_mail_message_date`

    Description: Define the 'monit_mail_message_date' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '$DATE'
    Options    :
      Examples: '$DATE'

`monit_mail_message_description`

    Description: Define the 'monit_mail_message_description' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '$## Description'
    Options    :
      Examples: '$## Description'

`monit_mail_message_event`

    Description: Define the 'monit_mail_message_event' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '$EVENT'
    Options    :
      Examples: '$EVENT'

`monit_mail_message_host`

    Description: Define the 'monit_mail_message_host' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '$HOST'
    Options    :
      Examples: '$HOST'

`monit_mail_message_service`

    Description: Define the 'monit_mail_message_service' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : '$SERVICE'
    Options    :
      Examples: '$SERVICE'

`monit_mail_server`

    Description: Define the 'monit_mail_server' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : []
    Options    :
      Examples: [{address: '127.0.0.1', port: '25'}] |
                [{address: 'mail01.domain.tld', port: '587', username: 'user01.domain.tld', password: '5uaT,4hr32_WX.bnu78w', tls: 'TLSV12', timeout: 30}]
      None    : []

`monit_mail_subject`

    Description: Define the 'monit_mail_subject' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : "Monit: $EVENT '$SERVICE' ($HOST)"
    Options    :
      Examples: "Monit: $EVENT '$SERVICE' ($HOST)"

`monit_mail_to`

    Description: Define the 'monit_mail_to' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : [{address: "admin@{{ansible_domain}}", filter: 'not on', events: ['instance', 'action']}]
    Options    :
      Examples: [{address: 'admin@domain.tld'},
                 {address: 'secops@domain.tld', filter: 'only', events: ['checksum', 'permission', 'uid', 'gid']},
                 {address: 'itops@domain.tld', filter: 'not on', events: ['instance', 'action']}]
      None    : []

`monit_pf_filters`

    Description: Define the 'monit_pf_filters' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : |
      pass in inet proto tcp from { 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 } to port 2812 # monit from internal private addresses
      pass in inet6 proto tcp from fc00::/7 to port 2812 # monit from unique local addresses
    Options    :
      Examples: |
        pass in inet proto tcp from 10.0.0.0/8 to port 2812 # monit from internal-networks

`monit_pf_state`

    Description: Control the 'monit_pf_state' option.
    Required   : False
    Value      : Predetermined
    Type       : Boolean
    Default    : False
    Options    : True | False

`monit_web_server_address`

    Description: Define the 'monit_web_server_address' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : 'localhost'
    Options    :
      Examples: 'localhost'

`monit_web_server_allow`

    Description: Define the 'monit_web_server_allow' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : [{user: 'localhost'}]
    Options    :
      Examples: [{user: 'localhost'}, {user: 'monit_exporter', password: 's5g6qHb_KEWeLX.xY4vt'}
      None    : []

`monit_web_server_port`

    Description: Set the 'monit_web_server_port' option.
    Required   : False
    Value      : Arbitrary
    Type       : Integer, String
    Default    : 2812
    Options    :
      Examples: 2080 | 2812 | 8080

## Conflicts

## Dependencies

### Packages

`monit`

    Version: >= 5.9
    Name   :
      OpenBSD 7.6: 'monit'
      OpenBSD 7.7: 'monit'

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
