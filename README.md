# Azure cloud environment with Elk Stack

### Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![web_plus_elk](https://user-images.githubusercontent.com/77121974/123343646-64f5fb00-d50f-11eb-8e2a-091ebdef865a.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yml and config file may be used to install only certain pieces of it, such as Filebeat.

- [Install Web Virtual Machines](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/dd3db2528135bc681ec2a2fc6cb56229dfbf2310/Ansible/install_dvwa.txt)
- [Ansible Configuration file](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/ansible-config.txt)  
- [Ansible Hosts Configuration](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/hosts_elk&dvwa.txt)  
- [Install Elk Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/install_elk_playbook.txt)
- [Filebeat Configuration](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/filebeat-config.txt)
- [Filebeat Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/filebeat-playbook.txt)
- [Metricbeat Configuration](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/metricbeat-config.txt)
- [Metricbeat Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/metricbeat-playbook.txt)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
The advantages to incorporating load balancers into the topology are: availability, focusing traffic, and network security.  

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the metric data and system logs.  This includes two beats as well: Filebeats and Metricbeats.
- Filebeat helps generate logs across the selected endpoints and forwards it to Elasticsearch and/or Logstash.  Specifically, its built to monitor changes on the file system. 
- Metricbeats collects metrics across the selected endpoints and sends the data to Elasticsearch and/or Logstash.  

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web_1    |Web Server| 10.0.0.5   | Linux            |
| Web_2    |Web Server| 10.0.0.6   | Linux            |
| Web_3    |Web Server| 10.0.0.7   | Linux            |
| Elk-vm   |Elk Server| 10.1.0.6   | Linux            |
| Load Balancer|Gateway|104.42.255.28|Linux           |
### Access Policies

The machines on the internal network are not exposed to the public Internet.  Using key pairs, for SSH, generated on the Ansible control node helps control users and hosts.
-Configuring the authentication of users with SSH keys can prevent unwanted traffic to the network.

Through the Ansible control node generate the key using in the working directory of ~/.ssh:

- ssh-keygen 
- cat id_rsa.pub

Following the keys creation, reset the SSH public key for each VM that will be accessed through port 22.

Only the declared Workstation can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- The public IP address of the Workstation has been whitelisted to allow access through 5601 to utilize Kibana. 

Machines within the network can access the ELK VM through two methods:
- The workstation with a whitelisted IP address can access through TCP port 5601 utilizing the Kibana interface.
- The Jump-Box can utilize SSH to access the ELK VM.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | No                  | Public IP of Workstation for SSH|
| Web_1    | No                  | 10.0.0.4 (IP of Jump-Box) for SSH|
| Web_2    | No                  | 10.0.0.4 (IP of Jump-Box) for SSH|
| Web_3    | No                  | 10.0.0.4 (IP of Jump-Box) for SSH|
| Elk-vm   | No                  | Public IP of Workstation using TCP 5601|
| Load Balancer| No              | Public IP of Workstation on HTTP 80|

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because this ensures the provisioning of new instances will run identically.  The process of eliminating variability between each configuration is a way to remove errors between new deployments.    

The playbook implements the following tasks:
- First task is to call upon the correct host group and remote user:
```
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azdmin
  become: true
  tasks:
```
- Using Ansible apt module, install docker.io and python3-pip first:
```
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present


    # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
```
- Use the Ansible pip module to install the docker module:
```
    #Use pip module (It will default to pip3)
    - name: Install Docker Module
      pip:
        name: docker
        state: present
```
- With the command module, increase the virtual memory of the elk-vm and then through the sysctl module automatically have this configuration run after an restart:
```
    #Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144


    #Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes
```        
- Initialize the docker elk container module and configure the correct port mapping:
```
    #Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        #Please list the ports that ELK runs on
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5400:5400
          - 80:80
```
- Lastly, through systemd module, enable service docker on reboot:
```
    #Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes
```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/77121974/123377431-51678600-d549-11eb-9d66-7df3478bad6d.png)

Confirming the image description is sebp/elk:761 means the correct Docker container was downloaded.

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web_1 with IP address of 10.0.0.5.
- Web_2 with IP address of 10.0.0.6.
- Web_3 with IP address of 10.0.0.7.

We have installed the following Beats on these machines:
- Filebeats was installed on Web_1, Web_2, and Web_3.
- Metricbeats was installed on Web_1, Web_2, and Web_3.

These Beats allow us to collect the following information from each machine:
- Filebeat will help generate log files being sent to Elasticsearch and Logstash from the webserver group of Web_1, Web_2, and Web_3.  Due to these web servers running DVWA containers, the logs to be organized are from the Apache server and MySQL server. As an example, Filebeats will collect Apache access logs like apache2.access.user_agent.original or maybe other possible exportable fields.
- Metricbeat is shipper of system and service metrics from the webserver group of Web_1, Web_2, Web_3 to the Elk-vm.  As configured the metrics are directed to Elasticsearch to be viewed Kibana.  An example of the kind of metrics to be collected is server status data from the Apache mod_status module.       

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below to initial the ELK VM:
- Copy the [Install Elk Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/install_elk_playbook.txt) to the Ansible control node's /etc/ansible directory.  Be sure to save the file with extension .yml.
- Update the hosts file under /etc/ansible/hosts to include an elk group with the IP address of the ELK VM.
```
[elk]
10.1.0.6 ansible_python_interpreter=/usr/bin/python3
```
- Run the playbook with: ```ansible-playbook install-elk.yml```

While still in the current session with the control node, download and configure the two beats. First, Filebeats:
- Download Filebeats:```curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml```
- Nano filebeat-config.yml by scrolling down to line 1106:
```
  # Array of hosts to connect to.
  # Scheme and port can be left out and will be set to the default (http and 9200)
  # In case you specify and additional path, the scheme is required: http://localh>  # IPv6 addresses should always be defined as: https://[2001:db8::1]:9200
  hosts: ["10.1.0.6:9200"]
  username: "elastic"
  password: "changeme"      #It's recommended to change this password.
```
-Scroll further down to line 1806:
```
  set.kibana:
  host: "10.1.0.6:5601" 
```
- Copy the [Filebeat Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/filebeat-playbook.txt) and be sure to nano the file to check the hosts and save the file with extension .yml.
- Run this playbook with: ```ansible-playbook filebeat-playbook.yml"
- To check if installation is working as expected: 
  - Navigate to the Filebeat installation page on the ELK's GUI (http://[your.ELK-VM.External.IP]:5601/app/kibana).
  - On the same page, scroll to 'Step 5: Module Status' and click 'Check Data'.
  - Scroll to the bottom of the page adn click 'Verify Incoming Data'. 

Next Metricbeats:
-Download Metricbeats:```#curl -L -O https://gist.githubusercontent.com/slape/58541585cc1886d2e26cd8be557ce04c/raw/0ce2c7e744c54513616966affb5e9d96f5e12f73/metricbeat > /etc/ansible/metricbeat-config.yml```
-Nano metricbeat-config.yml by scrolling down and changing to elasticsearch's port like the filebeat-config.yml.
```
hosts: ["10.1.0.6:9200"]
setup.kibana:
  host: "10.1.0.6:5601"
```
- Copy [Metricbeat Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/metricbeat-playbook.txt) and be sure to check the hosts and save this file with extension .yml.
- Run this playbook with: ```ansible-playbook metricbeat-playbook.yml``` 
- To check if installation is working as expected: 
  - Navigate to the Metricbeat installation page on the ELK's GUI (http://[your.ELK-VM.External.IP]:5601/app/kibana).
  - On the same page, scroll to 'Step 5: Module Status' and click 'Check Data'.
  - Scroll to the bottom of the page adn click 'Verify Incoming Data'. 
