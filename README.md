


**Ansible, Salt Stack, Chef, Puppet**

Ansible, Salt Stack, Chef, and Puppet are **IT automation frameworks**.

These frameworks automate many traditional system administrators' processes. Each framework uses a series of configuration data files -- usually YAML or XML -- that are evaluated by an executable. 

The configuration data files specify a sequence of actions to take to configure a system. The actions are then run by the executable. The executable differs in language between the systems -- Ansible and Salt Stack are Python based and Chef is Ruby.  This workflow is similar to running ad-hoc shell scripts but offers a more structured and refined experience through the respective platforms ecosystems. These tools are what will bring enable the automation needed to achieve CI/CD.


Ansible is an open-source IT automation engine that simplifies and streamlines various IT tasks, including provisioning, configuration management, application deployment, and orchestration.

It is known for its simplicity, **agentless architecture, and use of human-readable YAML for defining automation tasks**.

### Key Features and Concepts:

**Agentless Architecture:** Unlike some other automation tools, Ansible does not require agents to be installed on managed nodes. It connects to remote machines via standard protocols like SSH for Linux/Unix and WinRM for Windows, making it easy to deploy and manage.

**YAML Playbooks:** Automation tasks in Ansible are defined in human-readable YAML files called playbooks. These playbooks describe the desired state of systems and the actions to be performed, such as installing software, configuring services, or deploying applications.

**Modules:** Ansible provides a vast library of modules, which are small programs that execute specific tasks on managed nodes. These modules abstract away the complexities of interacting with different systems and applications, offering a consistent interface for automation.

**Inventories:** Ansible uses inventories to define the hosts or groups of hosts that it will manage. These can be static files or dynamically generated, allowing for flexible management of infrastructure.

**Idempotence:** Ansible operations are designed to be idempotent, meaning that applying a playbook multiple times will result in the same system state without causing unintended side effects.

**Orchestration:** Ansible can orchestrate complex workflows involving multiple systems and services, enabling the automation of multi-tier application deployments and other intricate IT processes.

### How Ansible Works:

**Control Node:** Ansible runs from a central control node, which can be any machine.This is the machine where Ansible is installed and from where you execute Ansible commands and playbooks.

**Managed Nodes (Hosts):** These are the remote systems (servers, network devices, etc.) that Ansible manages and automates.

**Inventory:** An inventory file, typically in INI or YAML format, is a centralized configuration file that lists and organizes your managed nodes. It enables you to group hosts for easier management and define variables specific to certain hosts or groups.
The control node uses the inventory file to identify the target hosts (such as servers, network devices, or other machines) for automation, allowing Ansible to manage and orchestrate their configurations and workflows.

**Plays:** An ordered list of tasks that are executed on a specific group of hosts defined in the inventory.

**Playbooks:** Playbooks are the core of Ansible automation, written in YAML to describe the desired state of your infrastructure in a human-readable, declarative way.
These files contain one or more plays that outline the automation tasks to be executed on managed nodes, providing a clear and concise roadmap for Ansible to follow.

**Modules:** Modules are small, reusable units of code that Ansible executes on managed nodes to perform specific tasks, such as installing software, managing services, creating users, or configuring files. By leveraging these modules, Ansible can perform a wide range of actions on target hosts, including configuration management, package installation, and service management. When a playbook is executed, Ansible connects to the target hosts via SSH or WinRM and pushes the necessary modules to carry out the defined tasks.

**Execution:** The modules are executed on the managed nodes, and the results are reported back to the control node.

**Roles: A way to organize playbooks and related files (templates, variables, handlers) into a reusable and shareable structure.**

**Task:**

A task in Ansible is a single unit of work that is executed on a managed node. It is a call to an Ansible module with specific arguments that define the desired state or action to be performed.

Key aspects:

1. Module invocation: A task invokes an Ansible module, which is a pre-written piece of code that performs a specific function.

2. Specific arguments: The task provides specific arguments to the module, which define the desired state or action to be performed.

3. Single unit of work: A task represents a single unit of work that is executed on a managed node.

Example:

Here's an example of a task in an Ansible playbook:
```
- name: Install Apache
  apt:
    name: apache2
    state: present
```
In this example:

- The task is named "Install Apache".

- The task invokes the apt module.

- The name argument specifies the package to install (apache2).

- The state argument specifies the desired state (present).

Importance:

Tasks are the building blocks of Ansible playbooks, and they provide a way to define the desired state of your infrastructure. By combining multiple tasks, you can create complex workflows and automate a wide range of IT processes.

   - How Ansible Works:
- You write playbooks in YAML, defining the desired state of your systems.

- From the control node, you execute these playbooks using the ansible-playbook command.

- Ansible connects to the managed nodes via SSH (or WinRM).

- It pushes the necessary modules to the managed nodes and executes them to achieve the state defined in the playbook.

- Ansible is idempotent, meaning it will only make changes if the system is not already in the desired state, preventing unnecessary modifications.

**In summary:**

A task in Ansible is a single unit of work that invokes a module with specific arguments to perform a specific action or achieve a desired state on a managed node. Tasks are the fundamental components of Ansible playbooks and are used to define the automation workflow.

### Benefits of using Ansible:

**Simplicity and Ease of Use:** The human-readable YAML syntax and agentless architecture make Ansible relatively easy to learn and use, even for those without extensive programming experience.

**Increased Efficiency:** Automating repetitive IT tasks significantly reduces manual effort and improves operational efficiency.

**Consistency and Reliability:** Ansible ensures consistent configurations across systems, reducing human error and improving system reliability.

**Scalability:** Ansible can manage a large number of systems, making it suitable for organizations of all sizes.

**Security:** By leveraging standard and secure protocols like SSH, Ansible offers a secure approach to IT automation.


**Advantages of Ansible:**

Agentless: No need to install and maintain agents on managed nodes.

Simple and Human-Readable: Playbooks are written in YAML, making them easy to understand and write.

Powerful: Can automate a wide range of IT tasks, from simple configurations to complex multi-tier application deployments.

Extensible: A vast collection of modules and the ability to create custom modules.

Idempotent: Ensures consistent and predictable results.


### Ansible Tracking Changes:

**Ansible is a configuration management tool that helps track changes to your infrastructure over time. Here's how:**

Key Features:

1. Playbooks: Ansible uses YAML playbooks to define the desired state of your infrastructure. These playbooks are version-controlled, allowing you to track changes.
2. Idempotence: Ansible playbooks are idempotent, meaning you can run them multiple times without changing the outcome. This ensures consistency and allows you to track changes.
3. Facts: Ansible collects facts about your infrastructure, providing a snapshot of the current state. This helps you understand what's changed.

How Ansible Tracks Changes:

1. Version Control Systems (VCS): Ansible playbooks are typically stored in a VCS like Git. This allows you to track changes, collaborate, and roll back to previous versions.
2. Playbook Runs: When you run an Ansible playbook, it checks the current state of your infrastructure and makes changes only if necessary. This ensures that the infrastructure is brought to the desired state.
3. Change Management: Ansible provides logging and reporting features that help you track changes made to your infrastructure.

Benefits:

1. Auditing: Ansible provides a clear audit trail of changes made to your infrastructure.
2. Versioning: You can track changes and roll back to previous versions if something goes wrong.
3. Consistency: Ansible ensures consistency across your infrastructure, reducing errors and downtime.

Example:

Suppose you have a playbook that installs and configures a web server. You can track changes to this playbook in Git. If someone makes a change to the playbook, you can see the changes and roll back if necessary.

By using Ansible to track changes, you can ensure that your infrastructure is consistent, reliable, and easy to manage.

#### Q: "When you run an Ansible playbook, it checks the current state of your infrastructure and makes changes only if necessary. This ensures that the infrastructure is brought to the desired state." Can you describe it with a scenario based example ?

- Let's consider a scenario to illustrate how Ansible works.

Scenario:

You have a web server (let's call it webserver1) that needs to have the following configuration:

- Apache installed
- Apache service running
- A specific configuration file (/etc/apache2/apache2.conf) with a custom setting

You want to ensure that this configuration is consistent across all your web servers.

Ansible Playbook:

You create an Ansible playbook (webserver.yml) that defines the desired state of your web server:
```
---
- name: Configure web server
  hosts: webserver1
  become: yes

  tasks:
  - name: Install Apache
    apt:
      name: apache2
      state: present

  - name: Start Apache service
    service:
      name: apache2
      state: started
      enabled: yes

  - name: Configure Apache
    template:
      src: templates/apache2.conf.j2
      dest: /etc/apache2/apache2.conf
      mode: '0644'
    notify: restart apache
```
Initial State:

Let's say the current state of webserver1 is:

- Apache is not installed

- Apache service is not running

- The /etc/apache2/apache2.conf file does not exist

Running the Playbook:

You run the Ansible playbook (ansible-playbook webserver.yml). Ansible checks the current state of webserver1 and compares it to the desired state defined in the playbook.

Ansible's Actions:

1. Ansible sees that Apache is not installed, so it installs it.

2. Ansible sees that the Apache service is not running, so it starts it and enables it to start automatically on boot.

3. Ansible sees that the /etc/apache2/apache2.conf file does not exist, so it creates it with the custom setting.

Result:

After running the playbook, webserver1 is now in the desired state:

- Apache is installed

- Apache service is running

- The /etc/apache2/apache2.conf file exists with the custom setting

Idempotence:

If you run the playbook again, Ansible will check the current state of webserver1 and see that it is already in the desired state. Ansible will not make any changes, because the current state matches the desired state.

This is an example of Ansible's idempotence, which ensures that your infrastructure is brought to the desired state and remains consistent over time.

### Q: Is there an other way by which ansible can check the current state of your infrastructure

- Yes, Ansible can check the current state of your infrastructure in several ways:

1. Gathering Facts: Ansible can gather facts about your infrastructure using the setup module. This module collects information about the system, such as:
    
    - Operating system and version
    
    - Hardware information (CPU, memory, etc.)
    
    - Network configuration
    
    - Package manager information

2. Using Modules: Ansible has a vast collection of modules that can be used to gather information about specific components of your infrastructure, such as:

    - apt module for package management on Debian-based systems

    - yum module for package management on RHEL-based systems

    - service module for managing services

4. Custom Facts: You can also create custom facts using Ansible's setup module. This allows you to gather information specific to your environment.

5. External Data Sources: Ansible can also gather information from external data sources, such as:

    - Cloud providers (e.g., AWS, Azure, GCP)
    
    - Monitoring tools (e.g., Prometheus, Nagios)
    
    - CMDBs (Configuration Management Databases)

By gathering this information, Ansible can determine the current state of your infrastructure and make changes as needed to bring it to the desired state.

Some examples of Ansible modules that can be used to gather information include:

- ansible_facts: a built-in variable that contains information about the system

- setup module: gathers facts about the system

- stat module: gathers information about files and directories

- package module: gathers information about packages

These are just a few examples of how Ansible can check the current state of your infrastructure. The specific method used will depend on your use case and the requirements of your environment.


### Q: Explain the following two files elaborately - "ansible.cfg" & "hosts".

- Let's dive into the two essential files in Ansible: ansible.cfg and hosts.

1. ansible.cfg

The ansible.cfg file is the main configuration file for Ansible. It contains settings that control the behavior of Ansible, such as:

- Inventory: Specifies the location of the inventory file (e.g., hosts file).
- Remote user: Specifies the default user to use when connecting to remote hosts.
- Privilege escalation: Specifies how to escalate privileges when running tasks.
- SSH settings: Specifies SSH connection settings, such as the private key file or SSH port.

The ansible.cfg file is usually located in the current working directory or in the ~/.ansible directory.

Example ansible.cfg file:
```
[defaults]
inventory = hosts
remote_user = ubuntu
become = yes
become_method = sudo

[ssh_connection]
ssh_args = -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no
```
In this example, we define:

- inventory: The location of the inventory file is set to hosts.
- remote_user: The default user to use when connecting to remote hosts is set to ubuntu.
- become: Privilege escalation is enabled.
- become_method: The method to use for privilege escalation is set to sudo.
- ssh_args: SSH connection settings are defined to disable strict host key checking.

2. hosts

The hosts file, also known as the inventory file, is used to define the hosts that Ansible will manage. It's a simple text file that contains a list of hostnames or IP addresses, one per line.

The hosts file can also be used to define groups of hosts, which can be targeted by Ansible playbooks.

Example hosts file:
```
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
db2.example.com

[all:vars]
ansible_user = ubuntu
ansible_ssh_private_key_file = ~/.ssh/my_private_key
```
In this example, we define:

- Two groups: webservers and dbservers, each containing two hosts.
- A variable ansible_user is defined for all hosts, specifying the user to use when connecting.
- A variable ansible_ssh_private_key_file is defined for all hosts, specifying the private key file to use for SSH connections.

Using the hosts file

When running an Ansible playbook, you can specify the hosts file using the -i option:

bash
ansible-playbook -i hosts playbook.yml

This tells Ansible to use the hosts file as the inventory file for the playbook.

In summary, the ansible.cfg file controls the behavior of Ansible, while the hosts file defines the hosts that Ansible will manage. Both files are essential for Ansible to work correctly.


### Customizing the Inventory File

Yes, you can definitely have a customized inventory file with a different name and location. To do this, you would specify the full path to the inventory file in the ansible.cfg file.

Example:

Let's say you have an inventory file named my_inventory.ini located in a directory /home/user/my_ansible_project. You can specify the path to this file in the ansible.cfg file like this:
```
[defaults]
inventory = /home/user/my_ansible_project/my_inventory.ini
```
Benefits:

1. Flexibility: You can keep your inventory file anywhere you like, with any name you prefer.

2. Organization: You can organize your inventory files by project or environment.

3. Reusability: You can reuse the same inventory file across multiple Ansible projects.

How Ansible finds the inventory file:

When you run an Ansible command, it will look for the inventory file in the location specified in the ansible.cfg file. If you don't specify a path, Ansible will look for a file named hosts in the current working directory.

Specifying the inventory file at runtime:

You can also specify the inventory file at runtime using the -i option:

```
ansible-playbook -i /home/user/my_ansible_project/my_inventory.ini playbook.yml
```
This allows you to override the inventory file specified in the ansible.cfg file.

Best practices:

1. Keep your inventory file organized: Use a consistent naming convention and directory structure for your inventory files.

2. Use a version control system: Store your inventory files in a version control system like Git to track changes and collaborate with others.

By customizing the inventory file location and name, you can better manage your Ansible projects and inventory.

### .ini File Format

.ini is a file extension for a configuration file format that is commonly used for storing settings and preferences. INI stands for "Initialization" file.

Characteristics:

1. Simple text format: INI files are plain text files that can be edited with any text editor.

2. Section-based: INI files are divided into sections, each with its own set of key-value pairs.

3. Key-value pairs: Each line in an INI file consists of a key (or property) followed by an equals sign (=) and a value.

Example:

Here's an example of an INI file:
```
[database]
username = myuser
password = mypassword
host = localhost
port = 5432

[application]
debug = true
log_level = info
```
In this example, we have two sections: database and application. Each section has its own set of key-value pairs.

Common uses:

1. Configuration files: INI files are often used to store configuration settings for applications, such as database connections or user preferences.

2. Settings files: INI files can be used to store settings that need to be persisted between application runs.

INI in Ansible:

In Ansible, INI files are used for inventory files (like the hosts file) and sometimes for configuration files like ansible.cfg. However, Ansible also supports YAML and JSON formats for configuration and inventory files.

Advantages:

1. Easy to read and write: INI files are simple and human-readable.

2. Platform-independent: INI files can be used on multiple platforms, including Windows, macOS, and Linux.

Overall, INI files are a simple and widely-supported format for storing configuration settings and preferences.


### Best Practices for Inventory File Location

The best practice for inventory file location depends on your specific use case and requirements. Consider the following options:

Option 1: /etc/ansible/hosts

- Pros:
    - Centralized location for system-wide inventory
   
    - Easy to manage for small-scale environments

- Cons:
   
    - May not be suitable for large-scale environments or multiple projects
    
    - Requires root access to modify the file

Option 2: Custom location (e.g., project directory)

- Pros:
   
    - Allows for project-specific inventory management
    
    - Easier to manage access control and versioning
    
    
    - Suitable for large-scale environments or multiple projects

- Cons:
    
    - Requires specifying the inventory file location in ansible.cfg or using the -i option

Recommendation

For most cases, it's recommended to keep the inventory file outside of the /etc/ansible directory, in a custom location that makes sense for your project or organization. This approach offers:

1. Flexibility: Easier to manage multiple projects or environments

2. Version control: Inventory files can be version-controlled along with the project code

3. Access control: Access to the inventory file can be controlled at the project level

Example Directory Structure

```
my_project/
├── ansible.cfg
├── inventory/
│   ├── hosts
│   └── group_vars/
├── playbooks/
│   └── my_playbook.yml
└── roles/
    └── my_role/
```
In this example, the inventory file is located in the inventory directory within the project directory. The ansible.cfg file is also located in the project root, specifying the inventory file location.

Best Practices

1. Keep inventory files organized: Use a consistent directory structure and naming convention for inventory files.

2. Use version control: Store inventory files in a version control system like Git to track changes and collaborate with others.

3. Specify inventory file location: Use the ansible.cfg file or the -i option to specify the inventory file location.

By following these best practices, you can effectively manage your Ansible inventory files and improve the scalability and maintainability of your Ansible projects.

Changes made:

- Minor formatting adjustments for better readability

- Added bold headings to separate sections

- Used shorter sentences and concise language

- Emphasized key points using bold text

The revised text maintains the same content and structure as the original, but with some minor improvements to make it more readable and engaging.
