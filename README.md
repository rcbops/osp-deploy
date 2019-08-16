# osp-deploy

Deploys Red Hat OpenStack Platform 13
Work in Progress, for lab use only currently

```
# provision RHEL 7 or CentOS node as KVM virtualization host
git clone https://gitlab.com/antonym/osp-deploy.git /opt/osp-deploy
cd /opt/osp-deploy
# setup a director vm
# will need to make sure to have bridges present in NIC configs or can be simulated for testing
#
# br-ctlplane
# br-ex 
# br-ipmi
#
# ex. brcrl add <bridge> for simulation
./prepare_vm.sh
# modify group_vars/undercloud.yml for undercloud overrides, anything set in undercloud.conf
./deploy_director.sh
# director/undercloud node will be deployed
# modify group_vars/all.yml settings
# generate a new playbooks/files/instackenv.json for hosts 
# modify overcloud_templates for environment, this directory is copied to /home/stack/templates and used for 
./deploy_overcloud.sh
# will generate all files and create a deploy script on the director node
ssh stack@director
cd scripts
./deploy_overcloud.sh
```

# TODO

* Automate networking template generation, or make it easier to generate based on deploy scenario
* Automate SSL Cert generation and options, currently using a self signed
* Move settings to a better template/config structure that can be extracted out for a seperate configuration repo per environment
* Move to specifiying template directory to avoid having to use config file naming in the deploy_overcloud template.
* Add configuration capture script for capturing working config and making a config repo that can then be deployed.
* Add simple RHEL Kickstart ISO/USB for getting directory on-line
* Cleanup
