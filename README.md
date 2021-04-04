## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below .

![](https://github.com/nejunaj/Elk-stack-Project/blob/main/Images/VMNet%20RG%20Network%20diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the filebeat.yml file may be used to install only certain pieces of it, such as Filebeat.


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting the traffic coming into the network.
The load balancer is used to minimze the volume of traffic directed at the internal servers by receiving and distributing incoming traffic to the servers that are capable of handling it, so that the network won't be congested. The advantage of our jumpbox is that it is secure as it operates under hardened security rules. And inturn it offers a localized option for  admins to manage the other security zones that are located in our related network groups.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to their Syslog files and system metrics.
The configuration details of each machine may be found below.

| Name                | Function     | IP Addresses                 | Operating System |
|---------------------|--------------|----------------------------|------------------|
| Jumpbox-Provisoner  | Gateway      | 40.113.216.31 / 10.0.0.1   | Linux            |
| Loadbalancer1       | Loadbalancer | 23.99.254.95               | Microsoft        |
| Web1                | Webserver    | 10.0.0.12                  | Linux            |
| Web2                | Webserver    | 10.0.0.13                  | Linux            |
| ELK-VM              | Logserver    | 65.52.200.196 / 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 
Only the Jumpbox and ELK-VM machines can accept connections from the Internet. Access to these machines is only allowed from the following IP address:
- 69.225.59.45 to the Jumpbox 
- 69.225.59.45 / 10.0.0.1  to the ELK machine

Machines within the network can only be accessed by the Jumpbox-provisioner, whose internal IP address is noted above.
The ELK VM can also be made to be accessed directly from your local machine through SSH, provided access is requested from the above whitelisted IP.

A summary of the access policies in place can be found in the table below.

| Name                | Publicly Accessible  | Allowed IP Addresses            |
|---------------------|----------------------|---------------------------------|
| Jumpbox-Provisioner | Yes                  | 69.225.59.45 with SSH           |
| Loadbalancer        | Yes                  | No restrictions                 |
| Web1                | No                   | 10.0.0.1 with SSH               |
| Web2                | No                   | 10.0.0.1 with SSH               |
| ELK-VM              | Yes                  | 69.225.59.45 / 10.0.0.1 with SSH|  

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible allows for automation of installation and deployment of multiple services. It allows a user to include a set of tasks listing services within a playbook that can be run to seamleslly install to a specified VM. It also allows for routine updates that can be carried out by simply running the playbook.

The playbook implements the following tasks:

```- ---
- name: Configure Elk VM with Docker
  hosts: Elk
  remote_user: azadmin
  become: true
  tasks:
  ```
    
- The above specifies what this playbook is for. 
- It also denotes which VM to deploy to, which is the Elk VM. 

```   # Use apt module
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

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present
```    

- This states what services are to be installed which are noted in the name field.
- It also notes what state the packages should be in once these tasks are completed.

```  # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144
     # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: " 262144 "
        state: present
        reload: yes
```
        
- These increase the memory of the VM that we will be using to run the ELK server.

``` # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044
  ```
  
- This will launch a docker container on our ELK server. 
- It also specifies which ports are to be exposed.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
[](https://github.com/nejunaj/Elk-stack-Project/blob/main/Images/Docker_ps_output.png)
