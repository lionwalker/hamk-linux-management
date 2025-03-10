# Linux Management 

This repository created to update weekly course progress of Linux Management in ICT Robotics (BEIRP24A6)

## Assingment 1

### Azure Virtual Machine Setup

#### Initial Setup
1. Used existing personal GitHub account
2. Linked HAMK student email to personal GitHub account through Settings > Emails

#### Azure Account Setup
1. Created Azure account using HAMK student email at portal.azure.com
2. Activated Azure for Students subscription for additional credits

#### Virtual Machine Creation

##### Machine Specifications:
- **Image**: Ubuntu Server 24.04 LTS gen 2 (Canonical)
- **Name**: Ubuntu1-tja-NEur-B1s
- **Size**: Standard_B2ls_v2
- **Authentication**: SSH key
- **Network**: New resource group and subnet created
- **Security**: Configured to allow SSH traffic from specified IP

![VM Overview](images/w1-azure-vm-overview.png)

### SSH Connection Setup with PuTTY

#### Converting .PEM to .PPK
1. Launched PuTTYgen from Start menu
2. Selected RSA as the key type
3. Clicked 'Load' and changed file filter to 'All Files'
4. Located and selected the .pem file from Azure
5. Clicked 'Save private key'
6. Confirmed saving without passphrase
7. Saved the converted key with .ppk extension

![PuTTY Key Generator](images/w1-putty-key-generator.jpg)

#### Establishing SSH Connection
1. Opened PuTTY
2. Entered VM's public IP address in the Host Name field
3. Navigated to Connection > SSH > Auth
4. Browsed and selected the converted .ppk key file
5. Clicked 'Open' to start the session
6. Successfully connected to the VM using the specified username

![PuTTY Connection](images/w1-putty-connection.jpg)

#### Connection Verification
- Successfully logged into the Ubuntu VM shell
- Verified system access and basic functionality
- System settings left at default as per instructions

### Notes
- VM created according to Microsoft Azure course specifications
- All security best practices followed
- SSH key authentication used for enhanced security

## Assingment 2

The task involves selecting five level 2 directories and saving their contents in a file named "listing.md." A level 2 directory is defined as a directory that is two levels deep within the file system, such as /home/mylogin/. Examples of directories that do not qualify as level 2 include /tmp/ (level 1) and /usr/local/bin/ (level 3).

For each of the five selected level 2 directories, the contents should be listed, which involves listing the filenames within each directory. The resulting file, "listing.md," will contain the directory paths along with their respective filenames.

``` bash
sudo ls /etc/{security,selinux,systemd,sos,ssh} > listing.md
```

![Linux Command](images/w2-linux-command-assignment.jpg)

Download [here](assets/assigment-2.md)

## Assingment 3

This document details the process of creating users and managing permissions in a Linux environment. The tasks include creating different types of users, managing sudo privileges, and setting up a shared directory with specific permissions.

### Task 1: Creating User Tupu

The adduser script was used to create the Tupu user. This script provides an interactive way to create a new user with all necessary directories and settings.

``` bash
sudo adduser tupu
```

![Creating User Tupu](images/w3-task-1.jpg)

The adduser script automatically:
- Creates the home directory
- Sets up default shell
- Prompts for password
- Creates user-specific group
- Sets up basic profile

### Task 2: Creating User Lupu

Initially, I tried using the following given command:

``` bash
sudo useradd -m -d /home/lupu -s /bin/bash -G lupu lupu
```

However, this resulted in an error:

![Command error](images/w3-task-2-error.jpg)

The issue was that we tried to add the user to a group that didn't exist yet. To fix this, we modified the command to include the -U flag, which automatically creates a user group with the same name as the user:

``` bash
sudo useradd -m -d /home/lupu -s /bin/bash -U lupu
```

![Creating User Lupu](images/w3-task-2.1.jpg)

After creation, Set the password:

``` bash
sudo passwd lupu
```

![Creating User Lupu](images/w3-task-2.2.jpg)

### Task 3: Creating System User Hupu

Created a system user Hupu with restricted login capabilities:

``` bash
sudo useradd --system --shell /bin/false hupu
```

![Creating System User Hupu](images/w3-task-3.1.jpg)

This creates a system account that:

- Has no login shell (/bin/false)
- Is typically used for running services
- Has no home directory by default

### Task 4: Adding Sudo Privileges

Added sudo privileges for both Tupu and Lupu users using the usermod command:

``` bash
sudo visodu
```

Then adding:

``` bash
tupu ALL=(ALL:ALL) ALL
lupu ALL=(ALL:ALL) ALL
```

![Adding Sudo Privileges](images/w3-task-4.1.jpg)

Alternative method using:

``` bash
sudo usermod -aG sudo tupu
sudo usermod -aG sudo lupu
```

![Adding Sudo Privileges](images/w3-task-4.2.jpg)

### Task 5: Setting Up Shared Directory

This task required creating a shared directory with specific permissions for Tupu and Lupu. Here's the detailed solution:

**First, created a new group for the shared directory:**

``` bash
sudo groupadd projekti
```

**Created the directory:**

``` bash
sudo mkdir /opt/projekti
```

**Added Tupu and Lupu to the projekti group:**

``` bash
sudo usermod -aG projekti tupu
sudo usermod -aG projekti lupu
```

**Set group ownership and permissions:**

``` bash
sudo chown :projekti /opt/projekti
sudo chmod 2770 /opt/projekti
```

![Setting Up Shared Directory](images/w3-task-5.jpg)

The permission setup (2770) breaks down as:

- 2: SetGID bit (ensures new files inherit group ownership)
- 7: Owner has full permissions (read/write/execute)
- 7: Group has full permissions (read/write/execute)
- 0: Others have no permissions

This configuration ensures:

- Only Tupu and Lupu can access the directory
- New files automatically inherit the projekti group
- Other users cannot access the directory
- Both users have full control over the directory and its contents

To verify the setup:

``` bash
sudo ls -la /opt/projekti
```

![Setting Up Shared Directory](images/w3-task-5-verificaion.jpg)

## Assingment 5

In this assignment, we created a shell script called print.sh to add a line to the file diskspace.txt, reporting the home directory size and the current date and time. We then used crontab to schedule this script to run every 12 hours, ensuring it runs at least six times to populate diskspace.txt with multiple entries. Finally, we utilized an awk command to find the line with the maximum value in the first column and printed it in the format: Max=[maximum value], at [date and time]. This task helped us automate the process of monitoring disk space and identifying the largest recorded value efficiently.

### Step 1: Make a script and add it to cron

**Script: `print.sh`**

This script adds a line to the file `diskspace.txt`, reporting the home directory size and the current date.

``` bash
#!/bin/bash

output=$(du -s $HOME | awk '{print $1}')
current_datetime=$(date '+%b %d %H:%M:%S %Z %Y')
echo "$output $current_datetime" >> /home/lahiru_hewawasam/diskspace.txt
```

![Bash file](images/w4-sh-file.jpg)

**Add to Cron**

Use the following command to open your crontab file:

``` bash
crontab -e
```

Add the following line to run the print.sh script every 12 hours:

``` bash
0 */12 * * * /home/lahiru_hewawasam/print.sh
```

![Bash file](images/w4-crontab.jpg)

**Note: For ease of testing I raned it in every 2 minutes ( \*/2 * * * * )**

### Step 2: Run the script a minimum of 6 times

After adding the script to cron, it will automatically run every 12 hours, adding a line to diskspace.txt each time. Ensure that it runs at least 6 times so that the file contains at least 6 lines.

![Bash file](images/w4-discspace-file.jpg)

### Step 3: Find and print the line containing the maximum size

Use the following awk command to find the line with the maximum size and print it in the specified format:

``` bash
awk '{if($1 > max) {max=$1; line=$0}} END {print "Max=" max ", at " substr(line, index(line, $2))}' diskspace.txt
```

![Bash file](images/w4-awk-command.jpg)

### Explanation

#### Script (print.sh):

- output=$(du -s $HOME | awk '{print $1}'): Get the size of the home directory.
- awk '{print $1}': Extract the size part.
- current_datetime=$(date '+%b %d %H:%M:%S %Z %Y'): Get the current date and time and stored in the variable current_datetime.
- echo "$output $current_datetime" >> /home/lahiru_hewawasam/diskspace.txt: Append the output to diskspace.txt.

#### Cron:

- 0 */12 * * *: Run the script every 12 hours. 

**Note: For ease of testing I raned it in every 2 minutes ( \*/2 * * * * )**

#### awk command:

```bash
awk '{if($1 > max) {max=$1; line=$0}} END {print "Max=" max ", at " substr(line, index(line, $2))}' diskspace.txt
```

- awk '{if($1 > max) {max=$1; line=$0}}
  - $1: Refers to the first column of the current line.
  - max: A variable to store the maximum value found in the first column. Initially, it is undefined.
  - line: A variable to store the entire line where the maximum value is found.
  - if($1 > max): This condition checks if the first column value of the current line is greater than the current value of max.
  - {max=$1; line=$0}: If the condition is true, it updates max to the value in the first column and line to the entire current line ($0).
- END {print "Max=" max ", at " substr(line, index(line, $2))}'
  - END: This block is executed after all lines have been processed.
  - print "Max=" max ", at " substr(line, index(line, $2)): This command prints the maximum value and the corresponding line's content starting from the second column.
  - "Max=" max: Outputs the maximum value found.
  - index(line, $2): Finds the starting position of the second column in the stored line (line).
  - substr(line, index(line, $2)): Extracts the substring from line starting from the position of the second column to the end of the line.

## Assingment 6

This report documents the hands-on exploration of the Advanced Package Tool (APT) on a Linux system, covering system updates, package management, repository handling, and troubleshooting procedures.

### Part 1: Understanding APT & System Updates

**APT Version Check**

Command executed:
``` bash
apt --version
```

Output:

![Command output](./images/w5-apt-version.jpg)

**Package List Update**

Command executed:
``` bash
sudo apt update
```

This step is important because:

- It synchronizes the local package index with the remote repositories
- Ensures the system has the latest information about available packages
- Required before performing any package installations or upgrades
- Helps identify which packages need updates

**System Upgrade**

Command executed:
``` bash
sudo apt upgrade -y
```

Difference between update and upgrade:

- apt update only refreshes the package index and metadata
- apt upgrade actually downloads and installs newer versions of installed packages
- update is like checking what's new, while upgrade is actually getting those new versions

**Pending Updates Check**

Command executed:
``` bash
apt list --upgradable
```

Output:

![Command output](./images/w5-apt-list-upgradable.jpg)

### Part 2: Installing & Managing Packages

**Package Search**

Command executed:
``` bash
apt search image editor
```

*Selected package: gimp (GNU Image Manipulation Program)*

**Package Details**

Command executed:
``` bash
apt show gimp
```

Output:

![Command output](./images/w5-show-gimp.jpg)

Key dependencies:

- libc6
- libgdk-pixbuf2.0-0
- libgegl-0.4-0t64
- libgimp2.0t64
- libgtk2.0-0t64

**Package Installation**

Command executed:
``` bash
sudo apt install gimp -y
```

*Installation was successful, verified by launching the application.*

**Version Check**

Command executed:
``` bash
apt list --installed | grep gimp
```

Output:

![Command output](./images/w5-gimp-version-check.jpg)

### Part 3: Removing & Cleaning Packages

Package Removal

Command executed:
``` bash
sudo apt remove gimp -y
```

*Note: This removes the package but keeps configuration files.*

**Complete Package Purge**

Command executed:
``` bash
sudo apt purge gimp -y
```

Difference between remove and purge:

- remove only uninstalls the package binaries
- purge removes both the binaries and configuration files
- purge is more thorough when you want to completely remove all traces of a package

**Autoremove Unused Dependencies**

Command executed:
``` bash
sudo apt autoremove -y
```

This step is important because:

- Removes packages that were installed as dependencies but are no longer needed
- Frees up disk space
- Keeps the system clean from unused software

**Clean Package Cache**

Command executed:
``` bash
sudo apt clean
```

This command:

- Removes all downloaded package files (.deb) from the local cache
- Frees up disk space in /var/cache/apt/archives/
- Doesn't affect installed packages

### Part 4: Managing Repositories & Troubleshooting

**Repository List**

Command executed:
``` bash
cat /etc/apt/sources.list
```

Output:

![Command output](./images/w5-repository-list.jpg)

Observations:

- Contains main Ubuntu repositories
- Includes different components (main, restricted, universe, multiverse)
- Lists both source and binary package repositories
- Contains security updates repositories

**Adding Universe Repository**

Command executed:
``` bash
sudo add-apt-repository universe
sudo apt update
```

Output:

![Command output](./images/w5-adding-universe-repository.jpg)

Universe repository contains:

- Community-maintained packages
- Open-source software not officially supported by Ubuntu
- Larger selection of packages compared to main repository
- Software that may have more frequent updates

**Installation Failure Simulation**

Command executed:
``` bash
sudo apt install fakepackage
```

Error message:

![Command output](./images/w5-adding-fackpackage.jpg)

Troubleshooting steps:

1. Verify package name spelling
2. Check if the required repository is enabled
3. Run apt update to refresh package lists
4. Search for similar package names using apt search
5. Consider using alternative package names or sources

### Bonus Challenge: Package Hold Management

Commands executed:
``` bash
sudo apt-mark hold firefox
sudo apt-mark unhold firefox
```

![Command output](./images/w5-hold-unhold-packages.jpg)

Reasons to hold a package:

- Prevent automatic updates of critical production software
- Maintain specific version compatibility with other software
- Avoid potential breaking changes from updates
- Keep a stable working environment for specific applications

### Conclusion

Through this assignment, we've gained practical experience with APT package management, including:

- System updates and upgrades
- Package installation and removal
- Repository management
- Troubleshooting common issues
- Advanced package management techniques

These skills are fundamental for maintaining and managing Linux systems effectively.

## Assingment 7

### Linux Virtualization Exercise

### Part 1: Virtualization Concepts

#### Key Concepts Overview

**Virtualization**

Virtualization is a technology that allows the creation of virtual versions of computing resources, including hardware platforms, storage devices, and network resources. It enables multiple virtual systems to run on a single physical machine.

**Hypervisor**

A hypervisor (or Virtual Machine Monitor) is software that creates and manages virtual machines. There are two types:

Type 1 (Bare Metal): Runs directly on hardware (e.g., VMware ESXi, Xen)
Type 2 (Hosted): Runs on top of an operating system (e.g., VirtualBox, VMware Workstation)

**Virtual Machines (VMs)**

VMs are complete virtualized computing environments that include their own:

- Operating system
- Virtual hardware (CPU, RAM, storage, network interfaces)
- Isolated system resources

**Containers**

Containers are lightweight, portable environments that package application code and dependencies. They:

- Share the host OS kernel
- Are more resource-efficient than VMs
- Start up faster and are more portable

#### VMs vs. Containers Comparison

**Architecture**

- VMs: Complete isolation with dedicated kernel and resources
- Containers: Shared kernel with isolated user space

**Resource Utilization**

- VMs: Higher overhead due to running complete OS
- Containers: Lower overhead, shares host OS resources

**Isolation Level**

- VMs: Complete hardware-level isolation
- Containers: Process-level isolation

### Part 2: Multipass Implementation

#### Installation

``` bash
# Install Multipass
sudo snap install multipass
```

#### Basic Commands Implementation

```bash
# Launch default Ubuntu instance
multipass launch --name primary-vm
```
![Command output](./images/w7-multipass-launch.jpg)

```bash
# List running instances
multipass list
```
![Command output](./images/w7-multipass-list.jpg)

```bash
# View instance details
multipass info primary-vm
```
![Command output](./images/w7-multipass-info.jpg)

```bash
# Access instance shell
multipass shell primary-vm
```
![Command output](./images/w7-multipass-shell.jpg)

```bash
# Execute command on instance
multipass exec primary-vm -- ls -la
```
![Command output](./images/w7-multipass-shell-from-outside.jpg)

```bash
# Stop instance
multipass stop primary-vm

# Delete instance
multipass delete primary-vm
```
![Command output](./images/w7-multipass-stop-and-delete.jpg)

#### Cloud-init Configuration

**Start a New Instance with Multipass**

Save the configuration as cloud-init

![Command output](./images/w7-cloud-init.jpg)

To start a new instance using this cloud-init configuration, you can use the following multipass command:

```bash
multipass launch --name my-instance --cloud-init cloud-init
```
![Command output](./images/w7-multipass-cloud-list.jpg)

#### File sharing

```bash
# Create shared directory
mkdir ~/shared-folder

# Mount to instance
multipass mount ~/shared-folder my-instance:/shared
```
![Command output](./images/w7-multipass-shared.jpg)

### Part 3: LXD Implementation

```bash
# Create container
lxc launch ubuntu:20.04 test-container

# List containers
lxc list

# Execute commands
lxc exec test-container -- apt update

# Stop container
lxc stop test-container

# Delete container
lxc delete test-container
```
![Command output](./images/w7-lxd-list.jpg)

### Part 4: Docker Implementation

#### Installation

```bash
# Install Docker Engine
sudo apt update
sudo apt install docker.io

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker
```

Basic Docker Commands

```bash
# Pull image
docker pull ubuntu:latest

# Run container
docker run -it ubuntu:latest

# List containers
docker ps

# Build from Dockerfile
docker build -t myapp .

# Stop container
docker stop container_id
```
![Command output](./images/w7-docker-setup.jpg)

### Part 5: Snap Implementation

#### Install snapcraft

First, install Snapcraft if you haven't already:

```bash
sudo apt update
sudo apt install snapcraft -y
```

If you're on a non-Ubuntu system, you may need to install Snapcraft via Snap:

```bash
sudo snap install snapcraft --classic
```

#### Create a project directory

Create a directory for your Snap project:

```bash
mkdir my-snapcraft
cd my-snapcraft
```

#### Create the Application Script

We’ll make a simple Bash script that prints "Hello, Snap!".

```bash
mkdir bin
nano bin/hello-snap
```

Paste the following content into the file:

```bash
#!/bin/bash
echo "Hello, Snap!"
```

Save and exit (in nano, press CTRL+X, then Y, then Enter).

Make the script executable:

```bash
chmod +x bin/hello-snap
```

#### Create the snapcraft YAML file

Snapcraft requires a snapcraft.yaml file to define how the Snap should be built.
Create the file:

```bash
nano snapcraft.yaml
```

Add the following content:

```bash
name: my-snapcraft
base: core22
version: "1.0"
summary: "A simple Snapcraft app"
description: "This is a simple Snap application that prints Hello, Snap!"

grade: stable
confinement: strict

apps:
  hello:
    command: bin/hello-snap

parts:
  hello:
    plugin: dump
    source: .
```
![Command output](./images/w7-snapcraft-yaml.jpg)

This configuration:

- Names the Snap my-snapcraft
- Uses the core22 base (Ubuntu 22.04)
- Defines a simple app that runs bin/hello-snap
- Uses the dump plugin to include our script in the package

#### Build the snap package

Run the following command to build your Snap:

```bash
snapcraft
```

If it's the first time running Snapcraft, it may prompt you to install additional dependencies.

#### Install and run your snap

Once the build is complete, install the generated .snap file (e.g., my-snapcraft_1.0_amd64.snap):

```bash
sudo snap install my-snapcraft_1.0_amd64.snap --dangerous

```

The --dangerous flag is needed because it’s not from the official Snap store.

Now, run your Snap:

```bash
my-snapcraft.hello
```

You should see:

```bash
Hello, Snap!
```

![Command output](./images/w7-snapcraft-setup.jpg)

## Assingment 8

### Linux Server Firewall Configuration

### Introduction

This document outlines the configuration of a Linux firewall (using ufw - Uncomplicated Firewall) to protect server services and prevent common network attacks.

### Base configuration

#### 1. Enable UFW on system start

```bash
sudo systemctl enable ufw
sudo ufw enable
```

#### 2. Default Policies

```bash
# Set default policies to deny incoming and allow outgoing
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
![Command output](./images/w8-fw-default-policies.jpg)

### Service Rules

#### SSH Server

```bash
# Allow incoming SSH connections on standard port 22
# This enables secure remote administration of the server
sudo ufw allow 22/tcp

# Implement rate limiting for SSH to prevent brute force attacks
# Limits connection attempts from the same IP address
sudo ufw limit ssh comment 'Rate limit SSH connections'
```
![Command output](./images/w8-fw-ssh-rules.jpg)

#### Web Server (HTTP/HTTPS) rules

```bash
# Allow incoming HTTP traffic on port 80
# Required for standard web server operation
sudo ufw allow 80/tcp

# Allow incoming HTTPS traffic on port 443
# Required for secure web server operation using SSL/TLS
sudo ufw allow 443/tcp
```
![Command output](./images/w8-fw-web-rules.jpg)

### Logging Configuration

```bash
# Enable logging for all blocked connections
sudo ufw logging on

# Enable high-level logging to track all connections
sudo ufw logging high

# Log location: /var/log/ufw.log
# Logs both allowed and denied connections for monitoring
```
![Command output](./images/w8-fw-enable-logging.jpg)

### Protection Against Common Attacks

#### 1. SYN Flood Protection

```bash
# Add to /etc/sysctl.conf
# Enable SYN cookies to prevent SYN flood attacks
net.ipv4.tcp_syncookies = 1

# Increase backlog queue to handle more concurrent SYN packets
net.ipv4.tcp_max_syn_backlog = 2048

# Reduce number of SYN-ACK retries to limit resource consumption
net.ipv4.tcp_synack_retries = 2

# Also add these parameters for additional SYN flood protection
net.ipv4.tcp_syn_retries = 5
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_ecn = 0
net.ipv4.tcp_wmem = 4096 87380 8388608
net.ipv4.tcp_rmem = 4096 87380 8388608

# Apply settings
sudo sysctl -p
```
![Command output](./images/w8-fw-syn-flood.jpg)

#### 2. Additional Protection Measures

#### Block invalid packets

**What are invalid packets?**

Invalid packets include any incoming connections that don’t start with the SYN flag alone.

In a standard TCP Three-Way Handshake, every new connection begins with a SYN packet.

If a TCP connection starts with a different flag or an unusual combination of flags – like those generated by port-scanning tools such as Nmap – these packets should be blocked.

**How UFW blocks invalid packets**

Default rules added by UFW on /etc/ufw/before.rules file:

```bash
# drop INVALID packets (logs these in loglevel medium and higher)
-A ufw-before-input -m conntrack --ctstate INVALID -j ufw-logging-deny
-A ufw-before-input -m conntrack --ctstate INVALID -j DROP
```

Add these two rules below the ones added by default by UFW:

```bash
-A ufw-before-input -p tcp -m tcp ! --tcp-flags FIN,SYN,RST,ACK SYN -m conntrack --ctstate NEW -j ufw-logging-deny
-A ufw-before-input -p tcp -m tcp ! --tcp-flags FIN,SYN,RST,ACK SYN -m conntrack --ctstate NEW -j DROP
```
![Command output](./images/w8-fw-invalid-packets-rule.jpg)

Don't forget to add them to the before6.rules file as well:

```bash
-A ufw6-before-input -p tcp -m tcp ! --tcp-flags FIN,SYN,RST,ACK SYN -m conntrack --ctstate NEW -j ufw6-logging-deny
-A ufw6-before-input -p tcp -m tcp ! --tcp-flags FIN,SYN,RST,ACK SYN -m conntrack --ctstate NEW -j DROP
```

Reference: [ivansalloum.com](https://ivansalloum.com/how-to-block-invalid-packets-with-ufw/)

#### Block ping (ICMP) requests

**What is ICMP flood attack?**

An ICMP flood attack, also known as a ping flood, is a type of Denial of Service (DoS) attack. It overwhelms a system by sending a huge number of ICMP packets, specifically echo requests (pings).

**How to prevent ICMP requests**

By commenting the lines below to block ping requests (icmp protocol) by ufw on file /etc/ufw/before.rules:

```bash
# ok icmp codes for INPUT
-A ufw-before-input -p icmp --icmp-type destination-unreachable -j ACCEPT
-A ufw-before-input -p icmp --icmp-type time-exceeded -j ACCEPT
-A ufw-before-input -p icmp --icmp-type parameter-problem -j ACCEPT
-A ufw-before-input -p icmp --icmp-type echo-request -j ACCEPT

# ok icmp code for FORWARD
-A ufw-before-forward -p icmp --icmp-type destination-unreachable -j ACCEPT
-A ufw-before-forward -p icmp --icmp-type time-exceeded -j ACCEPT
-A ufw-before-forward -p icmp --icmp-type parameter-problem -j ACCEPT
-A ufw-before-forward -p icmp --icmp-type echo-request -j ACCEPT
```
![Command output](./images/w8-fw-icmp-block.jpg)

Reference: [indusface.com](https://www.indusface.com/learning/icmp-flood-attacks/)

Reference: [andreyka26.com](https://andreyka26.com/how-to-block-ping-icmp-requests-using-ufw#edit-etcufwbeforerules)

Now, reload UFW:

```bash
sudo ufw reload
```

### Verification and Monitoring

```bash
# Display current rule status
sudo ufw status verbose

# Verify logging is working
sudo tail -f /var/log/ufw.log

# Test service accessibility
nc -zv [server-ip] 22
nc -zv [server-ip] 80
nc -zv [server-ip] 443
```
![Command output](./images/w8-fw-verification.jpg)

### Implementation Notes

#### Rule Order

- Rules are processed in order from top to bottom
- More specific rules are placed before general rules
- Default policies act as a catch-all for undefined traffic

#### Security Considerations

- All unnecessary ports remain closed by default
- Rate limiting on SSH prevents automated attacks
- SYN flood protection helps maintain service availability
- Logging enables security monitoring and incident response

#### Maintenance Requirements

- Regular log review required
- Update rules as service requirements change
- Monitor for unusual patterns in blocked traffic