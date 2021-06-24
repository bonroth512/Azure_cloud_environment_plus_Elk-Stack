# Azure cloud environment with Elk Stack

### Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.


![web_plus_elk](https://user-images.githubusercontent.com/77121974/123343033-2dd31a00-d50e-11eb-8002-f78d9a948041.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yml and config file may be used to install only certain pieces of it, such as Filebeat.

  - _TODO: Enter the playbook file._

This document contains the following details:
- Description of the Topologu
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

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the _____ machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _TODO: Add whitelisted IP addresses_

Machines within the network can only be accessed by _____.
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address?_

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes/No              | 10.0.0.1 10.0.0.2    |
|          |                     |                      |
|          |                     |                      |

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

