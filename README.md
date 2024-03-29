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
* debug module {register ,var, msg}
* user module
* copy module
* fetch module
* file module
* lineinfile module
* archive module
* unarchive module
* yum / apt / dnf module
* service module
* setup module 

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

  # Playbooks: -
  * playbook syntax check-
     ```ansible-playbook --syntax-check <yml file>```

# ==> Shell Module: - with Debug Module

```
---

- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  tasks:
    - name: checking uptime
      shell: uptime
      register: uptime_results

    - name: debug
      debug:
        var: uptime_results

    - name: debug
      debug:
        msg: Checking  uptime_results for "{{ inventory_hostname}} {{uptime_results.stdout_lines}}"

    - name: store uptime_results to file in ansible machine
      lineinfile:
        path: /tmp/uptime_results.csv
        line: "{{ inventory_hostname}},{{uptime_results.stdout}}"
        create: yes
      delegate_to: localhost
```

# ==> User Module:-
  * Playbook exec. command syntax- ``` ansible-playbook -i hosts user-creation.yml``` 

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

* Force users to change password on their first login -

```
  - name: force the user to change the password on the first login
    command: passwd -e {{ item }}
    with_items: "{{ users }}"
```
* Set user expire days -
```
  - name: set user expire days
    command: chage -M 30 {{ item }}
    with_items: "{{ users }}"
```
* Set uid and groups for user -
```
  - name: set gid uid for user
      user:
        name: test4
        password: "{{ '9246' | password_hash('sha512') }}"
        shell: /bin/bash
        uid: 1077
        group: docker


# add user to mmultiple groups
        groups: admins,developers
        append: yes
```

* Multi-user creation with additional settings -
```
---

- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  vars:
    users:
      - {name: 'testuser1', uid: 1080, groups: ['admin', 'docker']}
      - {name: 'testuser2', uid: 1078, groups: ['docker','prashanthg']}
      - {name: 'testuser', uid: 1079, groups: ['docker']}


  tasks: 
    - name: Multi-user creation with additional settings
      user:
        name: "{{ item.name }}"
        uid: "{{ item.uid }}"
        groups: "{{ item.groups }}"
        update_password: always
        password: "{{ '9246' | password_hash('sha512') }}"
        shell: /bin/bash
        append: yes
      with_items: "{{ users }}"

```
* User Deletion -
```
---

- name: user deletion
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  vars:
    users:
      - ['test1','test2','test3','test4','testuser1','testuser2','testuser']

  tasks: 
    - name: User deletion
      user:
        name: "{{ item }}"
        state: absent
        remove: yes
      with_items: "{{ users }}"
```
# ==> Copy Module
```
---

- name: copy
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  tasks:
    - name: copy files to dest. nodes
      copy: 
        src: /tmp/test.txt
        dest: /tmp/file.txt
        owner: prashanthg
        group: docker
        #Copy file with owner and permission
        mode: '0644'  # 1
        mode: u=rw,g=r,o=r # 2
        # adding some permissions and removing other
        mode: u+rw,g-wx,o-rwx
        # backup original file 
        backup: yes
```

* Multiple files copy : Method 1-
```
---

- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  vars:
    files:
      - {src: '/tmp/test.txt', dest: '/tmp/'}
      - {src: '/tmp/test1.txt', dest: '/home/prashanthg'}

  tasks:
    - name: 
      copy: 
        src: "{{ item.src }}"
        dest: "{{ item.dest }}"
        owner: prashanthg
        group: docker
        mode: '0644'
      with_items: "{{ files }}"
```
* Method- 2
```
---

- name: copy files
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  vars:
    source_files:
      - /tmp/test.txt
      - /tmp/test1.txt
      - /tmp/test2.txt
  tasks:
    - name: copy files
      copy: 
        src: "{{ item }}"
        dest: /home/prashanthg/
        owner: prashanthg
        group: root
        mode: '0644'
      with_items: "{{ source_files }}" 
```
# ==> Fetch Module : -
NOTE: ``` * src: is Remote machine path to fetch the files
          * dest: is Ansible Engine path to save files
      ```
```
---

- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg


  tasks:
    - name: fetching files from other servers.
      fetch:
        src: /etc/hosts
        dest: /tmp/{{ inventory_hostname }}-hosts.txt
        flat: yes
```
* Multi files fetch-
```
---

- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  vars:
    fetched_files:
      - {src: '/etc/hosts', dest: '/tmp/hosts/{{ inventory_hostname }}-hosts.txt'}
      - {src: '/home/prashanthg/file_archive.yml', dest: '/tmp/files/{{ inventory_hostname }}-file_archive.yml'}


  tasks:
    - name: fetching files from other servers.
      fetch:
        src: "{{ item.src }}"
        dest: "{{ item.dest }}"
        flat: yes
      with_items: "{{ fetched_files }}"
```
# ==> File Module: -

* Refer: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html

# ==> Lineinfile Module: -

```
---
  - name: Learning lineinfile module for insertafter
    hosts: prod_dev
    remote_user: prashanthg
    become: true

    vars:
      text: "\t\tserver_name 192.168.1.113;"

    tasks:
      - name: Replace a localhost entry with our own
        lineinfile:
          path: /etc/hosts
          regexp: '^127\.0\.0\.1'
          line: 127.0.0.1 localhost
          owner: root
          group: root
          mode: '0644'
          backup: yes 

      - name: Replace a localhost entry searching for a literal string to avoid escaping
       lineinfile:
          path: /etc/hosts
          search_string: '127.0.0.1'
          line: 127.0.0.1 localhost
          owner: root
          group: root
          mode: '0644'

      - name: lineinfile module
        lineinfile:
          path: /etc/nginx/conf.d/test.conf
          regexp: '^listen'
          insertafter: 'listen	801;'
          line: "{{ text }}"
          state: present

      - name: lineinfile insertbefore
        lineinfile:
          path: /etc/nginx/conf.d/test.conf
          regexp: '^listen'
          insertbefore: 'listen	801;'
          line: "\t#insertbrefore using lineinfile module"   
          state: present
```

# ==>  Archive Module: - 
* Refer: https://docs.ansible.com/ansible/latest/collections/community/general/archive_module.html
# ==>  Unarchive Module: - 
* Refer: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/unarchive_module.html

# ==> yum / apt / dnf module and service module



# Ansible facts and magic variables

```
# ansible localhost -m setup
```
```
IPv4 :{{ ansible_facts.default_ipv4.address }}
IPv6 : {{ ansible_facts.default_ipv6.address }}
TOATL MEMORY: {{ ansible_facts.memtotal_mb }}
FREE MEMORY: {{ ansible_facts.memfree_mb}}
MEMORY: {{ ansible_facts.memory_mb}}
KERNEL: {{ ansible_facts.kernel}}
KERNEL_VERSION: {{ ansible_facts.kernel_version }}
OS_FAMILY: {{ ansible_facts.os_family }}
DNS: {{ ansible_facts.dns.nameservers }}kk0
OS_DISTRIBUTION: {{ ansible_facts.distribution.description }}




{{ ansible_facts.hostname }} {{ ansible_facts.default_ipv4.address }} {{ ansible_facts.os_family }} {{ ansible_facts.distribution }} {{ ansible_facts.distribution_version }}
```

```
---
- name: user creation
  hosts: prod_dev
  become: True
  remote_user: prashanthg

  tasks:
    - name: template testing
      template:
        src: ./os-info_test.j2
        dest: /tmp/os-info_test.txt

    - name: fetching files from other servers.
      fetch:
        src: /tmp/os-info_test.txt
        dest: /tmp/{{ inventory_hostname }}-os-info.txt
        flat: yes
```
