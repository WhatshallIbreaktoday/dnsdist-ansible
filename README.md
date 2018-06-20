PowerDNS dnsdist Role
=====================

[![Build Status](https://travis-ci.org/PowerDNS/dnsdist-ansible.svg?branch=master)](https://travis-ci.org/PowerDNS/dnsdist-ansible)
[![Galaxy](http://img.shields.io/badge/galaxy-PowerDNS.pdns-dnsdist.svg?style=flat-square)](https://galaxy.ansible.com/PowerDNS/dnsdist)

An Ansible role create by the folks behind PowerDNS to set up dnsdist.

Requirements
------------

An Ansible 2.3 or higher installation.

Dependencies
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    dnsdist_install_repo: "" 

By default dnsdist is installed from the os default repositories.
You can install dnsdist from the official PowerDNS repository overriding
the `dnsdist_install_repo` variable value as follows
(for the complete list of pre-defined repos see `vars/main.yml`):

    # Install dnsdist from the master branch
    - hosts: pdns-dnsdists
      roles:
      - { role: PowerDNS.dnsdist,
          dnsdist_install_repo: "{{ dnsdist_powerdns_repo_master }}"

    # Install dnsdist 1.3.x
    - hosts: pdns-dnsdists
      roles:
      - { role: PowerDNS.dnsdist,
          dnsdist_install_repo: "{{ dnsdist_powerdns_repo_13 }}"

The roles also supports custom repositories

    - hosts: all
      vars:
        dnsdist_install_repo:
          name: "dnsdist" # the repository name
          apt_repo_origin: "my.repo.com"  # used to pin dnsdist to the provided repository
          apt_repo: "deb http://my.repo.com/{{ ansible_distribution | lower }} {{ ansible_distribution_release | lower }}/dnsdist main"
          gpg_key: "http://my.repo.com/MYREPOGPGPUBKEY.asc" # repository public GPG key
          gpg_key_id: "MYREPOGPGPUBKEYID" # to avoid to reimport the key each time the role is executed
          yum_repo_baseurl: "http://my.repo.com/centos/$basearch/$releasever/dnsdist"
          yum_debug_symbols_repo_baseurl: "http://repo.powerdns.com/centos/$basearch/$releasever/dnsdist/debug"
      roles:
      - { role: PowerDNS.dnsdist }

If targeting only a specific platform (e.g. Debian) it's not needed to provide other platform (e.g. yum) repositories informations.

    dnsdist_install_epel: True

By default the role installs also the EPEL repository.
EPEL is needed to satisfy some dnsdist dependencies like `lidsodium`.
If these dependencies are included into other repositories already configured in the
host or in the custom `dnsdist_install_repo`, set this variable to `False` to skip
EPEL installation.

    dnsdist_package_name: "{{ default_dnsdist_package_name }}"

The name of the dnsdist package: "dnsdist" on both RHEL and Debian derivates distributions.

    dnsdist_package_version: ""

Install a specific version of dnsdist
NB: The usage of this variable makes only sense on RedHat-like systems,
    where each YUM repository can contains multiple versions of the same package.

    dnsdist_install_debug_symbols_package: False

Install dnsdist debug symbols package.

    dnsdist_debug_symbols_package_name: "{{ default_dnsdist_debug_symbols_package_name }}"

The name of the dnsdist debug symbols package.

    dnsdist_acls: []

A list of dnsdist ACLS (netmasks) to add to the configuration.

    dnsdist_carbonserver: ""

The IP address of the Carbon server that should receive dnsdist metrics.

    dnsdist_controlsocket: "127.0.0.1"

The IP address to listen on for the control socket.

    dnsdist_locals: ['127.0.0.1:5300']

A list of IP addresses dnsdist should listen on.

    dnsdist_servers: []

A list of IP addresses denoting the downstream DNS servers in the default pool.

    dnsdist_setkey: ""

A string that has the key for the dnsdist client.

    dnsdist_webserver_address: ""

The IP address where the built-in webserver should listen, empty thus disabled by default.

    dnsdist_webserver_password: ""

The password for the webserver. Must be set when `dnsdist_webserver_address` is set.

    dnsdist_config: ""

A string containing the full config for dnsdist. This is copied verbatim to the `dnsdist.conf` file.


Example Playbook
----------------

Deploy dnsdist in front of Google DNS and enable the web monitoring interface

    - hosts: pdns-dnsdists
      roles:
        - { role: PowerDNS.dnsdist,
            dnsdist_servers: ['8.8.8.8', '8.8.4.4'],
            dnsdist_webserver_address: "{{ ansible_default_ipv4['address']:8083 }}",
            dnsdist_webserver_password: 'geheim' }

Configure dnsdist provide custom configuration directives

    - hosts: pdns-dnsdists
      roles:
        - { role: PowerDNS.dnsdist,
            dnsdist_config: |
              setACL("127.0.0.1/8")
              newServer("192.0.2.53")
          }

License
-------

MIT
