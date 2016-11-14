VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "parallels/debian-8.6"

  config.vm.provision :ansible do |ansible|
       ansible.playbook = "test.yml"
       ansible.sudo = true
  end
end
