# coreos-iso-maker V2.0
Create a single coreos ISO for OCP 4.x installs when you need to set static IPs

This project is borderline close to a gist but should be enough to get you started.

# Problem definition
Some customers would like to use static IPs for their OCP nodes but don't have a
working DHCP server for various reasons.  This can be done using the ISO for CoreOS
and messing with the boot parameters.  However, this is involves lots of error prone
typing.  This project is designed to work around that and originally created individualized ISOs
for each node.  In v2.0, it now creates a single ISO with a menu item for each node being
built.  Due to screen space limitations, it is NOT recommend that you use this to create
more than 7 nodes at a time (1 bootstrap, 3 master control planes, and 3 worker nodes).
It _might_ work, but you might have problems with the display on boot.  Caveat user.

# Variables to define
In the `group_vars/all.yml` file, define the following variables:

`gateway`  	- default router IP

`netmask`  	- default netmask

`interface` 	- NIC device name.  Defaults to `ens192` which is default for VMWare

`dns`		- dns server

`webserver_url` - webserver that holds the Ignition file

`webserver_port` - webserver port for the webserver above


`ocp_version` 	- OCP version you are going for.  Currently defaults to 4.2

`iso_checksum`	- sha256 checksum of the ISO.  Currently correct as of 2019-10-23 and OCP 4.2

`iso_name`	- Name of the ISO to download.  Makes certain assumptions that should be verified

`rhcos_bios`	- Name of the BIOS image to boot from.  Make certain assumptions that should be verified

In `inventory.yml` you will need to define your hosts:

`bootstrap`	- bootstrap node and its `ipv4` address

`masters`	- You will need to define `3` master nodes and their `ipv4` address

`workers`	- However many worker nodes you want and their corresponding `ipv4` addresses.  Recommend no more than 3 at a go.

You will need to use create individual ignition files and load them to your webserver.
This project does NOT currently do that.

# Acknowlegements
Special thanks to Shanna Chan for trailblazing these issues (detail 
here: https://shanna-chan.blog/2019/07/26/openshift4-vsphere-static-ip/) and inspiring its creation.
