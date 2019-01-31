# -*- mode: ruby -*-

$box = "ubuntu/bionic64"
$mem = "2048"

$install_script = <<-SCRIPT
 apt-get update -y
 apt-get upgrade -y
 apt-get install python vim tmux htop ssh -y
 apt-get install build-essential libblas-dev libopenmpi-dev -y
SCRIPT

$install_other_tools_script = <<-SCRIPT
 # fast grep alternative (https://github.com/BurntSushi/ripgrep)
 curl -LO https://github.com/BurntSushi/ripgrep/releases/download/0.10.0/ripgrep_0.10.0_amd64.deb
 dpkg -i ripgrep_0.10.0_amd64.deb
 rm ripgrep_0.10.0_amd64.deb
 # fancy cat alternative (https://github.com/sharkdp/bat)
 curl -LO https://github.com/sharkdp/bat/releases/download/v0.9.0/bat_0.9.0_amd64.deb
 dpkg -i bat_0.9.0_amd64.deb
 rm bat_0.9.0_amd64.deb
SCRIPT


$upcxx_script = <<-SCRIPT
 wget -q "https://bitbucket.org/berkeleylab/upcxx/downloads/upcxx-2018.9.0-offline.tar.gz"
 tar -xf upcxx-2018.9.0-offline.tar.gz
 cd upcxx-2018.9.0-offline
 ./install $HOME/upcxx
 cd ..
 rm -rf upcxx-2018.9.0-offline.tar.gz upcxx-2018.9.0-offline
 echo "export PATH=$HOME/upcxx/bin:$PATH" >> ~/.profile
SCRIPT

$clone_script = <<-SCRIPT
 # TODO: use updated hw with working Makefiles
 wget -q https://cs.berkeley.edu/~brock/cs267/hw/cs267_hw1_part1_2018.tar.gz
 wget -q https://cs.berkeley.edu/~brock/cs267/hw/cs267_hw1_part2_2018.tar.gz
 wget -q https://cs.berkeley.edu/~brock/cs267/hw/cs267_hw2_2018_src.tar.gz
 wget -q https://cs.berkeley.edu/~brock/cs267/hw/cs267_hw3_2018_src.tar.gz
 mkdir hw1
 tar -xf cs267_hw1_part1_2018.tar.gz
 mv cs267_hw1_part1_2018 hw1/part1
 tar -xf cs267_hw1_part2_2018.tar.gz
 mv cs267_hw1_part2_2018 hw1/part2
 tar -xf cs267_hw2_2018_src.tar.gz
 mv cs267_hw2_2018_src hw2
 tar -xf cs267_hw3_2018_src.tar.gz
 mv cs267_hw3_2018_src hw3
 rm *.tar.gz
SCRIPT

$config_script = <<-SCRIPT
 ln -s /vagrant $HOME/cs267
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = $box
  config.vm.provider "virtualbox" do |vb|
    vb.memory = $mem
  end
  config.vm.provision "install", type: "shell", privileged: true,  inline: $install_script
  config.vm.provision "install other tools", type: "shell", privileged: true,  inline: $install_other_tools_script
  config.vm.provision "upcxx",   type: "shell", privileged: false, inline: $upcxx_script
  #config.vm.provision "clone",   type: "shell", privileged: false, inline: $clone_script
  config.vm.provision "config",  type: "shell", privileged: false, inline: $config_script
end
