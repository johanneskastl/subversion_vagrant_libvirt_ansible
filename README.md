# subversion_vagrant_libvirt_ansible

This Vagrant setup creates a VM and installs a
[subversion](https://subversion.apache.org/) server on it.

Default OS on this branch is openSUSE Leap 15.6. Although that can be changed in
the Vagrantfile, please beware that this will break the Ansible provisioning.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
1. Fetch the box, per default this is `opensuse/Leap-15.6.x86_64`, using
   `vagrant box add opensuse/Leap-15.6.x86_64`.
1. Make sure the git submodules are fully working by issuing `git submodule init
   && git submodule update`
1. Run `vagrant up`
1. Run `vagrant ssh` to log into the VM or install `curl` and `svn` on your
   host and check from there. Keep in mind that running these commands from the
   host clutters up your host's `~/.subversion/` directory.
1. Run the following commands to check that the Apache2 server is working and is
   serving both a simple `index.html` and the SVN repos overview. Replace the IP
   address in those commands (`192.0.2.13`) with your VM's actual IP address,
   that was printed out at the end of the Ansible deployment.

   When you are asked to enter a password for the users (`hpotter`, `hgranger`
   or `rweasley`), use `dobby` as the password.

   ```
   $ curl http://192.0.2.13.sslip.io
   Subversion server via vagrant-libvirt
   $ curl http://192.0.2.13.sslip.io/repos/
   <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
   <html><head>
   <title>401 Unauthorized</title>
   </head><body>
   <h1>Unauthorized</h1>
   <p>This server could not verify that you
   are authorized to access the document
   requested.  Either you supplied the wrong
   credentials (e.g., bad password), or your
   browser doesn't understand how to supply
   the credentials required.</p>
   </body></html>
   $ curl -u hpotter http://192.0.2.13.sslip.io/repos/
   Enter host password for user 'hpotter':
   <html><head><title>Collection of Repositories</title></head>
   <body>
    <h2>Collection of Repositories</h2>
    <ul>
     <li><a href="myproject1/">myproject1/</a></li>
    </ul>
   </body></html>
   $ curl -u hpotter http://192.0.2.13.sslip.io/repos/myproject1/
   Enter host password for user 'hpotter':
   <html><head><title>myproject1 - Revision 0: /</title></head>
   <body>
    <h2>myproject1 - Revision 0: /</h2>
    <ul>
     <li><a href="../">..</a></li>
    </ul>
   </body></html>
   ```

   As you can see, the "root url" of the server is reachable without
   authentication and just outputs a single line of text.

   Listing the repository overview or one of the repositories requires
   authentication, where `hpotter` is only allowed to see the `myproject1`
   repository.

1. Next you can try to checkout the repos and make some changes:

   ```
   # myproject1 via svnserve (unencrypted traffic)
   svn --username hpotter checkout svn://192.0.2.13.sslip.io/repos/myproject1/
   cd myproject1
   touch hpotter_made_this.txt
   svn add hpotter_made_this.txt
   svn commit -m "ADD hpotter_made_this.txt"
   # enter your password
   ```

   Now check in your browser if your file is visible in the repository:

   ```
   curl -u hpotter http://192.0.2.13.sslip.io/repos/myproject1/
   ```

   ```
   # myproject2 via http (in this setup also unencrypted traffic, normally via
   TLS):
   svn --username hgranger checkout http://192.0.2.13.sslip.io/repos/myproject2/
   cd myproject2
   touch created_by_hgranger.txt
   svn add created_by_hgranger.txt
   svn commit -m "ADD created_by_hgranger.txt"
   # enter your password
   ```

   Now check in your browser if your file is visible in the repository:

   ```
   curl -u hgranger http://192.0.2.13.sslip.io/repos/myproject2/
   ```

1. Check out the details on authorization, both for Apache2 and svnserve in the
   repositories (in the `conf` subdirectory) and in `/srv/svn/auth` and
   `/srv/svn/user_access/`.
1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`.

Do not forget to carefully clean up your `~/.subversion/` directory, if you
checked out from your host machine...

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
