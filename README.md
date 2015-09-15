ansible-postfwd2
=========

This role installs and configures Postfwd2, a postfix policyd to combine complex restrictions in a ruleset.

Supported Platforms
-------------------

* RHEL 6
* Ubuntu 14.04 lts
* FreeBSD 10

It will likely run on other platforms, just drop in vars/ a new file to support your os variant, vars are parsed in the following order/format:
* {{ ansible_distribution }}_{{ ansible_distribution_major_version }}.yml
* {{ ansible_distribution }}.yml
* {{ ansible_os_family }}_{{ ansible_distribution_major_version }}.yml
* {{ ansible_os_family }}.yml"

Requirements
------------

* For FreeBSD a working pkgng setup is required (see: https://www.freebsd.org/doc/handbook/pkgng-intro.html )


Role Variables
--------------

By default this role will install postfwd2 and provide a minimal configuration, however variables can be passed to this role
and configuration can be overridden, for additional informations please have a look to **defaults/main.yml**


Dependencies
------------

None at this time.

Examples
----------------

1) Just install postfwd2, use a default policy (allow all)
```yaml
- hosts: all
  remote_user: root
  sudo: no
  roles:
    - { role: postfwd2 }
```

2) Install postfwd2 with a custom policy
```yaml
- hosts: all
  remote_user: root
  sudo: no
  vars:
    postfwd2_policy:
      00_deny_origin_eq_recipient:
        - sender==$$recipient
        - action=REJECT not allowed (o==s)
      01_allow_printers:
        - client_address=10.2.1.0/24
        - sender=printer[0-9]@lan
        - action=dunno
      02_allow_internal_wiki_to_our_domain:
        - client_address=10.1.2.3/32
        - recipient_domain=my.domain.tld
        - sender=wiki-internal@my.domain.tld
        - action=dunno
      DEFAULT:
        - action=REJECT not allowed
  roles:
    - { role: postfwd2 }
```

3) Install postfwd2 using a custom, user provided, policy template
```yaml
- hosts: all
  remote_user: root
  sudo: no
  vars:
    postfwd2_policy_template: templates/postfwd2_my_custom_policy.j2
  roles:
    - { role: postfwd2 }
```

License
-------

GPLv2

Author Information
------------------

Alessio Cassibba (x-drum) http://blog.zerodev.it

