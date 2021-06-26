# -*- mode: ruby -*-
# vi: set ft=ruby :

Dotenv.load

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  config.vm.disk :disk, size: "30GB", primary: true
  config.vm.box_check_update = true

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of cpus and memory on the VM:
    vb.cpus = 4
    vb.memory = 1024 * 8

    # Fix https://www.virtualbox.org/ticket/15705
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end

  config.vm.provision :root_user, type: "shell", path: "provision_root.sh"
  config.vm.provision :vagrant_user, type: "shell", privileged: false, path: "provision_nonroot.sh"

  config.vm.provision :vagrant_user_runner, type: "shell", privileged: false, inline: <<-SHELL
    # Install actions/runner
    mkdir ~/actions-runner && cd ~/actions-runner
    GHA_RUNNER_VERSION="2.278.0"
    curl -so actions-runner-linux-x64-${GHA_RUNNER_VERSION}.tar.gz -L https://github.com/actions/runner/releases/download/v${GHA_RUNNER_VERSION}/actions-runner-linux-x64-${GHA_RUNNER_VERSION}.tar.gz
    tar xzf ./actions-runner-linux-x64-${GHA_RUNNER_VERSION}.tar.gz
    ./config.sh --url #{ENV['REPOSITORY_URL']} --token #{ENV['RUNNER_TOKEN']}
    nohup ./run.sh &
  SHELL
end
