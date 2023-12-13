# Ansible Automation:-

  Ansible is an open-source IT automation engine that automates provisioning, configuration management, application deployment, orchestration, and many other IT processes.

# Advantages:
 1)  Free:
   Ansible is a opensource tool
 2)  Very simple to set up and use:
  No special coding skills are necessary to use Ansible’s playbooks (more on playbooks later).
 3) Powerful:
Ansible lets you model even highly complex IT workflows.
 4) Flexible:
You can orchestrate the entire application environment no matter where it’s deployed. You can also customize it based on your needs.
 5) Agentless & Efficient:
You don’t need to install any other software or firewall ports on the client systems you want to automate. You also don’t have to set up a separate management structure.

# How Ansible Works? :-
  Ansible works by connecting to your nodes and pushing out small programs, called "Ansible Modules"
to them. Ansible then executes these modules (over SSH by default) and removes them when finished.
Your library of modules can reside on any machine, and there are no servers, daemons, or databases
required.


![image](https://github.com/prashanthgrebel/Ansible-projects/assets/92351464/bb3d5cf9-be7a-49b7-8850-4547aee743c7)




# Environment Setup :-
Mainly, there are two types of machines when we talk about deployment −

* Control machine − Machine from where we can manage other machines.
* Remote machine − Machines that are handled/controlled by the control machine.

    # Control Machine Requirements
* Ansible can be run from any machine with Python 2 (versions 2.6 or 2.7) or Python 3 (versions 3.5 and
higher) installed.
* Note − Windows does not support a control machine.
* By default, Ansible uses SSH to manage a remote machine.
* Required ssh user credentials / passwordless login

# Ansible Modules for system Admin:- 
* ping module
* command module
* shell module
* user module
* copy module
* fetch module
* file module
* lineinfile module
* archive module
* unarchive module
* yum / apt / dnf module
* service module
* 
  # Ansible adhoc Commands:-
    Adhoc commands are one-off commands that are executed on the command line of an Ansible control node, without the need for a playbook or any additional configuration. These commands are used to quickly perform tasks on one or more managed nodes

# Create Ansible configuration:-
```
      1) ansible.cfg  ----> config file
      
            [defaults]
            inventory  = /home/prashanthg/Dev_Ops-Projects/Ansible_project/hosts

     2) hosts ------------>  Inventory file
            [all:vars]
            ansible_connection=ssh
            ansible_ssh_user=prashanthg
            ansible_ssh_pass=9246


            [prod_dev]
            192.168.1.115
            192.168.1.116

            [prod]
            192.168.1.115

            [dev]
            192.168.1.116
```

 * ping -
    ```# ansible -i hosts prod_dev  -m ping```

* create user -
      ```# ansible -i hosts prod_dev  -m user -a "user=testinguser1 password={{ '9246' | password_hash('sha512') }} shell=/bin/bash" -b -k```
* delete user -
      ```ansible -i hosts prod_dev  -m user -a "user=testinguser1 state=absent remove=yes" -b -k```
* shell -
   ```ansible -i hosts prod_dev  -m shell  -a "ls -lrth" -b -k  ```
* copy -
    ```ansible -i hosts prod_dev  -m copy  -a "src=./hosts dest=/tmp/ owner=prashanthg group=root mode=777" -b -k```
* fetch -
     ```ansible -i hosts prod_dev  -m fetch  -a "src=/etc/hosts dest=/tmp/{{ inventory_hostname }}-hosts " -b -k```

# ==> User Module:-

* User creation -
```
---

- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  vars:
    users:
      - ['test1','test2','test3']

  tasks: 
    - name: User creation
      user:
        name: "{{ item }}"
        update_password: always
        password: "{{ '9246' | password_hash('sha512') }}"
        shell: /bin/bash
      with_items: "{{ users }}"
```
