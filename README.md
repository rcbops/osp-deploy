# osp-deploy

Automates the Red Hat OpenStack Platform 13 process

* Provisions a Director Node on Metal or within a VM
* Prepares and configures the Undercloud
* Introspects nodes
* Deploys the Overcloud

## Provisioning a Director VM

Start with a base install of RHEL or CentOS and configure it to be a Virtualization Host.  This will provision the director node as a VM (TODO, add ability to skip and change inventory for Physical Server deployment):

If the kickstart to provision the host wasn't set up with virtualization support, install these packages and reboot:

```
yum -y install libvirt-client libvirt-daemon qemu-kvm libvirt-daemon-driver-qemu libvirt-daemon-kvm virtinstall bridge-utils rsync virt-viewer libvirt git
```

#### Check out osp-deploy 

```
git clone https://gitlab.com/antonym/osp-deploy.git /opt/osp-deploy
cd /opt/osp-deploy
```

#### Setup Host Networking

We will need to make sure to have bridges available and up in the NIC configs.  They can also be simulated for testing (ex. brcrl addbr bridge_name):

* br-ctlplane
* br-ex 
* br-ipmi

#### Virtualized Director

If you are virtualizing the director, the following command will add the necessary packages,
download the latest RHEL 7.7 ISO and setup a director VM.  If you are deploying director to
a baremetal RHEL OS, you can skip this step and start with the next step.

```
./prepare_vm.sh
```

It will prompt for root and stack passwords and then provision the director VM.

## Provision the Undercloud

Modify group_vars/undercloud.yml for undercloud overrides, anything set in undercloud.conf:

```
./deploy_director.sh
```
It will prompt for RHEL credentials so that the VM can registered and packages can be downloaded.

## Prepare the Overcloud

* modify group_vars/all.yml settings
* generate a new playbooks/files/instackenv.json for hosts

This will prep the steps necessary for Overcloud deployment including Introspection:

```
./prepare_overcloud.sh
```

## Provision the Overcloud (Under Development, recommend generating templates by hand)

* modify overcloud_templates for environment, this directory is copied to /home/stack/templates and used for 

```
./deploy_overcloud.sh
```

The script will copy all of the template files and create a deploy script on the director node.  Then you'll log
into the director node and kick off the overcloud deploy.

```
ssh stack@director
cd scripts
./deploy_overcloud.sh
```

## Contributing and Merge Request Validation

Merge requests are tested with a sample deployment that simulates provisioning of the Director VM, undercloud deployment,
and overcloud deployment.  This ensures future changes don't break the existing deployment.

# TODO

* Automate networking template generation, or make it easier to generate based on deploy scenario
* Automate SSL Cert generation and options, currently using a self signed
* Move settings to a better template/config structure that can be extracted out for a seperate configuration repo per environment
* Move to specifiying template directory to avoid having to use config file naming in the deploy_overcloud template.
* Add configuration capture script for capturing working config and making a config repo that can then be deployed.
* Add support for multiple versions outside of OSP 13
* Cleanup
