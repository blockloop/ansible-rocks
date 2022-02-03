Vagrant.require_version ">= 1.7.0"

IMAGE = "ubuntu/focal64"
NODE_COUNT = 3
PROMETHEUS_COUNT = 1
DISK_SIZE = "10GB"
MEMORY = 1024
CPUS = 1
PUBLIC_KEY_FILE = "#{ENV['HOME']}/.ssh/id_rsa.pub"

PROMETHEUS_NODE_IP_ADDRESS = "192.168.2.70"
WEB_SERVER_IP_ADDRESSES = [
  "192.168.2.71",
  "192.168.2.72",
  "192.168.2.74",
]

ssh_pub_key = File.readlines(PUBLIC_KEY_FILE).first.strip
add_ssh_key_script = <<-SHELL
  echo "#{ssh_pub_key}" >> /home/vagrant/.ssh/authorized_keys
  echo "#{ssh_pub_key}" >> /root/.ssh/authorized_keys
SHELL

Vagrant.configure(2) do |config|
  # prometheus
  hostname = "prometheus"
  config.vm.define hostname do |node|
    node.vm.box = "ubuntu/focal64"
    node.vm.disk :disk, size: DISK_SIZE, primary: true
    node.vm.network "public_network", bridge: ["#{ENV['BRIDGE_INTERFACE']}"], ip: PROMETHEUS_NODE_IP_ADDRESS
    node.vm.hostname = hostname

    node.vm.provider :virtualbox do |vbox|
      vbox.gui = false
      vbox.memory = MEMORY
      vbox.cpus = CPUS
    end

    # add my public key so I can use ansible
    node.vm.provision "shell" do |sh|
      sh.inline = add_ssh_key_script
    end

    node.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbooks/prometheus.yaml"
    end
  end

  # Web servers
  [1,3,4].each do |i|
    hostname = "web-#{i}" 
    config.vm.define hostname do |node|
      node.vm.box = "ubuntu/focal64"

      node.vm.disk :disk, size: DISK_SIZE, primary: true
      node.vm.network "public_network", bridge: ["#{ENV['BRIDGE_INTERFACE']}"], ip: WEB_SERVER_IP_ADDRESSES[i]
      node.vm.hostname = hostname

      node.vm.provider :virtualbox do |vbox|
        vbox.gui = false
        vbox.memory = MEMORY
        vbox.cpus = CPUS
      end

      # add my public key so I can use ansible
      node.vm.provision "shell" do |sh|
        sh.inline = add_ssh_key_script
      end

      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbooks/web-server.yaml"
      end

    end
  end

end
