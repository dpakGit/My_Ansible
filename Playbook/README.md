```
---
- name: First Playbook            # Playbook name/description
  hosts: all                     # Target hosts (all hosts in inventory)
  become: yes                    # Run tasks with elevated (root) privileges
  tasks:                         # List of tasks to execute
    - name: Update all packages  # Task name
      ansible.builtin.apt:       # Module to manage packages (apt)
        name: "*"                # Update all packages
        state: latest            # Ensure packages are up-to-date

    - name: Install Apache-2     # Task name
      ansible.builtin.apt:       # Module to manage packages (apt)
        name: apache2            # Package name
        state: present           # Ensure package is installed

    - name: Install apache httpd # Task name (duplicate, will be skipped)
      ansible.builtin.apt:       # Module to manage packages (apt)
        name: apache2            # Package name
        state: present           # Ensure package is installed

    - name: Start Apache service # Task name
      ansible.builtin.service:   # Module to manage services
        name: apache2            # Service name
        state: restarted         # Restart the service
```

This playbook:

- Updates all packages on target hosts.
- Installs Apache2 ( duplicate task is ignored).
- Restarts the Apache2 service.

Note: The duplicate task (Install apache httpd) will be skipped by Ansible.

The name parameter in the apt module is case-sensitive for package names.

For example, apache2 and Apache2 would be treated as different packages.

Best practice: Use the exact package name as specified in the distribution's package repository.

Using become_user: root is useful when:

You need to run tasks as a specific user (e.g., root).
The default become user isn't root (e.g., using become_user: postgres for PostgreSQL tasks).

However, since become defaults to root, you can omit become_user: root in this case. It's implied.

Best practice: Use become: yes and specify become_user only when needed (e.g., postgres, www-data).


If you want to run the playbook as user deepak, use:
```
become: yes
become_user: deepak
```

This will:

Escalate privileges (become: yes)
Run tasks as user deepak (become_user: deepak)

Note: Ensure deepak has necessary permissions and is configured for sudo access (if required).


state: latest is a directive used with Ansible's package management modules (e.g., apt, yum, pip) to ensure a package is installed and up-to-date.

Use cases:

Install and update: If the package isn't installed, install it. If it's installed, update it to the latest version.
Idempotence: If the package is already at the latest version, no changes are made.

Examples:

Install/Update Apache:
```
- name: Install Apache
  ansible.builtin.apt:
    name: apache2
    state: latest


Update all packages:

- name: Update all packages
  ansible.builtin.apt:
    name: "*"
    state: latest
```

Note: state: latest doesn't downgrade packages. If a newer version is available, it upgrades; if not, it remains unchanged.

Common states:

- present: Ensure package is installed (no upgrade if already installed).
- latest: Ensure package is installed and up-to-date.
- absent: Ensure package is removed.
- started/stopped/restarted: Service states (not applicable to package management).


The - name: directive is just a descriptive name for the task and is not case-sensitive. It's used for:

- Readability
- Logging
- Output display


The name parameter in the apt module is case-sensitive for package names.

For example, apache2 and Apache2 would be treated as different packages.

Best practice: Use the exact package name as specified in the distribution's package repository.


Q) What is the "-" in front of name attribute explain - name: Install Apache on Ubuntu

The `-` (hyphen) in YAML syntax indicates a list item or an element in an array.

In Ansible playbooks, tasks are defined as a list of elements, and each task starts with a `-` (hyphen).

```
- name: Install Apache on Ubuntu
  ansible.builtin.apt:
    name: apache2
    state: present
```

Here, `- name: Install Apache on Ubuntu` is a single task in the list of tasks.

Think of it like this:
- `-` indicates "this is a new task"
- `name: Install Apache on Ubuntu` is the task name (a key-value pair)

It's a YAML syntax quirk, but essential for Ansible playbooks!

Q)Then what is the hypen in the following 
```
- name: First Playbook    
  hosts: all                     
  become: yes                    
  tasks:                         
```
The `-` (hyphen) in the following snippet:

```
- name: First Playbook
  hosts: all
  become: yes
  tasks:
    ...
```
is also indicating a list item, but at a different level.

In this case, it's indicating that this is a *play* (not a task). A playbook can contain multiple plays, and each play starts with a `-` (hyphen).

Think of it as:

- `-` indicates "this is a new play"
- `name: First Playbook` is a key-value pair describing the play

So, the structure is:
```
- Playbook (contains plays)
    - Play 1 (`- name: First Playbook`)
        - Tasks (list of tasks within the play)
            - Task 1 (`- name: Install Apache`)
            - Task 2, etc.
```
In short, the `-` is used to indicate a new item in a list, whether it's a play or a task!

Q)So is it that a playbook contains Plays and plays contain tasks and a playbook can contain multiple plays ?

Spot on!

A Playbook:

- Contains one or more Plays

- Defines the overall workflow

A Play:

- Contains one or more Tasks
- Targets a specific set of hosts (defined by `hosts`)
- Can have its own variables, handlers, and other settings

A Task:

- Is a single unit of work (e.g., install a package, start a service)

- Is executed on the target hosts

So, the hierarchy is:

#### Playbook → Play(s) → Task(s)

And yes, a Playbook can contain multiple Plays, each with its own set of Tasks!

Q)Give me a simple example to illustrate the above

Here's a simple example:

```
- name: Play 1 - Setup Web Server
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      ansible.builtin.apt:
        name: apache2
        state: present
    - name: Start Apache service
      ansible.builtin.service:
        name: apache2
        state: started

- name: Play 2 - Setup Database
  hosts: dbservers
  become: yes
  tasks:
    - name: Install MySQL
      ansible.builtin.apt:
        name: mysql-server
        state: present
    - name: Start MySQL service
      ansible.builtin.service:
        name: mysql
        state: started
```

In this example:

- The Playbook contains 2 Plays:
    1. `Play 1 - Setup Web Server` (targets `webservers` hosts)

    2. `Play 2 - Setup Database` (targets `dbservers` hosts)

- Each Play contains 2 Tasks:

    1. Install and start Apache (Play 1)

    2. Install and start MySQL (Play 2)

This illustrates the hierarchy: Playbook → Play(s) → Task(s)!

Can we distribute the following play into two plays - 
```
- name: Play 1 - Setup Web Server
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      ansible.builtin.apt:
        name: apache2
        state: present
    - name: Start Apache service
      ansible.builtin.service:
        name: apache2
        state: started
```
You can distribute the play into two plays like this:

```
- name: Play 1 - Install Apache
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      ansible.builtin.apt:
        name: apache2
        state: present

- name: Play 2 - Start Apache service
  hosts: webservers
  become: yes
  tasks:
    - name: Start Apache service
      ansible.builtin.service:
        name: apache2
        state: started
```

However, it's worth noting that this might not be the best approach, as it increases the number of plays and can make the playbook more complex.

A better approach might be to keep the original play as it is, since both tasks are closely related and target the same hosts. This way, you can ensure that Apache is installed before trying to start the service.

Alternatively, you could use handlers to start the Apache service only after it's installed:

```
- name: Play 1 - Setup Web Server
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      ansible.builtin.apt:
        name: apache2
        state: present
      notify: Start Apache service

  handlers:
    - name: Start Apache service
      ansible.builtin.service:
        name: apache2
        state: started
```

This way, you can keep the tasks separate, but still ensure they're executed in the correct order.
