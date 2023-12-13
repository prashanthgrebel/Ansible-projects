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
* By default, Ansible uses ssh to manage a remote machine.
* Required ssh user credentials / passwordless login

# Ansible Modules for system Admin:- 
