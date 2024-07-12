# LXD

## Intro
LXD scales from one instance on a single machine to a cluster in a full data center rack, making it suitable for running workloads both for development and in production.

LXD allows you to easily set up a system that feels like a small private cloud. LXD and LXC are two distinct implementations of Linux containers.

LXC is a low-level user space interface for the Linux kernel containment features. It consists of tools (lxc-* commands), templates, and library and language bindings.

LXD is a more intuitive and user-friendly tool aimed at making it easy to work with Linux containers. It is an alternative to LXC’s tools and distribution template system, with the added features that come from being controllable over the network. Under the hood, LXD uses LXC to create and manage the containers.

LXD provides a superset of the features that LXC supports, and it is easier to use. Therefore, if you are unsure which of the tools to use, you should go for LXD. LXC should be seen as an alternative for experienced users that want to run Linux containers on distributions that don’t support LXD.
lxd vs. lxc
To control LXD, you typically use two different commands: lxd and lxc.

LXD daemon
The lxd command controls the LXD daemon. Since the daemon is typically started automatically, you hardly ever need to use the lxd command. An exception is the lxd init subcommand that you run to initialize LXD.

There are also some subcommands for debugging and administrating the daemon, but they are intended for advanced users only. See lxd --help for an overview of all available subcommands.

LXD client
The lxc command is a command-line client for LXD, which you can use to interact with the LXD daemon. You use the lxc command to manage your instances, the server settings, and overall the entities you create in LXD. See lxc --help for an overview of all available subcommands.

The lxc tool is not the only client you can use to interact with the LXD daemon. You can also use the API, the UI, or a custom LXD client.




## Installation

the best way is to use snap

****very very important note:****
if you use lxd in virtual machine in vmware you should check virtual intel-vt / amd-v in virtualization in proccessor section to avoid error:

Error: Failed instance creation: Failed creating instance record: Instance type "virtual-machine" is not supported on this server: KVM support is missing (no /dev/kvm)

when you create vm 

### install snap

sudo apt update
sudo apt install snapd
snap version

### install and initial lxd and req,..

sudo snap install lxd
sudo snap refresh lxd

sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils


getent group lxd | grep -qwF "$USER" || sudo usermod -aG lxd "$USER"

if you dont have cluster or maas you can use this command to initial lxd:

lxd init --minimal




## Launch and inspect instances

lxc image list ubuntu:

lxc image list ubuntu: 24.04 architecture=$(uname -m)


lxc launch ubuntu:24.04 first
lxc copy first third
lxc launch ubuntu:24.04 ubuntu-vm --vm
lxc list


lxc start third

lxc info first
lxc info third
lxc info ubuntu-vm

lxc stop second
lxc delete third
lxc delete third --force
lxc launch ubuntu:24.04 limited --config limits.cpu=1 --config limits.memory=192MiB

lxc config show limited
lxc config show first

free -m
lxc exec first -- free -m
lxc exec limited -- free -m

nproc
lxc exec first -- nproc
lxc exec limited -- nproc


lxc config set limited limits.memory=128MiB

lxc config show limited


lxc exec limited -- free -m


lxc exec ubuntu-vm -- df -h


Override the size of the root disk device:

lxc config device override ubuntu-vm root size=30GiB


Restart the VM:

lxc restart ubuntu-vm


get console to vm:


lxc exec first -- bash

cat /etc/*release

exit


lxc exec first -- apt-get update
lxc exec first -- apt-get install sl -y
lxc exec first -- /usr/games/sl


Pull a file from the container:

lxc file pull first/etc/hosts .
Add an entry to the file:

echo "1.2.3.4 my-example" >> hosts
Push the file back to the container:

lxc file push hosts first/etc/hosts
Use the same mechanism to access log files:

lxc file pull first/var/log/syslog - | less


Create a snapshot called “clean”:

lxc snapshot first clean
Confirm that the snapshot has been created:

lxc list first
lxc info first
Note

lxc list shows the number of snapshots. lxc info displays information about each snapshot.

Break the container:

lxc exec first -- rm /usr/bin/bash
Confirm the breakage:

lxc exec first -- bash
Note

You do not get a shell, because you deleted the bash command.

Restore the container to the state of the snapshot:

lxc restore first clean
Confirm that everything is back to normal:

lxc exec first -- bash
exit
Delete the snapshot:

lxc delete first/clean


## access the LXD web UI

Starting with LXD 5.21, the UI is enabled by default. If you want to disable it, set the option to false:

sudo snap set lxd ui.enable=false
sudo systemctl reload snap.lxd.daemon
To enable it again, or to enable it for older LXD versions (that include the UI), set the option to true:

sudo snap set lxd ui.enable=true
sudo systemctl reload snap.lxd.daemon

expose 8443 port to access on the web:

lxc config set core.https_address :8443


open in browser :

https://lxd_ip:8443

follow instruction:

generate and download .pfx and .cert --> import in browser


login and enjoy using website.













### refrences:

https://documentation.ubuntu.com/lxd/en/latest/










