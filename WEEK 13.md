## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

**Note**: The following image link needs to be updated. Replace `diagram_filename.png` with the name of your diagram image file.  

![TODO: Update the path with the name of your diagram](Images/diagram_filename.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the __playbook___ file may be used to install only certain pieces of it, such as Filebeat.

  - _TODO: Enter the playbook file._( filebeat.yml)

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly _available____, in addition to restricting ___inbound access__ to the network.
- _TODO: What aspect of security do load balancers protect?     It does this by shifting attack traffic from the corporate server to a public cloud provider.
What is the advantage of a jump box?_ The advantage of a jump box is it’s a  hardened and monitored device that spans two dissimilar security zones and provides a controlled means of access between them.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the ___file systems_ and system __performance_or metrics_.
- _TODO: What does Filebeat watch for?_filebeat watches log files and collects data for any suspicious changes.
- _TODO: What does Metricbeat record? Records metrics from the operating system and collect specific information about the machines in the network
The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| JumpBox-1| Gateway  | 10.0.0.7   | Linux            |
| Web-1    |webservers| 10.0.0.4   | Linux            |
| Web-2    |webservers| 10.0.0.5   | Linux            |
| web-3A   |webservers| 10.0.0.10  | Linux            |
| ELK      |Monitoring| 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the _jumpbox__ machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _TODO: XX.215.XXX.XXX or your ip address. ( to see your ip address go to whatismyipaddress.com) 

Machines within the network can only be accessed by __each other__.
- _TODO: Which machine did you allow to access your ELK VM? Web-1, Web-2 and Web 3A
What was its IP address? 10.1.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 10.0.0.1 10.0.0.2    |
|ELK-server|  No                 |                      |
| Web-1    |  No                 |   10.0.0.1-254       |
| Web-2    |No                   |    10.0.0.1-254      | 
| web-3A   |NO                   |    10.0.0.1-254      |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible?_ frees the user to focus on efforts that help deliver more value to the business by spending time on more important tasks

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Log into your jumpbox
- Create an Ansible playbook that installs Docker and configures an ELK container.
 - Run the playbook to launch the container' ansible-playbook install_elk.yml.elk
  

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

**Note**: The following image link needs to be updated. Replace `docker_ps_output.png` with the name of your screenshot image file.  


![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_
WEB- 1, web-2 and web-3A at 10.0.0.4, 10.0.0.5 and 10.0.0.10. 

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_
-Filebeat
Metricbeat

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
 Filebeat monitors the log files or locations {from the VM}, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
**Filebeat**: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
- **Metricbeat**: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed `sudo` escalations, and CPU/RAM statistics.
- 
Metricbeat periodically collect metrics from the operating system and from services running on the server. (ELK Server)



### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the ___playbook_ file to the ansible control node_____.
- Update the _hosts____ file to include..webservers..


- Run the playbook, and navigate to "__kiana address by using the curl command__http://[your ip address]:5601" to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

First 'cd /etc/ansible' to the play book
To install elk run 'ansible-playbook install_elk.yml' in the ansible file
To install filebeat run 'ansible-playbook install_filebeat.yml'
To install metricbeat run 'ansible-playbook install_metricbeat.yml'