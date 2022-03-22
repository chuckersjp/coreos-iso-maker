## WARNING!  WARNING!  WARNING!
It has been brought to my attention that use of coreos-iso-maker may geerate images that may NOT be supported
if you plan to install your cluster in FIPS mode.  A customer was having issues with a FIPS based cluster that
they couldn't update which may or may not be related to their use of this repo.  You have been warned!

Also note that in upcoming version of OpenShift, there will be the supported ability customize the ISO installer.
See [here](https://coreos.github.io/coreos-installer/cmd/iso/#coreos-installer-iso-customize) for more details

# coreos-iso-maker V2.10
Update this version:  This version supports the new OCP 4.6 ISO.  There was a name change as well as a change
in the underlying directory structure of the ISO which broke everything so this is no longer backwards compatible.
Versions should be tagged as either OCP4.5 (which works with earlier versions as well) or OCP4.6.  Make sure you
use the correct one or you will like have issues.

My thanks to Steve Ovens for pointing out how broken this became.

A *HUGE* thanks to ITS4U (https://github.com/its4u) for a number of pull requests that were to fix a lot
of the issues with the 4.6 liveCD due to all the changes in the ISO as well as my lack of understanding with some of 
the variables in use.

As part of ITS4U's work, there is a new parameter for the inventory that defines whether DHCP should be used.
Normally, of course, this will be set to false as that is the entire point of this project.  But if you have 
other NICs that make use of it, it can be turned on for those.

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

* `gateway`  	- default router IP
* `netmask`  	- default netmask
* `interface` 	- NIC device name.  Defaults to `ens192` which is default for VMWare
* `dns`		- dns server.  This can be done as a list.  Don't add more than 3.
* `webserver_url` - webserver that holds the Ignition file
* `webserver_port` - webserver port for the webserver above
* `webserver_ignition_path` - Ignition subpath in http server
* `install_drive` - drive to install RHCOS on
* `ocp_version` 	- Full OCP version you are going for. 4.4.3
* `iso_checksum`	- sha256 checksum of the ISO.  Currently correct as of 2020-01-23 and OCP 4.3
* `iso_name`	- Name of the ISO to download.  Makes certain assumptions that should be verified
* `rhcos_bios`	- Name of the BIOS image to boot from.  This is how the file is named on your webserver.  Make certain assumptions that should be verified. 
* `arch`		- CPU Architecture type.  Must be one of `x86_64` (default) or `ppc64le` Can be defined on the commandline with `-e`

In `inventory.yml` you will need to define your hosts:

* `bootstrap`	- bootstrap node and its `ipv4` address
* `masters`	- You will need to define `3` master nodes and their `ipv4` address
* `workers`	- However many worker nodes you want and their corresponding `ipv4` addresses.  Recommend no more than 3 at a go.

There is a second example file called `inventory-multinic.yml` for an example of how to setup multiple NIC machines.

You will need to use create individual ignition files and load them to your webserver.
This project does **NOT** currently do that.

# Running the Playbook

Once the inventory is created, you can run either of the following commands:

For a single ISO:
`ansible-playbook playbook-single.yml -K`

For multiple ISOs:
`ansible-playbook playbook-multi.yml -K`

The included `ansible.cfg` file assumes the name of the inventory file is `inventory.yml`  Change it
in there if you want to use a different one or include the `-i <inventory-filename>` option to change it
on the command line.

The ISO(s) will be created in the `/tmp` directory.  The `-K` is to request for the BECOME password which is
required to mount an ISO (assuming you don't have passwordless `sudo`).

# Acknowlegements
Special thanks to Shanna Chan for trailblazing these issues (detail 
here: https://shanna-chan.blog/2019/07/26/openshift4-vsphere-static-ip/) and inspiring its creation.
