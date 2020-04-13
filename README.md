# coreos-iso-maker V2.4.1
Bug fixes and updated output to call the ignition files named based on the group.  E.g. The `[masters]` group creates
a `masters.ign` file to boot from, `[bootstrap]` uses the `bootstrap.ign` file.

New in  coreos-iso-maker V2.4
This version:  The `dns` variable in `group_vars/all.yml` is now a list.  Because of the way DNS works
do NOT specify more than 3 servers for this.  Thanks to Scott Worthington (https://github.com/worsco) for pointers
on how to do this.

New in Version 2.3
This version incorporates the ability to generate either a single ISO for OCP 4.x
installations or multiple ISOs depending on needs.  These ISOs are created for
when you need statis IPs.

New from version 2.2:  Make sure that genisoimage package is installed.
Special thanks to Scott Worthington (https://github.com/worsco)
for this Pull Request.

New in version 2.3: Enable UEFI booting by also adjusting the grub.cfg.  This
also fixed a long standing and unnoticed bug in previous version.  Special thanks
to ahmbas (https://github.com/ahmbas) for helping me find this bug and making me learn
more about ISOLINUX vs. UEFI booting.

# Problem definition
Some customers would like to use static IPs for their OCP nodes but don't have a
working DHCP server for various reasons.  This can be done using the ISO for CoreOS
and messing with the boot parameters.  However, this is involves lots of error prone
typing.  This project is designed to work around that and originally created individualized ISOs
for each node.  In v2.1, it can be used to  create a single ISO with a menu item for each node being
built.  Due to screen space limitations, it is NOT recommend that you use this to create
more than 7 nodes at a time (1 bootstrap, 3 master control planes, and 3 worker nodes).
It _might_ work, but you might have problems with the display on boot.  Caveat user.
If you would prefer to have multiple ISOs, there is a separate playbook for that.

# Variables to define
In the `group_vars/all.yml` file, define the following variables:

`gateway`  	- default router IP

`netmask`  	- default netmask

`interface` 	- NIC device name.  Defaults to `ens192` which is default for VMWare

`dns`		- dns server

`webserver_url` - webserver that holds the Ignition file

`webserver_port` - webserver port for the webserver above


`ocp_version` 	- OCP version you are going for.  Currently defaults to 4.3

`iso_checksum`	- sha256 checksum of the ISO.  Currently correct as of 2020-01-23 and OCP 4.3

`iso_name`	- Name of the ISO to download.  Makes certain assumptions that should be verified

`rhcos_bios`	- Name of the BIOS image to boot from.  Make certain assumptions that should be verified

In `inventory.yml` you will need to define your hosts:

`bootstrap`	- bootstrap node and its `ipv4` address

`masters`	- You will need to define `3` master nodes and their `ipv4` address

`workers`	- However many worker nodes you want and their corresponding `ipv4` addresses.  Recommend no more than 3 at a go.

You will need to use create individual ignition files and load them to your webserver.
This project does NOT currently do that.

Once the inventory is created, you can run either of the following commands:

For single:
`ansible-playbook playbook-single.yml -K`

For multiple:
`ansible-playbook playbook-multi.yml -K`

The ISO(s) will be created in the `/tmp` directory.  The `-K` is to request for the BECOME password which is
required to mount an ISO (assuming you don't have passwordless `sudo`).

# Acknowlegements
Special thanks to Shanna Chan for trailblazing these issues (detail 
here: https://shanna-chan.blog/2019/07/26/openshift4-vsphere-static-ip/) and inspiring its creation.
