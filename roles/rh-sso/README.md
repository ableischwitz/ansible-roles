Role Name
=========

This role will install and configure RH-SSO on a RHEL8/CentOS8 system. 

Requirements
------------

As this role will install a Apache webserver as a front-end which will get it's TLS certificates managed by IDM, you should have an IDM server installed.
The hostname of this IDM server will be discovered via DNS (`_kerberos-master._tcp.{{ ansible_domain }}`). You may override this by setting ipa_host.
The system deploying RH-SSO should already be registered to IDM prior running this role.

Role Variables
--------------

The following variables and defaults are provided for customization of this configuration:

Some variables should be kept in a ansible-vault in order to protect them from abuse. Those are marked with [vault]. The Postgresql variables
could be stored in a vault, but they would be visible in cleartext in the standalone.xml of RH-SSO anyways.

```
ipa_host: "{{ lookup('dig', '_kerberos-master._tcp.{{ ansible_domain }}
# username and password of user allowed to create services and cert-requests
ipa_username[vault]: username
ipa_password[vault]: password

# username and password of initial admin account of RH-SSO and authentication realm (should be kept at "master")
sso_admin_user[vault]: admin
sso_admin_pass[vault]: admin
sso_admin_auth_realm: master

# Details of a mail-server configured within RH-SSO to send out user/alerting mails.
sso_mailserver_host: "mail.example.com"
sso_mailserver_port: "465"
sso_mailserver_use_ssl: true
sso_mailserver_use_starttls: true
sso_mailserver_from: "sso@example.com"
sso_mailserver_from_displayname: "SSO"

# SSO-client definition (currently only one client supported, should be extended to an array of hashes)
sso_client: OpenID-client
sso_client_name: OpenID sample client
sso_client_description: OpenID sample client
sso_client_realm: ExampleRealm
sso_client_secret: REALLYWELLKEPTSECRET
sso_client_redirect_uris:
  - https://client.example.com/oauth2callback/SSO
sso_client_root_url: https://client.example.com

# Postgresql settings
pg_root_vg: rootvg
pg_lv_size: 10G
pg_username[vault]: rh-sso
pg_password[vault]: (automatically generated)
```

Dependencies
------------

No other dependencies are required for this role.

Example Playbook
----------------

To run this role, make sure your desired SSO-host is registered at IDM, has proper repositories assigned (see RH-SSO docs).

* Create an ansible-vault for your secrets:

`ansible-vault create ansible-vault.yaml`
 -> add the following

```
ipa_username: <IDM-user>
ipa_password: <IDM-password>

sso_admin_user: <SSO-admin-username>
sso_admin_pass: <SSO-admin-password>
```

* Create the inventory.yaml file:
```
sso:
  hosts:
    sso.example.com
  vars:
    sso_mailserver_host: "mail.example.com"
    sso_mailserver_port: "465"
    sso_mailserver_use_ssl: true
    sso_mailserver_use_starttls: true
    sso_mailserver_from: "sso@example.com"
    sso_mailserver_from_displayname: "SSO"
    sso_client....
    ....
```

* Create a playbook `rh-sso-test.yaml`:

```
---
- hosts: all
  roles:
    - rh-sso
```

* Run the playbook as follows:

`ansible-playbook --ask-vault-pass -e @ansible-vault.yaml -i inventory.yaml rh-sso-test.yaml`

License
-------

GPLv3 SA-NC

Author Information
------------------

