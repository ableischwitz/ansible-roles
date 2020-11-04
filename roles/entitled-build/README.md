Role Name
=========

This role assists with the creation of OpenShift MachineConfigs in order to consume Red Hat subscriptions on a OpenShift node.
Please see https://access.redhat.com/solutions/4908771 for detailed manual instructions on preparing a node for entitled-builds.
Also note that the [documentation](https://docs.openshift.com/container-platform/4.5/builds/running-entitled-builds.html) also lists another option for builds. 

Requirements
------------

Besides an OpenShift cluster with RHCOS nodes and a subscribed host, there are no other requirements.

Role Variables
--------------

* `subscription_host` : hostname of host wich is subscribed to either a local Satellite or directly to RHSM.
* `create_MCO`: boolean which switches between machineconfig and secrets/configmaps output. Defaults to false 
  resulting file will either be `/tmp/MCO.yaml` or `/tmp/secrets_configmaps.yaml` depending on `create_MCO`

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
You may also use a one-liner like this:

`ansible-playbook -D entitled-build.yaml -i <subscription-host>, -e subscription_host=<subscription-host>`

Afterwards run the playbook against this host:

`ansible-playbook -i localhost, <playbook.yaml>`

You will find the resulting MCO.yaml/secrets_configmaps.yaml in /tmp of the invoking host.

If you are running OpenShift, you may deploy the machineconfigs / secrets as follows:

* `oc apply -f /tmp/MCO.yaml`
	* wait for every node to be rebooted into updated machineconfig

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
