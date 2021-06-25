# Azure cloud environment with Elk Stack

### Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![web_plus_elk](https://user-images.githubusercontent.com/77121974/123343646-64f5fb00-d50f-11eb-8e2a-091ebdef865a.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yml and config file may be used to install only certain pieces of it, such as Filebeat.

[Install Web Virtual Machines](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/dd3db2528135bc681ec2a2fc6cb56229dfbf2310/Ansible/install_dvwa.txt)
[Ansible Configuration file](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/ansible-config.txt)  
[Ansible Hosts Configuration](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/hosts_elk&dvwa.txt)  
[Install Elk Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/install_elk_playbook.txt)
[Filebeat Configuration](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/filebeat-config.txt)
[Filebeat Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/filebeat-playbook.txt)
[Metricbeat Configuration](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/metricbeat-config.txt)
[Metricbeat Playbook](https://github.com/bonroth512/Azure_cloud_environment_plus_Elk-Stack/blob/974d96d60337910844c51bccfca37117e18e9892/Ansible/metricbeat-playbook.txt)

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
- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- _TODO: What does Filebeat watch for?_
- _TODO: What does Metricbeat record?_

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web_1    |Web Server| 10.0.0.5   | Linux            |
| Web_2    |Web Server| 10.0.0.6   | Linux            |
| Web_3    |Web Server| 10.0.0.7   | Linux            |
| Elk-vm   |Elk Server| 10.1.0.6   | Linux            |
| Load Balancer|Gateway|104.42.255.28|Linux           |
### Access Policies

(Need to include SSH key and generation in this section)

The machines on the internal network are not exposed to the public Internet. 

Only the declared Workstation can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-The public IP address of the Workstation has been whitelisted to allow access through 5601 to utilize Kibana. 

Machines within the network can access the ELK VM through two methods:
-The workstation with a whitelisted IP address can access through TCP port 5601 utilizing the Kibana interface.
-The Jump-Box can utilize SSH to access the ELK VM.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | No                  | Public IP of Workstation for SSH|
| Web_1    | No                  | 10.0.0.4 (IP of Jump-Box) for SSH|
| Web_2    | No                  | 10.0.0.4 (IP of Jump-Box) for SSH|


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because this ensures the provisioning of new instances will run identically.  The process of eliminating variability between each configuration is a way to remove errors between new deployments.    

The playbook implements the following tasks:
- Using Ansible apt module, install docker.io and python3-pip first, then use the Ansible pip module to install the docker module. 
- With the command module, increase the virtual memory of the elk-vm and then through the sysctl module automatically have this configuration run after an restart.
- Initialize the docker elk container module and configure the correct port mapping.
- Lastly, through systemd module, enable service docker on reboot.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

https://github.com/bonroth512/Cyber_Projects/blob/main/Diagrams/elk_docker_ps.PNG

(need to include why this represents an successfully configured ELK instance) = sebp/elk:761

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
-Web_1 with IP address of 10.0.0.5.
-Web_2 with IP address of 10.0.0.6.
-Web_3 with IP address of 10.0.0.7.

We have installed the following Beats on these machines:
-Filebeats was installed on Web_1, Web_2, and Web_3.
-Metricbeats was installed on Web_1, Web_2, and Web_3.

These Beats allow us to collect the following information from each machine:
- Filebeat will help generate log files being sent to Elasticsearch and Logstash from the webserver group of Web_1, Web_2, and Web_3.  Due to these web servers running DVWA containers, the logs to be organized are from the Apache server and MySQL server. As an example, Filebeats will collect Apache access logs like apache2.access.user_agent.original or maybe other possible exportable fields.
- Metricbeat is shipper of system and service metrics from the webserver group of Web_1, Web_2, Web_3 to the Elk-vm.  As configured the metrics are directed to Elasticsearch to be viewed Kibana.  An example of the kind of metrics to be collected is server status data from the Apache mod_status module.       

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the _____ file to _____.
- Update the _____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

