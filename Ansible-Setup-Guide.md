Your polished version looks great! Here's a minor suggestion to improve the formatting and readability:

### Ansible Managed Nodes and Control Nodes Setup Guide

Table of Contents

1. Step-1 Installing-ansible-on-control-node
2. Step-2 Configuring-ansible-inventory-file
3. Step-3 Creating-a-user-on-control-node-and-managed-nodes
4. Step-4 Updating-sshd-config-file
5. Step-5 Enabling-password-authentication
6. Step-6 Configuring-sudo-privileges-for-ansible


- Step 1: Installing Ansible on Control Node


```
sudo -s
apt update -y
apt install software-properties-common
add-apt-repository --yes --update ppa:ansible/ansible
apt install ansible -y
```

- Step 2: Configuring Ansible Inventory File

1. Open the inventory file:  

```
sudo vi /etc/ansible/hosts`
```

2. Add IPs of the Managed Nodes in the "hosts" file.

- Step 3: Creating a User on Control Node and Managed Nodes

1. Become a root user: sudo su

2. Create a new user: adduser <User-Name> (e.g., adduser devops)

3. Set the password to be the same as the username.

4. Repeat the process on the Managed Nodes.

- Step 4: Updating SSHD Config File

1. Open the SSH config file: 
```
sudo vi /etc/ssh/sshd_config
```
2. Update the following settings:
    
 - Uncomment and set PermitRootLogin to yes (around line 42):

   - Before: #PermitRootLogin prohibit-password

     - After: PermitRootLogin yes

- Uncomment PublicKeyAuthentication to ensure it's set to yes (around line 47):

    - Before: #PubkeyAuthentication yes
   
    - After: PubkeyAuthentication yes (if it's not already set to yes)
    
- Uncomment and set PasswordAuthentication to yes (around line 66):

    - Before: #PasswordAuthentication no
    
    - After: PasswordAuthentication yes
      
3. Repeat the steps on Managed Nodes.

- Step 5: Enabling Password Authentication

1. Open the SSH config file:
2. 
```
sudo vi /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
```

2. Update the `PasswordAuthentication` directive to `yes`.

3. Restart the SSH service : `Very important step`
   
```
sudo systemctl restart sshd
```
OR
```
sudo service ssh restart
```

- Step 6: Configuring Sudo Privileges for Ansible

1. Open the sudoers file: `sudo visudo`

2. Add the following line: 
```
devops ALL=(ALL:ALL) NOPASSWD:ALL
```
3. This grants the devops user sudo privileges without requiring a password.

Security Implications

- Enabling password authentication can increase the risk of brute-force attacks and unauthorized access.
- Granting NOPASSWD sudo access can be a security risk if not properly managed.
- Ensure that the user or group being granted these privileges is properly secured and monitored.

By following these steps, you can set up Ansible on your control node and managed nodes, and configure the necessary settings for automation.

- Step 7:

1. Switch as "devops" user on Master and all nodes.

  `sudo - devops`

2. Generate keypair on Control node

   `ssh-keygen` is the command

3. Copy the Public key to the nodes

- switch to the folder where the keys are stored

  `cd /home/devops/.ssh/ `

  - Run the copy command:
 
    ssh-copy-id  devops@<Private Ip of Node-1>

    ssh-copy-id  devops@<Private Ip of Node-2>

    cd ..

-------------------------------------------------

Notes:

### Editing SSH Configuration to Enable Password Authentication

vi /etc/ssh/sshd_config.d/60-cloudimg-settings.conf

This command is used to edit the SSH server configuration file on a Linux system to enable password authentication, specifically on a cloud image.

Here's a step-by-step guide:

#### File Location
The configuration file is usually located at:

/etc/ssh/sshd_config.d/60-cloudimg-settings.conf

#### Editing the File
1. Open the file using a text editor (e.g., vi or nano):

bash
sudo vi /etc/ssh/sshd_config.d/60-cloudimg-settings.conf


2. Look for the PasswordAuthentication directive and change its value from no to yes:

    PasswordAuthentication yes



Here's a breakdown:
-. `/etc/ssh/sshd_config.d/60-cloudimg-settings.conf`: This is the path to the file being edited. This file is part of the SSH server configuration, and the .d directory suggests that it's a drop-in configuration file that's included by the main sshd_config file.

-. `PasswordAuthentication`: This is a directive in the SSH server configuration that controls whether password authentication is allowed.

When you change PasswordAuthentication from no to yes, you're enabling password authentication for SSH connections. This means that users will be able to log in to the server using their passwords, in addition to or instead of public key authentication.

#### Why is it used?

This change is often necessary in cloud environments where public key authentication is enabled by default for security reasons. However, in some cases, you might need to enable password authentication, such as:

- When you don't have a public key pair set up.
- When you need to allow users to log in without a public key.
- When you're troubleshooting SSH connectivity issues.

Security implications:

Keep in mind that enabling password authentication can increase the risk of brute-force attacks and unauthorized access. It's essential to use strong passwords and consider additional security measures, such as:

- Using a firewall to restrict SSH access to specific IP addresses.
- Implementing rate limiting or fail2ban to prevent brute-force attacks.
- Using a more secure authentication method, like public key authentication or two-factor authentication, whenever possible.

#### Applying Changes
After editing the configuration file, restart the SSH service to apply the changes:


bash
sudo systemctl restart sshd


or


bash
sudo service ssh restart


This depends on your Linux distribution and service manager.



#### To edit the sudoers file, use the following command:

Also called  Configuring Sudo Privileges for Ansible Or Granting Sudo Access to Ansible User or Enabling Passwordless Sudo for Ansible Automation


bash
sudo visudo



The command you're referring to is not exactly a command, but rather a line that's added to the sudoers file using visudo. The line is:

devops ALL=(ALL:ALL) NOPASSWD:ALL

Explanation:

This line grants the devops user sudo privileges with specific settings. Here's a breakdown:

- devops: The username or group name that this rule applies to.
- ALL=(ALL:ALL): This specifies the hosts, users, and groups that the devops user can run commands as.
    - ALL (first): The hosts on which this rule applies.
    - (ALL:ALL): The users and groups that the devops user can run commands as. ALL:ALL means the devops user can run commands as any user and any group.
- NOPASSWD: This specifies that the devops user does not need to enter a password when using sudo.
- :ALL: This specifies the commands that the devops user can run. ALL means the devops user can run any command.

Why run it:

This line is often added to the sudoers file to grant a user or group elevated privileges without requiring a password. This is useful in automation scenarios, such as with Ansible, where automated tasks need to perform actions that require root privileges.

Running it on Ansible control node and managed nodes:

**In the context of Ansible, this line would typically be added to the sudoers file on the managed nodes (not the control node). This is because the managed nodes are the ones that need to allow the Ansible automation to run commands with elevated privileges.**

By adding this line to the sudoers file on the managed nodes, you're allowing the devops user (or the user specified in the Ansible playbook) to run commands with sudo privileges without entering a password. This enables Ansible to automate tasks that require root access on the managed nodes.

Security implications:

Keep in mind that granting NOPASSWD sudo access can be a security risk if not properly managed. It's essential to ensure that the user or group being granted these privileges is properly secured and monitored.

#### Visudo Command

The visudo command is used to edit the sudoers file, which defines the rules for sudo access on a Linux system.

When you run visudo, it will open the sudoers file in a text editor. By default, visudo will use the vi editor, but it may also use another editor depending on your system's configuration.



    
  
