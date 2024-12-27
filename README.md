# subversion_vagrant_libvirt_ansible

This Vagrant setup creates a VM and installs a
[subversion](https://subversion.apache.org/) server on it.

Default OS is openSUSE Tumbleweed. Although that can be changed in the
Vagrantfile, please beware that this will break the Ansible provisioning.

There is a branch called `Leap_15.6` that uses openSUSE Leap 15.6.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
1. Fetch the box, per default this is `opensuse/Tumbleweed.x86_64`, using
   `vagrant box add opensuse/Tumbleweed.x86_64`.
1. Make sure the git submodules are fully working by issuing `git submodule init
   && git submodule update`
1. Run `vagrant up`
1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
