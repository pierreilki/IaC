$script = <<-SCRIPT
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
mkdir /etc/docker/
echo '
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
' > /etc/docker/daemon.json
sudo sysctl --system
sudo apt-get update
sudo apt-get install -yqq \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -yqq docker-ce docker-ce-cli containerd.io
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
echo '
/dev/mapper/vagrant--vg-root /               ext4    errors=remount-ro 0       1
/vagrant /vagrant vboxsf uid=1000,gid=1000,nofail 0 0
' > /etc/fstab

swapoff -a


echo '
127.0.0.1       localhost
127.0.1.1       vagrant.vm      vagrant

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.20.30.10 devops
' > /etc/hosts

SCRIPT



Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: $script
    config.vm.box = "bento/ubuntu-20.04"
	config.vm.define "devops" do |devops|
		devops.vm.hostname = "devops"
		devops.vm.network "private_network", ip: "10.20.30.10"
		devops.vm.provider "virtualbox" do |v|
			v.memory = 13000
			v.cpus = 2
			v.name = "devops"
		end
	end
end