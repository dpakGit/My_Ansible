
```
---
- name: Configure Apache on webservers
  hosts: webservers
  become: yes
  tasks:
    - name: Update apt cache and upgrade packages
      ansible.builtin.apt:
        update_cache: yes
        upgrade: yes

    - name: Install Apache2
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Copy prem.txt to /opt
      ansible.builtin.copy:
        src: file.txt
        dest: /opt/file.txt
        mode: '0644'

    - name: Ensure Apache2 service is started and enabled
      ansible.builtin.service:
        name: apache2
        state: started
        enabled: yes
```
I made the following changes:

1. Added a more descriptive name for the play.
2. Added the `ansible.builtin.` prefix to the module names to ensure they are using the correct modules.
3. Changed the `apt` module to use the `update_cache` and `upgrade` parameters separately for clarity.
4. Added a `mode` parameter to the `copy` module to set the file permissions.
5. Modified the `service` module to ensure the Apache2 service is started and enabled.

Note: I've changed the `state` parameter of the `service` module to `started` instead of `restarted`. This is because the Apache2 service is not necessarily restarted after installation, but it's good practice to ensure it's started and enabled to run on boot.

Let's name this playbook `apache_setup.yml`.

To run this playbook, save it as `apache_setup.yml` and execute the following command:
```
bash
ansible-playbook apache_setup.yml
```
