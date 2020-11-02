Role Name
=========

This role assists with the creation of OpenShift MachineConfigs in order to consume Red Hat subscriptions on a OpenShift node

Requirements
------------

Besides an OpenShift cluster with RHCOS nodes and a subscribed host, there are no other requirements.

Role Variables
--------------

* subscription_host : hostname of host wich is subscribed to either a local Satellite or directly to RHSM.

Dependencies
------------

none

Example Playbook
----------------

To create a MCO config, create the following playbook:

```
- hosts: all
  roles:
    - entitled-build
  vars:
    subscription_host: localhost
```

Afterwards run the playbook against this host:

`ansible-playbook -i localhost, <playbook.yaml>`

You will find the resulting MCO.yaml in /tmp on the invoking host.

License
-------

GPLv3

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
