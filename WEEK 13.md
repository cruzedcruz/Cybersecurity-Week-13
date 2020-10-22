## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

**Note**: The following image link needs to be updated. Replace `diagram_filename.png` with the name of your diagram image file.  

[WEEK 13 Diagram.png](https://github.com/cruzedcruz/Cybersecurity-Week-13/blob/main/Diagrams/WEEK%2013%20Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  [filebeat.yml](https://github.com/cruzedcruz/Cybersecurity-Week-13/blob/main/Ansible/filebeat.yml.PNG)

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
- _TODO: XX.215.XXX.XXX or your ip address. [CHeck you ip address](http://whatismyipaddress.com) 

Machines within the network can only be accessed by each other.
- _TODO: Which machine did you allow to access your ELK VM? Web-1, Web-2 and Web 3A
What was its IP address? 10.1.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | Your IP address      |
|ELK-server|  No                 |   10.0.0.1-254       |
| Web-1    |  No                 |   10.0.0.1-254       |
| Web-2    |  No                 |    10.0.0.1-254      | 
| web-3A   |  No                 |    10.0.0.1-254      |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- What is the main advantage of automating configuration with Ansible? Frees the user to focus on efforts that help deliver more value to the business by spending time on more important tasks

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Log into your jumpbox
- Create an Ansible playbook that installs Docker and configures an ELK container.
 - Run the playbook to launch the container 'ansible-playbook install_elk.yml'
  

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[docker ps output](https://github.com/cruzedcruz/Cybersecurity-Week-13/blob/main/Linux/Docker%20ps%20elk%20server.PNG)

The playbook is duplicated below.

```yaml
---
# install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
```

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
WEB- 1, web-2 and web-3A at 10.0.0.4, 10.0.0.5 and 10.0.0.10. 

We have installed the following Beats on these machines:
-Filebeat
-Metricbeat
-Packetbeat

These Beats allow us to collect the following information from each machine: 
 Filebeat monitors the log files or locations {from the VM}, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
**Filebeat**: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
- **Metricbeat**: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed `sudo` escalations, and CPU/RAM statistics.

Metricbeat periodically collect metrics from the operating system and from services running on the server. (ELK Server)
The playbook below installs Metricbeat on the target hosts. The playbook for installing Filebeat is not included, but looks essentially identical — simply replace `metricbeat` with `filebeat`, and it will work as expected.

```yaml
---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

    # Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.4.0-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    # Use command module
  - name: setup metric beat
    command: metricbeat setup

    # Use command module
  - name: start metric beat
    command: service metricbeat start
```


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook file to the ansible control node.
- Update the hosts file to include webservers and elk


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
