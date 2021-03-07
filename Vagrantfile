$script = <<-SCRIPT
echo I am provisioning...
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io git
echo Creating docker group if needed...
sudo usermod -aG docker vagrant
sudo apt-get install -y python3-pip
sudo python3 -m pip install --upgrade pip
sudo pip3 install docker-compose
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.network "private_network", type: "dhcp"

  config.vm.define "master" do |subconfig|
    subconfig.vm.box = "ubuntu/bionic64"
    subconfig.vm.hostname = "mastervm"
    subconfig.vm.network :forwarded_port, guest: 8080, host: 8080
    subconfig.vm.network :forwarded_port, guest: 8000, host: 8000
    subconfig.vm.network :forwarded_port, guest: 8443, host: 8443
    subconfig.vm.network :forwarded_port, guest: 8022, host: 8022
    subconfig.vm.provision "shell", inline: $script

    subconfig.vm.provider "virtualbox" do |v|
      v.memory = 4096
    end
  end

  config.vm.define "node1" do |subconfig|
    subconfig.vm.box = "ubuntu/bionic64"
    subconfig.vm.hostname = "node1vm"
    subconfig.vm.provision "shell", inline: $script
  end
end