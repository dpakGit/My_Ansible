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


### Ansible Playbook Output or Ansible Play Recap.

The logs that you see after running an Ansible playbook are called Ansible Playbook Output or Ansible Play Recap.

The output typically includes:

- PLAY: The name of the play.
- TASK: The name of each task in the play.
- RESULT: The result of each task (e.g., "ok", "changed", "failed", "skipped").
- PLAY RECAP: A summary of the play, including the number of hosts that were targeted, the number of tasks that were                        successful, and any errors that occurred.

Here's an example of what the output might look like:

```
PLAY [Deploy Apache Web Server with Custom Index Page using Ansible] ****************************************************************

TASK [Install Apache] *********************************************************
changed: [172.31.73.6]

TASK [Start Service httpd] ******************************************************
ok: [172.31.73.6]

TASK [Get index.html] ***********************************************************
changed: [172.31.73.6]

PLAY RECAP **********************************************************************
172.31.73.6               : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

The PLAY RECAP section provides a concise summary of the play, making it easy to quickly see the outcome of the playbook run.
10:55 pm
```

[27/11, 11:55 am] Deepak Ranjan Mishra: Following is the yaml file ---
- name: First Playbook
  hosts: all
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
    - name: Copy Task
      copy:
        src: 1.yaml
        dest: /opt
    - name: Restart Apache
      service:
        name: apache2
        state: restarted
and following is the logs  ansible-playbook 1.yaml 

PLAY [First Playbook] ********************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************
[WARNING]: Host '172.31.66.196' is using the discovered Python interpreter at '/usr/bin/python3.12', but future installation of another Python interpreter could cause a different interpreter to be discovered. See https://docs.ansible.com/ansible-core/2.19/reference_appendices/interpreter_discovery.html for more information.
ok: [172.31.66.196]

TASK [Install Apache] ********************************************************************************************************************
changed: [172.31.66.196]

TASK [Copy Task] *************************************************************************************************************************
changed: [172.31.66.196]

TASK [Restart Apache] ********************************************************************************************************************
changed: [172.31.66.196]

PLAY RECAP *******************************************************************************************************************************
172.31.66.196              : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

devops@ip-172-31-66-180:~/playbook$ , explain me what is the meaning of the following attributes in the logs  - " ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0"
[27/11, 11:55 am] Meta AI: The attributes in the logs are part of the Ansible playbook output, specifically the PLAY RECAP section. Here's what each attribute means:

- *`ok=4`*: The number of tasks that were executed successfully and didn't make any changes to the system. In this case, 4 tasks were executed successfully, including the "Gathering Facts" task.
- *`changed=3`*: The number of tasks that made changes to the system. In this case, 3 tasks made changes:
    - Install Apache (installed Apache2 package)
    - Copy Task (copied 1.yaml file to /opt)
    - Restart Apache (restarted Apache2 service)
- *`unreachable=0`*: The number of hosts that were unreachable during the playbook execution. In this case, 0 hosts were unreachable, meaning Ansible was able to connect to all hosts.
- *`failed=0`*: The number of tasks that failed during execution. In this case, 0 tasks failed, meaning all tasks were executed successfully.
- *`skipped=0`*: The number of tasks that were skipped due to conditional statements (e.g., `when` clause). In this case, 0 tasks were skipped.
- *`rescued=0`*: The number of tasks that were rescued by an `ignore_errors` or `rescue` block. In this case, 0 tasks were rescued.
- *`ignored=0`*: The number of tasks that were ignored due to an `ignore_errors` directive. In this case, 0 tasks were ignored.

These attributes provide a summary of the playbook execution, helping you quickly understand the outcome of the playbook run.
[27/11, 11:56 am] Deepak Ranjan Mishra: how 4 tasks ok=4: as in the playbook we have three tasks
[27/11, 11:56 am] Meta AI: The `ok=4` count includes the implicit "Gathering Facts" task, which is executed automatically by Ansible at the beginning of every playbook run.

So, the tasks are:

1. Gathering Facts ( implicit task, not written in the playbook)
2. Install Apache
3. Copy Task
4. Restart Apache

That's why `ok=4`, even though you only wrote 3 tasks in the playbook.
[27/11, 11:57 am] Deepak Ranjan Mishra: Explain what is Gathering Facts
[27/11, 11:57 am] Meta AI: Gathering Facts is an implicit task that Ansible performs at the beginning of every playbook run. Its purpose is to collect information about the target hosts, such as:

- Operating System (OS) version
- IP addresses
- Hostname
- Network interfaces
- Disk space
- Memory (RAM)
- CPU architecture
- and many more...

This information is stored in Ansible's memory as "facts" and can be used throughout the playbook run to make decisions, such as:

- Conditional statements (`when` clause)
- Variable substitutions (`{{ ansible_os_family }}`)
- Task execution (e.g., installing packages specific to the OS)

Gathering Facts is usually the first task executed by Ansible, and it's done automatically, so you don't need to write any code for it.

Think of it as Ansible saying, "Hey, let me learn a bit about this host before I start doing stuff on it!"
[27/11, 11:58 am] Deepak Ranjan Mishra: so if the playbook has "n" number of tasks then Ok will be equal to n+1 for any playbook
[27/11, 11:58 am] Meta AI: Not exactly. The `ok` count will be equal to `n+1` only if:

1. The "Gathering Facts" task is successful (which is usually the case).
2. All tasks in the playbook are successful (i.e., no failures or skips).

If any task fails, the playbook will stop executing, and the `ok` count will be less than `n+1`.

If a task is skipped (e.g., due to a `when` clause), it won't contribute to the `ok` count.

Here's a more accurate breakdown:

- `ok` count includes:
    - Successful "Gathering Facts" task (1)
    - Successful tasks in the playbook (up to `n`)
- `ok` count excludes:
    - Failed tasks
    - Skipped tasks

So, the `ok` count can be equal to, less than, or more than `n+1`, depending on the playbook's execution outcome.
[27/11, 12:17 pm] Deepak Ranjan Mishra: why now changed=1 ansible-playbook 1.yaml

PLAY [First Playbook] **********************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************
[WARNING]: Host '172.31.66.196' is using the discovered Python interpreter at '/usr/bin/python3.12', but future installation of another Python interpreter could cause a different interpreter to be discovered. See https://docs.ansible.com/ansible-core/2.19/reference_appendices/interpreter_discovery.html for more information.
ok: [172.31.66.196]

TASK [Install Apache] **********************************************************************************************************************
ok: [172.31.66.196]

TASK [Copy Task] ***************************************************************************************************************************
ok: [172.31.66.196]

TASK [Restart Apache] **********************************************************************************************************************
changed: [172.31.66.196]

PLAY RECAP *********************************************************************************************************************************
172.31.66.196              : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

devops@ip-172-31-66-180:~/playbook$
[27/11, 12:17 pm] Meta AI: The `changed=1` count indicates that only one task made changes to the system, which is the "Restart Apache" task.

Here's what's happening:

- `Install Apache` task: Apache is already installed, so this task doesn't make any changes (`ok` state).
- `Copy Task` task: The file `1.yaml` is already copied to `/opt`, so this task doesn't make any changes (`ok` state).
- `Restart Apache` task: This task restarts the Apache service, which changes the system state, so it's reported as `changed`.

The `changed=1` count reflects that only one task (Restart Apache) made changes to the system.

The other tasks (`Install Apache` and `Copy Task`) didn't make any changes, so they're counted as `ok` (no changes needed).
