# -*- mode: ruby -*-
# vi: set ft=ruby :

# Inline shell script that updates the APT package index
# and installs OpenJDK 17 inside the virtual machine.
$install_deps = <<-'SHELL'
  set -e

  echo "Updating APT package index..."
  apt-get update

  echo "Installing OpenJDK 17..."
  DEBIAN_FRONTEND=noninteractive apt-get install -y openjdk-17-jdk

  echo "Installed Java version:"
  java -version
SHELL

# Vagrant configuration version 2.
Vagrant.configure("2") do |config|

  # Use Ubuntu 22.04 LTS as the base operating system.
  config.vm.box = "ubuntu/jammy64"

  # Configure a private network with a static IP address.
  config.vm.network "private_network", ip: "192.168.101.100"

  # Configure the VirtualBox provider and allocate VM resources.
  config.vm.provider "virtualbox" do |vb|
    # Set a recognizable name for the VM in VirtualBox.
    vb.name = "vagrant-devops"

    # Allocate 4 GB of RAM to the virtual machine.
    vb.memory = 4096

    # Allocate 2 virtual CPUs to the virtual machine.
    vb.cpus = 2
  end

  # Execute the install_deps shell script during provisioning.
  config.vm.provision "shell", inline: $install_deps

  # Run the Java application inside the VM after vagrant up completes.
  config.trigger.after :up do |trigger|
    trigger.name = "Start Java application"
    trigger.info = "Starting the application with Gradle..."

    trigger.run_remote = {
      inline: "cd /vagrant && nohup ./gradlew bootRun > /tmp/vagrant-devops-app.log 2>&1 &"
    }
  end
end
