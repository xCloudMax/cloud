# Openstack installation
## Refreshing the repositories for updated builds of openstack
sudo snap refresh snapd --edge
## For installing microstack and it's dependencies
sudo snap install microstack --beta --devmode
## For initialising microstack
sudo microstack init --auto --control
## For getting the admin password for the openstack dashboard
sudo snap get microstack config.credentials.keystone-password
## web dashboard 
available at http://10.20.20.1/
