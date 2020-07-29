pnq_ansible-osp16-ovn-deploy
=========

This playbook will deploy an OpenStack 16 environment with OVN, DVR. By default, it will deploy a three-node HA clsuter for the controllers and three compute nodes. The compute nodes have been configured to have publicly-accessible IP addresses on br-ex to support DVR.

Updates
------------
1) LDAP is now used on the undercloud as well as the overcloud. To login to the undercloud or the overcloud, you must be in the Red Hat LDAP group called "gcs-tam". On the undercloud, you can use sudo -u stack -i to login as stack. On the overcloud, you must create a project and assign a role to your user, which is in the "redhat" domain.

2) The plybook can now run on either a RHEL7 or RHEL8 KVM server.

Requirements
------------

A physical hypervisor in the Red Hat TAM Lab in Pune. Each controller is configured with 30GB RAM and each compute node with 30GB RAM, with the undercloud requiring an additional 30GB for the undercloud node. This brigns the total for the default configuration to 180GB of RAM required on the hypervisor.

The playbook will create the required internal network for the control plane and introspection. Additionally, the hypervisor is expected to have a publicly-accessible bridge network called br0 on the 10.74.169.0/24 network.

Role Variables
--------------

Most of the customizations can be found in group_vars/all/cloud.yml:

- server_sshkeys is a list of SSH keys that will be added to the stack user on the undercloud. Add your SSH key here if you want password-less SSH to work from your workstation. But do not remove the other keys.
- Ceph Pools: If you make any changes here, such as the names of the pools or the ceph username, be aware that the playbook will NOT update the authorization of the openstack user for those pools. This will have to be done manually before running the playbook. Also, the pools must be created in advance.
- Passwords: The password for the stack user and for the root user on the overcloud nodes can be set here.

If you want to change the configuration of the overcloud nodes (e.g. RAM and CPU), then look at the files in group_vars/compute.yaml and group_vars/control.yml.

To change the overcloud nodes' MAC addresses or vBMC ports, modify the files in host_vars/*. Note that in order to add a compute node, a new file will need to be added to host_vars/ that contains information required for the playbook to build the node.

r0-virt-network:
No modifications should be made here.

r1-create-nodes:
To add packages and/or repositories to the hypervisor node, modify the appropriate fields in r1-create-nodes/vars/main.yml. Note that this does not impact the undercloud or overcloud nodes.

r2-deployundercloud:
No modifications should be made here.

r3-configovercloud:
No modifications should be made here.

r4-deployovercloud:
No modifications should be made here.

r5-postdeployment (Not currently used):
This role is optional and will really only create the public network. Note that the script is hard-coded to use IP addresses on the 10.74.169.0/24 network. If this network changes, or if the floating IP range needs to be modified, it must be done in r5-postdeployment/templates/create_public_network.sh.


Dependencies
------------

None

Author Information
------------------

Eric Beaudoin:  ebeaudoin@redhat.com
Matthew Secaur: msecaur@redhat.com
