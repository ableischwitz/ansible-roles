Role Name
=========

This role assists with the creation of OpenShift MachineConfigs in order to consume Red Hat subscriptions on a OpenShift node.
Please see https://access.redhat.com/solutions/4908771 for detailed manual instructions on preparing a node for entitled-builds.
Also note that the [documentation](https://docs.openshift.com/container-platform/4.13/cicd/builds/running-entitled-builds.html) also lists another option for builds. 

Requirements
------------

Besides an OpenShift cluster with RHCOS nodes and a subscribed host, there are no other requirements.

Role Variables
--------------

* entitled_build_subscription_host : hostname of host wich is subscribed to either a local Satellite or directly to RHSM.
* entitled_build_create_mc : boolean if a MachineConfig should be created. Defaults to `false`.

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
    entitled_build_subscription_host: localhost
    entitled_build_create_mc: false
```

You may also use a one-liner like this:

`ansible-playbook -i localhost, <playbook.yaml> -e entitled_build_subscription_host=<subscription_host> -e entitled_build_create_mc=true`


Afterwards run the playbook against this host:

`ansible-playbook -i localhost, <playbook.yaml>`

You will find the resulting `MCO.yaml` or `secrets_configmaps.yaml` in /tmp on the invoking host.

Finally the files can be used in OpenShift:

## Using configuration on all nodes and allowing **any** container to use RHEL packages
* `oc apply -f /tmp/MCO.yaml`
	* wait for every node to be rebooted into updated machineconfig

## Using configmaps and secrets to allow specific namespaces to use RHEL packages

* `oc new-project test-entitled-build`
* `oc apply -f /tmp/secrets_configmaps.yaml`

	* create a pod/build which includes the secret/configmaps:
		`oc apply -f roles/entitled-build/tests/001-entitled-pod.yaml`

License
-------

GPLv3

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
