# resolver

## Description

The resolver configuration file contains information that is read by the
resolver routines the first time they are invoked by a process. The file is
designed to be human readable and contains a list of keywords with values that
provide various types of resolver information.
The configuration file is considered a trusted source of DNS information
(e.g., DNSSEC AD-bit information will be returned unmodified from this source).

For more information on the usage and available configuration options,
consult the following sections.

## Usage

### Install

```
- hosts: all
  roles:
    - role: resolver
  vars:
    resolver_state: 'install'
    resolver_domain: 'domain.tld'
    resolver_nameserver: [{address: '10.1.1.11', comment: 'ns01.domain.tld'},
                          {address: '10.1.1.12', comment: 'ns02.domain.tld'}]
    resolver_search: ['domain01.tld', 'domain02.tld']
```

### Inactive

```
- hosts: all
  roles:
    - role: resolver
  vars:
    resolver_state: 'inactive'
```

## Parameters

### Role

`resolver_state`

    Description: Control the state of the role.
                 'resolv.conf' is a core file and can therefore not be removed.
    Required   : False
    Value      : Predetermined
    Type       : String
    Default    : 'install'
    Options    :
      Install : 'true' | 'yes' | 'install'
      Inactive: 'quiesce' | 'inactive'

`resolver_domain`

    Description: Define the 'resolver_domain' option.
    Required   : False
    Value      : Arbitrary
    Type       : String
    Default    : "{{ansible_domain}}"
    Options    :
      Examples: 'domain.tld' | 'example.tld'
      None    : ''

`resolver_nameserver`

    Description: Define the 'resolver_nameserver' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array/Hash
    Default    : [{address: "{{ansible_default_ipv4.gateway}}", comment: 'Default IPv4 gateway'}]
    Options    :
      Examples: [{address: '10.1.1.11', comment: 'ns01.domain.tld'},
                 {address: '10.1.1.12', comment: 'ns02.domain.tld'}]
      None    : []

`resolver_search`

    Description: Define the 'resolver_search' option.
    Required   : False
    Value      : Arbitrary
    Type       : Array
    Default    : ["{{ansible_domain}}"]
    Options    :
      Examples: ['domain.tld'] | ['domain01.tld', 'domain02.tld'] |
                ['domain01.tld', 'domain02.tld', 'domain03.tld']
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
