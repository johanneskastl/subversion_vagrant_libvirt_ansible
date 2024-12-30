Vagrant.configure("2") do |config|

  ###################################################################################
  config.vm.define "subversion" do |node|

    # which image to use
    node.vm.box = "opensuse/Leap-15.6.x86_64"

    # sizing of the VMs
    node.vm.provider "libvirt" do |lv|
      lv.random_hostname = false
      lv.memory = 4048
      lv.cpus = 2
    end

    # set the hostname
    node.vm.hostname = "subversion"

    # disable shared folders
    node.vm.synced_folder ".", "/vagrant", disabled: true

    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision

  end # config.vm.define

end # Vagrant.configure
