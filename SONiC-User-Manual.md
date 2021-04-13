![](images/Broadcom.png)

<br>
<br>
<br>
# Broadcom SONiC 3.0.0 
### User Manual
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Table of Contents
=================
   * [SONiC USER MANUAL](#sonic-user-manual)
   * [Table of Contents](#table-of-contents)
   * [Introduction](#introduction)
   * [1 Quick Start Guide](#1-quick-start-guide)
      * [1.1 Download Image](#11-download-image)
         * [1.1.1 Installation Over The Network](#111-installation-over-the-network)
            * [1.1.1.1 Install SONiC ONIE Image](#1111-install-sonic-onie-image)
         * [1.1.2 Installation using a USB Thumb Drive](#112-installation-using-a-usb-thumb-drive)
   * [2 Login Username &amp; Password](#2-login-username--password)
      * [2.1 Default Login](#21-default-login)
      * [2.2 Configuring Username &amp; Password](#22-configuring-username--password)
      * [2.3 How to reset Password ](#23-how-to-reset-password)      
   * [3 Basic Configuration &amp; Show](#3-basic-configuration--show)
      * [3.1 Configuring Management Interface and Loopback Interface](#31-configuring-management-interface-and-loopback-interface)
      * [3.2 Software version &amp; Upgrade](#32-software-version--upgrade)
         * [3.2.1 Show Versions](#321-show-versions)
      * [3.2.2 Check features available in this version](#322-check-features-available-in-this-version)
      * [3.2.3 Upgrade Or Downgrade Version](#323-upgrade-or-downgrade-version)
         * [3.2.3.1 SONiC Installer](#3231-sonic-installer)
      * [3.3 Startup Configuration](#33-startup-configuration)
         * [3.3.1 Default Startup Configuration](#331-default-startup-configuration)
         * [3.3.2 Modify Configuration](#332-modify-configuration)
            * [3.3.2.1 Modify config_db.json](#3321-modify-config_dbjson)
            * [3.3.2.2 Modify minigraph.xml](#3322-modify-minigraphxml)
         * [3.3.3 Zero Touch Provisioning](#333-zero-touch-provisioning)
         * [3.3.4 Erasing Configuration](#334-erasing-configuration)
   * [4 Detailed Configuration &amp; Show](#4-detailed-configuration--show)
      * [4.2 Links to Different Configuration Sections](#42-links-to-different-configuration-sections)
   * [5 Example Configuration](#5-example-configuration)
   * [6 Troubleshooting](#6-troubleshooting)
      * [6.1 Basic Troubleshooting Commands](#61-basic-troubleshooting-commands)
      * [6.2 Port up/down Troubleshooting](#62-port-updown-troubleshooting)
      * [6.3 Investigating Packet Drops](#63-investigating-packet-drops)
      * [6.4 Physical Link Signal](#64-physical-link-signal)
      * [6.5 Isolate SONiC Device from the Network](#65-isolate-sonic-device-from-the-network)
      * [6.6 NAT troubleshooting](#66-nat-troubleshooting)
      * [6.7 Orchagent troubleshooting](#67-Orchagent-troubleshooting)
      * [6.8 IGMP Snooping troubleshooting](#igmp-snooping-troubleshooting)
      * [6.9 PIM and IGMP troubleshooting](#69-pim-troubleshooting)
      * [6.10 Multicast Converged OIF (PIM & IGMP Snooping) troubleshooting](#610-multicast-converged-oif-troubleshooting)
      * [6.11 System Resource Monitoring](#610-system-resource-monitoring)
      * [6.12 Docker Resource Limits](#611-docker-resource-limits)
      * [6.13 Kernel Crash troubleshooting](#612-kernel-crash-troubleshooting)
      * [6.14 Thermal Monitoring](#613-thermal-monitoring)
      * [6.15 Hardware Watchdog Timer](#613-hardware-watchdog-timer)
   * [7 Common Framework Development & Usage](#7-common-framework-development-usage)
      * [7.1 Debug Framework](#71-debug-framework)
   * [8 Chef](#8-chef)
   * [9 SSH-server vrf](#9-ssh-server-vrf)
   * [10 Syslog Messages Forwarding](#10-syslog-messages-forwarding)

# Document History

| # | Date    |  Document Version | Details |
| --- | --- | --- | --- |
| 1 |  Sep 20, 2019 |v1 | Initial version |
| 2 |  Nov 22, 2019 |v2 | Broadcom SONiC 2.1.0 Release |
| 3 |  Feb 16, 2020 |v3 | Broadcom SONiC 3.0.0 Release |
| 4 |  Mar 31, 2021 |v4 | Broadcom SONiC 3.2.0 Release |


<br>
<br>
# Introduction
Broadcom SONiC network operating system is based on Linux that runs on switches from multiple vendors and ASICs. Broadcom SONiC offers a full-suite of network functionalities, like BGP, VRRP, PVST, VRF, BFD and more. 

SONiC software shall be loaded in supported devices and this user guide explains the basic steps for using the SONiC in those platforms.

Connect the console port of the device and use the 115200 baud rate to access the device. Follow the instructions in this document to boot the device in ONIE mode and install the SONiC software using the steps specified in the document and reboot the device. In some devices that are pre-loaded with SONiC software, this step can be skipped. 
Users shall use the default username/password "admin/YourPaSsWoRd" to login to the device through the console port.

After logging into the device, SONiC software can be configured in following three methods.
 1) Command Line Interface
 2) config_db.json 
 3) minigraph.xml

Users can use all of the above methods or choose either one method to configure and to view the status of the device.
This user manual explains the common commands & related configuration/show examples on how to use the SONiC device. Refer the above documents for more detailed information.


**Scope Of The Document**  

This manual provides some insights on the following:
1) First section explains how to load the SONiC image on the supported platforms
2) Next section explains how to login using default username & password, how to change password, how to configure management interface & loopback address configuration.
3) Next section how to check the software version running on the device, how to check the list of features available in this software version, how to upgrade to new software version, etc.,
4) Next section explains how to check the default startup configuration with which the device is currently running, how to load a new configuration to this device, etc., 
5) Next section explains how to check the interface/link/port status, basic cable connectivity status, port speed, etc.,
6) Next section provides the required web links to the corresponding documents (and sections) that explains the steps to configure "Interface","BGP", "ACL","COPP", "Mirroring", etc.,
7) Next section gives an example configuration for T0 topology
8) Next section gives basic information about troubleshooting and it provides the link to the detailed troubleshooting guide. 

Note that some parts of this document might be a repetition of few commands/paragraphs from other configuration documents (like "Command Reference", "Config DB Manual", "Troubleshooting Guide", etc.,). Refer those documents for detailed information.


# 1 Quick Start Guide 
This guide details the steps to install a SONiC image on your supported switch. 

## 1.1 Download Image

You can download SONiC Image from Broadcom docSAFE portal. 

Once the image is available in your local machine, the image can be installed via ONIE either by installing over the network or using a USB thumb drive as given in following sub-sections.
In case if the device is already preloaded with SONiC image, the device can be booted without the installation process.


### 1.1.1 Installation Over The Network
This sub-section explains how to transfer the image from remote server into the device and install it.

#### 1.1.1.1 Install SONiC ONIE Image

1. Connect to switch via serial console.

  **Note**: The default console baud rate for supported platforms are 115200. You may need to change the baud rate in case you cannot see anything from the console after reboot.

1. (Optional) Some switches may come with a NOS which will require you to uninstall the existing NOS first before you install SONiC. To do so, simply boot into ONIE and select `Uninstall OS`:

    ```
                         GNU GRUB  version 2.02~beta2+e4a1fe391
     +----------------------------------------------------------------------------+
     |*ONIE: Install OS                                                           | 
     | ONIE: Rescue                                                               |
     | ONIE: Uninstall OS  <----- Select this one                                 |
     | ONIE: Update ONIE                                                          |
     | ONIE: Embed ONIE                                                           |
     +----------------------------------------------------------------------------+

          Use the ^ and v keys to select which entry is highlighted.          
          Press enter to boot the selected OS, `e' to edit the commands       
          before booting or `c' for a command-line.                           
    ```


1. Reboot the switch into ONIE and select `Install OS`:

    ```
                         GNU GRUB  version 2.02~beta2+e4a1fe391
     +----------------------------------------------------------------------------+
     |*ONIE: Install OS    <----- Select this one                                 | 
     | ONIE: Rescue                                                               |
     | ONIE: Uninstall OS                                                         |
     | ONIE: Update ONIE                                                          |
     | ONIE: Embed ONIE                                                           |
     +----------------------------------------------------------------------------+

          Use the ^ and v keys to select which entry is highlighted.          
          Press enter to boot the selected OS, `e' to edit the commands       
          before booting or `c' for a command-line.                           
    ```

1. Install SONiC. Here, we assume you have uploaded SONiC image onto a http server (`192.168.2.10`). Once you are in ONIE, you can first configure a management IP (`192.168.0.2/24`)and default gateway (`192.168.0.1`) for your switch, and then install the SONiC image from the http server. 

    ```
    ONIE:/ # ifconfig eth0 192.168.0.2 netmask 255.255.255.0
    ONIE:/ # ip route add default via 192.168.0.1
    ONIE:/ # onie-nos-install http://192.168.2.10/sonic-broadcom.bin
    ```

  **Note:** There are many options to install SONiC ONIE image on a ONIE-enabled switch. For more installation options, visit the [ONIE Quick Start Guide](https://github.com/opencomputeproject/onie/wiki/Quick-Start-Guide).

When NOS installation finishes, the box will reboot into SONiC by default.

```
                    GNU GRUB  version 2.02~beta2+e4a1fe391

+----------------------------------------------------------------------------+
|*SONiC-OS-7069cef                                                           | 
| ONIE                                                                       | 
+----------------------------------------------------------------------------+
```

### 1.1.2 Installation using a USB Thumb Drive

This sub-section explains how to transfer the SONiC image to an USB thumb drive and install it using ONIE on the device

To install via USB simply copy the installer image (For eg., sonic-broadcom-cloud-base.bin) to the root directory of the USB thumb drive, using the file name onie-installer.

For the purposes of this guide, assume the NOS installer image is named sonic-broadcom.bin. Let us assume the USB drive shows up at /dev/sdd1 under Linux (it might be different on your system and OS).

Copy the installer file to the root of the USB thumb drive like this:

```
linux:~$ sudo mkdir /mnt/usb
linux:~$ sudo mount /dev/sdd1 /mnt/usb
linux:~$ sudo cp sonic-broadcom-cloud-base.bin /mnt/usb/onie-installer
linux:~$ sudo umount /mnt/usb
```
Now remove the USB drive from your computer and insert it into the USB port on the front (or rear) panel of your ONIE enabled device. Power on the device. 

On Reboot, follow the steps mentioned above to boot into "ONIE" menu, and select "ONIE: Install OS" option.  Device will boot into ONIE, and ONIE will discover the onie-installer file on the root of the USB drive and execute it. 

When NOS installation finishes, the box will reboot into SONiC by default.

# 2 Login Username & Password
This section explains the default username & password and how to change the password

## 2.1 Default Login

All SONiC devices support both the serial console based login and the SSH based login by default.
The default credential (if not modified at image build time) for login is admin/YourPaSsWoRd.
In case of SSH login, users can login to the management interface (eth0) IP address after configuring the same using serial console. 
Refer the next section for configuring the IP address for management interface.

The default user is forced to change the default login password when attempting to login for the first time. The user will have to provide
the default login password followed by a new password and also confirm the new password. The user is prompted for password change until the password has been successfully modified.
This is applicable for both console based login and the SSH based login. When a password change prompt is processed on SSH based login,
the user is disconnected after a successful password change. The user can then SSH login again using the new password.

  - Example First time login password change prompt:
  ```
Debian GNU/Linux 9 sonic ttyS2

sonic login: admin
Password:
You are required to change your password immediately (root enforced)
Changing password for admin.
(current) UNIX password:YourPaSsWoRd
Enter new UNIX password:newpassword
Retype new UNIX password:newpassword
  ```

For subsequent logins to the SONiC switch, the login event takes the user to default prompt.

  - Example:
  ```
  At Console:
  Debian GNU/Linux 9 sonic ttyS1

  sonic login: admin
  Password: YourPaSsWoRd

  SSH from any remote server to sonic can be done by connecting to SONiC IP
  user@debug:~$ ssh admin@sonic_ip_address(or SONIC DNS Name)
  admin@sonic's password:
  ```

By default, login takes the user to the default prompt from which all the show commands can be executed.  

On successful login, SONiC Welcome Message of the Day shall be printed as follows.

```
You are on
  ____   ___  _   _ _  ____
 / ___| / _ \| \ | (_)/ ___|
 \___ \| | | |  \| | | |
  ___) | |_| | |\  | | |___
 |____/ \___/|_| \_|_|\____|

-- Software for Open Networking In the Cloud --

Unauthorized access and/or use are prohibited.
All access and/or use are subject to monitoring.

admin@sonic:~$ 
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

## 2.2 Configuring Username & Password  

There is no separate CLI for adding users and for changing passwords. 
Users shall use the linux commands "useradd" command to add new users.
Users shall use the linux command "passwd <username>" to change the password for the specific username.
	
## 2.3 How to reset Password  

This TSG gives the instruction of how to reset a SONiC switch password.

1. Edit Grub boot menu options
1.1 First you need to get into grub menu options. This menu is displayed right at the beginning of the boot.  You should get something similar to this, but not the exactly the same. 
Choose the choice Start with SONiC-:
    ![](https://github.com/Azure/SONiC/blob/master/images/PW-1.png)

1.2 Now we attempt to edit grub's boot option. Press "e" to edit the first grub menu option and navigate to kernel line:
 ![](https://github.com/Azure/SONiC/blob/master/images/PW-2.png)

1.3 Remove quiet and add  init=/bin/bash
 ![](https://github.com/Azure/SONiC/blob/master/images/PW-3.png)

1.4 Press Ctrl-x to boot

2. Remount / and /proc
2.1 After successfully boot you will be presented with bash command prompt:
 ![](https://github.com/Azure/SONiC/blob/master/images/PW-4.png)

```
mount -o remount,rw / 
mount -o remount,rw /proc
```

3 Reset password
3.1 To reset an actual password is now simple as typing :
`passwd admin`

  ![](https://github.com/Azure/SONiC/blob/master/images/PW-5.png)

```
sync
sudo reboot -f
```





# 3 Basic Configuration & Show

SONiC is managing configuration in a single source of truth - a redisDB instance that we refer as ConfigDB. Applications subscribe to ConfigDB and generate their running configuration correspondingly.

Please refer to the ConfigDB Manual for details about ConfigDB and schema design.

SONiC includes commands that allow user to show platform, transceivers, L2, IP, BGP status, etc.

Please refer to the Command Reference Manual for details.

Note that all the configuration commands need root privileges to execute them and the commands are case-sensitive.
Show commands can be executed by all users without the root privileges.
Root privileges can be obtained either by using "sudo" keyword in front of all config commands, or by going to root prompt using "sudo -i".


## 3.1 Configuring Management Interface and Loopback Interface

The management interface (eth0) in SONiC is configured (by default) to use DHCP client to get the IP address from the DHCP server. Connect the management interface to the same network in which your DHCP server is connected and get the IP address from DHCP server.
The IP address received from DHCP server can be verified using the "/sbin/ifconfig eth0" linux command.

SONiC does not provide a CLI to configure the static IP for the management interface. There are few alternate ways by which a static IP address can be configured for the management interface.  
   1) use "ifconfig eth0" linux command (example: ifconfig eth0 10.11.12.13/24). This configuration won't be preserved across reboot.
      - Example:
   ```
   admin@sonic:~$ /sbin/ifconfig eth0 10.11.12.13/24
   ```
Note that SONiC does not support management VRF and hence it is not possible to differentiate data traffic and management traffic. Work is in progress to support the mgmtVRF in Aug2019 release. 

   2) use config_db.json and configure the MGMT_INTERFACE key with the appropriate values. Refer [here](https://github.com/Azure/SONiC/wiki/Configuration#Management-Interface) 


   Add the following example configuration in a file (ex: mgmt_ip.json) and load it as follows.
   ```
   "MGMT_INTERFACE": {
        "eth0|10.11.12.13/24": {
            "gwaddr": "10.11.12.1"
        }
   }
   ```
   NOTE: If the interface IP address and default gateway were already present, users should remove them before loading the above configuration.

   Users can use the "show runningconfiguration all" to check the already configured MGMT_INTERFACE. Or, users can use the "redis-cli" command as follows to check the same.
```
   root@T1-2:/etc/sonic# redis-cli -n 4 keys "MGMT_INTERFACE*"
	1) "MGMT_INTERFACE|eth0|10.20.30.40/24"
```
   In the above redis-cli command example, it gets the keys starting with MGMT_INTERFACE and it displays the already configured MGMT_INTERFACE in the CONFIG_DB.
   To remove this key from CONFIG_DB, users shall use the following redis-cli command.
```   
   redis-cli -n 4 DEL "MGMT_INTERFACE|eth0|10.20.30.40/24"
```
   After removing the key, users can load the new configuration using "config load mgmt_ip.json" command and then do "systemctl restart interfaces-config" to make it effective. Users shall verify the configured management interface IP address value using "ifconfig" linux command.
      

   3) use minigraph.xml and configure "ManagementIPInterfaces" tag inside "DpgDesc" tag as given at the [page](https://github.com/Azure/SONiC/wiki/Configuration-with-Minigraph-(~Sep-2017))

Once the IP address is configured, the same can be verified using "/sbin/ifconfig eth0" linux command.
Users can SSH login to this management interface IP address from their management network.

  - Example:
   ```
   admin@sonic:~$ /sbin/ifconfig eth0
   eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
         inet 10.11.11.13  netmask 255.255.255.0  broadcast 10.11.12.255
   ```

The same method shall be used to configure the loopback interface address as follows.
1) "/sbin/ifconfig lo" linux command shall be used, OR,
2) Add the key LOOPBACK_INTERFACE & value in config_db.json and load it, OR,
3) use minigraph.xml and configure LoopbackIPInterfaces tag inside the "DpgDesc" tag.


Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)



## 3.2 Software version & Upgrade  
This section explains how to check the current version of the software running in the device, how to check the features available in the version and how to upgrade/downgrade to different versions.

### 3.2.1 Show Versions 

**show version**  
This command displays software component versions of the currently running SONiC image. This includes the SONiC image version as well as Docker image versions.
This command displays relevant information as the SONiC and Linux kernel version being utilized, as well as the commit-id used to build the SONiC image. The second section of the output displays the various docker images and their associated IDs. 

- Usage:  
  show version  

- Example:
  ```
  admin@sonic:~$ show version
  SONiC Software Version: SONiC-OS-HEAD.32-21ea29a
  Distribution: Debian 9.8
  Kernel: 4.9.0-8-amd64
  Build commit: 21ea29a
  Build date: Fri Mar 22 01:55:48 UTC 2019
  Built by: johnar@jenkins-worker-4

  Docker images:
  REPOSITORY                 TAG                 IMAGE ID            SIZE
  docker-syncd-brcm          HEAD.32-21ea29a     434240daff6e        362MB
  docker-syncd-brcm          latest              434240daff6e        362MB
  docker-orchagent-brcm      HEAD.32-21ea29a     e4f9c4631025        287MB
  docker-orchagent-brcm      latest              e4f9c4631025        287MB
  docker-lldp-sv2            HEAD.32-21ea29a     9681bbfea3ac        275MB
  docker-lldp-sv2            latest              9681bbfea3ac        275MB
  docker-dhcp-relay          HEAD.32-21ea29a     2db34c7bc6f4        257MB
  docker-dhcp-relay          latest              2db34c7bc6f4        257MB
  docker-database            HEAD.32-21ea29a     badc6fc84cdb        256MB
  docker-database            latest              badc6fc84cdb        256MB
  docker-snmp-sv2            HEAD.32-21ea29a     e2776e2a30b7        295MB
  docker-snmp-sv2            latest              e2776e2a30b7        295MB
  docker-teamd               HEAD.32-21ea29a     caf957cd2ad1        275MB
  docker-teamd               latest              caf957cd2ad1        275MB
  docker-router-advertiser   HEAD.32-21ea29a     b1a62023958c        255MB
  docker-router-advertiser   latest              b1a62023958c        255MB
  docker-platform-monitor    HEAD.32-21ea29a     40b40a4b2164        287MB
  docker-platform-monitor    latest              40b40a4b2164        287MB
  docker-fpm-quagga          HEAD.32-21ea29a     546036fe6838        282MB
  docker-fpm-quagga          latest              546036fe6838        282MB

  ```
Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

## 3.2.2 Check features available in this version

Please consult the Release notes for information on the features available in this version of the software.

## 3.2.3 Upgrade Or Downgrade Version 

SONiC software can be installed in two methods, viz, using "ONIE Installer" or by using "sonic_installer tool".
"ONIE Installer" shall be used as explained in the [QuickStartGuide](#Quick-Start-Guide)
"sonic_installer" shall be used as given below.


### 3.2.3.1 SONiC Installer
This is a command line tool available as part of the SONiC software; If the device is already running the SONiC software, this tool can be used to install an alternate image in the partition.
This tool has facility to install an alternate image, list the available images and to set the next reboot image.

**sonic_installer install**  

This command is used to install a new image on the alternate image partition.  This command takes 
 - A path to an installable SONiC image, or
 - URL of the image, or
 - Path of the image on a remote scp/sftp server, where the server address, protocol, username and password can be provided using the command line arguments

After a successful installation, the new image is marked as the image that shall be used in next reboot. SONiC configuration files are also copied to a backup directory and
restored when the switch reboots to the newly installed image version.
  - Following configuration files are migrated as part of the install command:
    - Saved ConfigDB (/etc/sonic/config_db.json)
    - FRR configuration (/etc/sonic/frr/*)
    - Minigraph schema (/etc/sonic/minigraph.xml)
    - SNMP Configuration (/etc/sonic/snmp.yml)
    - ACL configuration (/etc/sonic/acl.json)
    - Linux users added and corresponding passwords
    - Linux user groups added

  - Usage:    
    sonic_installer install <path>  


- Example:
  ```	 
  admin@sonic:~$ sonic_installer install https://sonic-jenkins.westus.cloudapp.azure.com/job/xxxx/job/buildimage-xxxx-all/xxx/artifact/target/sonic-xxxx.bin
  New image will be installed, continue? [y/N]: y
  Downloading image...
  ...100%, 480 MB, 3357 KB/s, 146 seconds passed
  Command: /tmp/sonic_image
  Verifying image checksum ... OK.
  Preparing image archive ... OK.
  ONIE Installer: platform: XXXX
  onie_platform: 
  Installing SONiC in SONiC
  Installing SONiC to /host/image-xxxx
  Directory /host/image-xxxx/ already exists. Cleaning up...
  Archive:  fs.zip
     creating: /host/image-xxxx/boot/
    inflating: /host/image-xxxx/boot/vmlinuz-3.16.0-4-amd64  
    inflating: /host/image-xxxx/boot/config-3.16.0-4-amd64  
    inflating: /host/image-xxxx/boot/System.map-3.16.0-4-amd64  
    inflating: /host/image-xxxx/boot/initrd.img-3.16.0-4-amd64  
     creating: /host/image-xxxx/platform/
   extracting: /host/image-xxxx/platform/firsttime  
    inflating: /host/image-xxxx/fs.squashfs  
    inflating: /host/image-xxxx/dockerfs.tar.gz  
  Log file system already exists. Size: 4096MB
  Installed SONiC base image SONiC-OS successfully

  Command: cp /etc/sonic/minigraph.xml /host/

  Command: grub-set-default --boot-directory=/host 0

  Command: config-setup backup
  Taking backup of curent configuration

  Done
  ```

  ```
  admin@sonic:~$ sudo sonic_installer install --protocol scp --server 10.175.121.155 --username admin /home/admin/sonic-img/sonic-broadcom.bin
  New image will be installed, continue? [y/N]: y
  password:
  Downloading image...
  ...99%, 2 M101 KB 0 seconds left...
  Command: /tmp/sonic_image
  Verifying image checksum ... OK.
  Preparing image archive ... OK.
  Installing SONiC in SONiC
  ONIE Installer: platform: xxxx
  onie_platform: xxxx
  Installing SONiC to /host/image-xxxx
  Directory /host/image-xxxx/ already exists. Cleaning up...
  Archive:  fs.zip
     creating: /host/image-xxxx/boot/
    inflating: /host/image-xxxx/fs.squashfs
  Installed SONiC base image SONiC-OS successfully

  Command: grub-set-default --boot-directory=/host 0

  Command: config-setup backup
  Taking backup of curent configuration

  Command: sync;sync;sync

  Command: sleep 3

  Done
  ```

  ```
  admin@sonic:~$ sudo sonic_installer install --protocol sftp --server 10.59.132.53 --username root /root/sonic-broadcom.bin

  New image will be installed, continue? [y/N]: y
  password:
  Downloading image...

  ...1%, 27 MB, 27840 KB/s, 50 seconds left...
  ...99%, 2 M101 KB 0 seconds left...
  Command: /tmp/sonic_image
  Verifying image checksum ... OK.
  Preparing image archive ... OK.
  Installing SONiC in SONiC
  ONIE Installer: platform: xxxx
  onie_platform: xxxx
  Installing SONiC to /host/image-xxxx
  Directory /host/image-xxxx/ already exists. Cleaning up...
  Archive:  fs.zip
     creating: /host/image-xxxx/boot/
     creating: /host/image-xxxx/platform/x86_64-grub/
    inflating: /host/image-xxxx/platform/x86_64-grub/grub-pc-bin_2.02~beta3-5+deb9u2_amd64.deb
    inflating: /host/image-xxxx/fs.squashfs
  Installed SONiC base image SONiC-OS successfully

  Command: grub-set-default --boot-directory=/host 0

  Command: config-setup backup
  Taking backup of curent configuration

  Command: sync;sync;sync

  Command: sleep 3

  Done
  ```


**sonic_installer list**  

This command displays information about currently installed images. It displays a list of installed images, currently running image and image set to be loaded in next reboot.

  - Usage:  
    sonic_installer list

- Example:  
   ```
  admin@sonic:~$ sonic_installer list 
  Current: SONiC-OS-HEAD.XXXX
  Next: SONiC-OS-HEAD.XXXX
  Available: 
  SONiC-OS-HEAD.XXXX
  SONiC-OS-HEAD.YYYY
  ```

**sonic_installer set_default**  

This command is be used to change the image which can be loaded by default in all the subsequent reboots.

  - Usage:  
    sonic_installer set_default <image_name>

- Example:
  ```   
  admin@sonic:~$ sonic_installer set_default SONiC-OS-HEAD.XXXX
  ```

**sonic_installer set_next_boot**  

This command is used to change the image that can be loaded in the *next* reboot only. Note that it will fallback to current image in all other subsequent reboots after the next reboot.

  - Usage:  
    sonic_installer set_next_boot <image_name>

- Example:
  ```
  admin@sonic:~$ sonic_installer set_next_boot SONiC-OS-HEAD.XXXX
  ```

**sonic_installer remove**  

This command is used to remove the unused SONiC image from the disk. Note that it's *not* allowed to remove currently running image.

  - Usage:  
    sonic_installer remove <image_name>

- Example:
  ```
  admin@sonic:~$ sonic_installer remove SONiC-OS-HEAD.YYYY
  Image will be removed, continue? [y/N]: y
  Updating GRUB...
  Done
  Removing image root filesystem...
  Done
  Command: grub-set-default --boot-directory=/host 0

  Image removed
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Software-Installation-Commands)



## 3.3 Startup Configuration

This section explains how to check the default startup configuration with which the device is currently running and how to load a new configuration to this device.

### 3.3.1 Default Startup Configuration 

Users shall use the "show runningconfiguration" command to check the current running configuration. 
If users had not done any configuration change after the reboot, this will be same as the default startup configuration.
SONiC device contains the startup configuration in the file /etc/sonic/config_db.json. During reboot, this configuration will be loaded by default. 
Following are some of the keys that are configured by default in the config_db.json.
1) DEVICE_METADATA
2) MAP_PFC_PRIORITY_TO_QUEUE
3) QUEUE
4) PORT
5) CRM
6) NTP_SERVER
7) BUFFER_QUEUE
8) WRED_PROFILE
9) TC_TO_PRIORITY_GROUP_MAP
10) BUFFER_PROFILE
11) DEVICE_NEIGHBOR
12) TC_TO_QUEUE_MAP
13) CABLE_LENGTH
14) SCHEDULER
15) BUFFER_POOL


SONiC provides an alternate method for loading the startup configuration from minigraph.xml from a remote server when DHCP is used. SONiC contains a file /etc/sonic/updategraph.conf that contains a flag "enabled" which is set to "false" by default. Similarly, management interface is configured to use DHCP by default for getting the management interface IP address from the DHCP server. Users can modify this flag to "true" and then reboot the device. SONiC will use DHCP to get the management IP address as well as the details about the configuration file minigraph.xml (DHCP server should have been configured to provide the details like management interface IP address, default route, configuration file name and the server IP address from this the configuration file should be fetched). SONiC shall contact the remote server and get the minigraph.xml and loads the same.


### 3.3.2 Modify Configuration  

#### 3.3.2.1 Modify config_db.json  
Users can directly edit & modify the file /etc/sonic/config_db.json or do a SCP and copy this file from a remote server. 
User can either do "config reload" command to load this new configuration, or users can simply reboot to make it effective.


#### 3.3.2.2 Modify minigraph.xml  

Users can directly edit & modify the file /etc/sonic/mingraph.xml or do a SCP and copy this file from a remote server. 
User can either do "config load_minigraph" command to load this new configuration, or users can simply reboot to make it effective.
Or, users can modify the "enabled" flag in /etc/sonic/updategraph.conf to true and then reboot the device as explained above.

### 3.3.3 Zero Touch Provisioning

The Zero Touch Provisioning (ZTP) service is used by switch administrators to configure a fleet of switches using common configuration templates. Switches booting from a factory default state should be able to communicate with a remote provisioning server, download  relevant configuration files and scripts to kick start more complex configuration steps. The SONiC ZTP service processes this provisioning information in the form of a JSON file called ZTP JSON file.


#### SONiC ZTP Service

On a switch bootup, the ZTP service starts and checks for the presence of the startup configuration file */etc/sonic/config_db.json*.  If the startup configuration file is not found on disk, the ZTP service creates a temporary switch configuration to perform DHCP discovery in order to receive information about the location of the ZTP JSON file. If the ZTP admin mode is disabled, the ZTP service exits and the switch proceeds to boot with factory default configuration.



The DHCP discovery is performed on all connected in-band interfaces and the out-of-band management interface. Also, both DHCPv4 and DHCPv6 address discovery are performed on all the interfaces. The first interface that receives a valid DHCP offer is used to determine the URL specifying the location of the ZTP JSON file to be downloaded from. At the DHCP server end, the DHCP offer must include the DHCPv4 Option 67 or the DHCPv6 Option 59 to specify the ZTP JSON file URL.



If a ZTP JSON file is already present on the switch,  the ZTP service continues to process it and does not download a new ZTP JSON. This allows the ZTP service to perform configuration steps which may involve multiple switch reboots.



The SONiC ZTP service exits only after a ZTP JSON file is downloaded and processed by it. After processing the ZTP JSON file, if the startup configuration file  */etc/sonic/config_db.json* is not found, the ZTP service restarts DHCP discovery to obtain a new ZTP JSON file and start processing it.

At any given point of time, the user can choose to stop a SONiC ZTP operation by executing the *config ztp disable* command. The disable action creates a factory default configuration and saves it as the switch startup configuration.  The switch continues to operate with the loaded factory default configuration.

More details about the SONiC ZTP service exit criteria and configuration options to change the default behavior can be found in the [SONiC ZTP design document](https://github.com/Azure/SONiC/blob/master/doc/ztp/ztp.md).



#### SONiC ZTP Flow Diagram

Below flow diagram pictorially explains in detail the sequence of events performed by a SONiC switch up on bootup.



![](images\sonic_config_flow.png)



#### ZTP JSON

SONiC consists of many pre-installed software modules that are part of the default image. Some of these modules are network protocol applications (e.g FRR) and others provide support services (e.g syslog, DNS). The data and logic to configure various SONiC modules is encoded in a user defined input file in JSON format. This data file is the ZTP JSON file.

When a SONiC switch boots for the first time, the ZTP service checks if there is an existing ZTP JSON file. If no such file exists, DHCP Option 67 value is used to obtain the URL of the ZTP JSON file. The ZTP service then downloads the ZTP JSON file and processes it. If the DHCP Option 67 value is not available, the ZTP service waits forever till it is provided by the DHCP server. Other switch services including the SWSS service continue to boot.



Typically in a ZTP JSON, file multiple configuration sections are defined by the user. They are all enclosed within a ztp object. Following is an example ZTP JSON file that has four configuration sections *01-firmware*, *02-configdb-json*, *03-provisioning-script* and *04-connectivity-check* which are included as part of the value of *ztp* object. The top level ztp object also has other objects which specify the progress of whole the ZTP service and also input to the ZTP service on how to process the ZTP JSON file. In a ZTP JSON file it is mandatory to have a *ztp* object defined as the first object and rest of the objects must appear nested under it. The ZTP service will not parse the JSON data if the top level *ztp* object is not found. Also the ZTP JSON file must be syntactically correct.

The configuration sections defined in the ZTP JSON file are processed in the lexical order of their names. So it is convenient if user can name them with a leading numerical number, like in the example, indicating the order in which they need to be executed.

In the example ZTP JSON file, the following steps are performed on first boot:

1. SONiC firmware image is downloaded and installed on to the switch. The switch is then automatically rebooted to start using the newly installed image
2. The startup configuration file config_db.json associated with the switch is downloaded and loaded. The  config_db.json is stored with the file name  *$hostname_config_db.json* at the webroot of the HTTP server with address 192.168.1.1. This allows the ZTP service to uniquely identify the configuration file associated with the switch.
3. A post-provisioning script post_install.sh is downloaded and executed. The switch is rebooted if post_install.sh script exits with successful exit code 0.
4. Post boot, connectivity check is performed by pinging hosts 10.1.1.1 and yahoo.com to verify connectivity

```json
{
  "ztp": {
    "01-firmware": {
      "install": {
        "url": "http://192.168.1.1/broadcom-sonic-v1.0.bin"
      }
    },
    "02-configdb-json": {
      "dynamic-url": {
        "source": {
          "prefix": "http://192.168.1.1/",
          "identifier": "hostname",
          "suffix": "_config_db.json"
        }
      }
    },
    "03-provisioning-script": {
      "plugin": {
        "url":"http://192.168.1.1/post_install.sh"
      },
      "reboot-on-success": true
    },
    "04-connectivity-check": {
      "ping-hosts": [ "10.1.1.1", "yahoo.com" ]
    }
  }
}
```


The ZTP JSON file provides a wide variety of options to the user to fine tune and extend configuration actions.  Please refer to the [SONiC ZTP design document](https://github.com/Azure/SONiC/blob/master/doc/ztp/ztp.md) for more details.

#### ZTP Status

The *show ztp status* command displays detailed information about current state of a ZTP session and also displays the current ZTP configuration of the switch. It displays information related to all the configuration sections defined in the ZTP JSON file that is currently being processed. It also displays information about the previous completed ZTP session. More details about the ztp commands can be found [here](Command-Reference.md#ztp-configuration-and-show-commands).


### 3.3.4 Erasing Configuration

Users can rollback the switch configuration to the factory default configuration state using the *config erase* command. It requires a reboot to complete the configuration erase operation. 

Following are available options provided to the user while using this command:
- **config erase** : This command deletes the startup configuration JSON file /etc/sonic/config_db.json and all other application configuration files in the /etc/sonic directory.
                   The management interface configuration in the startup configuration file is retained so that the user can access the
                   switch using the same management address along with other factory default configuration after the switch reboots.

- **config erase boot** : This command deletes the startup configuration JSON file and all other application configuration files in the /etc/sonic directory.
                        The management interface configuration in the startup configuration JSON file is also removed.
                        The SONiC switch boots with a factory default configuration file.

- **config erase install** : This command removes all changes made by the user. All user installed packages and file changes are removed. It also deletes the startup configuration JSON file and the files in /etc/sonic directory. The SONiC switch is reverted to a state similar to a newly installed image. After the SONiC switch is rebooted, if the Zero Touch Provisioning (ZTP) feature is enabled, the SONIC switch will start performing ZTP to discover and download the switch configuration.

- **config erase cancel** : For the *config erase* command operation to take effect, the user has to reboot the switch after issuing the command. If the user wishes to not proceed with the configuration removal operation, the *config erase cancel* command can be used to undo the previously issued *config erase* command.


# 4 Detailed Configuration & Show  

Basic cable connectivity shall be verified by configuring the IP address for the ports and by using the "ping" test.

## 4.2 Links to Different Configuration Sections

| # | Module    |  CLI Link | ConfigDB Link |  Remarks |
| --- | --- | --- | --- | --- |
| 1 |  Interface |[Interface CLI](Command-Reference.md#Interface-Configuration-And-Show-Commands) | [Interface ConfigDB](Configuration.md)| To view the details about the interface |
| 2 |  BGP |[BGP CLI](Command-Reference.md#BGP-Configuration-And-Show-Commands) | [BGP ConfigDB](Configuration.md)| To view the details about the BGP |
| 3 |  ACL |[ACL CLI](Command-Reference.md#ACL-Configuration-And-Show) | [ACL ConfigDB](Configuration.md)| To view the details about the ACL |
| 4 |  COPP |[COPP CLI](Command-Reference.md#copp-configuration-and-show-commands) | [COPP ConfigDB](Configuration.md)| To view the details about the COPP |
| 5 |  Mirroring |[Mirroring CLI](Command-Reference.md#Mirroring-Configuration-And-Show) | [Mirroring ConfigDB](Configuration.md)| To view the details about the Mirroring |
| 6 | BFD |[BFD CLI](Command-Reference.md#bfd) | [BFD ConfigDB](Configuration.md) | To view the details about the BFD |
| 7 | STP |[STP CLI](Command-Reference.md##spanning-tree) | [STP ConfigDB](Configuration.md) | To view the details about the STP |
| 8 | VRF |[VRF CLI](Command-Reference.md##vrf) | [VRF ConfigDB](Configuration.md) | To view the details about the VRF |
| 9 | VRRP |[VRRP CLI](Command-Reference.md##vrrp) | [VRRP ConfigDB](Configuration.md) | To view the details about the VRRP |
| 10 | ZTP |[ZTP CLI](Command-Reference.md#ztp-configuration-and-show-commands) | N/A | To view the details about the ZTP |
| 11 | NAT |[NAT CLI](Command-Reference.md#nat-configuration-and-show-commands) | [NAT ConfigDB](Configuration.md) | To view the config schema details for NAT |
| 12 | Error handling |[Error handling CLI](Command-Reference.md#error-handling-framework-configuration-and-show-commands) | N/A | To view the details about the Error handling framework |
| 13 | CRM |[CRM CLI](Command-Reference.md#crm-configuration-and-show-commands) | N/A | To view the details about the CRM |
| 14 | PFC |[PFC CLI](Command-Reference.md#pfc-configuration-and-show-commands) | N/A | To view the details about the PFC |
| 15 | IGMP Snooping |[IGMP Snooping CLI](Command-Reference.md#pfc-configuration-and-show-commands) | [IGMP Snooping ConfigDB](Configuration.md) | To view the details about the IGMP Snooping |
| 16 | OSPFv2 | [OSPFv2 CLI](Command-Reference.md#OSPFv2-configuration-and-show-commands) | N/A | To view the details about the OSPFv2 |
| 17 | PIM |[PIM CLI](Command-Reference.md#pim-source-specific-multicast) | N/A | To view the details about PIM |
| 18 | KDUMP |[ KDUMP CLI](Command-Reference.md#kdump-configuration-and-show-commands) | [KDUMP ConfigDB](ConfigDB-Manual.md#kdump) | To view the details about the KDUMP feature|
| 19 | UDLD |[UDLD CLI](Command-Reference.md##udld) | [UDLD ConfigDB](Configuration.md) | To view the details about UDLD feature |
| 20 | IP Unnumbered |[IP Unnumbered CLI](Command-Reference.md###ipv4-unnumbered-interface-commands) | [IP unnumbered ConfigDB](Configuration.md) | To view the details about IP unnumbered feature |
| 21 | VXLAN |[VXLAN CLI](Command-Reference.md#vxlan) | [VXLAN ConfigDB](ConfigDB-Manual.md#vxlan_tunnel) | To view the details about VXLAN feature |
| 22 | BUM Storm Control|[BUM Storm Control CLI](Command-Reference.md#BUM-Storm-Control-Configuration-and-Show-Commands) | [BUM Storm Control ConfigDB](ConfigDB-Manual.md#BUM-Storm-Control) | To view the details about BUM Storm Control feature |
| 23 | Qos Maps |[Qos Maps CLI](Command-Reference.md#qos-configuration-and-show) | [Qos Maps Config DB](ConfigDB-Manual.md#dot1p_to_tc_map) | To view the details about QoS Map feature |
| 24 | MCLAG |[MCLAG CLI](Command-Reference.md#mclag-configuration-and-show) | [MCLAG Config DB](ConfigDB-Manual.md#mclag) | To view the details about MCLAG feature |
| 25 | Dynamic Port Breakout |[Dynamic Port Breakout CLI](Command-Reference.md#dynamic-port-breakout-configuration-and-show) | [Dynamic Port Bbreakout Config DB](ConfigDB-Manual.md#dynamic-port-breakout) | To view the details about Dynamic Port Breakout feature |
| 26 | IP ECMP Loadshare and Symmetric hash |[IP ECMP Loadshare and Symmetric hash CLI](Command-Reference.md#ip_ecmp_loadshare_Symmetric_hash) | [IP ECMP Loadshare and Symmetric hash Config DB](ConfigDB-Manual.md#ip_ecmp_loadshare_Symmetric_hash) | To view the details about IP ECMP Loadshare and Symmetric hash feature |
| 27 | IP Sub-interface |[IP Sub-interface CLI](Command-Reference.md#ip_subinterfaces) | [IP Sub-interface Config DB](ConfigDB-Manual.md#ip_subinterfaces) | To view the details about IP Sub-interface feature |


# 5 Example Configuration

Refer the following links/files for the example configuration based on CLI, ConfigDB and Minigraph.

  1)  [Example CLI Configuration File](Example_CLI.md)
  2)  [Example T0 ConfigDB](T0_config_db.json)
  3)  [Example T0 Minigraph.xml](T0_minigraph.xml)


# 6 Troubleshooting

This section captures some of the frequenently used troubleshooting commands and methods.
Users can refer the [Troubleshooting Guide](Troubleshooting-Guide.md) for more details about troubleshooting.

## 6.1 Basic Troubleshooting Commands

Users shall use "show techsupport" to collect the information from the device, shall use syslog to view the syslogs printed by the services, shall use the linux utitlies like "ping", "tcpdump", etc., to check the connectivity and packet tracing.

**show techsupport**  
This command gathers pertinent information about the state of the device; information is as diverse as syslog entries, database state, routing-stack state, etc., It then compresses it into an archive file. This archive file can be sent to the SONiC development team for examination.
Resulting archive file is saved as `/var/dump/<DEVICE_HOST_NAME>_YYYYMMDD_HHMMSS.tar.gz`
Few details that the dump includes are given below: 
-	Interface details  
-	Platform  details
-	Machine.conf 
-	Vlan configs
-	Routes
-	Sensor , transceiver details 
-	Syslog
-	Ip configs 
-	Bgp details 
-	Device Configs  (json/ minigraph) 


  - Usage:  
    show techsupport


- Example:
  ```	
  admin@sonic:~$ show techsupport
  ```

**syslog**  

-	System logs and event messages  from all dockers are captured via rsyslog  and saved in /var/log/syslog 
-	Console logs can be viewed using "show logging" command also. This command prints the information in syslog in console .
-	Show logging -f  will tail the output of syslogs in  console/ssh session.
-	In addition, the INFO and DEBUG severity messages can now be viewed using show in-memory-logging command.
    "show in-memory-logging" will display system debug info log messages
    "show in-memory-logging -f" will tail the output of debug info syslogs in console/ssh session


**tcpdump**  

-	tcpdump is a common packet analyzer that runs under the sonic command line . It allows the user to display TCP/IP and other packets being transmitted or received over a network
ex: tcpdump -i Ethernet0 

## 6.2 Port up/down Troubleshooting  

All port related configuration done using CLI/ConfigDB/Minigraph are saved in the redis config database. Such configuration is handled by the appropriate modules and the result of such operation might be stored in the application database (APP_DB).
Once if the modules complete their operation, if the result needs to be programmed into the ASIC, same will be synchronized by syncd service and the result is stored in the ASIC_DB.

When user need to debug/troubleshoot any issue, the best is to verify all of these databases as explained below.
1) Check the configuration in the CONFIG_DB and status using "show" commands.
2) Check the application status of the application in APP_DB.
3) Check the ASIC related programming state and the status in ASIC_DB.
4) Check the actual ASIC.

1) How to check the configuration & status of ports

Following "show" commands can be used to check the port status.

-	Show interface status ( up/down)
-	Show interface transceiver  presence 

Following "redis-dump" command can be used to dump the port configuraiton from the ConfigDB.

Example : 
```
root@sonic-z9100-02:~# redis-dump -d 4 -k  "PORT|Ethernet4" -y
{
  "PORT|Ethernet4": {
    "type": "hash",
    "value": {
      "admin_status": "up",
      "alias": "fiftyGigE1/2/1",
      "description": "Servers1:eth0",
      "index": "2",
      "lanes": "53,54",
      "mtu": "9100",
      "pfc_asym": "off",
      "speed": "50000"
    }
  }
  
```

Following redis-dump can be used to check the port status in the APP_DB.

Example:
```
root@sonic-z9100-02:~# redis-dump -d 0 -k *PORT_TABLE:Ethernet62* -y
{
  "PORT_TABLE:Ethernet62": {
    "type": "hash",
    "value": {
      "admin_status": "down",
      "alias": "fiftyGigE1/16/2",
      "description": "fiftyGigE1/16/2",
      "index": "16",
      "lanes": "95,96",
      "mtu": "9100",
      "oper_status": "down",
      "pfc_asym": "off",
      "speed": "50000"
    }
  }
```

Following redis-dump can be used to check the port status in the ASIC_DB.

Example:
```
root@sonic-z9100-02:~# redis-dump -d 1 -k  "ASIC_STATE:SAI_OBJECT_TYPE_PORT:oid:0x1000000000014"  -y
{
  "ASIC_STATE:SAI_OBJECT_TYPE_PORT:oid:0x1000000000014": {
    "type": "hash",
    "value": {
      "NULL": "NULL",
      "SAI_PORT_ATTR_ADMIN_STATE": "true",
      "SAI_PORT_ATTR_INGRESS_ACL": "oid:0xb000000000a61",
      "SAI_PORT_ATTR_MTU": "9122",
      "SAI_PORT_ATTR_PORT_VLAN_ID": "1000",
      "SAI_PORT_ATTR_PRIORITY_FLOW_CONTROL": "24",
      "SAI_PORT_ATTR_QOS_DSCP_TO_TC_MAP": "oid:0x14000000000a34",
      "SAI_PORT_ATTR_QOS_PFC_PRIORITY_TO_QUEUE_MAP": "oid:0x14000000000a35",
      "SAI_PORT_ATTR_QOS_TC_TO_PRIORITY_GROUP_MAP": "oid:0x14000000000a38",
      "SAI_PORT_ATTR_QOS_TC_TO_QUEUE_MAP": "oid:0x14000000000a39",
      "SAI_PORT_ATTR_SPEED": "50000"
    }
  }
```

Following is an example for checking the port status for Broadcom ASICs.
From command line, enter "bcmsh" to enter into Broadcom shell. Users can use "Ctrcl c" to come out of Broadcom shell.
In the broadcom shell, users shall use "ps" command to check the port state.

Example:
```
BCM : bcmcmd ps
       port      ena/link  Lanes  Speed Duplex   LinkScan  AutoNeg?   STPstate    pause  discrd  LrnOps   Interface MaxFrame  CutThru?  Loopback
       xe0( 50)  down      2      50G   FD       SW        No         Forward            None    FA       KR2       9122      No
       xe1( 51)  down      2      50G   FD       SW        No         Forward            None    FA       KR2       9122      No
       xe2( 54)  up        2      50G   FD       SW        No         Forward            None    FA       KR2       9122      No

```

## 6.3 Investigating Packet Drops 
Packet drops can be investigated by viewing counters using the `show interfaces counters` command.

- **RX_ERR/TX_ERR** includes all physical layer (layer-2) related drops, such as FCS error, RUNT frames. If there is RX_ERR or TX_ERR, it usually indicates some physical layer link issues.

- **RX_DRP** include all layer-2, layer-3, ACL related drops in the switch ingress pipeline, drops due to insufficient ingress buffer.

- **TX_DRP** include mainly the egress buffer related drop due to congestion, including WRED drop.

- **RX_OVR/TX_OVR** counts the oversized packets.

- Example:
  ```
  admin@sonic:~$ show interfaces counters
        Iface            RX_OK      RX_RATE    RX_UTIL    RX_ERR    RX_DRP    RX_OVR            TX_OK      TX_RATE    TX_UTIL    TX_ERR    TX_DRP    TX_OVR
  -----------  ---------------  -----------  ---------  --------  --------  --------  ---------------  -----------  ---------  --------  --------  --------
    Ethernet0  471,729,839,997  653.87 MB/s     12.77%         0    18,682         0  409,682,385,925  556.84 MB/s     10.88%         0         0         0
    Ethernet4  453,838,006,636  632.97 MB/s     12.36%         0     1,636         0  388,299,875,056  529.34 MB/s     10.34%         0         0         0
    Ethernet8  549,034,764,539  761.15 MB/s     14.87%         0    18,274         0  457,603,227,659  615.20 MB/s     12.02%         0         0         0
   Ethernet12  458,052,204,029  636.84 MB/s     12.44%         0    17,614         0  388,341,776,615  527.37 MB/s     10.30%         0         0         0
   Ethernet16   16,679,692,972   13.83 MB/s      0.27%         0    17,605         0   18,206,586,265   17.51 MB/s      0.34%         0         0         0
   Ethernet20   47,983,339,172   35.89 MB/s      0.70%         0     2,174         0   58,986,354,359   51.83 MB/s      1.01%         0         0         0
   Ethernet24   33,543,533,441   36.59 MB/s      0.71%         0     1,613         0   43,066,076,370   49.92 MB/s      0.97%         0         0         0
  ```

## 6.4 Physical Link Signal 

Use the following command to get optical signal strength. Note: not all types of links have such channel monitor values. The AOC and DAC cables do not have such values.

Generally, optical power should be greater than -10dBm.

- Example:
  ```
  admin@sonic:~$ show interfaces transceiver eeprom Ethernet12 --dom
  Ethernet12: SFP detected
  
          Connector : Unknown
          EncodingCodes : Unspecified
          ExtIdentOfTypeOfTransceiver : GBIC def not specified
          LengthOM3(UnitsOf10m) : 144
          RateIdentifier : Unspecified
          ReceivedPowerMeasurementType : Avg power
          TransceiverCodes :
                  10GEthernetComplianceCode : 10G Base-SR
                  InfinibandComplianceCode : 1X Copper Passive
          TypeOfTransceiver : QSFP
          VendorDataCode(YYYY-MM-DD Lot) : 2013-11-29 
          VendorName : MOLEX
          VendorOUI : MOL
          VendorPN : 1064141400
          VendorRev : E th
          VendorSN : G13474P0120
          ChannelMonitorValues :
                  RX1Power : -5.7398dBm
                  RX2Power : -4.6055dBm
                  RX3Power : -5.0252dBm
                  RX4Power : -12.5414dBm
                  TX1Bias : 19.1600mA
                  TX2Bias : 19.1600mA
                  TX3Bias : 19.1600mA
                  TX4Bias : 19.1600mA
          ChannelStatus :
                  Rx1LOS : Off
                  Rx2LOS : Off
                  Rx3LOS : Off
                  Rx4LOS : Off
                  Tx1Fault : Off
                  Tx1LOS : Off
                  Tx2Fault : Off
                  Tx2LOS : Off
                  Tx3Fault : Off
                  Tx3LOS : Off
                  Tx4Fault : Off
                  Tx4LOS : Off
          ModuleMonitorValues :
                  Temperature : 23.7500C
                  Vcc : 3.2805Volts
          StatusIndicators :
                  DataNotReady : Off
  ```


## 6.5 Isolate SONiC Device from the Network 

When there is suspicion that a SONiC device is dropping traffic and behaving abnormally, you may want to isolate the device from the network. Before isolating the device, please generate SONiC tech-support first.

You can shut down BGP sessions to neighbors using a form of the `config bgp shutdown` command. There are a few variations of this command, examples follow.

- Shutdown BGP session with neighbor by neighbor's hostname:
- Example:
  ```
  admin@sonic:~$ sudo config bgp shutdown neighbor SONIC02SPINE
  ```

- Shutdown BGP session with neighbor by neighbor's IP address:
- Example:
  ```
  admin@sonic:~$ sudo config bgp shutdown neighbor 192.168.1.124
  ```

- Shutdown BGP sessions with all neighbors:
- Example:
  ```
  admin@sonic:~$ sudo config bgp shutdown all
  ```

## 6.6 NAT troubleshooting 

All NAT related configuration done via CLI is saved in the REDIS database (CONFIG_DB). Please following the below troubleshooting steps for debugging NAT issues.

- Check if NAT global mode is enabled using `show nat config` CLI command to display all the NAT configuration done.
- Check if ingress and egress L3 interfaces are configured in different NAT zones. Use bcmcmd `l3 intf show` to check the zones configured in the hardware.
- Check if the NAT miss packets are reaching Linux stack. Use `tcpdump` Linux command to monitor the ingress L3 interface.
- Check if the NAT translation entries are learned by the Linux kernel. Use `conntrack -j -L`  and `iptables -t nat -nv -L` Linux commands.
- Check if NAT translation entry is created in CONFIG_DB/APPL_DB/ASIC_DB. Use `show nat statistics`, `show nat translations` and `redis-cli` commands.
- Check if the NAT translation entries are installed in the switching ASIC. Use bcmcmd `l3 nat_ingress show` and `l3 nat_egress show` BCM shell commands.
- Check if the packet counters for the installed NAT entries are incrementing. Use `show nat statistics` command to monitor packet and byte counter per entry.
- Ensure that the translated source IP address for the outbound traffic belongs to one of the L3 interfaces in the system.
- Check whether the translated destination IP address for the inbound traffic is reachable via one of the L3 interfaces in the system.

## 6.7 Orchagent troubleshooting

Orchagent components like RouteOrch, NeighOrch, FdbOrch internal cache/datastructures can be debugged using "show debug <component> <cmd>" commands. For details please refer [here](Command-Reference.md#debug-framework-commands)

## 6.8 IGMP Snooping troubleshooting
All IGMP Snooping related configuration done via CLI is saved in the REDIS database (CONFIG_DB). Please follow below troubleshooting steps for debugging IGMP Snooping issues.

- Check if IGMP Snooping enabled on VLAN using `show ip igmp snooping vlan <vlan-id>` CLI command to display snooping configuration applied on the VLAN.
- Check if IGMP Snooping enabled on VLAN is created in CONFIG_DB/APPL_DB/ASIC_DB . Use `redis-cli` to verify DB entries created.
- Check multicast entry learnt using  `show ip igmp snooping groups vlan <vlan-id>` CLI command to display all multicast groups learnt on given VLAN.
- Check if the L2mc orchagent received multicast entry update.  Use `show debug l2mcorch all` command in the SONiC Click CLI shell.
- Check if the multicast entries are installed in the switching ASIC. Use bcmcmd `ipmc table show`  BCM shell commands.
- Check if the IPMC group created and member added to the group using `multicast show` BCM shell command.

## 6.9 PIM and IGMP  troubleshooting

All PIM configuration is done in the FRR VTYSH shell.  If the last-hop PIM router doesn't receive multicast traffic from the multicast source, please follow the below troubleshooting steps to debug PIM issues.

- Check if the multicast interfaces are programmed in the Linux kernel.  Use `show ip multicast` and `show ip multicast vrf <vrf-name>` commands.
- Check if PIM and IGMP are enabled on the downstream and upstream interfaces.  Use `show ip pim vrf all interface` and `show ip igmp vrf all interface` commands.
- Check if IGMP report is received on the downstream interface.  Use `show ip igmp vrf all groups` and `show ip igmp vrf all sources` commands.
- Check if the multicast route entry is created. Use `show ip mroute vrf all` command.
- Check if the multicast route entry is correctly programmed in the Linux kernel.  Use `ip mroute show table all` in the Linux shell.
- Check if the multicast route entry is updated to APP_DB using redis-cli.
- Check if the multicast orchagent received the multicast route update.  Use `show debug ipmcorch all` command in the SONiC Click CLI shell.
- Check if the multicast route entry is updated to ASIC_DB using redis-cli.
- Check if the multicast route entry is installed in the switching ASIC.  Use `bcmcmd 'ipmc table show'` and `bcmcmd 'multicast show'` commands in the SONiC Click CLI shell.
- Check if the packet counters for the installed multicast route entry are incrementing.  Use `show interfaces counters` in the SONiC Click CLI shell.

## 6.10 Multicast converged OIF troubleshooting
Please follow below troubleshooting steps when PIM and IGMP snooping configured on a Vlan.

- Follow troubleshooting steps mentioned in section 6.8 for IGMP Snooping and section 6.9 for PIM
- Check PIM + IGMP configuration on a Vlan has created APPL_DB entry. Use `redis-cli` to verify DB entries created.
- Check IPMC sharing mode is off in orchagent when PIM and IGMP Snooping enabled on vlan. Use `show debug ipmcorch all` command in the SONiC Click CLI shell and check sharing mode on/off.
- Check PIM created IPMC group shared with IGMP Snooping on particular vlan. Use `show debug ipmcorch ipmcvlan-l2oifs` command in the SONiC Click CLI shell.

## 6.11 System Resource Monitoring
- The system monitor service monitors the system resource usage and reports the alert message on the syslog console when resource usage crosses the predefined threshold limit. 
- There are predefined threshold limits for each of the resource types.

### System service monitoring
- It monitors the system's core services and the ports initialization state. 
- When the system boots up, the following message on the console indicates that the core services are up and the initialization of ports is done. 

```
   Feb 18 21:18:47.546456 System is ready
```
- The system status can also be viewed by the CLI command -'show system status'. When the system is up with core services, it will print the following message. 
```
  admin@sonic:~$ show system status
  System is ready
```
- When one of the core services is down, it prints the following message.
```
  root@sonic:/home/admin# show system status
  System is not ready - Core services are not up
  swss       : Up
  bgp        : Down
  teamd      : Up
  pmon       : Up
  syncd      : Up
  database   : Up
  mgmt-framework : Up
```
- When core services are up, but the port init is not complete, it prints the following message.
```
  root@sonic:/home/admin# show system status
  System is not ready - Ports are not up
```

### CPU/Memory/Disk usage monitoring
- It also monitors the CPU/Memory/Disk usage and reports the alert message on the Syslog console. 

### Memory monitoring
- It monitors the memory usage on system-level and also on a per-process level
- Report the alert message when it crosses the threshold limits.

  Example:
```
  Jan 14 09:26:54.065576 sonic WARNING system#monitor: System free memory usage is below 28%, Total: 15.2G, Free: 4.3G, Used: 9.4G, Buffers: 231.3M, Cached: 1.3G, Avail: 5.5G
  Jan 14 09:26:54.257784 sonic INFO system#monitor: MEM :: Name:syncd, Pid:16513, Rss:374.5M
  Jan 14 09:26:54.258232 sonic INFO system#monitor: MEM :: Name:telemetry, Pid:22188, Rss:158.8M
  ... etc
```
### CPU monitoring 
- It reports the CPU usage of process when a high CPU condition is detected.

  Example:
```
  Jan 14 09:21:45.794238 sonic CRIT system#monitor: Current CPU usage of process syncd[28697] is 98.6%
  Jan 14 09:22:17.739944 sonic WARNING system#monitor: Current CPU usage of process syncd[6229] is 79.4%
  Jan 14 09:22:23.011507 sonic ALERT system#monitor: Current CPU usage of process syncd[6229] is 82.7%
```

- When the CPU usage of a process comes down from high to a lower level, a clear syslog message is generated.

  Example:
```
  May 4 17:32:55.596696 sonic WARNING system#monitor: High CPU usage of process snmp.out[10952] is cleared, Current CPU usage is 79.4%
```

### Disk Partition Usage monitoring
- It also monitors the disk partition usage and reports the alert message when usage crosses the pre-defined threshold limits.

  Example:
```
  DISK usage of '/' is above [0-70%], Total: 28.6G, Free: 12.5G, Used: 14.7G
```
## 6.12 Docker Resource Limits

All SONiC Dockers are configured to specific CPU and Memory quotas to protect the overall system from any single Docker (and the applications hosted within them) hogging the resources during unusual events like packet storms that can trigger critically high CPU and Memory usage including memory leaks. The Docker infrastructure will take corrective measures to keep the system operating at normal levels.

The Factory default settings are specified in /etc/sonic/docker_limits.json. These are applied in the first reboot after the installation. 

To view the set memory limits, use the 'docker stats --no-stream' command. The value under the LIMIT column indicates the set limit.
```
docker stats --no-stream 

admin@sonic:~$ docker stats --no-stream
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT    MEM %               NET I/O             BLOCK I/O           PIDS
ee861502011e        nat                 0.07%               24.3MiB / 3.19GiB    0.74%               0B / 0B             4.19MB / 49.2kB     10
5e39ba57bf4b        lldp                0.02%               38.57MiB / 3.2GiB    1.18%               0B / 0B             5.94MB / 53.2kB     11
e27c0b49c311        tam                 0.15%               20.21MiB / 3.2GiB    0.62%               0B / 0B             5.15MB / 69.6kB     17
61e68b185a28        telemetry           0.27%               276.1MiB / 3.19GiB   8.45%               0B / 0B             35.1MB / 41kB       20
9b5d12ff741e        sflow               0.02%               31.92MiB / 3.19GiB   0.98%               0B / 0B             3.62MB / 41kB       9
c484e7faa577        l2mcd               0.04%               24.24MiB / 3.19GiB   0.74%               0B / 0B             2.34MB / 49.2kB     10
240522133ece        snmp                0.24%               67.38MiB / 3.19GiB   2.06%               0B / 0B             18.8MB / 73.7kB     14
628d771cc0a4        radv                0.02%               22.81MiB / 3.2GiB    0.70%               0B / 0B             139kB / 41kB        6
7739440010f7        mgmt-framework      0.02%               260.8MiB / 3.2GiB    7.96%               0B / 0B             50.1MB / 36.9kB     19
fa197692eda9        stp                 0.04%               23.81MiB / 3.2GiB    0.73%               0B / 0B             2.31MB / 53.2kB     10
8b1407b2eab8        dhcp_relay          0.02%               23.83MiB / 3.2GiB    0.73%               0B / 0B             1.85MB / 45.1kB     8
fa008826a95f        syncd               26.11%              777.4MiB / 3.19GiB   23.80%              0B / 0B             118MB / 356kB       49
33ca98a71d38        iccpd               0.03%               25.28MiB / 3.19GiB   0.77%               0B / 0B             2.74MB / 53.2kB     11
9cf37fb3c05f        bgp                 0.30%               61.6MiB / 3.19GiB    1.89%               0B / 0B             15.6MB / 131kB      34
65d0242b93e4        teamd               0.04%               23.78MiB / 3.19GiB   0.73%               0B / 0B             2.47MB / 45.1kB     10
b6126b3c0f28        swss                1.00%               40.47MiB / 3.2GiB    1.24%               0B / 0B             21.5MB / 193kB      43
f3e1f3121d48        pmon                0.10%               23.96MiB / 3.19GiB   0.73%               0B / 0B             6.15MB / 41kB       6
b248391a9349        vrrp                0.96%               23.67MiB / 3.19GiB   0.72%               0B / 0B             2.95MB / 49.2kB     10
323fef817fda        database            0.84%               42.73MiB / 3.19GiB   1.31%               0B / 0B             31.3MB / 65.5kB     22

```
To view the allotted CPU cores use the 'docker inspect' command as shown below
```
docker inspect <container name> | grep Nano

For example:

admin@sonic:~$ docker inspect syncd | grep Nano
            "NanoCpus": 7000000000,
```
The displayed value is #numCpuCores * 1000000000. In the above example, 7 CPU cores are allotted to the sycnd Docker.

### System Behavior when resource limits crossed

- If application(s) in a specific Docker attempts to utilize more CPU cores than allotted, the Docker
 infrastructure automatically throttles the utilization to stay within set limits. It should be noted that such limits are imposed only when there is a CPU contention between applications.
 
- If application(s) in a specific Docker attempts to utilize more memory than the set limit, the Docker infrastructure terminates the application and the Docker will be restarted. It should also be noted that in such an event, dependent Dockers are also restarted.  
- When an application is killed by the Docker infrastructure a log similar to the one shown below will be written to syslog. 
```
[255131.028788] Memory cgroup out of memory: Kill process 15288 (sflowmgrd) score 962 or sacrifice child
[255131.032073] Killed process 15288 (sflowmgrd) total-vm:4155224kB, anon-rss:4150636kB, file-rss:1076kB, shmem-rss:0kB
```


## 6.13 Kernel Crash troubleshooting
- If the output of the command *show reboot-cause* is Unknown and the user did not perform a power cycle, it is most likely that a kernel crash has occurred.
- The kdump feature in SONiC allows the users to identify if a kernel crash has happened. It also captures a snapshot of the kernel state and kernel log buffer, saving it on to the flash for future reference.
- Enable kdump using the *config kdump enable* command and reboot for kdump to be operational. If and when the switch encounters a kernel crash condition, the kdump service will create a kernel core file along with the kernel message log.
- The user can use *show kdump status* command to view kernel crash information.
- To view the kernel crash log, the user can use the *show kdump log <key>* command.
- The command *show kdump files* displays the location of the kernel core files. The Linux crash utility provides various options to debug the core files to obtain more information about kernel state when the crash has happened. More information can be obtained by visiting the following web links: 
  - [Crash Utility](https://people.redhat.com/anderson/crash_whitepaper)
  - [Crash Command Help](https://people.redhat.com/anderson/help.html)
- More information on how to use SONiC KDUMP feature can be found in the [SONiC KDUMP Design Document](https://github.com/Azure/SONiC/pull/510).

###

## 6.14 Thermal Monitoring
- Platform specific thermal monitoring is supported. Every platform vendor provides platform specific thermal policy, which controls the system fan speed based on the system temperature. 
- System temperature is calculated based on various thermal sensors placed at differnet locations on the board.
- Syslogs are generated if high temperature threshold is crossed. This is applicable to only those platforms where thermal policy is not implemented in Baseboard Management Controller (BMC).
  Example:
```
Dec  4 14:39:54.975395 sonic WARNING [accton_as7816_monitor] Alarm for temperature high is detected

Mar 14 13:01:29.509180 sonic WARNING [accton_as7712_monitor] Alarm for temperature high is detected
```
- If the temperature comes down from high threshold, a clearing syslog is sent.
  Example:
```
Mar 14 13:01:49.534867 sonic WARNING [accton_as7712_monitor] Alarm for temperature high is cleared
```

## 6.15 Hardware Watchdog Timer
- The Hardware Watchdog Timer(WDT) detects the software or hardware fault on the system and reset the system automatically for recovery. 
- The iTCO based PCH intel watchdog timer is enabled by default.
- If platform doesn't support the watchdog timer, it is disabled automatically.
- If the system is reset because of the watchdog timeout, the following CLI indicates the reboot reason. 

```
# show reboot-cause

Example:
# show reboot-cause
Hardware Watchdog Reset
```
- If platform doesn't support the reboot reason, it will show as 'Unknown'.
- The following platform matrix defines the support for iTCO WDT feature with reboot reason for different platforms.

```
:----------------|:--------------------|:------------------------
|    PLATFORM    |  Watchdog supported | Reboot Reason Supported |          
:----------------|:--------------------|:------------------------
|  AS7326_56X    | No                  | No                      |
|  AS7816_64X    | No                  | No                      |
|  AS7712_32X    | Yes                 | No                      |
|  AS9716_32D    | Yes                 | No                      |
|  AS7726_32X    | No                  | No                      |
|  AS5712_54X    | Yes                 | No                      |
|  AS4630_54PE   | Yes                 | Yes                     |
|  AS5835_54X    | Yes                 | Yes                     |
|  QIX4-64X      | Yes                 | No                      |
|  QIX7-BWDE-32X | Yes                 | No                      |
|  QIX8-56X      | Yes                 | No                      |
|  QIX9-32X      | Yes                 | No                      |
:----------------|:--------------------|:-------------------------
```


# 7 Common Framework Development & Usage 

## 7.1 Debug Framework 

Debug Framework provides mechanism for new modules/components to register with the framework & dump its internal data for debugging.

Please refer to [Debug framework HLD](https://github.com/Azure/SONiC/pull/398) on how to register components and implement dump routines.

# 8 Chef 
The need for configuring a large number of devices arises with the continuous growth of data center and cloud environments. To make this task of configuration easy and automatic, different suite of tools are being developed. Chef is one such tool which helps the IT and Network configuration automation. Please refer the Chef User Guide for Chef usage in SONiC and for supported SONiC cookbook operations.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA0MTU5MTA5MCwtNjk5NDI2Njg4LC04Nj
k1MjE1NzddfQ==
-->

# 9 SSH-server vrf
By default SSH connection to the switch is allowed via default-vrf and mgmt-vrf only and not allowed via user defined VRF. SONiC provides REST APIs and CLI commands to allow SSH connection to swtch via user defined VRFs. Please refer command reference guide to enable/disable using CLI

# 10 Syslog Messages Forwarding
By default syslog messages are not forwarded to any server. User need to configure syslog server to forward messages to external server.
User can configure switch using REST and KLISH CLI to forward  messages to remote syslog servers with optional source-interface and VRF to be used while sending out syslog messages.
If source-interface is not configured then route interface ip is used as source ip of syslog packets. If source-interface is configured then source-interface ip is used as source ip of outgoing syslog packets.

  **Note**: Do not configure ipv4 and ipv6 combination for server and source-interface.


# 11 Advanced Troubleshooting with TAM 

Broadcom SONiC supports TAM - Telemetry and Monitoring - features that take advantage of the underlying silicon's telemetry features to simplify troubleshooting and monitoring networking issues. Some of the key use cases for the TAM features include -

- SLA conformance that require end-to-end path latency for targeted flows
- Identifying switches and flows that are undergoing congestion
- Identifying network bandwidth usage patterns
- Packet path and path change analysis
- Identifying switches and flows that are undergoing packet-drops
- Identifying packet-drop patterns


## Flow Monitoring with IFA

Inband Flow Analyzer (IFA) records flow specific information from switches across a network for specific flows. It is described at the [IETF draft](https://datatracker.ietf.org/doc/draft-kumar-ippm-ifa). The protocol defines an IFA header to mark the flow and direct the collection of analyzed metadata per marked packet per hop across the network. Some of the text in the document is taken from the above IETF draft, for contextual purposes.

IFA performs flow analysis, and possible actions on the flow data, inband.  Once a flow is enabled for analysis, an Ingress node makes a copy of the flow or samples the live traffic flow, or tags a live traffic flow for analysis and data collection. Copying of a flow is done by sampling or cloning the flow.  These new packets are representative packets of the original flow and possess the exact same characteristics as the original flow.  This means that IFA packets traverse the same path in the network and same queues in the networking element as the original packet would.  

The intermediate nodes keep appending their own metadata to the IFA-tagged packets before forwarding them. The terminating node is responsible for terminating the IFA flow by summarizing/extracting the metadata of the entire path and sending it to a designated Collector. A network monitoring application on a Collector can analyze the telemetry information provided by IFA feature and provide full visibility of a network by providing metrics such as latency, packet loss and full network path that packet travels. A complete description of the functionality can be obtained from the IETF draft mentioned above.

The IFA feature in SONiC allows the user to setup flow monitoring sessions for specific flows. The switch may be marked as an ingress node, a transit node or a terminating node for a flow. A Collector, identified by an IP address and associated transport parameters, can be configured on the terminating nodes to send the extracted metadata. 

### Sample Workflow for IFA

This section provides a sample IFA workflow using CLI, for monitoring the traffic as described below.

> Need to monitor all flows to the webserver running at 20.20.1.1. A IFA collector for analyzing the metadata is running at 20.20.20.4:9090 (UDP). Not every packet needs monitored, but one in 1000 is acceptable. The flows are ingressing onto switch1 on port 'Ethernet44'

Note that there would be other system wide configuration that is not covered in the sample workflow below, but would be required for the packet forwarding/routing. 

#### Ingress Node configuration

```
; setup switch-wide configuration

sonic (config-tam)# switch-id 1234
sonic (config-tam)# enterprise-id 4434

; setup the sample-rate

sonic (config-tam)# sampler websamp rate 1000

; create the flowgroup

sonic (config-tam)# flow-group websrvflows dst-ip 20.20.1.1 dst-l4-port 80 protocol TCP

# associate the ingress interface to the flowgroup

sonic (config) # interface Ethernet 44
sonic (config-if-Ethernet44)# flow-group websrvflows

; Enable IFA on the switch

sonic (config-tam-ifa)# enable

; Create the IFA monitoring session

sonic(config-tam-ifa)# session webflowmonitor flowgroup websrvflows sample-rate websamp node-type ingress

```

#### Intermediate/Transit Node configuration

```
; setup switch-wide configuration

sonic (config-tam)# switch-id 1235
sonic (config-tam)# enterprise-id 4434

; Enable IFA on the switch

sonic (config-tam-ifa)# enable

```

#### Egress Node configuration

```
; setup switch-wide configuration

sonic (config-tam)# switch-id 1236
sonic (config-tam)# enterprise-id 4434

; setup the collector

collector ifacol1 type ipv4 ip 20.20.20.4 port 9090 protocol UDP

; create the flowgroup

sonic (config-tam)# flow-group websrvflows dst-ip 20.20.1.1 dst-l4-port 80 protocol TCP

; Enable IFA on the switch

sonic (config-tam-ifa)# enable

; Create the IFA monitoring session

sonic(config-tam-ifa)# session webflowmonitor flowgroup websrvflows collector ifacol1 node-type egress

```

## Packet Drop Monitoring with Drop Monitor

The Drop Monitor feature in SONiC allows the user to setup packet-drop monitoring sessions for specific flows. A Collector, identified by an IP address and associated transport parameters, can be configured on the switch to send packet drop-reports. 

The drop monitor feature detects and reports drops on a per-flow basis to a configured external collector. Dropped packets of the flow are sampled (based upon the sampling rate configuration) to the drop-monitor application.  

Once a flowgroup (matching criteria) is configured for monitoring drops, the drop monitor application reports the following drop events on the flow to the collector in the protobuf format:

  - Drop start: Sent when drops are observed on the flow for the first time. The report contains the event, flow keys, first 128 bytes of the packet and the last observed drop reasons.

  - Drop active: Sent whenever the drop reason on the flow changes. The report contains the event, flow keys, and the drop reasons.

  - Drop stop: Sent when no sampled dropped packets are observed for the aging interval. The report contains event, flow keys and the last observed drop reasons.

### Sample Workflow with Drop Monitor

This section provides a sample Drop Monitor workflow using CLI, for monitoring the packet drops as described below.

> Need to monitor all packet drops on this switch, for all the flows destined for the webserver running at 20.20.1.1. A Drop Monitor collector for analyzing the metadata is running at 20.20.20.4:9091 (UDP). Not every packet drop needs monitored, but one in 100 is acceptable. If a flow didn't see any drop for 5sec, it can be assumed that the active drop window may have ended. The flows are ingressing onto switch1 on port 'Ethernet44'

Note that there would be other system wide configuration that is not covered in the sample workflow below, but would be required for the packet forwarding/routing.

```

; setup switch-wide configuration

sonic (config-tam)# switch-id 1234

; setup the sample-rate

sonic (config-tam)# sampler websamp rate 100

; create the flowgroup

sonic (config-tam)# flow-group websrvflows dst-ip 20.20.1.1 dst-l4-port 80 protocol TCP

# associate the ingress interface to the flowgroup

sonic (config) # interface Ethernet 44
sonic (config-if-Ethernet44)# flow-group websrvflows

; setup the collector

collector dmcol1 type ipv4 ip 20.20.20.4 port 9091 protocol UDP

; Enable Drop Monitor on the switch

sonic (config-tam-dm)# enable

; Setup aging interval

sonic (config-tam-dm)# aging-interval 5

; Create the drop-monitoring session

sonic(config-tam-dm)# session webflowmonitor flowgroup websrvflows sample-rate websamp collector dmcol1

```

### Drop Report format

The drop reports are encoded in protobuf format provided below.

```protobuf

/*
     * Copyright (c) 2017 Broadcom. The term "Broadcom" refers
     * to Broadcom Limited and/or its subsidiaries.

     * Licensed under the Apache License, Version 2.0 (the "License");
     * you may not use this file except in compliance with the License.

     * You may obtain a copy of the License at
     * http://www.apache.org/licenses/LICENSE-2.0

     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

    syntax = "proto2";

    message Event {
        required uint64 timestamp               = 1;

        oneof EventType {
            MirrorOnDrop mod_event              = 2;
            FlowLearning flow_event             = 3;
            RouteChangeDetection rcd_event      = 4;
            NetworkLatencyMonitoring nlm_event  = 5;
        }
    }

    message MirrorOnDrop {
        enum MOD_STATE {
            DROP_INVALID = 0;
            DROP_START   = 1;
            DROP_ACTIVE  = 2;
            DROP_STOP    = 3;
        }

        optional MOD_STATE mod_state       = 1;
    }

    message FlowLearning {
        enum FL_STATE {
        	FL_INVALID      = 0;   
	        FL_LEARN        = 1;
	        FL_AGING        = 2;
	        FL_EXPORT       = 3;
	        FL_TABLE_FULL   = 4; /* Atomic event, no other information need to be send */
        }

        optional FL_STATE fl_state    = 1;
    }

    message RouteChangeDetection {
        optional uint32 dummy           = 1;
    }

    message NetworkLatencyMonitoring {
        optional uint32 dummy           = 1;
    }

    message Flow {
        enum DROP_REASON {
	        DROP_INVALID                  = 0;  
         	L3_SOURCE_MISS                = 1;
	        L3_DEST_MISS                  = 2;
	        MCAST_MISS                    = 3;
	        IP_MCAST_MISS                 = 4;
	        UNKNOWN_VLAN                  = 5;
	        L3_HEADER_MISMATCH            = 6;
	        DOS_ATTACK                    = 7;
	        MARTIAN_ADDR                  = 8;
	        TUNNEL_ERROR                  = 9;
	        PARITY_ERROR                  = 10;  
	        L3_MTU_FAIL                   = 11;
	        HIGIG_HDR_ERROR               = 12;
            MCAST_IDX_ERROR               = 13;
            CLASS_BASED_MOVE              = 14;
            L3_ADDR_BIND_FAIL             = 15;
	        MPLS_LABEL_MISS               = 16;
	        MPLS_INVALID_ACTION           = 17;
	        MPLS_INVALID_PAYLOAD          = 18;
	        TUNNEL_OBJECT_VALIDATION_FAIL = 19;
	        MPLS_SEQUENCE_NUMBER          = 20;
	        L2_NON_UNICAST_MISS           = 21;
	        NIV_PRIO_DROP                 = 22;
	        NIV_RPF_FAIL                  = 23;
	        UNKNOWN_SUBTENDING_PORT       = 24;
	        TUNNEL_ADAPT_LOOKUP_MISS      = 25;
	        PACKET_FLOW_SELECT_MISS       = 26;
	        TUNNEL_DECAP_ECN_ERROR        = 27;
	        FAILOVER_DROP                 = 28;
	        OTHER_LOOKUP_MISS             = 29;
	        INVALID_TPID                  = 30;
	        TUNNEL_TTL_ERROR              = 31;
	        MPLS_ILLEGAL_RESERVED_LABEL   = 32;
	        L3_HEADER_ERROR               = 33;
	        L2_HEADER_ERROR               = 34;
	        TTL1                          = 35;
	        TTL                           = 36;
	        FCOE_ZONE_CHECK_FAIL          = 37;
	        IPMC_INTERFACE_MISMATCH       = 38;
	        MPLS_TTL                      = 39;
	        MPLS_UNKNOWN_ACH              = 40;
	        OAM_ERROR                     = 41;
	        L2_GRE_SIP_MISS               = 42;
	        L2_GRE_VPNID_MISS             = 43;
	        BFD_ERROR                     = 44;
	        CONGESTION_CNM_PROXY_ERROR    = 45;
	        VXLAN_SIP_MISS                = 46;
	        VXLAN_VPNID_MISS              = 47;
	        NIV_INTERFACE_MISS            = 48;
	        NIV_TAG_INVALID               = 49;
	        NIV_TAG_DROP                  = 50;
	        NIV_UNTAG_DROP                = 51;
	        TRILL_INVALID                 = 52;
	        TRILL_MISS                    = 53;
	        TRILL_RPF_FAIL                = 54;
	        TRILL_TTL                     = 55;
	        NAT_ERROR                     = 56;
            TCP_UDP_NAT_MISS              = 57;
            ICMP_NAT_MISS                 = 58;
            NAT_FRAGMENT                  = 59;
            NAT_MISS                      = 60;
        }

        optional uint32 proto              = 1;
        optional uint32 sip                = 2;
        optional uint32 dip                = 3;

        optional uint32 l4_sport           = 4;
        optional uint32 l4_dport           = 5;
        optional uint32 vnid               = 6;
        optional uint32 inner_proto        = 7;
        optional uint32 inner_sip          = 8;
        optional uint32 inner_dip          = 9;

        optional uint32 inner_l4_sport     = 10;
        optional uint32 inner_l4_dport     = 11;
        optional bytes  custom_key         = 12;
        optional uint32 group_id           = 13; /* Packet type is derived directly from group ID */
        optional bytes packet              = 14;

        optional uint64 packet_count       = 15;
        optional uint64 byte_count         = 16;

        optional DROP_REASON drop_reason_1 = 17;
        optional DROP_REASON drop_reason_2 = 18;

    }

    message EventPair {
        repeated Event event            = 1;
        repeated Flow flow              = 2;
    }

    message GenEvent {
        required string system_id        = 1;
        optional uint32 component_id     = 2;
        optional uint32 sub_component_id = 3;
        repeated EventPair eventpair     = 4;
        optional string hostname         = 5;
    }
```

## Flow Path Monitoring with Tailstamping

Tailstamping feature attaches arrival and departure timestamps along with a switch identifier to a frame at one or more switches along a flow path across the network for a given set of flows matching the user provided criterion. Time-stamps can be gathered and analyzed by a designated collector. A network monitoring application can analyze the time-stamps and measure the latency of a flow across the network, trace packets across a path, detect hotspots points and validate arrival sequence.

The Tailstamping feature in SONiC allows the user to setup flow monitoring sessions for specific flows.

### Sample Workflow for Tailstamping

This section provides a sample Tailstamping workflow using CLI, for monitoring the traffic as described below.

> Gather flow metadata for network probe packets sent from 10.10.1.1:8080 to 20.4.5.2:7070 (tcp)  

Note that there would be other system wide configuration that is not covered in the sample workflow below, but would be required for the packet forwarding/routing. 

```
; setup switch-wide configuration

sonic (config-tam)# switch-id 1234

; create the flowgroup

sonic (config-tam)# flow-group probeflow src-ip 10.10.1.1 src-l4-port 8080 dst-ip 20.4.5.2 dst-l4-port 7070 protocol TCP

; Enable Tailstamping on the switch

sonic (config-tam-ts)# enable

; Create the Tailstamping monitoring session

sonic(config-tam-ts)# session probemonitor flowgroup probeflow

```


