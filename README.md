This is a demo project I gave at northdallas.net on Feb 2 2022.

Link to the video will be posted soon.

## Instructions

1. Modify the Vagrant file and set the IP addresses to some available IPs in your network. This is important because if you choose DHCP they will change and mess with your inventory.
2. Update `inventory.ini` to match those IP addresses
3. Run `vagrant up` to build and provision your cluster. This will:
   1. Create a Prometheus server 
   2. Execute playbooks/prometheus.yaml on that server to install Prometheus and prepare it for writing
4. Execute `ansible-playbook playbooks/telegraf-agent.yaml` to start writing metrics to the Prometheus node
