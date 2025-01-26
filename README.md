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

Download [here](assets/assigment-2.md)

