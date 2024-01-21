# Born2beRoot
This is my full born2beroot guide of the 42 school. I hope you find this material useful. I made sure to get into deep details in this guide, including the bonus part of the project as well.

# Table of Contents
1. [Virtual machine 💿](#1--Virtual-machine-)
  
  	1.1 [Virtualization and the Hypervisor(VMM)](#11-Virtualization-and-the-Hypervisor(VMM))

  	1.2 [Vdi files](#12-Vdi-files)
	
	1.3 [Debian linux](#12-Debian-linux)
	
	1.4 [Rocky linux](#13-Rocky-linux)
    
2. [Linux security module (LSM) 🛠](#2--Linux-security-module-(LSM)-)
3. [Aptitude, apt](#3--Aptitude,-apt-)
4. [LVM (Logical Volume Manager) ⚙️](#4--LVM-logical-volume-manager-%EF%B8%8F)

	4.1 [Physical Volume](#41---Physical-Volume-)
	
	4.2 [Volume group](#42---Volume-group-)
	
	4.3 [Logical volume](#43-Logical-volume-)
	
5. [UFW service Firewall 🚨](#5--UFW-service-Firewall-)
	
6. [SSH Protocol](#6--SSH-Protocol-)
7. [Setup your machine 📝](#7--Setup-your-machine-)
8. [Configuration ✅](#9--Configuration-)

	8.1 [Getting system updates and configuring the sudo command](#91-evaluation-answers-)

	8.2 [SSH configuration](#9-2-evaluation-commands-%EF%B8%8F)
   
   	8.3 [Installing and Configuring UFW](#91-evaluation-answers-)
   
   	8.4 [Connecting to SSH remotely](#91-evaluation-answers-)
   
   	8.5 [Setting password policy](#91-evaluation-answers-)
   
   	8.6 [Creating groups, users, assigning](#91-evaluation-answers-)
   
  	8.7 [Configuring Sudoers file, logs, warning message](#91-evaluation-answers-)
   
9. [Script](#8--bonus-%EF%B8%8F)"

10. [Crontab ⏰](#6--crontab-)

11. [Bonus ⭐](#8--bonus-%EF%B8%8F)

	11.1 [WordPress](#81--manual-partition)

   	11.2 [PHP](#81--manual-partition)
	
	11.3 [Lighttpd webserver 🌐](#82---wordpress--services-configuration-)
    
	11.4 [MariaDB Database](#81--manual-partition)

	11.5 [Aditional service (Fail2ban) ➕](#83---aditional-service-)

## 1- Virtual machine 💿
In order to run a separate secondary OS on your machine, you’ll need a Virtual Machine. It's like a computer program that acts like a separate computer, so a virtual computer inside a real computer. It runs inside your physical computer but is isolated from it. It has its own "virtual" hardware (lCPU, memory, storage…) that is simulated by software (hypervisors).
### 1.1 - Virtualization and the Hypervisor(VMM)
Virtualization is the process of creating a virtual machine on a physical machine with the help of hypervisors which are a lightweight software layer. The hypervisor acts like an intermediate or also referred to as virtualization layer between the physical hardware of the host the machine and the virtual machines and the applications running on them. Think of the hardware as a restaurant which have appliances (stove, oven…), the chef is the hypervisor taking orders (processes) from the customers (OS and apps) of each table(VM) in the restaurant. 

The **virtual machine manager** or **monitor** is a software that enables the creation and management of virtual machines. It manages the backend operation of these VMs by allocating the necessary computing, memory, storage and input/output resources. It also offers an interface that manages the status and availability of VMs that are installed over a single host or interconnected hosts. (VirtualBox is a type-2 hypervisor)

Hypervisors are of two types, the **first one** being the **bare-metal** hypervisors, they don’t need to load an underlaying OS that exists on the host machine, but instead have direct contact with the hardware through their own integrated kernel. Microsoft designates **Hyper-V** as a Type 1 hypervisor. Hyper-V installs on Windows but runs directly on the physical hardware, inserting itself underneath the host OS. All guest operating systems then run through the hypervisor, but the host operating system gets special access to the hardware, giving it a performance advantage.

**The second** type are the hosted hyper-vs, they need the host machine kernel’s (OS) to establish the connection between the hardware and the VM. A Type 2 hypervisor doesn’t run directly on the underlying hardware. Instead, it runs as an application in an OS (**VirtualBox**, **VMware Fusion**). Type 2 hypervisors rarely show up in server-based environments. Instead, they’re suitable for individual PC users needing to run multiple operating systems.

**Type 1 or type 2 ?** 

A Type 2 hypervisor may function **slower** than a Type 1, as all of its commands must be filtered through the host computer’s operating system, creating a lag time which makes hyper-v of type 1 more **efficient**. It also introduces potential security risks if an attacker compromises the host OS because they could then manipulate any guest OS running in the Type 2 hypervisor.
### 1.2 - Vdi files
Virtual disk image, It acts like a virtual hard drive, storing the entire environment of a virtual machine (VM). When you create a new VM in VirtualBox, a VDI file is typically generated to hold its virtual disk. When you start the VM, VirtualBox treats the VDI file as a physical hard drive, allowing the guest OS to interact with it as if it were a real hard disk.
### 1.3 - Debian linux
Debian OS is a user-friendly linux distribution. Its open-source has made it possible to change its source code, so Debian is a parent distribution to 120 different models that are Debian based. It has a large community of experts and programmers who support and volunteer to error testing and developing it. Debian runs the GNOME desktop environment, it provides features like an application launcher, system tray, and workspace management. Debian is offered in three main channels, test(developing), stable and unstable; Stable channels have the highest security and lowest bugs possible and have regular updates.
### 1.4 - Rocky linux
Rocky is compatible with Red Hat Enterprise Linux, offering an economical way to familiarize yourself with an RHEL-like environment. It uses the DNF package manager (Dandified YUM, shortified to YUM).
## 2- Linux security module (LSM) 🛠
A framework which allows the linux kernel to enhance the protection system and support various security modules in virtual machines. They implement mandatory access control (MAC) on Linux systems. MAC is a policy-based mechanism that defines and enforces rules for how processes and users can access resources such as files, directories, sockets, ports, and devices (unlike DAC). They prevent unauthorized or malicious actions by limiting the capabilities and privileges of processes and applications. They also help isolate VMs from each other and the underlying host system, preventing malicious activity in one VM from affecting others or the host itself.

In the context of virtual machines, LSM have a crucial role in securing virtual environments alongside hypervisors, providing an additional layer of defense against vulnerabilities and malware. LSMs can be integrated directly into the hypervisor (allowing centralized policy management applied to all VM’s in the system) or implemented within individual VMs.

**AppArmor vs SELinux** 

**SELinux** stands for Security-Enhanced Linux. It uses **labels** to assign security contexts to processes and resources (objects), and checks these labels against a set of policies to determine whether an access request is allowed or denied. Imagine a label (Security context) on each object and process in your system, like a name tag that holds details about the object's role, permissions, and origin. SELinux has a set of pre-defined rules or policies that dictate how objects with specific contexts can interact with each other.

**AppArmor** stands for Application Armor and uses profiles to define and enforce rules for processes and resources. Profiles are files that specify the permissions and restrictions for each process or application, such as which files, directories, sockets, ports, and devices it can access, and how. Takes a **path-based** approach. It defines profiles for applications, specifying which files and directories they can access (read, write, execute). Imagine each profile like a map given to the app, it specifies which areas is it allowed to access, which it’s not authorized to, which resources it can use…

**SELinux** is more complex and difficult to learn, configure, manage and troubleshoot because of labels, security contexts, policies, and modes. **AppArmor**, on the other hand, is simpler and more intuitive due to profiles that are easier to create, modify, and debug.

Run ```aa-status``` to check if **AppArmor** is running.

## 3- Aptitude, apt
**dpkg** is the core infrastructure that handles package installation, removal, and updates within a Linux distribution in Debian-based distributions like Ubuntu. It handles Managing package files, Resolving dependencies, Maintaining package state and Performing updates. 

**Aptitude** and **apt** itself doesn't handle these core tasks directly. Instead, it builds upon **dpkg** and other tools, providing a more user-friendly interface and advanced features. However, the security of those features ultimately depends on the underlying package management system's robustness and trustworthiness.

Imagine **dpkg** as the foundation of a house, responsible for the structural integrity. **Aptitude** and **apt** is like an architect who designs and builds the house on top of that foundation, adding features and functionality. While the architect can make the house beautiful and functional, its overall security depends on the quality of the foundation materials and construction techniques.

**apt-get** or **apt** (Advanced package tool) is a lower level package manager that comes by default and lacks UI so it’s restricted only to command line. To install a package, you need to specify the name of the package just after the **apt install** command. The package manager reads the /etc/apt/sources.list file and the contents of the /etc/apt/sources.list.d folder to retrieve the ones that you need with all the dependencies. It combines functionalities from **apt-get** and **apt-cache**. Comes by default.

**Aptitude** is a higher-level tool which has a default text-only interactive interface along with option of command-line. (includes the **ncurses** library for GUI-like) It doesn't come by default, so you need to install it with the apt command.

- **Installation** : While removing any installed package, **Aptitude** will automatically remove unused packages, while **apt** would need user to explicitly specify this by either adding additional option of **—auto-remove** or specifying **apt autoremove**.
- aptitude has the **why** and **why-not** commands to tell you which manually installed packages are preventing an action that you might want to take.
- When facing a **package conflict**, apt will not fix the issue while aptitude will suggest a resolution that can do the job.
## 4- LVM (Logical Volume Manager) ⚙️
For sysadmins managing disk space is a very significant task. Running out of disk space used to be the start of a long and complex series of tasks to increase the space available to a disk partition. That’s where the concept of LVM first made its appearance allowing a flexible disk space management which is especially useful in servers or systems where your storage needs might change over time.
### 4.1 - Physical Volume
These are your physical hard drives or storage devices. They don’t have to take the whole hard drive, but just a partition of it that is then included in a big pool of storage space called volume group. This is done using LVM commands or tools specific to your operating system.
### 4.2 - Volume group
Created by combining multiple physical volumes into a big storage pool or one big chunk.
### 4.3 - Logical volume
You create partitions and resize them based on what your operating system and applications will actually need.
The true power of LVMs is in managing the logical volumes. You can resize them at any time on-demand without affecting the data stored on them. Also, if you ran out of space in the volume group you can add more physical volume to it. LVM allows you to take "snapshots" of a logical volume like photographs of the volume at a specific point in time, which is useful for backups or for testing changes without affecting the original data.

**Encrypted partition** refers to a portion of your server's storage space that has been secured with encryption technology. This means the data stored within that partition is scrambled and unreadable without the correct decryption key. Partitions are encrypted using tools like LUKS or using Key management.
## 5- UFW service Firewall 🚨
A firewall is a network security system that controls and monitors incoming and outgoing network traffic, deciding whether to allow or block specific traffic based on a defined set of security rules. It allows authorized visitors (data packets) in, but checks their IDs and credentials (packet headers) to ensure they're legitimate. Any suspicious or unauthorized visitors (malicious data) are denied entry.

The Uncomplicated Firewall, UFW is a management layer / frontend for iptables which aims to simplify the management of your firewall. UFW is not a firewall itself, but rather a **firewall management tool**. It uses a **command-line interface (CLI),** making it easy to add, remove, and modify firewall rules with no need to mess with complex iptables configurations which represent the underlying mechanics that UFW uses to implement and enforce the firewall rules (**[Iptables](https://en.wikipedia.org/wiki/Iptables)** is a host firewall built into the Linux kernel. It is typically configured to protect the server by preventing access to any ports that have not been expressly opened). It comes with predefined profiles for common scenarios, like enabling SSH access or opening ports for specific applications. It also provides logs of blocked connections and allows you to monitor firewall activity for potential security attacks.
## 6- SSH Protocol
Secure Shell is a cryptographic network protocol that encrypts all communication allowing secure access and remote computers management over a network. 

We can break down how SSH works into 6 parts :

- **Client-Server Model:** SSH operates on a client-server architecture, where the client initiates a connection to the server and requests a secure communication channel.
    
    **SSH Client:** The software you use on your local machine to initiate the connection (e.g., OpenSSH, PuTTY).
    
    **SSH Server:** The software running on the remote machine that accepts and manages the connection.
    
    When your web browser requests a website, the website's web server receives the request, retrieves the necessary files (e.g., HTML, images), and sends them back to your browser.
    
- **Connection Initiation:** The client sends a connection request to the server on port 22 (the default SSH port). The port number can be configured by changing the **[Port 22](https://www.ssh.com/academy/ssh/port)** directive in ```/etc/ssh/sshd_config```.
- **Authentication:** SSH uses two methods for authentication
    
    Password Authentication: The client provides a username and password for verification.
    
    Public Key Authentication: The client uses a cryptographic key pair (public and private keys) to authenticate
    
    - The server has the client's public key stored.
    - The client proves its identity by using its private key to encrypt a challenge sent by the server. Only the matching public key can decrypt the challenge, verifying identity.
- **Secure Session Establishment:** If authentication is successful, a secure session is established using encryption and integrity checks:
    
    Encryption**:** Data exchanged between the client and server is encrypted to prevent eavesdropping.
    
    Integrity Checks**:** SSH uses algorithms to ensure data hasn't been tampered with during transmission.
    
- **Secure Communication:** The client can now interact with the server securely, often through a terminal-like interface
- **Session Termination:** The session ends when the user logs out or the connection is closed

It provides secure file transfer (SFTP) capabilities, strong encryption algorithms like AES, port forwarding capabilities allowing users to access restricted services on remote servers securely, X11 forwarding capabilities allowing users to run graphical applications remotely without having to install them locally, agent forwarding capabilities allowing users to use SSH keys for authentication on remote servers without having to enter their password every time. 

Some SSH use cases include Remote server administration, Secure file transfer, Running graphical applications remotely, Port forwarding for accessing restricted services, Encrypted Tunneling for secure communication.
## 7- Setup your machine 📝
The choice for the RAM depends on the operating system and functionalities you want to use. 1024MB (1GB) is often considered a good starting point for basic Linux VMs. This is typically sufficient for running a lightweight desktop environment, web browsing, email, and basic productivity tasks. Some distributions, like Ubuntu or Fedora, have minimum RAM requirements of 512MB, while others, like Debian or Arch, can run on less.

A dynamically allocated hard disk file will grow in size as it is used. Meaning it won’t occupy and allocate the whole storage space that you specify initially, but instead it will grow only as needed when you want to store data within the VM. Although it will not shrink again automatically when that space is freed. This approach conserves disk space on your host machine, especially if you don't expect to use the full capacity of the VM's disk immediately.

You have to specify a virtual optical disc (virtual CD) that holds the installation files for the OS. An .iso file serves as a virtual optical disc that you can ‘insert’ within the VM that will boot from the ISO file, initiating the OS installation process.

When setting up the partitions, it’s important to allocate some memory for the **swap area** which is a designated portion of disk space used as virtual memory to supplement physical RAM when it is fully utilized. It plays a crucial role in preventing memory exhaustion and maintaining system stability. 

**Mount points** are similar to hidden portals that can teleport you to different storage location within the file system.
## 8- Configuration ✅
### 8.1 - Getting system updates and configuring the sudo command
### 

- ```su -``` : Login in as the root user.
- ```apt update``` : Fetching system updates, -y to automate all permission acceptance.
- ```apt upgrade``` : Actually doing the updates and upgrades found or fetched by the previous command.
- ```apt install sudo```: Install the sudo package.
- ```usermod -aG sudo your_username``` : Adds the user "your_username" to the "sudo" group while keeping their existing group memberships intact.
    
    Instead of granting individual permissions to each user, **groups** act as containers, categorizing users based on their roles, responsibilities, or access levels. **usermod** modifies user accounts. -a appends or keeps the existing group while adding the user to another group. 
    
- ```getent group sudo``` : Getent is a command for accessing entries from various system databases. In this context, it retrieves information about the "sudo" group from system databases.
- Open **sudoers** file by running the sudo visudo command, then find # User privilege specification, type ”your_username  	ALL=(ALL) ALL”. The first is for “run from any host on the system”, second for “run any command” (can borrow the privileges of any existing user account to perform actions.), third for “run commands as any user”.
### 8.2 - SSH configuration
This is an important step for remote management and secure communication between computers over an unsecured network. SSH provides a secure way to access and control your virtual machine remotely.

- ```sudo apt install openssh-server```: Install the component (openssh-server) is responsible for allowing secure remote access to your machine.
- ```sudo systemctl status ssh``` : Systemctl status shows detailed information about whether a service is running, its current state, and recent log messages related to the service. Here, we are checking the status of the SSH service on the system.
- ```sudo vim /etc/ssh/sshd_config``` : Editing the ssh server configuration file. sshd for SSH daemon is a background process or service that runs on a server to handle incoming SSH connections. It’s responsible for handling incoming SSH connections, and its behavior is determined by the settings in this configuration file. Some of the configurations include Port Number, PermitRootLogin, PasswordAuthentication, PubkeyAuthentication and AllowUsers or AllowGroups.
    
    Change the line **#Port22** to **Port 4242** without the # (Hash) in front of it.
    
- ```sudo grep Port /etc/ssh/sshd_config``` : this command will read the file directly and show you the current Port value in the file, not necessarily the currently active configuration in memory. If you run the sudo systemctl status ssh, it will show you that the port was not updated.
- ```sudo systemctl restart ssh``` : After making changes to the sshd_config file, you need to restart the SSH daemon for the changes to take effect.
### 8.3 - Installing and Configuring UFW
### 

- ```apt-get install ufw```: Installs the UFW package. Next, we’ll have to configure it to allow or deny specific network traffic.

The next 2 commands are used to define and update ufw rules. 

- ```sudo ufw allow ssh``` : Allowing traffic for the SSH service and ****permitting incoming connections on the default SSH port (22) in order to be able to connect to your system remotely using SSH. Without this rule, incoming SSH connections would be blocked, and you wouldn't be able to access the system via SSH.

- ```sudo ufw allow 4242``` : Allow traffic on port 4242.

- ```sudo ufw enable``` : This will activate the firewall with the rules you've defined.
  
- ```sudo ufw status numbered``` : Display a numbered list of the currently active UFW rules. IPv6 is the most recent **V**ersion of the

**I**nternet **P**rotocol, designed to replace the older IPv4. It is used to identify and locate devices on a network. The presence of "(v6)" in the rule indicates that the rule is for IPv6 traffic, as opposed to the rules without "(v6)" which are for IPv4 traffic. **IP**’s are a set of rules and conventions that govern how data is sent and received over computer networks (providing an addressing scheme, etc..).

**IPv4 vs. IPv6**:

IPv4 addresses are typically written in the format like **`192.168.1.1`**.

IPv6 addresses are longer and written in the format like **`2001:0db8:85a3:0000:0000:8a2e:0370:7334`**.

It’s good practice to run the ```sudo ufw enable``` first thing because it turns on the firewall to start enforcing the default policy which is to deny all incoming connections. But this doesn't automatically apply any specific rules. When you define rules, you should run the command “sudo ufw reload” to apply changes to the UFW configuration.
### 8.4 - Connecting to SSH remotely
Port forwarding in VirtualBox is a second approach to allow external connections to the virtual machine. It determines how traffic from the host machine is directed to the virtual machine. By this approach, external systems can connect to the host machine's IP address and port 4242. VirtualBox forwards that connection to the virtual machine's port 4242. However, using the first approach (```sudo ufw allow 4242```) allows for direct connection to the virtual machine using the virtual machine's IP address and port 4242.

- **Port forwarding** can be achieved by specifying the host port and guest port configuration in Port forwarding in the Network section. Note that the host port is the port on the host machine, and the guest port is the port on the virtual machine. You specify that requests coming to the host machine on port 4242 will be forwarded to port 4242 on the virtual machine.

- ```sudo systemctl restart ssh``` : updating the configuration including any changes related to the port that the SSH server is listening on.

- ```ssh username@<host_machine_ip> -p 4242``` : remotely connect to the VM via SSH. You need to specify the ip address of the physical machine that’s hosting your VM and by doing so the port forwarding rules will take care of redirecting the connection to the virtual machine, or you just directly specify that of the VM. If the machine you are trying to connect from is the same as the host machine, you can use localhost loopback address ```*ssh username@127.0.0.1 -p 4242``` to establish the connection from the host machine itself.

- ```exit``` : to disconnect from the VM.
### 8.5 - Setting password policy
### 

- ```sudo apt-get install libpam-pwquality``` : install the Pluggable Authentication Module (PAM) package for password quality checking. It offers features to enforce password policies by configuring the PAM configuration files on your system.

- ```sudo vim /etc/pam.d/common-password``` : access the PAM configuration file to change password policies.

- ```password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root``` :
    
    retry= specifies the number of times a user can attempt to enter a new password.
    
    minlen= sets the minimum required length for a password to 10 characters.
    
    ucredit=-1=Requires at least one uppercase letter.
    
    lcredit=-1= Requires at least one lowercase letter.
    
    dcredit=-1= Requires at least one digit.
    
    maxrepeat= limits the number of consecutive identical characters allowed in the password to 3.
    
    reject_username= ensures that the password cannot be the same as the username.
    
    difok= specifies the number of characters that must differ from the previous password.
    
    enforce_for_root= Since root users have more flexibility, this enforces the parameters even for the root user except for difok (root doesnt remember any previous passwords).
    
- ```sudo vim /etc/login.defs``` : Configuration file containing default settings related to user accounts and password policies, used by the **login** and **passwd** utilities.

- ```PASS_MAX_DAYS 30 , PASS_MIN_DAYS 2 , PASS_WARN_AGE 7``` :  Modifying the default password aging policy for user accounts.

- ```sudo reboot (optional)```: Reboot to have the changes take effect immediately. If not, the changes will be applied after the user had logged out.

- ```chage -m <num>, chage -M <num>, chage -W <num>``` : Modify minimum number of days between password changes, password expiration period, set a warning of <num> days before expiration.

- ```chage -l username``` : Display the current password aging information for the specified user and for the root.
### 8.6 - Creating groups, users, assigning 👤
### 

- ```sudo groupadd user42``` : Creating groups to organize users to collectively manage permissions, access, and resources for a set of users.
  
- ```getent group <group_name>```: Search and display information about a specific group in the system's group database.

- ```cd /etc/passwd``` : Access the file containing the names of all users on the system.

- ```sudo delgroup <group_name>``` : To delete a certain group.

- ```sudo usermod -aG user42 username``` : Adding the user to the user24 group.

- ```groups``` : See which groups the user account belongs to
### 8.7 - Configuring Sudoers file, logs, warning message
### 

- ```cd /var/log``` : Access the location where log files are stored, providing information about system events, services, processes.

- Each action using sudo has to be archived, both inputs and outputs. To do so, **create a folder** (name it sudo for clarity), then create a file.log, and save the path to that file.

- ```sudo vim /etc/sudoers or sudo visudo``` : Access the sudoers file responsible for defining the rules and permissions that determine which users or groups are allowed to execute commands with elevated privileges using the sudo command.

- ```Defaults	badpass_message="Wrong password, please try again!"``` : Adding warning message ****

- ```Defaults passwd_tries=3``` : Sets a default value for the maximum number of password entry attempts allowed when a user runs sudo.

- ```Defaults	logfile="/var/log/sudo/sudo.log"``` : Sets the location for the sudo log file.

- ```Defaults	log_input, log_output``` : Enable logging of both input (commands typed by user) and output (result) for sudo commands.

- ```Defaults	requiretty``` : (teletypewriter) By requiring a TTY, it limits the ability of users to run sudo commands from scripts or automated processes by adding an extra layer of authentication ensuring that users physically present at the console are the ones executing privileged commands.
## 9- Script
A script should start automatically, display informations, run every 10 minutes broadcast to all terminals using wall (send a message to all logged-in users), include banner (optional), avoid displaying error messages directly to the users.

Store the **monitoring.sh** script in **/usr/local/bin** (bin since it stores programs you can run)
### Common commands

```grep``` : Search for specific patterns.

```sort``` : Arrange lines in specific order. (default, ascending alphabetical order).

```uniq``` : Eliminate duplicated lines.

```wc -l``` : Count number of lines. (wc alone is to count words, lines, and characters)

```free —mega``` : Display information about system memory usage. m for megabytes

```NR``` : In Awk, NR is a built-in variable that represents the current line number being processed. Awk reads input data line by line, and NR keeps track of the line number.

```free``` : displays info for RAM memory 

```df``` :displays info about storage or disk space

```mpstat``` : displays cpu processor related info

```lsblk``` : displays system storage info
### Script

- ```#!/bin/bash``` : shebang, recognize the script as executable and execute based on the specified path to the interpreter (bash).
- **Architecture of the OS and the kernel version:** ```ARCH=$(uname -a)```
- **Number of physical processors:** ```PCPU=$(grep "physical id" /proc/cpuinfo | sort -u | wc -l)```
- **Number of virtual processors:** ```VCPU=$(grep "processor" /proc/cpuinfo | wc -l)``` total number of logical (virtual) processors, aka number of cores.
- **The current available RAM and its utilization rate as a percentage** :

$$
Free Memory = (\cfrac {Used Memory} {Total Memory} )×100
$$

```RAM=$(free —mega | awk 'NR==2{printf "%d/%dMB (%.2f%%)", $3,$2,((($2-$4)/$2)*100) }’)```

- **The current available memory (storage) on your server and its utilization rate as a percentage**:

```DISK=$(df -h --total | awk 'END{printf "%.1f/%dGb (%d%%)", $3, $2, (($3/$2)*100)}’)```

h for human-readable

- **The current utilization rate of your processors as a percentage** : activity monitor, idle is value is the amount of CPU left not doing any work

```CPU=$(mpstat | tail -1 | awk '{printf "%.1f %%", 100 - $NF}’)```

tail -1 the one for the number of lines can be 2, 3 …

- **The date and time of the last reboot**: ```BOOT=$(who -b | awk ‘{print $3 “ ” $4}’)```
- **LVM is active or no** : ```LVM=$(if lsblk | grep -q 'lvm'; then echo "yes"; else echo "no"; fi)```

q for quiet, dont print whats grepped

- **The number of active connections** : ```TCP=$(ss -t -a | grep -c ESTAB)```

ss : socket stats, -t : only tcp, -a : all sockets (listening and not), -c : count whats grepped that goes with ESTAB

- **The number of users using the server** : ```USR=$(who | awk '{print $1}' | sort -u | wc -l)```
- **The IPv4 address of your server and its MAC (Media Access Control) address** : ```ip=$(hostname -I)```, ```mac=$(ip link show | grep ether | awk '{printf $2}’)```
- **The number of commands executed with the sudo program** : ```SUDO=$(grep COMMAND /var/log/sudo/sudo.log | wc -l)```

```
ARCH=$(uname -a)
PCPU=$(grep "physical id" /proc/cpuinfo | sort -u| wc -l)
VCPU=$(grep "processor" /proc/cpuinfo | wc -l)
RAM=$(free —mega | awk 'NR==2{printf "%d/%dMB (%.2f%%)", $3,$2,((($2-$4)/$2)*100) }’)
DISK=$(df -h --total | awk 'END{printf "%.1f/%dGb (%d%%)", $3, $2, (($3/$2)*100)}’)
CPU=$(mpstat | tail -1 | awk '{printf "%.1f %%", 100 - $NF}’)
BOOT=$(who -b | awk ‘{print $3 “ ” $4}’)
LVM=$(if lsblk | grep -q 'lvm'; then echo "yes"; else echo "no"; fi)
TCP=$(ss -t -a | grep -c ESTAB)
USR=$(who | awk '{print $1}' | sort -u | wc -l)
ip=$(hostname -I)
mac=$(ip link show | grep ether | awk '{printf $2}’)
SUDO=$(grep COMMAND /var/log/sudo/sudo.log | wc -l)

wall “
#Architecture : $ARCH
#CPU physical : $PCPU
#vCPU : $VCPU
#Memory Usage: $RAM
#Disk Usage: $DISK
#CPU load: $CPU
#Last boot: $BOOT
#LVM use: $LVM
#Connections TCP : $TCP ESTABLISHED
#User log: $USR
#Network: IP $ip ($mac)
#Sudo : $SUDO cmd “
```
## 10- Crontab ⏰
### 

The Crontab (table) command is used to create, view, edit, or delete the cron jobs for a user. Crontab allows you to schedule a task that automates the execution of commands or scripts for a time interval. Cron is chronos (time) in greek. 

- ```chmod 777 monitoring.sh``` : Give permission to the script (execute especially).

- ```sudo visudo``` : Open your sudoers file.

- ```your_username ALL=(ALL) NOPASSWD: /usr/local/bin/monitoring.sh``` : Under where its written %sudo ALL=(ALL:ALL) ALL to grant permission to the user to run the script.

- ```sudo reboot```

- ```sudo crontab -e``` : Open crontab in an editor.

- ```*/10 * * * * /usr/local/bin/monitoring.sh``` : specify .Check [crontab.guru](https://crontab.guru/) for more info about the crontab format. /10 to specify that it has to be run every 10 minutes, not at the 10th minute of every hour, day,…

- ```*/10 * * * * /usr/local/bin/monitoring.sh```
## 11- Bonus ⭐
### 11.1 - WordPress
content management system for creating and managing websites, built using PHP as its server-side scripting language and paired with a MySQL or MariaDB database. WordPress provides a user-friendly interface and a wide range of themes and plugins. 

```apt install unzip wget```: Install the commands unzip and wget

```wget https://wordpress.org/latest.zip``` : Install WordPress

```unzip latest.zip -d /var/www/html/```: Unzip the file genereated by the previous command.

```cd /var/www/html/wordpress``` : Access the unzipped file.

```sudo chmod -R 755 wordpress``` : Grant the corresponding permissions.

### 11.2 - PHP
Hypertext Preprocessor, a server-side scripting language designed for web development. PHP is embedded within HTML code and interpreted by a web server with a PHP processor module before the HTML is sent to the client’s browser, resulting in dynamic web pages. PHP is embedded in WordPress themes to dynamically generate HTML, facilitates database interactions enabling the retrieval and manipulation of data stored in the WordPress database, processes user requests.

```sudo apt install php-cgi php-mysql``` : Install the corresponding PHP packages.

```php -v``` : Check that PHP is installed.
### 11.3 - Lighttpd webserver 🌐
Whenever a user open up a website on their web browser, the browser needs to fetch files (HTML, CSS, JS, images…) to display the webpage correctly, these files are hosted on a webserver, which are requested through HTTP. If the request reaches the webserv and gets accepted, the webserv will look for corresponding documents on the server’s storage, then send them back through HTTP. If the docs could not be found, the server returns a 404 response.

A webserver (such as **lighttpd***) is a software that “serves” content to users, it’s important for delivering resources and web content to users. It can be static or dynamic, the first includes files that don't change often and remain the same for all users, the files dont need any processing it’s suitable for websites with primarily informational content. Dynamic webservers generates websites that can be tailored based on user requests, user interactions, or real-time data.

```sudo apt-get install lighttpd``` : Install the lighttpd package.

```sudo ufw allow http```

```port forwarding 80 ``` Port 80 is the default port for unencrypted HTTP traffic, and it is the standard port through which web browsers communicate with web servers to request and receive web pages.

```sudo lighty-enable-mod fastcgi``` : FastCGI (Fast Common Gateway Interface) is a protocol that enables a web server to delegate the handling of certain dynamic content to external processes. It allows the web server to communicate with external programs or applications that can generate dynamic content.

```sudo lighty-enable-mod fastcgi-php```

```sudo service lighttpd force-reload``` : Reload to apply the changes.

### 11.4 - MariaDB Database
MariaDB is a fork of the MySQL relational database management system. It's a powerful and reliable relational database system that offers features, performance improvements, and enhancements over its MySQL roots.

```apt install mariadb-server mariadb-client```

```systemctl start mariadb```

```systemctl enable mariadb```

```mysql_secure_installation``` : secure your MariaDB server.

```systemctl restart mariadb``` : Restart the database server for the changes to take effect.

```mariadb``` : Access the database

```create DATABASE database_name;```

```show databases;```

```use WordPressDB;```

```CREATE USER 'gemartin'@'localhost' IDENTIFIED BY '12345';```

```GRANT ALL PRIVILEGES ON wp_database.* TO 'gemartin'@'localhost';```

```FLUSH PRIVILEGES;``` : Apply the changes

```use ‘dataB_name’;```

```show tables;```

```show columns from ‘table_name’;``` : Display a list of tables that are present is the Database currently in use.

```select ‘field_name1’, ‘field_name2’ from ‘table_name’;``` : Display the content of a table.

```exit``` : Exit mariaDB.
### 11.5 - Aditional service (Fail2ban) ➕
Fail2ban is a service that ban hosts who cause multiple authentication errors. It's designed to protect Linux servers from malicious activities and unauthorized access attempts by banning their IP addresses. It scans log files like ```/var/log/auth.log``` and bans IP addresses conducting too many failed login attempts. It does this by updating system firewall rules to reject new connections from those IP addresses, for a configurable amount of time.
```sudo apt-get install fail2ban``` : Install the corresponding fail2ban packages.

```sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local``` : It's safer to work with a copy than the original file itself.
1 ◦ Access the jail.local fail and try to locate this line of code  ```DEFAULT```, then change the following line to whatever suits you.

```ignoreip = 127.0.0.1/8
bantime = 10m
findtime = 10m
maxretry = 3
```

**ignoreip** is a whitelist of IP addresses that will never be banned. They have a permanent Get Out of Jail Free card

2 ◦ Next, look for the following line of code ```SSHD``` and add/change the following :

```port : 4242
enabled = true
maxretry = 3
backend = systemd
logpath = /var/log/auth.log
```
3 ◦ Finally, run the following commands in your terminal : 

```sudo systemctl enable fail2ban

sudo systemctl start fail2ban

sudo systemctl status fail2ban

sudo fail2ban-client status

sudo fail2ban-client status sshd
```
If you set a longer ban duration (like several hours), but want to allow an IP address to make another connection request sooner, you can parole it early: ```sudo fail2ban-client set sshd unbanip ‘ip’ ```. For localhost use ```10.0.2.2``` .

