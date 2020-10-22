## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
 
[WEEK 13 Diagram.png](https://github.com/cruzedcruz/Cybersecurity-Week-13/blob/main/Diagrams/WEEK%2013%20Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  [filebeat.yml](https://github.com/cruzedcruz/Cybersecurity-Week-13/blob/main/Ansible/filebeat.yml.PNG)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
- The aspect of security that load balancers protect is by shifting attack traffic from the corporate server to a public cloud provider.
- The advantage of a jump box is it’s a hardened and monitored device that spans two dissimilar security zones and provides a controlled means of access between them.

- The aspect of security that load balancers protect are by shifting the attack traffic from the corporate server to a public cloud provider.
-The advantage of a jump box is it’s a  hardened and monitored device that spans two dissimilar security zones and provides a controlled means of access between them.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems and system performance or metrics.
- Filebeat watches log files and collects data for any suspicious changes.
- Records metrics from the operating system and collect specific information about the machines in the network.

The configuration details of each machine may be found below.

*Please note: IP ADDRESS will be different on your machine. Check the private IP address on your machines once their created.*

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| JumpBox-1| Gateway  | 10.0.0.7   | Linux            |
| Web-1    |webservers| 10.0.0.4   | Linux            |
| Web-2    |webservers| 10.0.0.5   | Linux            |
| web-3A   |webservers| 10.0.0.10  | Linux            |
| ELK      |Monitoring| 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- XX.215.XXX.XXX or your own ip address. [Check you ip address](http://whatismyipaddress.com) 

Machines within the network can only be accessed by each other.
- Web-1, Web-2 and Web 3A send traffic to the ELK server.


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
-It frees the user to focus on efforts that help deliver more value to the business by spending time on more important tasks

The playbook implements the following tasks:
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
- **Filebeat**: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
- **Metricbeat**: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed `sudo` escalations, and CPU/RAM statistics.
- **Packetbeat**: Packetbeat collects packets that pass through the NIC, similar to Wireshark. We use it to generate a trace of all activity that takes place on the network, in case later forensic analysis should be warranted.

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
- Run each playbook on the appropriate targets

The easiest way to copy the playbooks is to use Git:

```bash
$ cd /etc/ansible
$ mkdir files
# Clone Repository + IaC Files
$ git clone https://github.com/cruzedcruz/Cybersecurity-Week-13.git
# Move Playbooks and hosts file Into `/etc/ansible`
$ cp Cybersecurity-Week-13/playbooks/* .

This copies the playbook files to the correct place.

#Next, you must create a `[hosts](https://github.com/cruzedcruz/Cybersecurity-Week-13/blob/main/Linux/Host.PNG)` file to specify which VMs to run each playbook on.#

Run the commands below:

```bash
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.4
10.0.0.5
10.0.0.10

[elk]
10.1.0.4
EOF
```

After this, the commands below run the playbook:

 ```bash
 $ cd /etc/ansible
 $ ansible-playbook install_elk.yml elk
 $ ansible-playbook install_filebeat.yml webservers
 $ ansible-playbook install_metricbeat.yml webservers
 ```

To verify success, wait five minutes to give ELK time to start up. 

- Run the playbook, and navigate to the Kibana address run: `curl http://10.0.0.8:5601`. This is the address of Kibana. If the installation succeeded, this command should print HTML to the console. to check that the installation worked as expected.

**REMEMBER**

First 'cd /etc/ansible' to the play book
To install elk run 'ansible-playbook install_elk.yml' in the ansible file
To install filebeat run 'ansible-playbook install_filebeat.yml'
To install metricbeat run 'ansible-playbook install_metricbeat.yml'

