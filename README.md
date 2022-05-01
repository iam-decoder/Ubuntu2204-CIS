Ubuntu 22.04 CIS STIG
================

[![Build Status](https://travis-ci.com/florianutz/Ubuntu2204-CIS.svg?branch=master)](https://travis-ci.com/florianutz/Ubuntu2204-CIS)
[![Ansible Role](https://img.shields.io/badge/role-florianutz.Ubuntu2204--CIS-blue.svg)](https://galaxy.ansible.com/florianutz/Ubuntu2204-CIS/)

Configure Ubuntu 22.04 machine to be CIS compliant. Level 1 and 2 findings will be corrected by default.

This role **will make changes to the system** that could break things. This is not an auditing tool but rather a remediation tool to be used after an audit has been conducted.

## IMPORTANT INSTALL STEP

If you want to install this via the `ansible-galaxy` command you'll need to run it like this:

`ansible-galaxy install -p roles -r requirements.yml`

With this in the file requirements.yml:

```
- src: https://github.com/florianutz/Ubuntu2204-CIS.git
```

Based on [CIS Ubuntu Benchmark v2.0.1 - 01-03-2020 ](https://www.cisecurity.org/cis-benchmarks/).

This repo originated from work done by [MindPointGroup](https://github.com/MindPointGroup/RHEL7-CIS)

Requirements
------------

You should carefully read through the tasks to make sure these changes will not break your systems before running this playbook.

Role Variables
--------------
There are many role variables defined in defaults/main.yml. This list shows the most important.

**ubuntu2204cis_notauto**: Run CIS checks that we typically do NOT want to automate due to the high probability of breaking the system (Default: false)

**ubuntu2204cis_section1**: CIS - General Settings (Section 1) (Default: true)

**ubuntu2204cis_section2**: CIS - Services settings (Section 2) (Default: true)

**ubuntu2204cis_section3**: CIS - Network settings (Section 3) (Default: true)

**ubuntu2204cis_section4**: CIS - Logging and Auditing settings (Section 4) (Default: true)

**ubuntu2204cis_section5**: CIS - Access, Authentication and Authorization settings (Section 5) (Default: true)

**ubuntu2204cis_section6**: CIS - System Maintenance settings (Section 6) (Default: true)  

##### Disable all selinux functions
`ubuntu2204cis_selinux_disable: false`

##### Service variables:
###### These control whether a server should or should not be allowed to continue to run these services

```
ubuntu2204cis_avahi_server: false  
ubuntu2204cis_cups_server: false  
ubuntu2204cis_dhcp_server: false  
ubuntu2204cis_ldap_server: false  
ubuntu2204cis_telnet_server: false  
ubuntu2204cis_nfs_server: false  
ubuntu2204cis_rpc_server: false  
ubuntu2204cis_ntalk_server: false  
ubuntu2204cis_rsyncd_server: false  
ubuntu2204cis_tftp_server: false  
ubuntu2204cis_rsh_server: false  
ubuntu2204cis_nis_server: false  
ubuntu2204cis_snmp_server: false  
ubuntu2204cis_squid_server: false  
ubuntu2204cis_smb_server: false  
ubuntu2204cis_dovecot_server: false  
ubuntu2204cis_httpd_server: false  
ubuntu2204cis_vsftpd_server: false  
ubuntu2204cis_named_server: false  
ubuntu2204cis_bind: false  
ubuntu2204cis_vsftpd: false  
ubuntu2204cis_httpd: false  
ubuntu2204cis_dovecot: false  
ubuntu2204cis_samba: false  
ubuntu2204cis_squid: false  
ubuntu2204cis_net_snmp: false  
```  

##### Designate server as a Mail server
`ubuntu2204cis_is_mail_server: false`


##### System network parameters (host only OR host and router)
`ubuntu2204cis_is_router: false`  


##### IPv6 required
`ubuntu2204cis_ipv6_required: true`  


##### AIDE
`ubuntu2204cis_config_aide: true`

###### AIDE cron settings
```
ubuntu2204cis_aide_cron:
  cron_user: root
  cron_file: /etc/crontab
  aide_job: '/usr/sbin/aide --check'
  aide_minute: 0
  aide_hour: 5
  aide_day: '*'
  aide_month: '*'
  aide_weekday: '*'  
```


##### Set to 'true' if X Windows is needed in your environment
`ubuntu2204cis_xwindows_required: no`


##### Client application requirements
```
ubuntu2204cis_openldap_clients_required: false
ubuntu2204cis_telnet_required: false
ubuntu2204cis_talk_required: false  
ubuntu2204cis_rsh_required: false
ubuntu2204cis_ypbind_required: false
```

##### Time Synchronization
```
ubuntu2204cis_time_synchronization: chrony
ubuntu2204cis_time_Synchronization: ntp

ubuntu2204cis_time_synchronization_servers:
  - uri: "0.pool.ntp.org"
    config: "minpoll 8"
  - uri: "1.pool.ntp.org"
    config: "minpoll 8"
  - uri: "2.pool.ntp.org"
    config: "minpoll 8"
  - uri: "3.pool.ntp.org"
    config: "minpoll 8"

```
##### - name: "SCORED | 1.1.5 | PATCH | Ensure noexec option set on /tmp partition"
It is not implemented, noexec for /tmp will disrupt apt. /tmp contains executable scripts during package installation
```

```  
##### 1.5.3 | PATCH | Ensure authentication required for single user mode
It is disabled by default as it is setting random password for root. To enable it set:
```yaml
ubuntu2204cis_rule_1_5_3: true
```
To use other than random password:
```yaml
ubuntu2204cis_root_password: 'new password'
```

##### 3.4.2 | PATCH | Ensure /etc/hosts.allow is configured
```
ubuntu2204cis_host_allow:
  - "10.0.0.0/255.0.0.0"  
  - "172.16.0.0/255.240.0.0"  
  - "192.168.0.0/255.255.0.0"    
```  

```
ubuntu2204cis_firewall: firewalld
ubuntu2204cis_firewall: iptables
```

##### 5.3.1 | PATCH | Ensure password creation requirements are configured
```
ubuntu2204cis_pwquality:
  - key: 'minlen'
    value: '14'
  - key: 'dcredit'
    value: '-1'
  - key: 'ucredit'
    value: '-1'
  - key: 'ocredit'
    value: '-1'
  - key: 'lcredit'
    value: '-1'
```


Dependencies
------------

Ansible >= 2.4 and <= 2.7 (2.8 is not yet supported)

Example Playbook
-------------------------

```
- name: Harden Server
  hosts: servers
  become: yes

  roles:
    - Ubuntu2204-CIS
```

To run the tasks in this repository, first create this file one level above the repository
(i.e. the playbook .yml and the directory `Ubuntu2204-CIS` should be next to each other),
then review the file `defaults/main.yml` and disable any rule/section you do not wish to execute.

Assuming you named the file `site.yml`, run it with:
```bash
ansible-playbook site.yml
```

Tags
----
Many tags are available for precise control of what is and is not changed.

Some examples of using tags:

```
    # Audit and patch the site
    ansible-playbook site.yml --tags="patch"
```

License
-------

MIT
