# Deploying-a-Web-Application-on-Azure-using-Ansible

Setup and Configuration:
Install Ansible and Azure CLI:
sudo apt update
sudo apt install ansible
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

Authenticate with Azure:
az login

Define Ansible Playbooks:
Create a Playbook for Resource Group and VNet:
- name: Create Azure resources
  hosts: localhost
  tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: myResourceGroup
        location: eastus

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: myResourceGroup
        name: myVNet
        address_prefixes: "10.0.0.0/16"

Create a Playbook for VMs and Load Balancer:
- name: Create VMs and Load Balancer
  hosts: localhost
  tasks:
    - name: Create virtual machine
      azure_rm_virtualmachine:
        resource_group: myResourceGroup
        name: myVM
        vm_size: Standard_B1s
        admin_username: azureuser
        admin_password: Password1234!
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 18.04-LTS
          version: latest

    - name: Create load balancer
      azure_rm_loadbalancer:
        resource_group: myResourceGroup
        name: myLoadBalancer
        frontend_ip_configurations:
          - name: myFrontEnd
            public_ip_address_name: myPublicIP

Deploy the Web Application:
Create a Playbook for Web Server Configuration:
- name: Configure web server
  hosts: myVM
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Deploy application code
      copy:
        src: /path/to/your/app
        dest: /var/www/html

Run the Playbooks:
Execute the playbooks to set up the infrastructure and deploy the application:
ansible-playbook create_resources.yml
ansible-playbook create_vms_loadbalancer.yml
ansible-playbook configure_web_server.yml
