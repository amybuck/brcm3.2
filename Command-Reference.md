![](images/Broadcom.png)

<br>
<br>
<br>
# Broadcom SONiC 3.1.0 

### Sonic Command Line Interface Guide
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

[TOC]

# Document History

| #    | Date         | Document Version | Details                      |
| ---- | ------------ | ---------------- | ---------------------------- |
| 1    | Sep 20, 2019 | v1               | Initial version              |
| 2    | Nov 22, 2019 | v2               | Broadcom SONiC 2.1.0 Release |
| 3    | Feb 16, 2020 | v3               | Broadcom SONiC 3.0.0 Release |
| 4    | Sep 30, 2020 | v4               | Broadcom SONiC 3.1.0 Release |
| 5    | Mar 31, 2021 | v5               | Broadcom SONiC 3.2.0 Release |

# Introduction
SONiC is an open source network operating system based on Linux that runs on switches from multiple vendors and ASICs. SONiC offers a full-suite of network functionality, like BGP and RDMA, that has been production-hardened in the data centers of some of the largest cloud-service providers. It offers teams the flexibility to create the network solutions they need while leveraging the collective strength of a large ecosystem and community.

SONiC software shall be loaded on supported devices and this CLI guide shall be used to configure the devices as well as to display the configuration, state and status.

Follow the user manual to boot the device in ONIE mode, install the SONiC software using the steps specified in the document and login to the device using the default username and password.

After logging into the device, SONiC software can be configured in following three methods.
 1) Command Line Interface (CLI)
 2) [config_db.json](https://github.com/Azure/SONiC/wiki/Configuration) 
 3) [minigraph.xml](https://github.com/Azure/SONiC/wiki/Configuration-with-Minigraph-(~Sep-2017))

This document explains the first method and gives the complete list of commands that are supported in SONiC 201904 version (build#19).
All the configuration commands need root privileges to execute them. Note that show commands can be executed by all users without the root privileges.
Root privileges can be obtained either by using "sudo" keyword in front of all config commands, or by going to root prompt using "sudo -i".
Note that all commands are case sensitive.

  - Example:
  ```
  admin@sonic:~$ sudo config aaa authentication login tacacs+
  
  OR
  
  admin@sonic:~$ sudo -i
  root@sonic:~#  config aaa authentication login tacacs+
  ```

Note that the command list given in this document is just a subset of all possible configurations in SONiC. 
Please follow config_db.json based configuration for the complete list of configuration options.

**Scope Of The Document**  
It is assumed that all configuration commands start with the keyword "config" as prefix. 
Any other scripts/utilities/commands  that need user configuration control are wrapped as sub-commands under the "config" command.
The direct scripts/utilities/commands (examples given below) that are not wrapped under the "config" command are not in the scope of this document.
  1) Acl_loader: This script is already wrapped inside "config acl" command; i.e. any ACL configuration that user is allowed to do is already part of "config acl" command; users are not expected to use the acl_loader script directly and hence this document need not explain the "acl_loader" script.
  2) Sonic-clear, sfputil, etc., This document does not explain these scripts also. 

# Basic Configuration And Show  

This section covers the basic configurations related to the following
 1) [SSH login](#SSH-Login), 
 2) [configuring the management interface](#Configuring-Management-Interface), 
 3) [Help for Config Commands](#Config-Help),
 4) [Help For Show Commands](#Show-Help), 
 5) [show version](#Show-Versions),
 6) [Show System Status](#Show-System-Status) and 
 7) [Show Hardware Platform](#Show-Hardware-Platform).

## SSH Login

All SONiC devices support both the serial console based login and the SSH based login by default.
The default credential (if not modified at image build time) for login is `admin/YourPaSsWoRd`.
In case of SSH login, users can login to the management interface (eth0) IP address after configuring the same using serial console. 
Refer the following section for configuring the IP address for management interface.

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

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

## Configuring Management Interface

The management interface (eth0) in SONiC is configured (by default) to use DHCP client to get the IP address from the DHCP server. Connect the management interface to the same network in which your DHCP server is connected and get the IP address from DHCP server.
The IP address received from DHCP server can be verified using the "/sbin/ifconfig eth0" linux command.

SONiC does not provide a CLI to configure the static IP for the management interface. There are few alternate ways by which a static IP address can be configured for the management interface.  
   1) use "ifconfig eth0" linux command (example: ifconfig eth0 10.11.12.13/24). This configuration won't be preserved across reboot.
   Example:
   ```
   admin@sonic:~$ /sbin/ifconfig eth0 10.11.12.13/24
   ```
   2) use config_db.json and configure the MGMT_INTERFACE key with the appropriate values. Refer [here](https://github.com/Azure/SONiC/wiki/Configuration#Management-Interface) 
   3) use minigraph.xml and configure "ManagementIPInterfaces" tag inside "DpgDesc" tag as given at the [page](https://github.com/Azure/SONiC/wiki/Configuration-with-Minigraph-(~Sep-2017))

Once the IP address is configured, the same can be verified using "/sbin/ifconfig eth0" linux command.
Users can SSH login to this management interface IP address from their management network.

  - Example:
   ```
   admin@sonic:~$ /sbin/ifconfig eth0
   eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
         inet 10.11.11.13  netmask 255.255.255.0  broadcast 10.11.12.255
   ```
Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

## Config Help  

All commands has got in-built help that helps the user to understand the command as well as the possible sub-commands and options.
"--help" can be used at any level of the command; i.e. it can be used at the command level, or sub-command level or at argument level. The in-built help will display the next possibilities corresponding to that particular command/sub-command.

**config --help**  

This command lists all the possible configuration commands at the top level. 

- Usage:
  config --help

- Example:
  ```
  admin@sonic:~$ config --help
  Usage: config [OPTIONS] COMMAND [ARGS]
  SONiC command line - 'config' command

  Options:
    --help  Show this message and exit.

  Commands:
    aaa                    AAA command line
    acl                    ACL-related configuration tasks
    bgp                    BGP-related configuration tasks
    ecn                    ECN-related configuration tasks
    interface              Interface-related configuration tasks
    interface_naming_mode  Modify interface naming mode for interacting...
    load                   Import a previous saved config DB dump file.
    load_mgmt_config       Reconfigure hostname and mgmt interface based...
    load_minigraph         Reconfigure based on minigraph.
    mirror_session
    platform               Platform-related configuration tasks
    portchannel
    portgroup              Port-group configuration tasks
    qos
    radius                 RADIUS server configuration
    reload                 Clear current configuration and import a...
    save                   Export current config DB to a file on disk.
    tacacs                 TACACS+ server configuration
    vlan                   VLAN-related configuration tasks
    warm_restart           warm_restart-related configuration tasks
    watermark              Configure watermark
  ```
  Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

## Show Help  

**show help**  
This command displays the full list of show commands available in the software; the output of each of those show commands can be used to analyze, debug or troubleshoot the network node.

- Usage:  
  You can enter `show -?`, `show -h` or `show --help`

- Example:
  ```
  admin@sonic:~$ show -?
  Usage: show [OPTIONS] COMMAND [ARGS]...
    SONiC command line - 'show' command

  Options:
    -?, -h, --help  Show this message and exit.

  Commands:
    aaa                   Show AAA configuration
    acl                   Show ACL related information
    arp                   Show IP ARP table
    clock                 Show date and time
    ecn                   Show ECN configuration
    environment           Show environmentals (voltages, fans, temps)
    interfaces            Show details of the network interfaces
    ip                    Show IP (IPv4) commands
    ipv6                  Show IPv6 commands
    line                  Show all /dev/ttyUSB lines and their info
    lldp                  LLDP (Link Layer Discovery Protocol)...
    logging               Show system log
    mac                   Show MAC (FDB) entries
    mirror_session        Show existing everflow sessions
    mmu                   Show mmu configuration
    ndp                   Show IPv6 Neighbour table
    ntp                   Show NTP information
    pfc                   Show details of the priority-flow-control...
    platform              Show platform-specific hardware info
    priority-group        Show details of the PGs
    processes             Display process information
    queue                 Show details of the queues
    radius                Show RADIUS configuration
    reboot-cause          Show cause of most recent reboot
    route-map             show route-map
    runningconfiguration  Show current running configuration...
    services              Show all daemon services
    startupconfiguration  Show startup configuration information
    system-memory         Show memory information
    tacacs                Show TACACS+ configuration
    techsupport           Gather information for troubleshooting
    uptime                Show system uptime
    users                 Show users
    version               Show version information
    vlan                  Show VLAN information
    warm_restart          Show warm restart configuration and state
    watermark             Show details of watermark
  ```

The same syntax applies to all subgroups of `show` which themselves contain subcommands, and subcommands which accept options/arguments.

- Example:
  ```
  user@debug:~$ show interfaces -?

    Show details of the network interfaces

  Options:
    -?, -h, --help  Show this message and exit.

  Commands:
    counters     Show interface counters
    description  Show interface status, protocol and...
    naming_mode  Show interface naming_mode status
    neighbor     Show neighbor related information
    pktdrops     Show interface packet drops
    portchannel  Show PortChannel information
    status       Show Interface status information
    transceiver  Show SFP Transceiver information
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)


## Show Versions

**show version**  
This command displays software component versions of the currently running SONiC image. This includes the SONiC image version as well as Docker image versions.
This command displays relevant information as the SONiC and Linux kernel version being utilized, as well as the commit-id used to build the SONiC image. The second section of the output displays the various docker images and their associated id?s. 

- Usage:  
  show version  

- Example:
  ```
  admin@sonic:~$ show version
  SONiC Software Version: SONiC.Broadcom_SONiC_2.0
  Product: Broadcom Cloud Base
  Distribution: Debian 9.9
  Kernel: 4.9.0-9-2-amd64
  Build commit: 4ef307ee
  Build date: Tue Aug 13 08:22:41 UTC 2019
  Built by: sonicbld@sonic-server

  Platform: x86_64-accton_as7326_56x-r0
  HwSKU: Accton-AS7326-56X
  ASIC: broadcom
  Serial Number: 732656XXXXXXXXX
  Uptime: 17:51:44 up 35 min,  1 user,  load average: 0.44, 0.59, 1.14

  Docker images:
  REPOSITORY                 TAG                               IMAGE ID            SIZE
  docker-fpm-frr             latest                            1c32fd90d830        477MB
  docker-fpm-frr             Broadcom_SONiC_2.0   1c32fd90d830        477MB
  docker-orchagent           latest                            df15e4f011e5        447MB
  docker-orchagent           Broadcom_SONiC_2.0   df15e4f011e5        447MB
  docker-vrrp                latest                            e6284352dcd8        444MB
  docker-vrrp                Broadcom_SONiC_2.0   e6284352dcd8        444MB
  docker-stp                 latest                            28edfe4c92fe        429MB
  docker-stp                 Broadcom_SONiC_2.0   28edfe4c92fe        429MB
  docker-teamd               latest                            fa79c9545898        430MB
  docker-teamd               Broadcom_SONiC_2.0   fa79c9545898        430MB
  docker-nat                 latest                            d4d7efc58d8b        433MB
  docker-nat                 Broadcom_SONiC_2.0   d4d7efc58d8b        433MB
  docker-syncd-brcm          latest                            63870c20e45a        443MB
  docker-syncd-brcm          Broadcom_SONiC_2.0   63870c20e45a        443MB
  docker-tam                 latest                            59b95af125ae        675MB
  docker-tam                 Broadcom_SONiC_2.0   59b95af125ae        675MB
  docker-lldp-sv2            latest                            3d1643706454        333MB
  docker-lldp-sv2            Broadcom_SONiC_2.0   3d1643706454        333MB
  docker-snmp-sv2            latest                            95253ab2447c        343MB
  docker-snmp-sv2            Broadcom_SONiC_2.0   95253ab2447c        343MB
  docker-platform-monitor    latest                            fee943fc2389        376MB
  docker-platform-monitor    Broadcom_SONiC_2.0   fee943fc2389        376MB
  docker-sonic-telemetry     latest                            cd8f502a0ee6        312MB
  docker-sonic-telemetry     Broadcom_SONiC_2.0   cd8f502a0ee6        312MB
  docker-dhcp-relay          latest                            c56dddd78a74        292MB
  docker-dhcp-relay          Broadcom_SONiC_2.0   c56dddd78a74        292MB
  docker-database            latest                            e5c7e3fa6540        282MB
  docker-database            Broadcom_SONiC_2.0   e5c7e3fa6540        282MB
  docker-router-advertiser   latest                            301ca6f95737        281MB
  docker-router-advertiser   Broadcom_SONiC_2.0   301ca6f95737        281MB
  ```
  Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)


## Show System Status
This sub-section explains some set of sub-commands that are used to display the status of various parameters pertaining to the physical state of the network node. 

**show clock**  
This command displays the current date and time configured on the system

-  Usage:  
   show clock

- Example:
  ```
  admin@sonic:~$ show clock
  Mon Mar 25 20:25:16 UTC 2019
  ```


**show environment**  
This command displays the platform environmentals, such as voltages, temperatures and fan speeds

-  Usage:  
   show environment

- Example: 
  ```
  admin@sonic:~$ show environment
  coretemp-isa-0000
  Adapter: ISA adapter
  Core 0:       +28.0 C  (high = +98.0 C, crit = +98.0 C)
  Core 1:       +28.0 C  (high = +98.0 C, crit = +98.0 C)
  Core 2:       +28.0 C  (high = +98.0 C, crit = +98.0 C)
  Core 3:       +28.0 C  (high = +98.0 C, crit = +98.0 C)
  SMF_Z9100_ON-isa-0000
  Adapter: ISA adapter
  CPU XP3R3V_EARLY:              +3.22 V  
  <... few more things ...>

  Onboard Temperature Sensors:
  CPU:                             30 C
  BCM56960 (PSU side):             35 C
  <... few more things ...>

  Onboard Voltage Sensors:
    CPU XP3R3V_EARLY                 3.22 V
  <... few more things ...>

  Fan Trays:
  Fan Tray 1:
    Fan1 Speed:     6192 RPM
    Fan2 Speed:     6362 RPM
    Fan1 State:        Normal
    Fan2 State:        Normal
    Air Flow:            F2B
  <... few more things ...>

  PSUs:
    PSU 1:
      Input:           AC
  <... few more things ...>

  ```
  NOTE: The show output has got lot of information; only the sample output is given in the above example. 
  Though the displayed output slightly differs from one platform to another platform, the overall content will be similar to the example mentioned above.

**show reboot-cause**  
This command displays the cause of the previous reboot

- Usage:  
  show reboot-cause

-  Example:
  ```
  admin@sonic:~$ show reboot-cause
  User issued reboot command [User: admin, Time: Mon Mar 25 01:02:03 UTC 2019]
  ```

**show uptime**  
This command displays the current system uptime

- Usage:  
  show uptime

- Example:
  ```
  admin@sonic:~$ show uptime
  up 2 days, 21 hours, 30 minutes
  ```

**show logging**  
This command displays all the currently stored non-debug-categorized log messages. 
All the latest processes and corresponding transactions are stored in the "syslog" file. 
This file is saved in the path `/var/log` and can be viewed by giving the command ` sudo cat syslog` as this requires root login. 
Individual process can also be viewed using the command `ps -ax | grep <\process name>

- Usage:  
  show logging ([\<process_name\>] [-l lines] | [-f])

- Example:
  ```
  admin@sonic:~$ show logging
  ```
  - It can be useful to pipe the output from `show logging` to the command `more` in order to examine one screenful of log messages at a time

- Example:
  ```
  admin@sonic:~$ show logging | more
  ```
  - Optionally, you can specify a process name in order to display only log messages mentioning that process

- Example:
  ```
  admin@sonic:~$ show logging sensord
  ```
  - Optionally, you can specify a number of lines to display using the `-l' or `--lines` option. Only the most recent N lines will be displayed. Also note that this option can be combined with a process name.

- Examples:
  ```
  admin@sonic:~$ show logging --lines 50
  ```
  ```
  admin@sonic:~$ show logging sensord --lines 50
  ```

  - Optionally, you can follow the log live as entries are written to it by specifying the `-f` or `--follow` flag

- Example: 
  ```
  admin@sonic:~$ show logging --follow
  ```

**show in-memory-logging**
This command displays all the currently stored debug-categorized log messages(LOG_INFO and LOG_DEBUG),
All the latest processes and corresponding transactions related to LOG_INFO and LOG_DEBUG are stored in the in-memory files and their corresponding disk files.

- Usage:
  show in-memory-logging ([\<process_name\>] | [-l lines] | [-f] | [<search keyword>] | more)

- Example:
  ```
  admin@sonic:~$ show in-memory-logging
  ```
  - It can be useful to pipe the output from `show in-memory-logging` to the command `more` in order to examine one screenful of log messages at a time

- Example:
  ```
  admin@sonic:~$ show in-memory-logging | more
  ```
  - Optionally, you can specify a process name in order to display only log messages mentioning that process

- Example:
  ```
  admin@sonic:~$ show in-memory-logging  sensord
  ```
  - Optionally, you can specify a number of lines to display using the `-l' or `--lines` option. Only the most recent N lines will be displayed. Also note that this option can be combin

- Examples:
  ```
  admin@sonic:~$ show in-memory-logging --lines 50
  ```
  ```
  admin@sonic:~$ show in-memory-logging sensord --lines 50
  ```

  - Optionally, you can follow the log live as entries are written to it by specifying the `-f` or `--follow` flag

- Example:
  ```
  admin@sonic:~$ show in-memory-logging --follow
  ```


**show users**  
This command displays a list of users currently logged in to the device

- Usage:  
  show users

- Examples:
  ```
  admin@sonic:~$ show users
  admin    pts/9        Mar 25 20:31 (100.127.20.23)

  admin@sonic:~$ show users
  admin    ttyS1        2019-03-25 20:31

  ```
  Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

## Show Hardware Platform

The information displayed in this set of commands partially overlaps with the one generated by "show environment" instruction. In this case though, the information is presented in a more succinct fashion. In the future these two CLI stanzas may end up getting combined.

**show platform summary**  
This command displays a summary of the device's hardware platform

- Usage:  
  show platform summary

- Example:
  ```
  admin@sonic:~$ show platform summary
  Platform: x86_64-dell_s6000_s1220-r0
  HwSKU: Force10-S6000
  ASIC: broadcom
  ```

**show platform syseeprom**  
This command displays information stored on the system EEPROM.
Note that the output of this command is not the same for all vendor's platforms. 
Couple of example outputs are given below.

- Usage:  
  show platform syseeprom

- Example:
  ```
  admin@sonic:~$ show platform syseeprom
  lsTLV Name             Len Value
  -------------------- --- -----
  PPID                  20 XX-XXXXXX-00000-000-0000
  DPN Rev                3 XXX
  Service Tag            7 XXXXXXX
  Part Number           10 XXXXXX
  Part Number Rev        3 XXX
  Mfg Test Results       2 FF
  Card ID                2 0x0000
  Module ID              2 0
  Base MAC Address      12 FE:EC:BA:AB:CD:EF
  (checksum valid)
  ```

  ```
  admin@arc-switch1025:~$ show platform syseeprom
  TlvInfo Header:
    Id String:    TlvInfo
    Version:      1
    Total Length: 527
  TLV Name             Code Len Value
  ---- --- -----
  Product Name         0x21  64 MSN2700
  Part Number          0x22  20 MSN2700-CS2FO
  Serial Number        0x23  24 MT1822K07815
  Base MAC Address     0x24   6 50:6B:4B:8F:CE:40
  Manufacture Date     0x25  19 05/28/2018 23:56:02
  Device Version       0x26   1 16
  MAC Addresses        0x2A   2 128
  Manufacturer         0x2B   8 Mellanox
  Vendor Extension     0xFD  36
  Vendor Extension     0xFD 164
  Vendor Extension     0xFD  36
  Vendor Extension     0xFD  36
  Vendor Extension     0xFD  36
  Platform Name        0x28  18 x86_64-mlnx_x86-r0
  ONIE Version         0x29  21 2018.08-5.2.0006-9600
  CRC-32               0xFE   4 0x11C017E1

  (checksum valid)
  ```



**show platform psustatus**  
This command displays the status of the device's power supply units

- Usage:  
  show platform psustatus

- Example:
  ```
  admin@sonic:~$ show platform psustatus
  PSU    Status
  -----  --------
  PSU 1  OK
  PSU 2  OK
  ```

**show platform psusummary**  
This command displays various manufacturer and runtime information of the device's power supply units. On the platforms where this command is not spported, deafult values e.g. None, 0, are printed in the output.

- Usage:  
  show platform psusummary

- Example:
  ```
  admin@sonic:~$ show platform psusummary
  PSU 1: NOT OK

  PSU 2: OK
  Manufacturer Id: 3Y POWER
  Model: YM-2651Y
  Serial Number: SA290N091739133077
  Output Voltage (mV): 11906.0
  Output Current (mA): 11421.0
  Output Power (mW): 137000.0
  Fan Direction: INTAKE
  Fan Speed (RPM): 4696
  admin@sonic:~$
  ```

  ```
  admin@sonic:~$ show platform psusummary
  PSU 1: OK
  Manufacturer Id: None
  Model: None
  Serial Number: None
  Output Voltage (mV): None
  Output Current (mA): None
  Output Power (mW): None
  Fan Direction: None
  Fan Speed (RPM): 0

  PSU 2: NOT OK
  admin@sonic:~$
  ```


**show platform fanstatus**  
This command displays various information of the device's FAN units. On the platforms where this command is not spported, nothing is printed in the output.

- Usage:  
  show platform fanstatus

- Example:
  ```
  admin@sonic:~$ show platform fanstatus
  FAN     Status      Speed (RPM)  Direction
  ------  --------  -------------  -----------
  FAN 1   OK                 8700  INTAKE
  FAN 2   OK                 7500  INTAKE
  FAN 3   OK                 8600  INTAKE
  FAN 4   OK                 7300  INTAKE
  FAN 5   OK                 8800  INTAKE
  FAN 6   OK                 7500  INTAKE
  FAN 7   OK                 8800  INTAKE
  FAN 8   OK                 7500  INTAKE
  FAN 9   OK                 8600  INTAKE
  FAN 10  OK                 7300  INTAKE
  FAN 11  OK                 8600  INTAKE
  FAN 12  OK                 7300  INTAKE
  admin@sonic:~$
  ```

  ```
  admin@sonic:~$ show platform fanstatus
  admin@sonic:~$
  ```



### Transceivers
Displays diagnostic monitoring information of the transceivers

**show interfaces transceiver**  
This command displays information for all the interfaces for the transceiver requested or a specific interface if the optional "interface-name" is specified.

- Usage:  
  show interfaces transceiver [eeprom | lpmode | presence]
  show interfaces transceiver [eeprom [-d | --dom] | lpmode | presence] [<interface_name>]

- Example (Decode and display information stored on the SFP EEPROM): 
  ```
  admin@sonic:~$ show interfaces transceiver eeprom --dom Ethernet0
  Ethernet0: SFP detected
          Connector : No separable connector
          Encoding : Unspecified
          Extended Identifier : Unknown
          Extended RateSelect Compliance : QSFP+ Rate Select Version 1
          Identifier : QSFP+
          Length Cable Assembly(m) : 1
          Specification compliance :
                  10/40G Ethernet Compliance Code : 40GBASE-CR4
                  Fibre Channel Speed : 1200 Mbytes/Sec
                  Fibre Channel link length/Transmitter Technology : Electrical inter-enclosure (EL)
                  Fibre Channel transmission media : Twin Axial Pair (TW)
          Vendor Date Code(YYYY-MM-DD Lot) : 2015-10-31
          Vendor Name : XXXXX
          Vendor OUI : XX-XX-XX
          Vendor PN : 1111111111
          Vendor Rev :
          Vendor SN : 111111111
          ChannelMonitorValues:
                RX1Power: -1.1936dBm
                RX2Power: -1.1793dBm
                RX3Power: -0.9388dBm
                RX4Power: -1.0729dBm
                TX1Bias: 4.0140mA
                TX2Bias: 4.0140mA
                TX3Bias: 4.0140mA
                TX4Bias: 4.0140mA
          ModuleMonitorValues :
                  Temperature : 1.1111C
                  Vcc : 0.0000Volts
  ```

- Example (Display status of low-power mode):
  ```  
  admin@sonic:~$ show interfaces transceiver lpmode Ethernet100
  Port         Low-power Mode
  -----------  ----------------
  Ethernet100  On
  ```


- Example (Display SFP transceiver presence):
  ```
  admin@sonic:~$ show interfaces transceiver presence Ethernet100
  Port         Presence
  -----------  ----------
  Ethernet100  Present
  ```
  Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Basic-Configuration-And-Show)

# AAA TACACS+ &amp; RADIUS Configuration And Show
This section captures the various show commands and configuration commands that are applicable for the AAA (Authentication, Authorization, and Accounting) module.
Admins can configure the type of authentication (local or remote tacacs based) required for the users and also the authentication failthrough and fallback options.
Following show command displays the current running configuration related to the AAA.

## AAA Configuration And Show

### AAA show commands

This command is used to view the Authentication, Authorization and Accounting settings that are configured in the network node.

**show aaa**  
This command displays the AAA settings currently present in the network node

- Usage:  
  show aaa  

- Example:
   ```
   admin@sonic:~$ show aaa
   AAA authentication login local (default)
   AAA authentication failthrough True (default)
   AAA authentication fallback True (default)
   AAA nss passwd login (default)
   AAA nss shadow login (default)
   AAA nss group login (default)
   AAA nss netgroup local (default)
   AAA authorization login local (default)
   ```

### AAA config commands

This sub-section explains all the possible CLI based configuration options for the AAA module. The list of commands/sub-commands possible for aaa is given below.

```
Command: aaa authentication
         sub-commands:
           aaa authentication debug
           aaa authentication failthrough
           aaa authentication fallback
           aaa authentication login
```

```
Command: aaa authorization
         sub-commands:
           aaa authorization login
```

```
Command: aaa nss
         sub-commands:
           aaa nss [ group | netgroup | passwd | shadow | sudoers ]
```

**aaa authentication failthrough**  

This command is used to either enable or disable the failthrough option. 
This command is useful when user has configured more than one tacacs+/radius server and when user has enabled tacacs+/radius authentication.
When authentication request to the first server fails, this configuration allows to continue the request to the next server.
When this configuration is enabled, authentication process continues through all servers configured.
When this is disabled and if the authentication request fails on first server, authentication process will stop and the login will be disallowed.


- Usage:  
  config aaa authentication failthrough enable|disable|default

  	   Allow AAA fail-through [enable | disable | default]
  	     enable - this allows the AAA module to process with local authentication if remote authentication fails.
  	   disbale - this disallows the AAA module to proceed further if remote authentication fails.
  	   default - this re-configures the default value, which is "enable". 


- Example:
  ```
  admin@sonic:~$ sudo -i
  root@sonic:~# config aaa authentication failthrough enable
  root@sonic:~# 
  ```
  **aaa authentication fallback**  

The command is not used at the moment. 
When the tacacs+ authentication fails, it falls back to local authentication by default.

- Usage:  
  config aaa authentication fallback enable|disable|default

     Allow AAA fallback [enable | disable | default]

- Example:
  ```
  root@sonic:~# config aaa authentication fallback enable
  root@sonic:~# 
  ```

**aaa authentication login**  

This command is used to configure whether AAA should use the local database, or remote ldap/radius/tacacs+ databases for user authentication. 
By default, AAA uses the local database for authentication. New users can be added/deleted using the linux commands (Note that the configuration done using linux commands are not preserved during reboot).
Admins can enable remote tacacs+/radius/ldap server based authentication by selecting the AUTH_PROTOCOL as tacacs+/radius in this command.
Admins need to configure the tacacs+/radius server accordingly and ensure that the connectivity to tacacs+/radius/ldap server is available via the management interface.
Once admins choose remote tacacs+/radius/ldap authentication, all user logins will be authenticated by the tacacs+/radius/ldap server.
If the authentication fails, AAA will check the "failthrough" configuration and authenticates the user based on local database if failthrough is enabled, and local authentication has been configured as fallback mechanism.

- Usage:  
  config aaa login authentication default
  config aaa login authentication local
  config aaa login authentication {tacacs+, radius, ldap} [ local ]

    tacacs+ - This enables remote authentication based on tacacs+
    radius - This enables remote authentication based on radius
    ldap - This enables remote authentication based on ldap
    local - this disables remote authentication and uses local authentication or uses local authentication as fallback
    default - reset back to default value, which is nothing but the "local" authentication


- Example:
  For configuring authentication with tacacs+, and if none respond fallback
  to local:
  ```
  root@sonic:~# config aaa authentication login tacacs+ local
  root@sonic:~# 
  ```
  For configuring authentication with radius, and if none respond fallback
  to local:
  ```
  root@sonic:~# config aaa authentication login radius local
  root@sonic:~# 
  ```
  For configuring authentication with ldap, and if none respond fallback
  to local:
  ```
  root@sonic:~# config aaa authentication login ldap local
  root@sonic:~#
  ```

**aaa authorization login**

By default, AAA performs login authorization (account verification) using the
local (or unix) module.  This command is used to configure whether AAA should use the remote ldap database for user login authorization.

- Usage:
  config aaa login authorization { ldap | local | default }

    ldap - This enables remote login authorization based on ldap
    local - This disables remote login authorization. (default)
    default - reset back to default value.


- Example:
  For configuring remote login authorization with ldap:
  ```
  root@sonic:~# config aaa authorization login ldap
  root@sonic:~#
  ```

**aaa nss**

By default, the Name Service information is provided in local flat files on the
SONiC device (/etc/passwd, /etc/shadow, /etc/group).
When remote authentication is configured, by default, the corresponding remote AAA servers also provide the Name Service information.
This command is used to configure whether AAA should use the remote ldap database for Name Service information.

- Usage:
  config aaa login nss { group | passwd | shadow } { ldap | local | login | default }
  config aaa login nss { netgroup | sudoers } { ldap | local | default }

    ldap - This enables Name Service based on ldap
    login - This enables Name Service based on authentication (default, for passwd, shadow, group)
    local - This enables Name Service based on local flat files. (default, for netgroup, sudoers)
    default - reset back to default value.

- Example:
  For configuring Name Service information provided by ldap:
  ```
  root@sonic:~# config aaa nss passwd ldap
  root@sonic:~# config aaa nss shadow ldap
  root@sonic:~# config aaa nss group ldap
  root@sonic:~# config aaa nss netgrop ldap
  root@sonic:~# config aaa nss sudoers ldap
  root@sonic:~#
  ```

## TACACS+ Configuration And Show

### TACACS+ show commands

**show tacacs**  

This command displays the global configuration fields and the list of all tacacs servers and their correponding configurations.

- Usage:  
  show tacacs 

- Example:
  ``` 
  TACPLUS global auth_type pap (default)
  TACPLUS global timeout 99
  TACPLUS global passkey <EMPTY_STRING> (default)

  TACPLUS_SERVER address 10.11.12.14
  			   priority 9
  			   tcp_port 50
  			   auth_type mschap
  			   timeout 10
  			   passkey testing789

  TACPLUS_SERVER address 10.0.0.9
  			   priority 1
  			   tcp_port 49
  ```

### TACACS+ Config commands

This sub-section explains the command "config tacacs" and its sub-commands that are used to configure the following tacacs+ parameters.
Some of the parameters like authtype, passkey and timeout can be either configured at per server level or at global level (global value will be applied if there no server level configuration)

1) Add/Delete the tacacs+ server details.
2) authtype - global configuration that is applied to all servers if there is no server specific configuration.
3) default - reset the authtype or passkey or timeout to the default values.
4) passkey - global configuration that is applied to all servers if there is no server specific configuration.
5) timeout - global configuration that is applied to all servers if there is no server specific configuration.

**config tacacs add**  

This command is used to add a TACACS+ server to the tacacs server list.
Note that more than one tacacs+ (maximum of eight) can be added in the device. 
When user tries to login, tacacs client shall contact the servers one by one. 
When any server times out, device will try the next server one by one based on the priority value configured for that server.
When this command is executed, the configured tacacs+ server addresses are updated in /etc/pam.d/common-auth-sonic configuration file which is being used by tacacs service.

- Usage:  
   config tacacs add <ip_address> [-t|--timeout SECOND] [-k|--key SECRET] [-a|--type TYPE] [-o|--port PORT] [-p|--pri PRIORITY] [-m|--use-mgmt-vrf]

    **Arguments:**  

    - ip_address - TACACS+ server IP address.
    - timeout - Transmission timeout interval in seconds, range 1 to 60, default 5
    - key - Shared secret
    - type - Authentication type, "chap" or "pap" or "mschap" or "login", default is "pap".
    - port - TCP port range is 1 to 65535, default 49
    - pri - Priority, priority range 1 to 64, default 1.
    - use-mgmt-vrf - this means that the server is part of Management vrf, default is "no vrf"


- Example:
  ```	 
  root@T1-2:~# config tacacs add 10.11.12.13 -t 10 -k testing789 -a mschap -o 50 -p 9
  root@T1-2:~#

  Example Server Configuration in /etc/pam.d/common-auth-sonic configuration file:

  auth    [success=done new_authtok_reqd=done default=ignore]     pam_tacplus.so server=10.11.12.14:50 secret=testing789 login=mschap timeout=10  try_first_pass
  auth    [success=done new_authtok_reqd=done default=ignore]     pam_tacplus.so server=10.11.12.24:50 secret=testing789 login=mschap timeout=987654321098765433211
  0987  try_first_pass
  auth    [success=done new_authtok_reqd=done default=ignore]     pam_tacplus.so server=10.0.0.9:49 secret= login=mschap timeout=5  try_first_pass
  auth    [success=done new_authtok_reqd=done default=ignore]     pam_tacplus.so server=10.0.0.8:49 secret= login=mschap timeout=5  try_first_pass
  auth    [success=done new_authtok_reqd=done default=ignore]     pam_tacplus.so server=10.11.12.13:50 secret=testing789 login=mschap timeout=10  try_first_pass
  auth    [success=1 default=ignore]      pam_unix.so nullok try_first_pass
  
  NOTE: In the above example, the servers are stored (sorted) based on the priority value configured for the server.
  ```

**config tacacs delete**  

This command is used to delete the tacacs+ servers configured.

- Usage:  
   config tacacs delete <ip_address>

- Example:
  ```
  root@T1-2:~# config tacacs delete 10.11.12.13
  root@T1-2:~#
  ```

**config tacacs authtype**  

This command is used to modify the global value for the TACACS+ authtype.
When user has not configured server specific authtype, this global value shall be used for that server.

- Usage:  
     config tacacs authtype  chap|pap||mschap|login

- Example:
  ```
  root@T1-2:~# config tacacs authtype mschap
  root@T1-2:~#
  ```


**config tacacs default**  

This command is used to reset the global value for authtype or passkey or timeout to default value. 
Default for authtype is "pap", default for passkey is EMPTY_STRING and default for timeout is 5 seconds.

- Usage:  
   config tacacs default authtype|passkey|timeout

- Example:
  ```
  root@T1-2:~# config tacacs default authtype
  This will reset the global authtype back to the default value "pap".
  ```

**config tacacs passkey**  

This command is used to modify the global value for the TACACS+ passkey.
When user has not configured server specific passkey, this global value shall be used for that server.
The passkey can include all printable ASCII characters with a few exceptions (#, SPACE, and COMMA). Its maximum length can be 65 characters.

- Usage:  
  config tacacs passkey <pass_key>


- Example:
  ```
  root@T1-2:~# config tacacs passkey testing123
  root@T1-2:~#
  ```


**config tacacs timeout**  

This command is used to modify the global value for the TACACS+ timeout.
When user has not configured server specific timeout, this global value shall be used for that server.


- Usage:
  ```
  config tacacs [default] timeout [\<timeout_value_in_seconds\>]  
  valid values for timeout is 1 to 60 seconds. 
  When the optional keyword "default" is specified, timeout_value_in_seconds parameter wont be used; default value of 5 is used.
  Configuration using the keyword "default" is introduced in 201904 release.
  ```

- Example: To configure non-default timeout value  
  ```
  root@T1-2:~# config tacacs timeout 60
  root@T1-2:~#
  ```



## RADIUS Configuration And Show

### RADIUS show commands

**show radius**  

This command displays the global configuration fields and the list of all radius servers and their correponding configurations.

- Usage:  
	show radius 

- Example:
  ``` 
	RADIUS global auth_type pap (default)
        RADIUS global retransmit 3 (default)
	RADIUS global timeout 5 (default)
	RADIUS global passkey <EMPTY_STRING> (default)

	RADIUS_SERVER address 10.11.12.17
				   priority 1
				   auth_port 1812
				   auth_type mschapv2
				   retransmit 2
				   timeout 10
				   passkey testing123
				   vrf mgmt

	RADIUS_SERVER address 10.0.0.15
				   priority 1
				   auth_port 1645
  ```

### RADIUS Config commands

This sub-section explains the command "config radius" and its sub-commands that are used to configure the following radius parameters.
Some of the parameters like authtype, passkey, retransmit, and timeout can be either configured at per server level or at global level (global value will be applied if there no server level configuration)

1) Add/Delete the radius server details.
2) authtype - global configuration that is applied to all servers if there is no server specific configuration.
3) default - reset the authtype, passkey, retranmit, or timeout to the default values.
4) passkey - global configuration that is applied to all servers if there is no server specific configuration.
5) timeout - global configuration that is applied to all servers if there is no server specific configuration.
5) retransmit - global configuration that is applied to all servers if there is no server specific configuration.
6) sourceip - global configuration for source ip address.
7) nasip - global configuration for NAS IP|IPv6 address.
8) statistics - global configuration to enable statistics collection

**config radius add**  

This command is used to add a RADIUS server to the radius server list.
Note that more than one radius server (maximum of eight) can be added in the device. 
When a user tries to login, radius client shall contact the servers one by one. 
When any server times out, device will try the next server one by one based on the priority value configured for that server.
When this command is executed, the configured radius server addresses are updated.

- Usage:  
   config radius add <ip_address> [-r|--retransmit INTEGER] [-t|--timeout SECOND] [-k|--key SECRET] [-a|--type TYPE] [-o|--auth-port PORT] [-p|--pri PRIORITY] [-m|--use-mgmt-vrf] [-s|--source-interface TEXT]
	
	 **Arguments:**  
	
	 ip_address - RADIUS server IP address.
	 retransmit - Retransmit attempts. range 0 to 10. default 3
	 timeout - Transmission timeout interval in seconds. range 1 to 60. default 5
	 key - Shared secret
	 type - Authentication type, "chap","pap", or "mschapv2". default is "pap".
	 auth-port - UDP port range is 1 to 65535, default 1812
	 pri - Priority, priority range 1 to 64, default 1.
	 use-mgmt-vrf - this means that the server is part of Management vrf, default is "no vrf"
         source-interface - Source Interface for outgoing RADIUS IP packets.


- Example:
  ```	 
  root@T1-2:~# config radius add 10.11.12.17 -t 10 -k testing123 -a mschapv2 -o 1645 -p 9
  ```

**config radius delete**  

This command is used to delete the radius servers configured.

- Usage:  
   config radius delete <ip_address>

- Example:
  ```
  root@T1-2:~# config radius delete 10.11.12.17
  root@T1-2:~#
  ```

**config radius authtype**  

This command is used to modify the global value for the RADIUS authtype.
When user has not configured server specific authtype, this global value shall be used for that server.

   - Usage:  
     config radius authtype  chap|pap||mschapv2

- Example:
  ```
  root@T1-2:~# config radius authtype mschapv2
  root@T1-2:~#
  ```
  

**config radius default**  

This command is used to reset the global value for authtype, passkey, retransmit, or timeout to default value. 
Default for authtype is "pap", default for passkey is EMPTY_STRING, default for retransmit is 3, and default for timeout is 5 seconds.

- Usage:  
   config radius default authtype|passkey|retransmit|timeout
	
- Example:
  ```
  root@T1-2:~# config radius default authtype
  This will reset the global authtype back to the default value "pap".
  ```

**config radius passkey**  

This command is used to modify the global value for the RADIUS passkey.
When user has not configured server specific passkey, this global value shall be used for that server.
The passkey can include all printable ASCII characters with a few exceptions (#, SPACE, and COMMA). Its maximum length can be 65 characters.

   - Usage:  
     config radius passkey <pass_key>


- Example:
  ```
  root@T1-2:~# config radius passkey testing123
  root@T1-2:~#
  ```
  

**config radius retransmit**  

This command is used to modify the global value for the RADIUS retransmit.
When user has not configured server specific retransmit, this global value shall be used for that server.


   - Usage:  

    config radius [default] retransmit [\<retransmit_attempts\>]  
     valid values for retransmit are from 0 to 10 seconds. 
     When the optional keyword "default" is specified, retransmit_attempts parameter wont be used; default value of 3 is used.

- Example: To configure non-default retransmit value  
  ```
  root@T1-2:~# config radius retransmit 2
  root@T1-2:~#
  ```

**config radius timeout**  

This command is used to modify the global value for the RADIUS timeout.
When user has not configured server specific timeout, this global value shall be used for that server.


   - Usage:  

    config radius [default] timeout [\<timeout_value_in_seconds\>]  
     valid values for timeout is 1 to 60 seconds. 
     When the optional keyword "default" is specified, timeout_value_in_seconds parameter wont be used; default value of 5 is used.

- Example: To configure non-default timeout value  
  ```
  root@T1-2:~# config radius timeout 60
  root@T1-2:~#
  ```

**config radius statistics**

This command is used to modify the global value for enabling RADIUS statistics collection.

   - Usage:

    config radius statistics  [enable | disable | default]

- Example: To configure statistics collection.
  ```
  root@T1-2:~# config radius statistics enable
  root@T1-2:~#
  ```

## LDAP Configuration And Show

### LDAP show commands

**show ldap**

This command displays the global configuration fields and the list of all ldap servers and their correponding configurations.

- Usage:
	show ldap

- Example:
  ```
        LDAP global timelimit 0 (default)
        LDAP global retry 0 (default)
        LDAP global ldap_version 3 (default)
        LDAP global scope sub (default)
        LDAP global idle_timelimit 0 (default)
        LDAP global ssl off (default)
        LDAP global base cn=example,cn=com
        LDAP global use_type all (default)
        LDAP global port 0 (default)
        LDAP global bind_timelimit 10 (default)

        LDAP_MAP_OBJECTCLASS

        LDAP_MAP_ATTRIBUTE
 
        LDAP_MAP_DEFAULT_ATTRIBUTE_VALUE
 
        LDAP_MAP_OVERRIDE_ATTRIBUTE_VALUE
 
        LDAP_SERVER address 10.11.12.17
                       priority 1
                       retry 2
                       use_type all

  ```

### LDAP Config commands

This sub-section explains the command "config ldap" and its sub-commands that are used to configure the ldap parameters.
LDAP parameters can be configured at the global level, or on a server use-type level. Servers can be configured for all use-types (the default), or only for certain use-types (nss -- Name Service, sudo -- sudoers authentication, pam -- authentication, login authorization).
Parameters like base, timelimit, ... can be configured at the global level or on a per use_type (nss, sudo, pam) level.
Some of the parameters like retry, ssl, and port can be either configured at per server level, per use-type level,  or at the global level (global value will be applied if there no server level configuration)

```

  add                         Add an LDAP server
  delete                      Delete an LDAP server
  base                        LDAP global|nss|pam|sudo base <STRING>
  bind_timelimit              LDAP global|nss|pam|sudo bind_timelimit
  binddn                      LDAP global|nss|pam|sudo binddn <STRING>
  bindpw                      LDAP global|nss|pam|sudo bindpw <STRING>
  default                     set its default configuration
  idle_timelimit              LDAP global|nss|pam|sudo idle_timelimit
  ldap_version                LDAP global|nss|pam|sudo ldap_version [2|3]
  map                         Specify LDAP Map
  nss                         LDAP server nss configuration
  nss_base_group              LDAP global|nss|pam|sudo nss_base_group...
  nss_base_netgroup           LDAP global|nss|pam|sudo nss_base_netgroup...
  nss_base_passwd             LDAP global|nss|pam|sudo nss_base_passwd...
  nss_base_shadow             LDAP global|nss|pam|sudo nss_base_shadow...
  nss_initgroups_ignoreusers  LDAP global|nss|pam|sudo...
  pam                         LDAP server pam configuration
  pam_filter                  LDAP global|nss|pam|sudo pam_filter <STRING>
  pam_groupdn                 LDAP global|nss|pam|sudo pam_groupdn <STRING>
  pam_login_attribute         LDAP global|nss|pam|sudo pam_login_attribute...
  pam_member_attribute        LDAP global|nss|pam|sudo pam_member_attribute...
  port                        LDAP global|nss|pam|sudo port <1 - 65535>
  retry                       LDAP global|nss|pam|sudo retry attempts <1 -...
  scope                       LDAP global|nss|pam|sudo scope [sub|one|base]
  ssl                         LDAP global|nss|pam|sudo ssl...
  sudo                        LDAP server sudo configuration
  sudoers_base                LDAP global|nss|pam|sudo sudoers_base...
  timelimit                   LDAP global|nss|pam|sudo timelimit

```

**config ldap add**

This command is used to add an LDAP server to the ldap server list.
Note that more than one ldap server (maximum of eight) can be added in the device.
When a user tries to login, ldap client shall contact the servers one by one.
When any server times out, device will try the next server one by one based on the priority value configured for that server.
When this command is executed, the configured ldap server addresses are updated.

- Usage:
   config ldap add <ip_address> [-r|--retry INTEGER] [-u|--use-type TYPE] [-o|--port PORT] [-p|--pri PRIORITY] [-s|--ssl TLS]
	
	 **Arguments:**
	
	 ip_address - LDAP server IP address.
	 retry - retry attempts. range 0 to 10. default 0
	 use-type - all,nss,pam,sudo,nss_sudo,nss_pam,sudo_pam
	 port - port range is 1 to 65535
	 pri - Priority, priority range 1 to 64, default 1.
         ssl - on,off,start_tls.


- Example:
  ```
  root@T1-2:~# config ldap add 10.11.12.17 -o 389 -p 9
  ```

**config ldap delete**

This command is used to delete the ldap servers configured.

- Usage:
   config ldap delete <ip_address>

- Example:
  ```
  root@T1-2:~# config ldap delete 10.11.12.17
  root@T1-2:~#
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#AAA-Configuration-And-Show)



# ACL Configuration And Show

This section explains the various show commands and configuration commands available for users.

## ACL show commands

**show acl table**  

This command displays either all the ACL tables that are configured or only the specified "TABLE_NAME". 
Output from the command displays the table name, type of the table, the list of interface(s) to which the table is bound and the description about the table.

- Usage:  
  show acl table [TABLE_NAME]

- Example:
  ```
  admin@sonic:~$ show acl table
  Name      Type       Binding          Description
  --------  ---------  ---------------  -------------
  EVERFLOW  MIRROR     Ethernet16       EVERFLOW
                       Ethernet96
                       Ethernet108
                       Ethernet112
                       PortChannel0001
                       PortChannel0002
  SNMP_ACL  CTRLPLANE  SNMP             SNMP_ACL
  DT_ACL_T1 L3         Ethernet0        DATA_ACL_TABLE_1
                       Ethernet4
                       Ethernet112
                       Ethernet116
  SSH_ONLY  CTRLPLANE  SSH              SSH_ONLY
  ```

**show acl rule**  

This command displays all the ACL rules present in all the ACL tables or only the rules present in specified table "TABLE_NAME" or only the rule matching the RULE_ID option.
Output from the command gives the following information about the rules
1) Table name - ACL table name to which the rule belongs to.
2) Rule name - ACL rule name
3) Priority - Priority for this rule.
4) Action - Action to be performed if the packet matches with this ACL rule. It could be either Drop or Permit. Users can choose to have a default permit rule or default deny rule. In case of default "deny all" rule, add the permitted rules on top of the deny rule. In case of the default "permit all" rule, users can add the deny rules on top of it. If users have not confgured any rule, SONiC allows all traffic (which is "permit all").
5) Match  - The fields from the packet header that need to be matched against the same present in the incoming traffic. 

- Usage:  
  show acl rule [TABLE_NAME] [RULE_ID]

- Example:
  ```
  admin@sonic:~$ show acl rule
  Table     Rule          Priority    Action    Match
  --------  ------------  ----------  --------  ------------------
  SNMP_ACL  RULE_1        9999        ACCEPT    IP_PROTOCOL: 17
                                                SRC_IP: 1.1.1.1/32
  SSH_ONLY  RULE_1        9999        ACCEPT    IP_PROTOCOL: 6
                                                SRC_IP: 1.1.1.1/32
  SNMP_ACL  DEFAULT_RULE  1           DROP      ETHER_TYPE: 2048
  SSH_ONLY  DEFAULT_RULE  1           DROP      ETHER_TYPE: 2048
  ```

## ACL config commands
This sub-section explains the list of configuration options available for ACL module.
Note that there is no direct command to add or delete or modify the ACL table and ACL rule. 
Existing ACL tables and ACL rules can be updated by specifying the ACL rules in json file formats and configure those files using this CLI command.

```
Command :acl
          update
             full
             incremental
```

**config acl update full**  

This command is to update the rules in all the tables or in one specific table in full. If a table_name is provided, the operation will be restricted in the specified table. All existing rules in the specified table or all tables will be removed. New rules loaded from file will be installed. If the table_name is specified, only rules within that table will be removed and new rules in that table will be installed. If the table_name is not specified, all rules from all tables will be removed and only the rules present in the input file will be added.

The command does not modify anything in the list of acl tables. It modifies only the rules present in those pre-existing tables.

In order to create acl tables, either follow the config_db.json method or minigraph method to populate the list of ACL tables.

After creating tables, either the config_db.json method or the minigraph method or the CLI method (explained here) can be used to populate the rules in those ACL tables. 

This command updates only the ACL rules and it does not disturb the ACL tables; i.e. the output of "show acl table" is not alterted by using this command; only the output of "show acl rule" will be changed after this command.

When "--session_name" optional argument is specified, command sets the session_name for the ACL table with this mirror session name. It fails if the specified mirror session name does not exist.

When the optional argument "max_priority"  is specified, each rules priority is calculated by subtracting its "sequence_id" value from the "max_priority". If this value is not passed, the default "max_priority" 10000 is used.
​
- Usage:  
  config acl update full FILE_NAME  
  Some of the possible options are
  1) --table_name <table_name>, Example: config acl update full " --table_name DT_ACL_T1  /etc/sonic/acl_table_1.json "
  2) --session_name <session_name>, Example: config acl update full " --session_name mirror_ses1 /etc/sonic/acl_table_1.json "
  3) --max_priority <priority_value>, Example: config acl update full " --max-priority 100  /etc/sonic/acl_table_1.json "

  NOTE: All these optional parameters should be inside the double quotes. If none of the options are provided, double quotes is not required for specifying filename alone.
  Any number of optional parameters can be configured in the same command.

- Example:
  ``` 
  admin@sonic:~$ config acl update full /etc/sonic/acl_full_snmp_1_2_ssh_4.json
  admin@sonic:~$ config acl update full " --table_name SNMP-ACL /etc/sonic/acl_full_snmp_1_2_ssh_4.json "
  admin@sonic:~$ config acl update full " --session_name everflow0 /etc/sonic/acl_full_snmp_1_2_ssh_4.json "

  This command will remove all rules from all the ACL tables and insert all the rules present in this input file.
  Refer the example file [acl_full_snmp_1_2_ssh_4.json](#) that adds two rules for SNMP (Rule1 and Rule2) and one rule for SSH (Rule4)
  Refer an example for input file format [here](https://github.com/Azure/sonic-mgmt/blob/master/ansible/roles/test/files/helpers/config_service_acls.sh)
  Refer another example [here](https://github.com/Azure/sonic-mgmt/blob/master/ansible/roles/test/tasks/acl/acltb_test_rules_part_1.json)
  ```


**config acl update incremental:**  

This command is used to perform incremental update of ACL rule table. This command gets existing rules from Config DB and compares with rules specified in input file and performs corresponding modifications.

With respect to DATA ACLs, the command does not assume that new dataplane ACLs can be inserted in betweeen by shifting existing ACLs in all ASICs. Therefore, this command performs a full update on dataplane ACLs. 
With respect to control plane ACLs, this command performs an incremental update.
If we assume that "file1.json" is the already loaded ACL rules file and if "file2.json" is the input file that is passed as parameter for this command, the following requirements are valid for the input file.
1) First copy the file1.json to file2.json.
2) Remove the unwanted ACL rules from file2.json
3) Add the newly required ACL rules into file2.json.
4) Modify the existing ACL rules (that require changes) in file2.json.

NOTE: If any ACL rule that is already available in file1.json is required even after this command execution, such rules should remain unalterted in file2.json. Don't remove them.
Note that "incremental" is working like "full".

When "--session_name" optional argument is specified, command sets the session_name for the ACL table with this mirror session name. It fails if the specified mirror session name does not exist.

When the optional argument "max_priority"  is specified, each rule?s priority is calculated by subtracting its ?sequence_id? value from the ?max_priority?. If this value is not passed, the default ?max_priority? 10000 is used.

  - Usage:  
    config acl update incremental FILE_NAME
    Some of the possible options are
    1) --session_name <session_name>, Example: config acl update full " --session_name mirror_ses1 /etc/sonic/acl_table_1.json "
    2) --max-priority <priority_value>, Example: config acl update full " --max-priority 100  /etc/sonic/acl_table_1.json "

    NOTE: All these optional parameters should be inside the double quotes. If none of the options are provided, double quotes is not required for specifying filename alone.
    Any number of optional parameters can be configured in the same command.

- Example:
  ```
  admin@sonic:~$ config acl update incremental /etc/sonic/acl_incremental_snmp_1_3_ssh_4.json
  admin@sonic:~$ config acl update incremental " --session_name everflow0 /etc/sonic/acl_incremental_snmp_1_3_ssh_4.json "

  Refer the example file [acl_incremental_snmp_1_3_ssh_4.json](#) that adds two rules for SNMP (Rule1 and Rule3) and one rule for SSH (Rule4)
  When this "incremental" command is executed after "full" command, it has removed SNMP Rule2 and added SNMP Rule3 in the example.
  File "acl_full_snmp_1_2_ssh_4.json" has got SNMP Rule1, SNMP Rule2 and SSH Rule4.
  File "acl_incremental_snmp_1_3_ssh_4.json" has got SNMP Rule1, SNMP Rule3 and SSH Rule4.
  This file is created by copying the file "acl_full_snmp_1_2_ssh_4.json" to "acl_incremental_snmp_1_3_ssh_4.json" and then removing SNMP Rule2 and adding SNMP Rule3. 

  ```

**config acl rule delete <table_name> [rule_name]**

Deletes a specified rule from the ACL. If the rule name is not provided then deletes all the rules for the ACL.

- Usage:  
  sudo config acl rule delete <table_name> [rule_name]

- Examples:

  ```
  admin@sonic:~$ sudo config acl rule delete l3_acl_0 rule_1
  admin@sonic:~$ sudo config acl rule delete l3_acl_0
  ```

**config acl table delete [table_name]**

Deletes a specified ACL Table. If the table name is not provided then deletes all the ACL Tables.

- Usage:  
  sudo config acl table delete [table_name]

- Examples:

  ```
  admin@sonic:~$ sudo config acl table delete l3_acl_0
  admin@sonic:~$ sudo config acl table delete
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#ACL-Configuration-And-Show)


# ARP &amp; NDP

## ARP show commands

**show arp**  

This command displays the ARP entries in the device with following options.
1) Display the entire table.
2) Display the ARP entries learnt on a specific interface.
3) Display the ARP entry of a specific ip-address.
4) Display the ARP entries learnt in a specific VRF.

  - Usage:  
    show arp [-if \<if_name\>] [\<ip_address\>] [-vrf \<vrf_name\>]
    show arp - displays all entries
    show arp -if <ifname> - displays the ARP entries specific to the interface.
    show arp <ip-address> - displays the ARP entries specific to the ip-address.
    show arp -vrf <vrf_name> - displays the ARP entries specific to the VRF.


- Example:
  ```
  admin@sonic:~$ show arp
   Address          MacAddress            Iface         Vlan
  -------------     -----------------     -------       ------
  192.168.1.183     88:5a:92:fb:bf:41     Ethernet44    -
  192.168.1.175     88:5a:92:fc:95:81     Ethernet28    -
  192.168.1.181     e4:c7:22:c1:07:7c     Ethernet40    -
  192.168.1.179     88:5a:92:de:a8:bc     Ethernet36    -
  192.168.1.118     00:1c:73:3c:de:43     Ethernet64    -
  192.168.1.11      00:1c:73:3c:e1:38     Ethernet88    -
  192.168.1.161     24:e9:b3:71:3a:01     Ethernet0     -
  192.168.1.189     24:e9:b3:9d:57:41     Ethernet56    -
  192.168.1.187     74:26:ac:8b:8f:c1     Ethernet52    -
  192.168.1.165     88:5a:92:de:a0:7c     Ethernet8     -

  Total number of entries 10
  ```

  - Optionally, you can specify the interface in order to display the ARPs learnt on that particular interface


- Example:
  ```
    admin@sonic:~$ show arp -if Ethernet40
    Address          MacAddress          Iface        Vlan
    -------------    -----------------   ----------   ------
    192.168.1.181    e4:c7:22:c1:07:7c   Ethernet40   -
    Total number of entries 1 
  ```

  - Optionally, you can specify an IP address in order to display only that particular entry

- Example:
  ```
    admin@sonic:~$ show arp 192.168.1.181
    Address          MacAddress          Iface        Vlan
    -------------    -----------------   ----------   ------
    192.168.1.181    e4:c7:22:c1:07:7c   Ethernet40   -
    Total number of entries 1 
  ```

  - Optionally, you can specify a VRF in order to display entries learnt in the VRF

- Example:
  ```
    root@sonic:~# show arp -vrf Vrf-blue
    Address     MacAddress         Iface      Vlan
    ----------  -----------------  ---------  ------
    192.85.2.2  00:10:94:00:00:02  Ethernet4  -
    192.85.2.3  00:10:94:00:00:02  Ethernet4  -
    Total number of entries 2
    root@sonic:~#
  ```

## NDP show commands

**show ndp**  
This command displays:
1) Mac addresses of all IPv6 neighbors
2) Mac address of a particular IPv6 neighbor
3) Mac addresses of all IPv6 neighbors reachable via a specific interface.
4) Mac addresses of all IPv6 neighbors reachable in a specific VRF

  - Usage:  
    show ndp [-if|--iface \<interface_name\>] [IPv6_ADDRESS] [-vrf|--vrf \<vrf_name\>]


- Example:
  ```
    **ALL IPv6 NEIGHBORS:**
    admin@sonic:~$ show ndp
    Address                   MacAddress         Iface    Vlan    Status
    ------------------------  -----------------  -------  ------  ---------
    fe80::20c:29ff:feb8:b11e  00:0c:29:b8:b1:1e  eth0     -       REACHABLE
    fe80::20c:29ff:feb8:cff0  00:0c:29:b8:cf:f0  eth0     -       REACHABLE
    fe80::20c:29ff:fef9:324   00:0c:29:f9:03:24  eth0     -       REACHABLE
    Total number of entries 3

    **SPECIFIC IPv6 NEIGHBOR**
    admin@sonic:~$ show ndp fe80::20c:29ff:feb8:b11e
    Address                   MacAddress         Iface    Vlan    Status
    ------------------------  -----------------  -------  ------  ---------
    fe80::20c:29ff:feb8:b11e  00:0c:29:b8:b1:1e  eth0     -       REACHABLE
    Total number of entries 1

    **SPECIFIC INTERFACE**
    admin@sonic:~$ show ndp -if eth0
    Address                   MacAddress         Iface    Vlan    Status
    ------------------------  -----------------  -------  ------  ---------
    fe80::20c:29ff:feb8:b11e  00:0c:29:b8:b1:1e  eth0     -       REACHABLE
    fe80::20c:29ff:feb8:cff0  00:0c:29:b8:cf:f0  eth0     -       REACHABLE
    fe80::20c:29ff:fef9:324   00:0c:29:f9:03:24  eth0     -       REACHABLE
    Total number of entries 3

    **SPECIFIC VRF**
    root@sonic:~# show ndp -vrf Vrf-blue
    Address    MacAddress         Iface      Vlan    Status
    ---------  -----------------  ---------  ------  ---------
    2001::2    00:10:94:00:00:02  Ethernet4  -       REACHABLE
    2001::3    00:10:94:00:00:02  Ethernet4  -       REACHABLE
    Total number of entries 2
    root@sonic:~#
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Watermark-Configuration-And-Show)

# VRF Configuration And Show Commands

This section describes all the VRF show commands and VRF configuation commands that are supported in SONiC.
Most of the VRRP show commands start with "show vrf". Detailed show commands examples for VRF are provided for each of the commands. 

VRF feature enables other modules in SONiC to use VRF to provide virtualizattion and multi-instances for L3 routers. These components' confiiguration and how commands have been extended to accept VRF as an optional CLI keyword. These CLI extensions are described in individual modules' sections.

## VRF show commands  

**show vrf [<vrf_name>] [--verbose]**

This command displays the summary of all VRFs in SONIC along with their associated interfaces. "--verbose" is an optional CLI keyword and this additionally displays VRF-ID and Table ID for each VRF.  This command allows user to display a particular VRF in summary or verbose mode by entering <vrf_name> on CLI

- Usage:   
  show vrf

- Example:

  ```
  admin@sonic:~$ show vrf
  VRF         Interfaces
  ----------  --------------
  Vrf-Core    Ethernet200
              Ethernet204
              Ethernet208
              Ethernet212
              PortChannel213
              Vlan234
  Vrf-Edge    Ethernet108
              Ethernet100
              Ethernet112
              Ethernet104
              Vlan100
  Vrf-Green   Ethernet12
              Vlan27
  Vrf-Red     Ethernet4
              PortChannel017
  Vrf-Yellow  Ethernet32
              Vlan45
              Vlan134
  ```

- Usage:   
  show vrf --verbose

- Example:

  ```
  admin@sonic:~$ show vrf --verbose
  VRF         Table Id    Vrf Id    Interfaces
  ----------  ----------  --------  --------------
  Vrf-Core    1005        81        Ethernet200
                                    Ethernet204
                                    Ethernet208
                                    Ethernet212
                                    PortChannel213
                                    Vlan234
  Vrf-Edge    1004        80        Ethernet108
                                    Ethernet100
                                    Ethernet112
                                    Ethernet104
                                    Vlan100
  Vrf-Green   1002        78        Ethernet12
                                    Vlan27
  Vrf-Red     1001        77        Ethernet4
                                    PortChannel017
  Vrf-Yellow  1003        79        Ethernet32
                                    Vlan45
                                    Vlan134
  ```

- Usage:   
  show vrf <vrf_name>

- Example:

  ```
  admin@sonic:~$ show vrf Vrf-Core
  VRF       Interfaces
  --------  --------------
  Vrf-Core  Ethernet200
            Ethernet204
            Ethernet208
            Ethernet212
            PortChannel213
            Vlan234
  ```

- Usage:   
  show vrf <vrf_name> --verbose

- Example:

  ```
  admin@sonic:~$ show vrf Vrf-Core --verbose
  VRF       Table Id    Vrf Id    Interfaces
  --------  ----------  --------  --------------
  Vrf-Core  1005        81        Ethernet200
                                  Ethernet204
                                  Ethernet208
                                  Ethernet212
                                  PortChannel213
                                  Vlan234
  ```

## VRF config commands  

This sub-section explains the various configuration commands available for VRF module 

The list of possible VRF config commands are given below.

```
vrf
    add <vrf_name>
    del <vrf_name>
```

**config vrf add <vrf_name>**  

This command enables user to create a new VRF in SONIC. User can associate interface and config routing protocols after a VRF is created. Vrf name must start with "Vrf" prefix with "V" in upper case.

- Usage:  
  sudo config vrf add <vrf_name>

- Examples:

  ```
  admin@sonic:~$ sudo config vrf add Vrf-Core
  ```

  ```
  admin@sonic:~$ sudo config vrf add Vrf-Red
  ```

**config vrf del <vrf_name>**  

This command enables user to delete a existing VRF in SONIC. All the associated interfaces in this particular VRF will move to default VRF and their IP addresses will be automatically deleted.

- Usage:  
  sudo config vrf del <vrf_name>

- Examples:

  ```
  admin@sonic:~$ sudo config vrf del Vrf-Core
  ```

  ```
  admin@sonic:~$ sudo config vrf del Vrf-Red
  ```

**config route add [vrf <vrf_name>] prefix <prefix>/<mask> next-hop [vrf <vrf_name>] {<nh_ip> | dev <dev_name>}**  

This command allows user to create a static route in SONIC. The static route can be created in a VRF with next-hop belongng to a different VRF. This is the typical case of a inter VRF route. When next-hop VRF is not entered, the next-hop VRF is same as the prefix VRF. If prefix VRF is not entered by user, then the static route gets created in default VRF.

- Usage:  
  config route add [vrf <vrf_name>] prefix <prefix>/<mask> next-hop [vrf <vrf_name>] {<nh_ip> | dev <dev_name>}

- Examples:

  The below command creates a static route 192.168.34.0/24 in VRF Vrf-Red with next-hop 10.17.34.71 in Vrf-Core.

  ```
  admin@sonic:~$ config route add vrf Vrf-Red prefix 192.168.34.0/24 next-hop vrf Vrf-Core 10.17.34.71
  ```

- ```
  admin@sonic:~$ config route add vrf Vrf-Edge prefix 71.39.196.0/27 next-hop 27.58.64.1
  ```

**config route del [vrf <vrf_name>] prefix <prefix>/<mask> next-hop [vrf <vrf_name>] {<nh_ip> | dev <dev_name>}**  

This command allows user to delete an already existing static route in SONIC. 

- Usage:  
  config route del [vrf <vrf_name>] prefix <prefix>/<mask> next-hop [vrf <vrf_name>] {<nh_ip> | dev <dev_name>}

- Examples:

  ```
  admin@sonic:~$ config route del vrf Vrf-Red prefix 192.168.34.0/24 next-hop vrf Vrf-Core 10.17.34.71
  ```

- ```
  admin@sonic:~$ config route del vrf Vrf-Edge prefix 71.39.196.0/27 next-hop 27.58.64.1
  ```

# Management VRF

Management VRF is a subset of VRF which provides a separation between the out-of-band management network and the in-band data plane network. The main routing table is the default table for all of the data plane switch ports. With Management VRF, a second table, mgmt, is used for routing through the Ethernet ports of the switch.

By default, Management VRF is disabled. The admin user can enable it using the CLI interfaces (Eg: KLISH, click) or programmable interfaces (Eg: RESTCONF)


## Management VRF config commands  

### Enable or Disable Management VRF

**config vrf add mgmt**  

This command enable the Management VRF

**config vrf del <vrf_name>**  

This command disables the Management VRF

Note: The corresponding KLISH CLI is:

    [no] ip vrf management


### Configure SNMP over Management VRF

When the Mangement VRF is enabled, the user needs to configure the SNMP agent to listen on the mgmt VRF using the "config snmpagentaddress" command.


**config snmpagentaddress add|del  [OPTIONS] <SNMP_AGENT_LISTENING_IP_Address>**

  **OPTIONS**

  -p, --port <SNMP_AGENT_LISTENING_PORT>

  -v, --vrf <VRF_Name|mgmt|None>

Note: The corresponding KLISH CLI is:

    snmp-server agentaddress <ip> interface <vrf_name>


## Using ssh scp ntpstat in Management VRF

When the Management VRF is enabled, the commands such as ssh, scp, and ntpstat which are used from the switch to outbound will need to be run in the Management VRF control group using cgexec using "cgexec -g l3mdev:mgmt <command>"

Eg:

```
    cgexec -g l3mdev:mgmt ssh user@ip
```

```
    cgexec -g l3mdev:mgmt ntpstat
```


# VRRP Configuration And Show Commands

This section explains all the IPv4 and IPv6 VRRP show commands and VRRP configuation commands that are supported in SONiC. 
Most of the VRRP show commands start with "show vrrp" for IPv4 and "show vrrp6" for IPv6. Detailed show commands examples for VRRP are provided for each of the commands.

VRRP is supported on both default VRF interfaces and non-default VRF interfaces.

## VRRP show commands  

**show vrrp**

This command displays the summary of all VRRP instances for all the VRRP enabled interfaces. Each row in the command output represent one VRRP instance for an interface. The row displays configuration as well as the dynamic state of a VRRP instance. 

- Usage:   
  show vrrp

  show vrrp6
  
- Example:
  ```
  admin@sonic:~$ show vrrp
  Interface_Name  VRID   State             VIP  Cfg_Prio  Curr_Prio
           Vlan1     1  Backup       4.1.1.100       100        130
           Vlan2     2  Backup       4.1.2.100       100        100
         Vlan100     1  Master  125.125.125.50       100        100
         Vlan100     2  Master  126.126.126.50       100        100
         Vlan100     3  Master  128.128.128.50       100        100
         Vlan100     4  Master  127.127.127.50       100        100
        Vlan1000     1  Backup     40.10.1.101       100        100
        Vlan1000     2  Backup     40.10.2.101       100        100
        Vlan1000     3  Backup     40.10.3.101       100        100
        
  admin@sonic:~$ show vrrp6
  Interface_Name  VRID   State             VIP  Cfg_Prio  Curr_Prio
           Vlan1     1  Backup        4:1::100       100        130
           Vlan2     2  Backup        4:2::100       100        100
         Vlan100     3  Master          5::100       100        100
         Vlan100     4  Master          5::100       100        120     
  ```

**show vrrp <interface_name> <vrid>**

**show vrrp6 <interface_name> <vrid>**

This command displays all the details of a particular VRRP instance on an VRRP enabled interface. The above command "show vrrp" & "show vrrp6" displays the summary of all the VRRP instances in the system whereas this command displays a particular VRRP instance in verbose mode with full configuration and state details. <interface_name> and <vr_id> both are mandatory parameter. <interface_name> is name of an interface where VRRP is enabled, example Ethernet40 or PortChannel003 or Vlan349. <vrid> is an integer between 1 and 255 and represents the instance ID of VRRP on an interface.

This command also display the list of interfaces that are being tracked by this VRRP instance along with their state (Up or Down) and their configured priority.


- Usage:  
  show vrrp <interface_name> <vr_id>   

  show vrrp6 <interface_name> <vr_id>   
  
- Example:
  ```
  admin@sonic:~$ show vrrp Vlan1 1
  Vlan1, VRID 1
  Version is 2
  State is Backup
  Virtual IP address:
    4.1.1.100
  Virtual MAC address is 0000.5e00.0101
  Track interface:
    Intfname       State  Priority
    Ethernet7      Up     10
    PortChannel001 Up     10
    Vlan100        Up     10
    Vlan200        Down   20
    Vlan300        Down   30
    Vlan400        Down   40
  Configured Priority is 100, Current Priority is 130
  Advertisement interval is 1 sec
  Preemption is enabled
  
  admin@sonic:~$ show vrrp6 Vlan1 1
  Vlan1, VRID 1
  Version is 3
  State is Backup
  Virtual IP address:
    4:1::100
  Virtual MAC address is 0000.5e00.0201
  Track interface:
    Intfname       State  Priority
    Ethernet7      Up     10
    PortChannel001 Up     10
    Vlan100        Up     10
    Vlan200        Down   20
  Configured Priority is 100, Current Priority is 130
  Advertisement interval is 1 sec
  Preemption is enabled
  ```
  
  

## VRRP config commands  

This sub-section explains the various configuration commands available for VRRP module for both IPv4 and IPv6 address family.

The list of possible VRRP config commands are given below.

	interface
	    vrrp
	        add <interface_name> <vrid>
	        remove <interface_name> <vrid>
	        vip
	            add <interface_name> <vrid> <virtual_ip_address>
	            remove <interface_name> <vrid> <virtual_ip_address>
	        priority <interface_name> <vrid> <priority>
	        adv_interval <interface_name> <vrid> <interval>
	        version <version_number>
	        pre_empt
	            enable <interface_name> <vrid>
	            disable <interface_name> <vrid>
	        track_interface
	            add <interface_name> <vrid> <track_interface> <weight>
	            remove <interface_name> <vrid> <track_interface>

**config interface vrrp add <interface_name> <vrid>** **  

**config interface vrrp6 add <interface_name> <vrid>** **  

This command enables user to create a new VRRP instance on an interface. This is an indirect way to enabling or configuring VRRP on an interface. Interface name can be any available L3 interface like Ethernet4 or Vlan206 or PortChannel003. <vrid> is a one byte integer between 1 and 255 whose scope is only for an interface. 

  - Usage:  
    sudo config interface vrrp add <interface_name> <vrid>

  - sudo config interface vrrp6 add <interface_name> <vrid>
    
- Examples for IPv4 VRRP:
  ```
  admin@sonic:~$ sudo config interface vrrp add Vlan206 13
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp add PortChannel007 34
  ```

- Examples for IPv6 VRRP

  ```
  admin@sonic:~$ sudo config interface vrrp6 add Vlan106 13
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 add PortChannel15 34
  ```



**config interface vrrp remove <interface_name> <vrid>**  

**config interface vrrp6 remove <interface_name> <vrid>**  

This command deletes a VRRP instance from an interface

  - Usage:  
    sudo config vrrp remove <interface_name> <vrid>

  - sudo config vrrp6 remove <interface_name> <vrid>
    
- Examples for IPv4 VRRP:  
  ```
  admin@sonic:~$ sudo config interface vrrp remove Vlan206 13
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp remove PortChannel007 34
  ```

- Examples for IPv6 VRRP:  
  ```
  admin@sonic:~$ sudo config interface vrrp6 remove Vlan106 13
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 remove PortChannel15 34
  ```

**config interface vrrp vip add <interface_name> <vrid> <virtual_ip_address>**  

**config interface vrrp6 vip add <interface_name> <vrid> <virtual_ip_address>** 

This command enables users to add one or more virtual IP address for a VRRP instance on an interface. Virtual IP address must be in interface's IP subnet. The command will be rejected of IP address doesn't belong to the interface's IP subnet.

  - Usage:     
    sudo config interface vrrp vip add <interface_name> <vrid> <virtual_ip_address>`

  - sudo config interface vrrp6 vip add <interface_name> <vrid> <virtual_ip_address>`
    
- Examples of IPv4 VRRP:  
  ```
  admin@sonic:~$ sudo config interface vrrp vip add Vlan206 13 72.41.61.101
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp vip add PortChannel007 34 206.52.72.201
  ```

- Examples of IPv6 VRRP:  
  ``` 
  admin@sonic:~$ sudo config interface vrrp6 vip add Vlan106 13 100::101
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 vip add PortChannel15 34 206::201
  ```

**config interface vrrp vip remove <interface_name> <vrid> <virtual_ip_address>**  

**config interface vrrp6 vip remove <interface_name> <vrid> <virtual_ip_address>**  

This command allows user to delete an existing VIP from a VRRP instance on an interface.

  - Usage:  
    sudo config interface vrrp vip remove <interface_name> <vrid> <virtual_ip_address>

  - sudo config interface vrrp6 vip remove <interface_name> <vrid> <virtual_ip_address>
    
- Examples:
  ```
  admin@sonic:~$ sudo config interface vrrp vip remove Vlan206 13 72.41.61.101
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp vip remove PortChannel007 34 206.52.72.201
  ```

- Examples:
  ```
  admin@sonic:~$ sudo config interface vrrp6 vip remove Vlan106 13 100::101
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 vip remove PortChannel15 34 206::201
  ```

**config interface vrrp priority <interface_name> <vrid> <priority>**  

**config interface vrrp6 priority <interface_name> <vrid> <priority>** 

This command allows user to configure priority for a VRRP instance on an interface. Priority should be in the range 1 to 254. Default priority is 100. Priority value decides the election of a Master VRRP node for a VRRP instance.

- Usage:  
  sudo config interface vrrp priority <interface_name> <vrid> <priority>

  sudo config interface vrrp6 priority <interface_name> <vrid> <priority>
  
- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp priority Vlan206 13 120
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 priority PortChannel15 34 80
  ```

**config interface vrrp adv_interval <interface_name> <vrid> <interval>**  

**config interface vrrp6 adv_interval <interface_name> <vrid> <interval>**  

This command allows user to configure advertisement interval in seconds for a VRRP instance on an interface. Advertisement interval should be in the range 1 to 255 sec. Default advertisement interval is 1sec. Advertisement interval dictates the periodicity of VRRP hello advertisement for a VRRP instance.

- Usage:  
  sudo config interface vrrp adv_interval <interface_name> <vrid> <interval>

  sudo config interface vrrp6 adv_interval <interface_name> <vrid> <interval>
  
- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp adv_interval Vlan206 13 2
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 adv_interval PortChannel15 34 5
  ```

**config interface vrrp pre_empt enable <interface_name> <vrid>**  

**config interface vrrp6 pre_empt enable <interface_name> <vrid>**  

This command allows user to configure whether a Master VRRP role can be preempted by a new high priority VRRP node for a VRRP instance on an interface. By default, preemption of VRRP Master node is enabled. 

- Usage:  
  sudo config interface vrrp pre_empt enable <interface_name> <vrid>

  sudo config interface vrrp6 pre_empt enable <interface_name> <vrid>
  
- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp pre_empt enable Vlan206 13
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 pre_empt enable PortChannel15 34
  ```

**config interface vrrp pre_empt disable <interface_name> <vrid>**  

This command allows user to disable premeption of a Master VRRP role by a new high priority VRRP node for a VRRP instance on an interface. By default, preemption of VRRP Master node is enabled. 

- Usage:  
  sudo config interface vrrp pre_empt disable <interface_name> <vrid>

- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp pre_empt disable Vlan206 13
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp pre_empt disable PortChannel007 34
  ```

**config interface vrrp track_interface add <interface_name> <vrid> <track_interface> <weight>**  

**config interface vrrp6 track_interface add <interface_name> <vrid> <track_interface> <weight>**  

This command allows user to add a track interface for a VRRP instance on an interface. The operational status of track interface determines the effective priority of VRR instance. When track interface is up, the effective priority of VRRP instance is <priority>+<weight>. When track interface goes down, the effective priority becomes <priority>-<weight>. This change in effective priority triggers election of a new master. Any interface L2 or L3 can be tracked using VRRP. <weight> is an integer in the range 1 to 254. A maximum of eight track interfaces can be added per VRRP instance.

- Usage:  
  sudo config interface vrrp track_interface add <interface_name> <vrid> <track_interface> <weight>

  sudo config interface vrrp track_interface add <interface_name> <vrid> <track_interface> <weight>
  
- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp track_interface add Vlan206 13 Ethernet8 10
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 track_interface add PortChannel15 34 Vlan1 20
  ```

**config interface vrrp track_interface remove <interface_name> <vrid> <track_interface> <weight>**  

**config interface vrrp6 track_interface remove <interface_name> <vrid> <track_interface> <weight>**  

This command enables user to delete a track interface for a VRRP instance on an interface. 

- Usage:  
  sudo config interface vrrp track_interface remove <interface_name> <vrid> <track_interface>

- sudo config interface vrrp6 track_interface remove <interface_name> <vrid> <track_interface>
  
- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp track_interface remove Vlan206 13 Ethernet8
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp6 track_interface remove PortChannel15 34 Vlan1
  ```

**config interface vrrp version <interface_name> <vrid> <version-number>**  

This command allows user to configure version for a IPv4 VRRP instance on an interface. By default IPv4 VRRP instances are version 2. IPv6 VRRP instance always run version 3.

- Usage:  
  sudo config interface vrrp version <interface_name> <vrid> <version-number>

- Examples:

  ```
  admin@sonic:~$ sudo config interface vrrp verison Vlan206 13 3
  ```

  ```
  admin@sonic:~$ sudo config interface vrrp version PortChannel15 34 2
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#BGP-Configuration-And-Show-Commands)

# Static Anycast Gateway(SAG) Configuration And Show Commands

This section explains the IPv4 and IPv6 Static Anycast Gateway(SAG) show commands and SAG configuation commands that are supported in SONiC. 

SAG is supported on both default VRF interfaces and non-default VRF interfaces.

SAG can be configured from KLISH CLI. Please refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring Static Anycast Gateway using KLISH CLIs.

## SAG show commands

**show ip static-anycast-gateway <interface_name>**

This command lists all of the SAG Gateway IPv4 addresses configured on the Vlan interface(s) including their current admin & operational states.

- Usage:   
  show ip static-anycast-gateway <interface_name> 

- Example:
  ```
  admin@sonic:~$ show ip static-anycast-gateway
  Configured Anycast Gateway MAC address: 00:00:00:01:02:03
  IPv4 Anycast Gateway MAC address: enable
  Total number of gateway: 6
  Total number of gateway admin UP: 6
  Total number of gateway oper UP: 4
  Interfaces    Gateway Address    Master    Admin/Oper
  ------------  -----------------  --------  ------------
  Vlan2         2.0.101.100/24               up/up
  Vlan200       200.0.0.10/24                up/up
                200.1.0.10/24                up/up
                200.1.0.11/32                up/up
  Vlan300       31.1.1.4/24        Vrf3      up/down
                31.1.1.5/32                  up/down

  ```

**show ipv6 static-anycast-gateway <interface_name>**

This command lists all of the SAG Gateway IPv6 addresses configured on the Vlan interface(s) including their current admin & operational states.

- Usage:   
  show ipv6 static-anycast-gateway <interface_name> 

- Example:
  ```
  admin@sonic:~$ show ipv6 static-anycast-gateway
  Configured Anycast Gateway MAC address: 00:00:00:01:02:03
  IPv6 Anycast Gateway MAC address: enable
  Total number of gateway: 6
  Total number of gateway admin UP: 6
  Total number of gateway oper UP: 4
  Interfaces    Gateway Address    Master    Admin/Oper
  ------------  -----------------  --------  ------------
  Vlan2         2100::1/64                   up/up
  Vlan200       2002::4/64                   up/up
                2003::4/64                   up/up
                2004::4/64                   up/up
  Vlan300       3002::4/64         Vrf3      up/down
                3003::4/64                   up/down

  ```

## SAG config commands

This sub-section explains the various configuration commands available for Static Anycast Gateway module for both IPv4 & IPv6 address family.

This list of possible SAG config commands are given below.

**config ip anycast-mac-address add <mac_address>**

The configured MAC address would be used as gateway MAC for all of the Vlans on which SAG is configured.

- Usage:
  ```
  sudo config ip anycast-mac-address add <mac_address>
  ```

- Examples for configuring Anycast Gateway MAC:
  ```
  admin@sonic:~$ sudo config ip anycast-mac-address add 00:00:00:01:02:03
  ```

**config ip anycast-mac-address remove <mac_address>**

This command is used to unconfigure anycast gateway MAC for SAG IPv4 & IPv6 addresses. 

- Usage:
  ```
  sudo config ip anycast-mac-address remove <mac_address>
  ```

- Examples for unconfiguring Anycast Gateway MAC:
  ```
  admin@sonic:~$ sudo config ip anycast-mac-address remove 00:00:00:01:02:03
  ```

**config <ip/ipv6> anycast-mac-address <enable/disable>**

This command is used to enable or disable SAG for IPv4 and IPv6 address family. 
This will enable/disable all SAG addresses on all interfaces for the address family.

- Usage:
  ```
  sudo config <ip/ipv6> anycast-mac-address <enable/disable>
  ```

- Example to disable IPv4 Anycast Gateway:
  ```
  admin@sonic:~$ sudo config ip anycast-mac-address disable
  ```

- Example to enable IPv4 Anycast Gateway:
  ```
  admin@sonic:~$ sudo config ip anycast-mac-address enable
  ```

- Example to disable IPv6 Anycast Gateway:
  ```
  admin@sonic:~$ sudo config ipv6 anycast-mac-address disable
  ```

- Example to enable IPv6 Anycast Gateway:
  ```
  admin@sonic:~$ sudo config ipv6 anycast-mac-address enable
  ```

**config interface ip anycast-address add <interface_name> <gwip_address>**

This command is used to configure SAG IPv4 and IPv6 addresses on the vlan interface.
Note: SAG IPv4 or IPv6 addresses on the interface will NOT be allowed in presence of real interface IP.

- Usage:
  ```
  sudo config interface ip anycast-address add <interface_name> <gwip_address>
  ```

- Example to configure IPv4 Anycast address on the interface:
  ```
  admin@sonic:~$ sudo config interface ip anycast-address add Vlan200 200.0.0.10/24 
  ```

- Example to configure IPv6 Anycast address on the interface:
  ```
  admin@sonic:~$ sudo config interface ip anycast-address add Vlan200 2002::4/64
  ```

**config interface ip anycast-address remove <interface_name> <gwip_address>**

This command is used to unconfigure SAG IPv4 and IPv6 addresses on the vlan interface.

- Usage:
  ```
  sudo config interface ip anycast-address remove <interface_name> <gwip_address>
  ```

- Example to unconfigure IPv4 Anycast address on the interface:
  ```
  admin@sonic:~$ sudo config interface ip anycast-address remove Vlan200 200.0.0.10/24 
  ```

- Example to unconfigure IPv6 Anycast address on the interface:
  ```
  admin@sonic:~$ sudo config interface ip anycast-address remove Vlan200 2002::4/64
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Static-Anycast-Gateway(SAG)-Configuration-And-Show-Commands)

# Bidirectional Forwarding Detection (BFD)

This section explains BFD show and debug commands. 

Note: BFD can be configured from KLISH CLI too. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring BFD using KLISH CLIs.

## BFD show commands

**show bfd peers brief** 

To display all the BFD peers in brief use **show bfd peers brief** command. 

Sample output:

```
sonic# show bfd peers brief
Session count: 1
SessionId      LocalAddr                NeighAddr                        State
=========      =========                =========                        =====
1              192.168.0.1              192.168.0.2                       UP
```

## BFD debug commands

**debug bfd**

To enable BFD debug logs for trouble shooting, a new command is added. Use **no debug bfd** to disable debug logging for BFD. 

Note: This command is available in FRR BGP container vtysh shell.

- Usage: 
  [no] debug bfd

- Example:
  ```
  sonic# debug bfd
  ```

## BFD clear commands

**clear bfd peer counters**

To clear BFD session counters a new command is added.  

- Usage: 
  clear bfd peer {label|A.B.C.D|X:X::X:X} 
  [multihop|{local-address A.B.C.D|X:X::X:X}|{interface if-name}] counters

- Example:
  ```
  sonic# clear bfd peer 10.1.1.1 counters
  sonic# clear bfd peer 20.1.1.1 local-address 10.1.1.1 counters
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#bfd)

# BGP Configuration And Show Commands

This section explains all the BGP show commands and BGP configuation commands in both "Quagga" and "FRR" routing software that are supported in SONiC.
In 201811 and older verisons "Quagga" was enabled by default. In current version "FRR" is enabled by default.  
Most of the FRR show commands start with "show bgp". Similar commands in Quagga starts with "show ip bgp". All sub-options supported in all these show commands are common for FRR and Quagga.
Detailed show commands examples for Quagga are provided at the end of this document.This section captures only the commands supported by FRR.

Note: BGP and BGP EVPN features can be configured from KLISH CLI too. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring  BGP and BGP EVPN using KLISH CLIs.



## BGP show commands  

**show bgp summary (for default FRR in 201904+ version)**  
**show ip bgp summary (for Quagga in 201811- version)**  

This command displays the summary of all IPv4 & IPv6 bgp neighbors that are configured and the corresponding states.  

- Usage:   
  show bgp summary (for default FRR in 201904+ version)   
  show ip bgp summary (for Quagga in 201811- version)   

- Example:

  ```
   root@sonic-z9264f-9251:~# show bgp summary

   IPv4 Unicast Summary:
   BGP router identifier 10.1.0.32, local AS number 65100 vrf-id 0
   BGP table version 6465
   RIB entries 12807, using 2001 KiB of memory
   Peers 4, using 83 KiB of memory
   Peer groups 2, using 128 bytes of memory

   Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down State/PfxRcd
   10.0.0.57       4      64600    3995    4001        0    0    0 00:39:32         6400
   10.0.0.59       4      64600    3995    3998        0    0    0 00:39:32         6400
   10.0.0.61       4      64600    3995    4001        0    0    0 00:39:32         6400
   10.0.0.63       4      64600    3995    3998        0    0    0 00:39:32         6400

   Total number of neighbors 4
   
   IPv6 Unicast Summary:
   BGP router identifier 10.1.0.32, local AS number 65100 vrf-id 0
   BGP table version 12803
   RIB entries 12805, using 2001 KiB of memory
   Peers 4, using 83 KiB of memory
   Peer groups 2, using 128 bytes of memory

   Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down State/PfxRcd
   fc00::72        4      64600    3995    5208        0    0    0 00:39:30         6400
   fc00::76        4      64600    3994    5208        0    0    0 00:39:30         6400
   fc00::7a        4      64600    3993    5208        0    0    0 00:39:30         6400
   fc00::7e        4      64600    3993    5208        0    0    0 00:39:30         6400

   Total number of neighbors 4
  ```

  Click [here](#Quagga-BGP-Show-Commands) to see the example for "show ip bgp summary" for Quagga. 



**show bgp neighbors (for default FRR in 201904+ version)**   
**show ip bgp neighbors (for Quagga in 201811- version)**   

This command displays all the details of IPv4 & IPv6 BGP neighbors when no optional argument is specified. 

When the optional argument IPv4_address is specified, it displays the detailed neighbor information about that specific IPv4 neighbor.

Command has got additional optional arguments to display only the advertised routes, or the received routes, or all routes.

In order to get details for an IPv6 neigbor, use "show bgp ipv6 neighbor <ipv6_address>" command.

- Usage:  
  show bgp neighbors [\<ipv4-address\> [advertised-routes | received-routes | routes]] (for default FRR in 201904+ version)  
  show ip bgp neighbors [\<ipv4-address\> [advertised-routes | received-routes | routes]] (for Quagga in 201811- version)  

- Example:

  ```
   admin@sonic:~$ show bgp neighbors
   BGP neighbor is 10.0.0.57, remote AS 64600, local AS 65100, external link
   Description: ARISTA01T1
   BGP version 4, remote router ID 100.1.0.29, local router ID 10.1.0.32
   BGP state = Established, up for 00:42:15
   Last read 00:00:00, Last write 00:00:03
   Hold time is 10, keepalive interval is 3 seconds
   Configured hold time is 10, keepalive interval is 3 seconds
   Neighbor capabilities:
     4 Byte AS: advertised and received
     AddPath:
       IPv4 Unicast: RX advertised IPv4 Unicast and received
     Route refresh: advertised and received(new)
     Address Family IPv4 Unicast: advertised and received
     Hostname Capability: advertised (name: sonic-z9264f-9251,domain name: n/a) not received
     Graceful Restart Capabilty: advertised and received
       Remote Restart timer is 300 seconds
       Address families by peer:
         none
   Graceful restart information:
     End-of-RIB send: IPv4 Unicast
     End-of-RIB received: IPv4 Unicast
   Message statistics:
     Inq depth is 0
     Outq depth is 0
                          Sent       Rcvd
     Opens:                  2          1
     Notifications:          2          0
     Updates:             3206       3202
     Keepalives:           845        847
     Route Refresh:          0          0
     Capability:             0          0
     Total:               4055       4050
   Minimum time between advertisement runs is 0 seconds

  For address family: IPv4 Unicast
   Update group 1, subgroup 1
   Packet Queue length 0
   Inbound soft reconfiguration allowed
   Community attribute sent to this neighbor(all)
   6400 accepted prefixes

   Connections established 1; dropped 0
   Last reset 00:42:37, due to NOTIFICATION sent (Cease/Connection collision resolution)
   Local host: 10.0.0.56, Local port: 179
   Foreign host: 10.0.0.57, Foreign port: 46419
   Nexthop: 10.0.0.56
   Nexthop global: fc00::71
   Nexthop local: fe80::2204:fff:fe36:9449
   BGP connection: shared network
   BGP Connect Retry Timer in Seconds: 120
   Read thread: on  Write thread: on
  ```

- Optionally, you can specify an IP address in order to display only that particular neighbor. In this mode, you can optionally specify whether you want to display all routes advertised to the specified neighbor, all routes received from the specified neighbor or all routes (received and accepted) from the specified neighbor.

- Example:

  ```
    admin@sonic:~$ show bgp neighbors 10.0.0.57

    admin@sonic:~$ show bgp neighbors 10.0.0.57 advertised-routes

    admin@sonic:~$ show bgp neighbors 10.0.0.57 received-routes

    admin@sonic:~$ show bgp neighbors 10.0.0.57 routes

  ```

  Click [here](#Quagga-BGP-Show-Commands) to see the example for "show ip bgp neighbors" for Quagga. 



**show bgp ipv6 summary (for default FRR in 201904+ version)**  
**show ipv6 bgp summary (for Quagga in 201811- version)**  

This command displays the summary of all IPv6 bgp neighbors that are configured and the corresponding states.

- Usage:  
  show bgp ipv6 summary (for default FRR in 201904+ version)    
  show ipv6 bgp summary (for Quagga in 201811- version)    

- Example:

  ```
  admin@sonic:~$ show bgp ipv6 summary
  BGP router identifier 10.1.0.32, local AS number 65100 vrf-id 0
  BGP table version 12803
  RIB entries 12805, using 2001 KiB of memory
  Peers 4, using 83 KiB of memory
  Peer groups 2, using 128 bytes of memory

  Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down State/PfxRcd
  fc00::72        4      64600    3995    5208        0    0    0 00:39:30         6400
  fc00::76        4      64600    3994    5208        0    0    0 00:39:30         6400
  fc00::7a        4      64600    3993    5208        0    0    0 00:39:30         6400
  fc00::7e        4      64600    3993    5208        0    0    0 00:39:30         6400

  Total number of neighbors 4

  ```

  Click [here](#Quagga-BGP-Show-Commands) to see the example for "show ipv6 bgp summary" for Quagga. 



**show bgp ipv6 neighbors (for default FRR in 201904+ version)**  
**show ipv6 bgp neighbors (for Quagga in 201811- version)**  

This command displays all the details of one particular IPv6 Border Gateway Protocol (BGP) neighbor. Option is also available to display only the advertised routes, or the received routes, or all routes.

- Usage:  
  show bgp ipv6 neighbors [\<ipv6-address\> [(advertised-routes | received-routes | routes)]] (for default FRR in 201904+ version)   
  show ipv6 bgp neighbors [\<ipv6-address\> [(advertised-routes | received-routes | routes)]] (for Quagga in 201811- version)   

- Example:

  ```
   admin@sonic:~$ show bgp ipv6 neighbors fc00::72 advertised-routes

   admin@sonic:~$ show bgp ipv6 neighbors fc00::72 received-routes

   admin@sonic:~$ show bgp ipv6 neighbors fc00::72 routes  
   
  ```

  Click [here](#Quagga-BGP-Show-Commands) to see the example for "show ip bgp summary" for Quagga. 



**show route-map**  

This command displays the routing policy that takes precedence over the other route processes that are configured.

- Usage:  
  show route-map
- Example:

```
	admin@T1-2:~$ show route-map
	ZEBRA:
	route-map RM_SET_SRC, permit, sequence 10
	  Match clauses:
	  Set clauses:
		src 10.12.0.102
	  Call clause:
	  Action:
		Exit routemap
	ZEBRA:
	route-map RM_SET_SRC6, permit, sequence 10
	  Match clauses:
	  Set clauses:
		src fc00:1::102
	  Call clause:
	  Action:
		Exit routemap
	BGP:
	route-map FROM_BGP_SPEAKER_V4, permit, sequence 10
	  Match clauses:
	  Set clauses:
	  Call clause:
	  Action:
	    Exit routemap
	BGP:
	route-map TO_BGP_SPEAKER_V4, deny, sequence 10
	  Match clauses:
	  Set clauses:
	  Call clause:
	  Action:
	    Exit routemap
	BGP:
	route-map ISOLATE, permit, sequence 10
	  Match clauses:
	  Set clauses:
		as-path prepend 65000
	  Call clause:
	  Action:
		Exit routemap
```

## BGP config commands 

This sub-section explains the list of configuration options available for BGP module for both IPv4 and IPv6 BGP neighbors.

The list of possible BGP config commands are given below.

```
bgp
    shutdown
        all
        neighbor
    startup
        all
        neighbor
```

**config bgp shut down all**  

This command is used to shutdown all the BGP IPv4 & IPv6 sessions.
When the session is shutdown using this command, BGP state in "show ip bgp summary" is displayed as "Idle (Admin)"

- Usage:  
  sudo config bgp shutdown all

- Examples:

  ```
  admin@sonic:~$ sudo config bgp shutdown all
  ```

**config bgp shutdown <neighbor>**  

This command is to shut down a BGP session with a neighbor by that neighbor's IP address or hostname

- Usage:  
  sudo config bgp shutdown (<ip-address> | <hostname>)

- Examples:  

  ```
  admin@sonic:~$ sudo config bgp shutdown neighbor 192.168.1.124
  ```

  ```
  admin@sonic:~$ sudo config bgp shutdown neighbor SONIC02SPINE
  ```

**config bgp startup all**  

This command is used to start up all the IPv4 & IPv6 BGP neighbors

- Usage:     
  sudo config bgp startup all`

- Examples:  

  ```
  admin@sonic:~$ sudo config bgp startup all
  ```

**config bgp startup <neighbor>**  

This command is used to start up the particular IPv4 or IPv6 BGP neighbor using either the IP address or hostname.

- Usage:  
  sudo config bgp startup (<ip-address> | <hostname>)`

- Examples:

  ```
  admin@sonic:~$ sudo config bgp startup neighbor 192.168.1.124
  ```

  ```
  admin@sonic:~$ sudo config bgp startup neighbor SONIC02SPINE
  ```

**config routing_config_mode**  

This command is used to configure routing configuration mode. This command requires config reload or system reboot for the configurations to take effect.

- Usage:  
  sudo config routing_config_mode {unified|split|separated}

- Examples:

  ```
  admin@sonic:~$ sudo config routing_config_mode unified
  ```

  ```
  admin@sonic:~$ sudo config routing_config_mode split
  ```
  

Note: In order to retain FRR features configuration like BGP, BFD, OSPF etc across BGP docker restart and SONiC system reboot:
- User should use "separated" mode in below cases, which is default routing mode from SONIC 3.1.0 release: 
	If user intends to use KLISH CLI or REST interface for all FRR features.
	If user intends to use KLISH CLI or REST interface for complete configuration of selected FRR features like BGP, BFD, OSPF etc. and use FRR vtysh CLI for complete configuration of remaining FRR features like PIM, Static route etc. 
	From SONIC 3.1.0 release onwards "separated" mode is the default routing mode. User need not configure any routing mode explicitley. If routing mode is not configured to any value, it is also considered as "separated" routing mode. 

- User should configure "split" mode for below cases: 
	If user intends to use FRR vtysh for managing ALL FRR features like BGP, BFD, OSPF etc.

- Using KLISH CLI or REST interface partially to configure a FRR feature for e.g. BGP and also using FRR vtysh CLIs to configure rest of BGP is not supported. This is applicale to other FRR features like BFD, OSPF etc as well. Users should avoid this model of configuration. 

- When user is upgrading devices from SONIC previous releases to SONIC 3.1.0 or later with FRR features configured using FRR vtysh CLIs, plesae make sure that routing mode is configured to "split" mode before upgrade. In this case if "split" mode is not configured in pre 3.1.0 releases, FRR vtysh configuration will be lost after upgrade to 3.1.0 release. 

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#BGP-Configuration-And-Show-Commands)

## BGP Error Handling

When BGP learns a prefix, it sends the route to route table manager(Zebra) to install in data plane.  The routes are installed in kernel and also sent to APP_DB via fpmsyncd. The Orchagent reads the route from APP_DB, creates new resources like nexthop or nexthop group Id and installs the route in ASIC_DB. The syncd triggers the appropriate SAI API and route is installed in hardware. Due to resource allocation failures in hardware, SAI API calls can fail and these failures should be notified to Zebra and BGP. On learning the prefix, BGP can immediately advertise the prefix to its neighbors. However, if the BGP error-handling feature is enabled, BGP waits for success notification from hardware installation before advertising the route to its peers. If the hardware installation returns error, the routes are not advertised to the peers.

### Configuration Commands

**bgp error-handling**

Below configuration command is added to enable or disable BGP error handling feature. BGP error handling feature is disabled by default. 

Note: This command is available in SONIC CLIs. 

- Usage: 

  config bgp error-handling {enable|disable}

- Example

  ```
  root@sonic:~# sudo config bgp error-handling enable

  root@sonic:~# sudo config bgp error-handling disable
  ```

### Show Commands

**show ip route not-installed**

A new command is added with this feature, to display the failed routes. 

Note: This command is available in FRR BGP vtysh shell.  

- Usage: 

  show {ip | ipv6} route not-installed [prefix/mask]

- Example

  ```
  sonic# show ip route not-installed
  Codes: K - kernel route, C - connected, S - static, R - RIP,
         O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
         T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
         F - PBR,
         > - selected route, * - FIB route # - not installed in hardware
  B> # 22.1.1.1/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 22.1.1.2/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.1/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.2/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.3/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.4/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.5/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.6/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.7/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  B> # 30.1.1.8/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:20
  ```

*show bgp {ipv4|ipv6}* and *show ip route* CLI outputs are also enhanced to display the routes that are failed to install in the hardware with different status code. 

Example: 

```
sonic# show ip route
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR,
       > - selected route, * - FIB route, # - Not installed in hardware

K>* 0.0.0.0/0 [0/0] via 10.59.128.1, eth0, 09:44:37
C>* 4.1.1.0/24 is directly connected, Ethernet4, 00:01:48
C>* 10.1.0.1/32 is directly connected, lo, 09:44:37
C>* 10.59.128.0/20 is directly connected, eth0, 09:44:37
B># 21.21.21.21/32 [20/0] via 4.1.1.2, Ethernet4, 00:00:07
```

### Debug Commands

To retry installation of failed routes from Zebra, a clear command has been provided.

- Usage:

  clear {ip | ipv6} route {not-installed | <prefix/mask>}

- Example 

  ```
  root@sonic:~# clear ip route not-installed
  ```

# EVPN Configuration And Show Commands

The following sections provide the basic configuration needed to use EVPN as the control plane for VXLAN. 

### Enable EVPN between BGP Neighbors

To enable EVPN between BGP neighbors, add the address family l2vpn evpn and activate the existing neighbor.

For a non-VTEP device such as a spine switch where the network deployment uses hop-by-hop eBGP or the switch is acting as an iBGP route reflector, 

### EVPN Configuration Commands

BGP EVPN configuration example for e-BGP and i-BGP neighbors is provided below.

```
router bgp 65535
 neighbor Ethernet48 interface remote-as external
 neighbor 169.100.0.1 remote-as 65535
 neighbor 169.200.0.1 remote-as 65535
 neighbor 169:100::1 remote-as 65550
 !
 address-family ipv4 unicast
  neighbor 169.100.0.1 route-reflector-client
  neighbor 169.200.0.1 route-reflector-client
 exit-address-family
 !
 address-family l2vpn evpn
  neighbor Ethernet48 activate
  neighbor 169.100.0.1 activate
  neighbor 169.100.0.1 route-reflector-client
  neighbor 169.200.0.1 activate
  neighbor 169.200.0.1 route-reflector-client
  neighbor 169:100::1 activate
 exit-address-family
!
```

IPv4, IPv6, and BGP unnumbered can be used as underlay for EVPN. In the above example configuration, BGP unnumbered session is established on Ethernet48. Neighbors 169.100.0.1 and 169.200.0.1 are i-BGP neighbors and are configured as route-reflector-client. Whereas, 169:100::1 is an e-BGP neighbor.

### Advertise All VNIs

FRR is not aware of any local VNIs and MACs, or hosts (neighbors) associated with those VNIs until user enable the BGP control plane for all VNIs configured on the switch by setting the advertise-all-vni option.

```
router bgp 65535
  address-family l2vpn evpn
  neighbor 169.100.0.1 activate
  advertise-all-vni
```

### Define RDs and RTs

If user do not want RDs and RTs to be derived automatically, user can define them manually. The following example commands are per VNI. User must specify these commands under address-family l2vpn evpn in BGP.

```
address-family l2vpn evpn
  advertise-all-vni
  vni 10200
   rd 172.16.100.1:20
   route-target import 65100:20
   route-target export 65100:20
```

Note: In the absence of explicit configuration for the VNI in FRR, the route distinguisher (RD), and import and export route targets (RTs) for this VNI are automatically derived. The RD uses RouterId:VLAN for L2VNI and RouterId:MAX-VLAN + vrf-table-id for L3VNI. The auto import and export RTs use AS:VNI for both L2VNI and L3VNI. For RT, the AS is always encoded as a 2-byte value to allow room for a large VNI. If the router has a 4-byte AS, only the lower 2 bytes are used. For eBGP EVPN peering, the peers are in a different AS so using an automatic RT of AS:VNI does not work for route import. Therefore, the import RT is treated as *:VNI to determine which received routes are applicable to a particular VNI. 



### Advertise Primary IP

VxLAN Primary IP (PIP) address configuration is available for user to specify PIP alongwith Virtual IP address (VIP) on the MCLAG/Logical-VTEP pair. User is required to configure VxLAN primary IP address in the VxLAN configuration (please refer to VxLAN section in this document.) However, user is required to configure below under BGP to complete the EVPN PIP configuration:

- BGP default-VRF router-id to be configured same as VxLAN primary IP address
- Configure *advertise-pip peer-ip <peer-ip>* under BGP default-VRF

```
sonic(config)# router bgp 10
sonic(config-router)# router-id 2.2.2.2
sonic(config-router)# address-family l2vpn evpn
sonic(config-router-af)# advertise-all-vni
sonic(config-router-af)# advertise-pip peer-ip 3.3.3.3
```

In the above example configuration, BGP router-id 2.2.2.2 is configured to be same as VxLAN PIP. And IP address 3.3.3.3 is VxLAN PIP configured on the MCLAG peer. Similar configuration is required on the MCLAG peer, like below:

```
sonic(config)# router bgp 20
sonic(config-router)# router-id 3.3.3.3
sonic(config-router)# address-family l2vpn evpn
sonic(config-router-af)# advertise-all-vni
sonic(config-router-af)# advertise-pip peer-ip 2.2.2.2
```

Advertise-PIP for both Type-2 and Type-5 routes get enabled if configured as described above. There is no need to configure advertise-pip under BGP non-default (user) VRFs. 



### Advertise SVI IP Addresses

In a typical EVPN deployment, user reuses SVI IP addresses on VTEPs across multiple racks. However, if user use unique SVI IP addresses across multiple racks and user want the local SVI IP address to be reachable via remote VTEPs, user can enable the advertise-svi-ip option. This option advertises the SVI IP/MAC address as a type-2 route and eliminates the need for any flooding over VXLAN to reach the IP from a remote VTEP.

```
address-family l2vpn evpn
  advertise-svi-ip
exit-address-family
```

### Duplicate Address Detection

EVPN considers a host MAC or MACIP address to be duplicate if the address moves across the network more than a certain number of times within a certain number of seconds (5 moves within 180 seconds by default).

Duplicate address detection is enabled by default and triggers when:

- Two hosts have the same MAC address (the host IP addresses might be the same or different)
- Two hosts have the same IP address but different MAC addresses

User can change the threshold for detecting the duplicate mac by specify max-moves in between 2 and 1000 and time in between 2 and 1800 seconds.

```
address-family l2vpn evpn
  dup-addr-detection max-moves 10 time 1200
exit-address-family
```

The VTEP that sees an address move from remote to local begins the detection process by starting a timer. Each VTEP runs duplicate address detection independently. Detection always starts with the first mobility event from remote to local. If the address is initially remote, the detection count can start with the very first move for the address. If the address is initially local, the detection count starts only with the second or higher move for the address. If an address is undergoing a mobility event between remote VTEPs, duplicate detection is not started.

EVPN provides a freeze option that takes action on a detected duplicate address. You can freeze the address permanently (until user intervene) or for a defined amount of time, after which it is cleared automatically.

```
address-family l2vpn evpn
  dup-addr-detection freeze 1000
exit-address-family
```

command freezes duplicate addresses permanently (until user issue the clear command):

```
address-family l2vpn evpn
  dup-addr-detection freeze permanent
exit-address-family
```

clear command to un-freezes duplicate addresses

```
switch# clear evpn dup-addr vni all
```

When freeze option is enabled and a duplicate address is detected: 

If the MAC or MACIP address is learned from a remote VTEP at the time it is frozen, the forwarding information in the kernel and hardware is not updated, leaving it in the prior state. 

If the MAC or MACIP address is locally learned on this VTEP at the time it is frozen, the address is not advertised to remote VTEPs. Future local updates are processed but are not advertised to remote VTEPs. If EVPN receives a local entry delete event, the frozen entry is removed from the EVPN database. 


### Multi-VRF for BGP EVPN

Multi-VRF can be configured for an IP Fabric using asymmetric routing or symmetric routing. For
asymmetric routing, all VLANs are configured on every leaf switch and enabled for IP routing and
forwarding. Traffic flows through different routes in different directions. This severely affects scaling
with each switch carrying the MAC and MAC-IP routes for hosts that are not necessarily locally
connected.

For symmetric routing, a VLAN is only configured at the leaf switch where a local host exists. A unique, common L3 VNI number is provisioned by operator for all leaf nodes for the VRF instance using the vni command. The ingress leaf switch performs a L3 lookup and routes the packets towards the remote leaf switch over the common L3 VNI. The inner MAC destination address (DA) of the packet is set to that of router MAC address of the egress VTEP and the packet is then encapsulated in a VXLAN tunnel and sent to the remote leaf switch. The remote leaf switch then terminates the VXLAN tunnel. Because the MAC DA of the inner packet is router MAC address advertised by the leaf switch, it performs L3 lookup and the packet is routed to the locally attached host.

L3 VNI can be added and removed for a VRF instance using the vni command.

```
vrf Vrf1
  vni 104001
```

Note: There is a one-to-one mapping between a layer 3 VNI and a tenant (VRF). The VRF to layer 3 VNI mapping has to be consistent across all VTEPs. A layer 3 VNI and a layer 2 VNI cannot have the same ID. If the VNI IDs are the same, the layer 2 VNI does not get created.

### VRF Define RDs and RTs

If user do not want the RD and RTs (layer 3 RTs) for the tenant VRF to be derived automatically, user can configure them manually by specifying them under the address-family l2vpn evpn address family for that specific VRF.

```
 router bgp 401 vrf Vrf1
 address-family l2vpn evpn
  rd 172.16.100.1:20
  route-target import 10:100
  route-target export 10:100
 exit-address-family
```

Note: Care must be taken while configuring RT manually for L2VNI so that the value field does not overlap with auto calcualted L3VNI RT and vice-versa. The RT for a VNI, when not manually configured, is auto calculated as AS:vni. Hence the manually configured value of RT for L2VNI or L3VNI should not overlap with this auto calculated RT for L3VNI or L2VNI respectively, else it will result in incorrect import/export of the routes. In general it is recomendated to use auto RT for both L2VNI and L3VNI. More details on calculation of RT are dicussed in the "Define RDs and RTs" section above. 

### Advertise IP Prefix in EVPN

The following configuration is required in the tenant VRF to announce IP prefixes in the BGP RIB as EVPN type-5 routes.

```
router bgp 401 vrf Vrf1
 address-family l2vpn evpn
  advertise ipv4 unicast
 exit-address-family
```

### Configuration for EVPN Warm reboot

System warm-reboot is supported for EVPN. For EVPN Warm reboot to function, system warm reboot must be enabled and BGP-GR must be configured for BGP on all the BGP peer nodes participating in EVPN.

The BGP GR restart and stale-path timers have a default value of 240 seconds and 720 seconds respectively. To scale the system higher, these values may need to be tuned . The restart timer should be long enough for the system to warm reboot , the ip connectivity to be re-established in the control plane and the data replay of the subsystems to the kernel to be complete.

```
router bgp 401
 bgp graceful-restart
 bgp graceful-restart preserve-fw-state
 bgp graceful-restart stalepath-time 720  ( default 720)
 bgp graceful-restart restart-time 240    ( default 240)
```

### EVPN Show Commands

EVPN show commands listed below are available only from FRR vtysh shell.

Below show command displays summary of EVPN neighbors.

```
sonic-frr# show bgp l2vpn evpn summary
BGP router identifier 12.1.10.1, local AS number 65535 vrf-id 0
BGP table version 0
RIB entries 3000, using 469 KiB of memory
Peers 4, using 83 KiB of memory

Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down State/PfxRcd
Ethernet48      4      65540    5040    5043        0    0    0 00:29:59         5000
169.100.0.1     4      65535    5040    5043        0    0    0 00:29:59         5000
169.200.0.1     4      65535    5040    5043        0    0    0 00:29:59         5000
1690:100::1     4      65550    5158 1958393        0    0    0 00:34:31         5000

Total number of neighbors 4
Total number of neighbors established 4
sonic-frr#
```



EVPN routes can be displayed using below show command. Appropriate option can be used to display detailed output or subset of routes.

```
show bgp l2vpn evpn [route [{rd <rd-value>} [type <type>]] [detail] ]
```



In order to see specific route-type, the 'type' filter can be used as shown below.

```
sonic-frr# show bgp l2vpn evpn route type ?
  ead        Ethernet Auto-Discovery (type-1) route
  es         Ethernet Segment (type-4) route
  macip      MAC-IP (Type-2) route
  multicast  Multicast (Type-3) route
  prefix     Prefix (type-5 )route
sonic-frr# 
```



```
sonic-frr# show bgp l2vpn evpn route rd 32.3.10.3:1001 type multicast
EVPN type-1 prefix: [1]:[ESI]:[EthTag]
EVPN type-2 prefix: [2]:[EthTag]:[MAClen]:[MAC]
EVPN type-3 prefix: [3]:[EthTag]:[IPlen]:[OrigIP]
EVPN type-4 prefix: [4]:[ESI]:[IPlen]:[OrigIP]
EVPN type-5 prefix: [5]:[EthTag]:[IPlen]:[IP]

BGP routing table entry for 32.3.10.3:1001:[3]:[0]:[32]:[3.3.100.3]
Paths: (3 available, best #3)
  Advertised to non peer-group peers:
  Ethernet56 21.2.9.9 22.2.10.10
  Route [3]:[0]:[32]:[3.3.100.3]
  900 300
    3.3.100.3 from Ethernet56 (31.3.9.9)
      Origin IGP, valid, external
      Extended Community: RT:300:999 ET:8
      Last update: Mon Oct 28 07:02:22 2019
      PMSI Tunnel Type: Ingress Replication, label: 999
...
```

In order to see vni information in EVPN and BGP.

```
leaf1# show bgp l2vpn evpn vni
Advertise Gateway Macip: Enabled
Advertise SVI Macip: Enabled
Advertise All VNI flag: Enabled
BUM flooding: Head-end replication
Number of L2 VNIs: 3
Number of L3 VNIs: 1
Flags: * - Kernel
  VNI        Type RD                    Import RT                 Export RT                 Tenant VRF
* 450        L2   200.0.0.1:450         400:450                   400:450                  Vrf1
* 1000       L2   200.0.0.1:1000        400:1000                  400:1000                 Vrf1
* 2000       L2   200.0.0.1:200         400:2000                  400:2000                 default
* 500        L3   145.1.1.10:5096       10:100                    10:100                   Vrf1
leaf1# show evpn vni
VNI        Type VxLAN IF              # MACs   # ARPs   # Remote VTEPs  Tenant VRF
450        L2   Vxlan-450             2        3        0               Vrf1
1000       L2   Vxlan-1000            1        1        0               Vrf1
2000       L2   Vxlan-200             1        1        0               default
500        L3   Vxlan-500             0        0        n/a             Vrf1
```

Useful show command for duplicate address detection.

```
leaf1# show evpn mac vni 2000 mac 00:02:55:00:00:01
MAC: 00:02:55:00:00:01
 Intf: Ethernet45(66) VLAN: 200
 Local Seq: 0 Remote Seq: 0
 Neighbors:
    No Neighbors

Leaf2# show evpn mac vni 2000 mac 00:02:55:00:00:01
MAC: 00:02:55:00:00:01
 Remote VTEP: 4.4.4.44
 Local Seq: 0 Remote Seq: 0
 Neighbors:
    No Neighbors

Leaf2# show evpn mac vni 2000 duplicate
Number of MACs (local and remote) known for this VNI: 1
MAC               Type   Intf/Remote VTEP      VLAN
00:02:55:00:00:01 local  Ethernet45            200   5/4 
```

# CMIS 4.0 Diagnostics Configuration And Show Commands

This section explains all the CMIS 4.0 Diagnostics configuation and show commands in Industry Standard CLI.

## CMIS 4.0 Diagnostics Klish Show Commands

**show interface transceiver diagnostics capability**

This command displays diagnostics capability of the transceiver.

- Usage:

  show interface transceiver diagnostics capability [summary] [Ethernet <1|2|..N>]

  If no specific transceiver is provided, all CMIS diagnostic supported transceivers are displayed with associated diagnostics capabilities.

- Example:

```
sonic# show interface transceiver diagnostics capability summary | no-more

+--------------------+-------------+---------------+
|                    |   Loopback  |     Report    |
| Interface          | HI HO MI MO | BER IPEAK SNR |
+--------------------+-------------+---------------+
| Ethernet0          |  *     *    |   *         * |
| Ethernet16         |             |               |
| Ethernet64         |  *     *  * |   *         * |
| Ethernet80         |  *     *  * |   *         * |
| Ethernet96         |             |               |
| Ethernet152        |  *     *    |   *         * |
sonic# show interface transceiver diagnostics capability Ethernet 0 | no-more

--------------------------------------------------------
Ethernet0
--------------------------------------------------------
Loopback Capabilities
    Host Side Input Loopback                    :  Supported
    Host Side Output Loopback                   :  Not Supported
    Media Side Input Loopback                   :  Supported
    Media Side Output Loopback                  :  Not Supported
    Per-lane Host Side Loopback                 :  Not Supported
    Per-lane Media Side Loopback                :  Not Supported
    Simultaneous Host and Media Side Loopback   :  Not Supported

General Pattern Capabilities
    Auto Restart                                :  Supported
    Gating                                      :  Supported
    Latched Error Information                   :  Supported
    Per-lane Gating Timer                       :  Not Supported
    Real-time BER Error Count                   :  Supported

Diagnostic Report Capabilities
    BER Error Count/Total Bits                  :  Supported
    BER register                                :  Supported
    Host side FEC                               :  Not Supported
    Host side Input Peak                        :  Not Supported
    Host side SNR                               :  Supported
    Media side FEC                              :  Not Supported
    Media side Input Peak                       :  Not Supported
    Media side SNR                              :  Supported

Pattern Generation and Checking Capabilities
    Host Side Pattern Checker                   :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
    Host Side Pattern Generator                 :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
    Media Side Pattern Checker                  :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
    Media Side Pattern Generator                :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
sonic#
```

**show interface transceiver diagnostics status**
This command displays the diagnostic controls of the transceiver.

- Usage:

  show interface transceiver diagnostics status [summary] [Ethernet <1|2|..N>]

  If no specific transceiver is provided, all CMIS diagnostic supported transceivers are displayed with associated diagnostics status.

- Example:

```
sonic# show interface transceiver diagnostics status summary | no-more

+--------------------+-------------+-------------+
|                    |   Loopback  |   Pattern   |
| Interface          | HI HO MI MO | CH CM GH GM |
+--------------------+-------------+-------------+
| Ethernet0          |             |             |
| Ethernet16         |             |             |
| Ethernet64         |             |             |
| Ethernet80         |             |             |
| Ethernet96         |             |             |
| Ethernet152        |             |             |
sonic# show interface transceiver diagnostics status Ethernet 0 | no-more

Ethernet0
---------------------------------------------------------------------
Attribute                           :  Value/State
---------------------------------------------------------------------
Loopback
    Host Side Input                 :  Enable  ( Enable in Config )
    Host Side Output                :  Disable ( Enable in Config )
    Media Side Input                :  Disable ( Disable in Config )
    Media Side Output               :  Disable ( Disable in Config )

Pattern Generation and Checking
    Host Side Pattern Checker       :  Disable ( Disable in Config )
    Host Side Pattern Generator     :  Disable ( Disable in Config )
    Media Side Pattern Checker      :  Disable ( Disable in Config )
    Media Side Pattern Generator    :  Disable ( Disable in Config )

Error Information
    Host side BER1                  : 5.0E-1
    Host side BER2                  : 5.0E-1
    Host side BER3                  : 5.0E-1
    Host side BER4                  : 5.0E-1
    Host side BER5                  : 5.0E-1
    Host side BER6                  : 5.0E-1
    Host side BER7                  : 5.0E-1
    Host side BER8                  : 5.0E-1
    Media side BER1                 : 5.0E-1
    Media side BER2                 : 5.0E-1
    Media side BER3                 : 5.0E-1
    Media side BER4                 : 5.0E-1
    Media side BER5                 : 0
    Media side BER6                 : 0
    Media side BER7                 : 0
    Media side BER8                 : 0

    Host side SNR1                  : 0
    Host side SNR2                  : 0
    Host side SNR3                  : 0
    Host side SNR4                  : 0
    Host side SNR5                  : 0
    Host side SNR6                  : 0
    Host side SNR7                  : 0
    Host side SNR8                  : 0
    Media side SNR1                 : 10.6
    Media side SNR2                 : 10.6
    Media side SNR3                 : 79.5
    Media side SNR4                 : 79.5
    Media side SNR5                 : 0
    Media side SNR6                 : 0
    Media side SNR7                 : 0
    Media side SNR8                 : 0
sonic#
```
## CMIS 4.0 Diagnostics Config Commands

**Transceiver diagnostics loopback controls**

Enable/Disable the transceiver diagnostics loopback controls

- Usage:
sonic(config)# [no] interface transceiver diagnostics loopback {media-side-input|media-side-output|host-side-input|host-side-output} Ethernet <1|2..N>

- Example to enable media-side-input-loopback:
```
sonic-cli(config)# interface transceiver diagnostics loopback media-side-input Ethernet 112
```
- Example to diable media-side-input-loopback:
```
sonic-cli(config)# no interface transceiver diagnostics loopback media-side-input Ethernet 112
```

**Transceiver Pattern Generation and Pattern Checking**

Enable/Disable the transceiver pattern generation and pattern checking

- Usage:
sonic(config)# [no] interface transceiver diagnostics pattern {checker-host|checker-media|generator-host|generator-media} Ethernet <1|2..N>

- Example to enable host-side pattern checker:
```
sonic-cli(config)# interface transceiver diagnostics pattern checker-host Ethernet 0
```
- Example to diable host-side pattern checker:
```
sonic-cli(config)# no interface transceiver diagnostics pattern checker-host Ethernet 0
```


Below show command shows details of the L3VNI in BGP.

```
sonic# show bgp l2vpn evpn vni 1000
VNI: 1000 (known to the kernel)
  Type: L3
  Tenant VRF: Vrf-red
  RD: 20:200
  Originator IP: 2.1.1.1
  Advertise-gw-macip : n/a
  Advertise-svi-macip : n/a
  Advertise-pip: Yes
  System-IP: 2.1.1.1
  System-MAC: 52:54:00:5e:d3:cf
  Router-MAC: 00:0a:0b:0c:0d:0e
  Import Route Target:
    64358:100
  Export Route Target:
    64358:100
```

If PIP is configured, it shows the Primary IP address, system-MAC and router-MAC addresses. This command can be used to confirm if PIP configuration is effective for the VRF.



# COPP Configuration And Show Commands

This section explains all the COPP show commands and COPP configuation commands in both CLICK and KLISH that are supported in SONiC. The switch creates a default *system CoPP policy* (**copp-system-policy**) that is statically applied to the control plane to allow the configuration of trap IDs (traffic punted to CPU), CPU queue assignments, and policers to rate limit the traffic. The switch also creates a default *scheduler COPP policy* (**copp-scheduler-policy**) that is statically applied to allow the configuration of CPU queue scheduling weights and CPU queue shaper rates.


## COPP Click Show Commands 

**show copp config**  
This command displays the COPP configuration in APPL_DB.

  - Usage:  
    show copp config

- Example:
   ```
   admin@sonic:~$ show copp config
   {
     "COPP_TABLE:default": {
       "value": {
         "cbs": "600", 
         "cir": "600", 
         "meter_type": "packets", 
         "mode": "sr_tcm", 
         "queue": "0", 
         "red_action": "drop"
       }
     }, 
     "COPP_TABLE:trap.group.arp": {
       "value": {
         "cbs": "6000", 
         "cir": "6000", 
         "meter_type": "packets", 
         "mode": "sr_tcm", 
         "queue": "3", 
         "red_action": "drop", 
         "trap_action": "copy", 
         "trap_ids": "arp_req,arp_resp,neigh_discovery", 
         "trap_priority": "3"
       }
     }
   }
   ```
**show copp rate-limit**

This command displays the global max rate limit to CPU and the current rate of packets to CPU.

  - Usage:  
    show copp rate-limit

- Example:
   ```
   admin@sonic:~$ show copp rate-limit
   Rx0 max rate     30000
   Rx0 max burst     3000
   Rx0 rate             0
   Rx0 tokens        3000
   ```

## COPP Click Config Commands

**config copp rx-rate**

This command configures the global max rate limit to CPU in packets/second.

  - Usage:  
    config copp rx-rate <100-100000>

- Example:
   ```
   root@sonic:/home/admin# config copp rx-rate 20000
   ```

**config copp rx-burst**

This command configures the global max burst limit to CPU in number of packets.

  - Usage:  
    config copp rx-burst <10-10000>

- Example:
   ```
   root@sonic:/home/admin# config copp rx-burst 2000
   ```


## COPP Klish Show Commands


**show copp policy**

Displays the CoPP policy **copp-system-policy**

- Usage:   
sonic# show policy type copp

- Example:

```
sonic# show policy type copp
Policy copp-system-policy Type copp
  Flow copp-system-bgp
    Action copp-system-bgp
      trap-action trap
      trap-priority 5
      trap-queue 5
      police cir 10000 cbs 10000
        meter-type: packets
        mode: sr_tcm
        red-action: drop    
  Flow copp-system-arp
    Action copp-system-arp
      trap-action: trap
      trap-priority: 4
      trap-queue: 4
      police cir 6000 cbs 6000
        meter-type: packets
        mode: sr_tcm
        red-action: drop    
```

**show copp class-map**

Dumps the configured CoPP class maps

- Usage:  
  sonic# show class-map match-type copp

- Example:

```
sonic# show class-map match-type copp
Classifier copp-system-bgp match-type copp
  protocol bgp
  protocol bgpv6
Classifier copp-system-arp match-type copp
  protocol arp_req
  protocol arp_resp
  protocol neigh_discovery
```
**show copp protocols**

Dumps a list of supported CoPP class-map protocol trap IDs

- Usage:  
sonic# show copp protocols
  
- Example:

```
sonic# show copp protocols
Classifier match-type copp protocols
  protocol bgp
  protocol bgpv6
  protocol arp_req
  protocol arp_resp
  protocol neigh_discovery
```

**show copp action groups**

Dumps the configured CoPP action groups

- Usage:   
sonic# show copp actions

- Example:

```
sonic# show copp actions
CoPP action group copp-system-bgp
  trap-action trap
  trap-priority 5
  trap-queue 5
  police cir 10000 cbs 10000
    meter-type: packets
    mode: sr_tcm
    red-action: drop
CoPP action group copp-system-arp
  trap-action: trap
  trap-priority: 4
  trap-queue: 4
  police cir 6000 cbs 6000
    meter-type: packets
    mode: sr_tcm
    red-action: drop
```

**show qos scheduler-policy copp-scheduler-policy** 

Displays the CoPP scheduler policy **copp-scheduler-policy**

- Usage:   
sonic# show qos scheduler-policy copp-scheduler-policy

- Example:

```
sonic# show qos scheduler-policy copp-scheduler-policy
Scheduler Policy: copp-scheduler-policy
  Queue: 0
            type: wrr
            weight: 1
            pir: 100         Pps
  Queue: 1
            type: wrr
            weight: 1
            pir: 100         Pps
```

## COPP Klish Config Commands

**Creating/Deleting copp class-maps**

Creates/Removes a copp class-map

- Usage:   
sonic(config)# [no] class-map \<class_name\> match-type copp

- Example to create copp class-map:
```
sonic(config)# class-map copp-user-arp match-type copp
```
- Example to delete copp class-map:
```
sonic(config)# no class-map copp-user-arp
```
**NOTE**: The copp system default classifier (unclassified CPU traffic) always exists in the system CoPP policy and cannot be removed from the copp-system-policy.

**Adding/Deleting match protocol trap to copp class-map**

- Usage:   
sonic(config)# [no] match protocol \<protocol_name\>

- Example to add match protocol:
```
sonic(config)# class-map copp-user-arp match-type copp
sonic(config-class-map)# match protocol arp_req
sonic(config-class-map)# match protocol arp_resp
sonic(config-class-map)# match protocol neigh_discovery
```
- Example to remove match protocol:
```
sonic(config)# class-map copp-user-arp match-type copp
sonic(config-class-map)# no match protocol arp_req
```
**NOTE**: The system CoPP classifiers cannot be deleted and the match conditions cannot be modified. User created CoPP classifiers can be deleted and match conditions be added and deleted.

**NOTE**: A protocol trap ID may only be added to a single user CoPP classifier. Adding a protocol trap ID to multiple user CoPP classifiers is user configuration error and can result in undefined behavior.

**Creating/Deleting a copp action group**

Configures/Deletes the copp action group

- Usage:   
sonic(config)# [no] copp-action \<action_group_name\>

- Example to create action group:
```
sonic(config)# copp-action copp-user-arp
```
- Example to delete action group:

```
sonic(config)# no copp-action copp-user-arp
```
**NOTE**: A default CoPP action group called "default" is created by the system and cannot be removed. This is used for the default CoPP action for unclassified traffic. 

**Configuring a copp trap action**

Configures the copp action group trap action. Valid trap actions are: drop, forward, copy, copy_cancel, trap, log, deny, transit. Optional configuration. Default trap action is 'trap'.

- Usage:   
sonic(config-action)# set trap-action \<trap_action_type\>

- Example:

```
sonic(config)# copp-action copp-system-ospf
sonic(config-action)# set trap-action copy
```

**Configuring a copp trap action priority**

Conifgures the copp action group trap action priority. Valid priority values are integer values in range 0-1023. This value refers to the priority of a class-map rule when a packet has multiple matches
Default trap-priority is 1
- Usage:   
sonic(config-action)# [no] set trap-priority \<trap_priority_value\>

- Example to configure trap priority:

```
sonic(config)# copp-action copp-system-lacp
sonic(config-action)# set trap-priority 7
```
- Example to reset trap priority to default:

```
sonic(config)# copp-action copp-system-lacp
sonic(config-action)# no set trap-priority 
```

**Configuring a copp action CPU queue**

Configures the copp action group CPU queue. This is an optional configuration with default value 0. CPU queue values are integer values in range 0-47.
Default CPU queue is 0.

- Usage:  
sonic(config-action)# [no] set trap-queue \<queue_val\>

- Example:

```
sonic(config)# copp-action copp-system-lacp
sonic(config-action)# set trap-queue 18
```
- Example to reset trap queue to default value 0:
```
sonic(config)# copp-action copp-system-lacp
sonic(config-action)# no set trap-queue
```


**NOTE**: "copp-system-ptp" and "copp-system-drop" queue assignments cannot be changed

**Configuring copp action policer**

Configures the copp action group policer settings. Settings are:

1. cir - committed information rate in pps 
2. cbs - committed burst size in packets 

 No policer means no rate limit for this CoPP class flow.

- Usage:   
sonic(config-action)# police cir \<cir_val in pps\> [cbs \<cbs_val in packets\>]

- Example:

```
sonic(config-action)# police cir 1000 cbs 1000
```

**Editing the copp policy**

Modifies the copp policy. The only control plane policy supported is **copp-system-policy**, which cannot be deleted. 

- Usage and example:  
sonic(config)# policy-map copp-system-policy type copp

**Adding/Removing a copp class-map to/from the copp policy**

- Usage:   
```
sonic(config)# policy-map copp-system-policy type copp
sonic(config-policy-map)#class <class-map-name>
```
- Example to add class to policy:

```
 sonic(config)# policy-map copp-system-policy type copp
 sonic(config-policy-map)# class copp-system-arp
```
- Example to remove class from policy:

```
 sonic(config)# policy-map copp-system-policy type copp
 sonic(config-policy-map)# no class copp-system-arp
```

**Binding a copp action to the copp class-map**

Binds a copp action group to a class-map match group. Copp action is mandatory when adding a copp class-map to the copp policy

- Usage:   
sonic-cli(config-policy-map-flow)# set copp-action \<copp_group_name\>

- Example:

```
sonic(config)# policy-map copp-system-policy type copp
sonic(config-policy-map)#class copp-system-arp
sonic-cli(config-policy-map-flow)# set copp-action copp-system-arp
```

**NOTE**: "copp-system-sflow" and "copp-system-ptp" classifiers should not be assigned to trap groups with other classifiers (ie sflow and ptp traps should have their own COPP action group)

**Set CPU Queue shaper rate**

Sets CPU queue shaper rate

- Usage:   
sonic(conf-scheduler-copp-scheduler-policy-queue-3)# pir \<pir value in pps\>

- Example:
```
sonic(config)# qos scheduler-policy copp-scheduler-policy
sonic(conf-sched-policy-copp-scheduler-policy)# queue 3
sonic(conf-scheduler-copp-scheduler-policy-queue-3)# pir 2000
```

**Set CPU Queue scheduler weight**

Sets CPU queue shaper rate

- Usage:   
sonic(conf-scheduler-copp-scheduler-policy-queue-3)# weight \<weight_value\>

- Example:
```
sonic(config)# qos scheduler-policy copp-scheduler-policy
sonic(conf-sched-policy-copp-scheduler-policy)# queue 3
sonic(conf-scheduler-copp-scheduler-policy-queue-3)# weight 2
```

**Modifying control traffic rate** 

- COPP policers provide rate-limit per trap (BRCM ASIC policers rate-limit independently per pipeline). 

- Shaping limits the max rate to CPU per queue (helpful when policers across multi-pipelines combine to higher rates to CPU)

- Example:

    *config of IP2ME traffic (reduce from default 6000 pps  to 2000 pps)*

```
sonic# show copp actions | find copp-system-ip2me
CoPP action group copp-system-ip2me
    trap-action trap
    trap-priority 7
    trap-queue 7
    police cir 6000 cbs 6000
      meter-type packets
      mode sr_tcm
      red-action drop
sonic# configure terminal
sonic(config)# copp-action copp-system-ip2me
sonic(config-action)# police cir 2000 cbs 2000
sonic(config-action)# exit
sonic(config)# qos scheduler-policy copp-scheduler-policy
sonic(conf-sched-policy-copp-scheduler-policy)# queue 7
sonic(conf-scheduler-copp-scheduler-policy-queue-7)# pir 2000
sonic(conf-scheduler-copp-scheduler-policy-queue-7)# end
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#COPP-Configuration-And-Show-Commands)

# Core Dump Configuration And Show Commands

This section provides details about various configuration and show commands for the core dump feaure supported in SONiC.

## Core Dump Show Commands

This sub-section explains the list all the commands available for viewing core dump feature configuration and the core dump events recorded.

**show core**

This command is used to display list of current core files available and their information. This is a wrapper command for the *coredumpctl* utility provided by *systemd-coredump* package. Configuration related to administrative mode of core dump feature can also be viewed using this command.



- Usage:     
  show core [ config | info | list ]
- Example:

Display core dump feature administration mode.

```
root@sonic:/home/admin# show core config
Coredump : Enabled
```

Display the list of application core events recorded.

```
root@sonic:/home/admin# show cores list
TIME                            PID   UID   GID SIG COREFILE EXE
Sat 2019-09-14 12:05:50 UTC   24547     0     0   6 present  /usr/bin/vlanmgrd
Sat 2019-09-14 12:06:11 UTC   26780     0     0   6 present  /usr/bin/natmgrd

root@sonic:/home/admin# show cores list natmgrd
TIME                            PID   UID   GID SIG COREFILE EXE
Sat 2019-09-14 12:06:11 UTC   26780     0     0   6 present  /usr/bin/natmgrd
```



Display detailed information of an application core event.

```
root@sonic:/home/admin# show cores info natmgrd
           PID: 26780 (natmgrd)
           UID: 0 (root)
           GID: 0 (root)
        Signal: 6 (ABRT)
     Timestamp: Sat 2019-09-14 12:06:11 UTC (2min 22s ago)
  Command Line: /usr/bin/natmgrd
    Executable: /usr/bin/natmgrd
 Control Group: /docker/b0774b109cca85b1c55cc632b4970a34224f27859b1c26df6174482419dc8671
         Slice: -.slice
       Boot ID: c5a8a9e4f02c49e4929361cbb5c6ce62
    Machine ID: 4e9149cb5654460eb41223b1f9755598
      Hostname: sonic
       Storage: /var/lib/systemd/coredump/core.natmgrd.0.c5a8a9e4f02c49e4929361cbb5c6ce62.26780.1568462771000000000000.lz4
       Message: Process 26780 (natmgrd) of user 0 dumped core.

                Stack trace of thread 23:
                #0  0x00007fa597ed6303 epoll_wait (libc.so.6)
                #1  0x00007fa598e83ad8 _ZN4swss6Select16poll_descriptorsEPPNS_10SelectableEj (libswsscommon.so.0)
                #2  0x00007fa598e83d0b _ZN4swss6Select6selectEPPNS_10SelectableEi (libswsscommon.so.0)
                #3  0x000055ac9c8c1270 n/a (/usr/bin/natmgrd)
```



## Core Dump Config Commands

This sub-section explains the list of the configuration options available for core dump file generation feature in SONiC.

**config core enable**

Use this command to enable the capability of application core dump file generation.

- Usage: config core enable
- Example:

```
root@sonic:/home/admin# config core enable
```



**config core disable**

Use this command to disable the capability of application core dump file generation. Even though application core dump file generation is disabled, information about the application fault is still available to view.

- Usage: config core disable
- Example:

```
root@sonic:/home/admin# config core disable
```


# Tech-support export Configuration And Show Commands

This section provides details about various configuration and show commands for the techsupport export feaure supported in SONiC.

## export Show Commands

This sub-section explains the list all the commands available for viewing techsupport export feature configuration.

**show export**

This command is used to display list of current configuration of techsupport export feature and their information.



- Usage:     
  show export
- Example:

```
root@sonic:/home/admin# show export
Export Info : {'username': 'admin', 'servername': '10.22.33.44', 'protocol': 'scp', 'destdir': './supportsave'}
```



## Tech-support export Config Commands

This section explains the list of the configuration options available for the techsupport export feature in SONiC.

Subsequent pages explain each of these commands in detail.

```
root@sonic:/home/admin# config export --help
Usage: config export [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  disable   Disable the tech-support export service
  enable    Enable the tech-support export service
  interval  Configure the tech-support export interval
  server    Configure the remote server name to connect


This section explains the list of the configuration options available for techsupport export feature in SONiC.

In order to capture and export techsupport data to an remote server, the export service should be configured with necessary remote machine credentials.

root@sonic:/home/admin# config export server
```

**config export server username destdir protocol <remote address> <username> <destination directory> <protocol- scp/sftp> **

Use this command to configure the techsupport export service.

- Usage: config export server username destdir protocol <remote address> <username> <destination directory> <protocol- scp/sftp>

# config export server username destdir protocol  --help
Usage: config export server username destdir protocol [OPTIONS] <server name>
                                                      <user name> <destination
                                                      directory> <protocol:
                                                      scp/sftp>

  Configures the export service to upload the tech-support data to a remote
  server

     <server name> -  Configure the remote server name to connect
     <user name>   -  Configure the remote server user name to upload
     <dest dir>    -  Configure the remote server directory to save the tech-support data
     <protocol>    -  Configure the protocol type[scp/sftp] to upload the tech-support data

Options:
  --help  Show this message and exit.

- Example:
```
root@sonic:/home/admin# config export server username destdir protocol 10.59.132.52 admin ./supportsave scp
Note: User is prompted for the remote server password.
```

**config export [enable/disable]**

Use this command to enable/disable the tech-support export service. By default techsupport export is disabled.

- Usage: config core enable
- Example:

```
root@sonic:/home/admin# config core enable
```


**config export interval**

Use this command to change the techsupport interval. By default the techsupport export interval is configured as 30 mintues. If interval is 0, periodic techsupport export service is disabled.

- Usage: config export interval <interval in minutes>
- Example:

```
root@sonic:/home/admin# config export interval 120
```





Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#BGP-Configuration-And-Show-Commands)



# CRM Configuration And Show Commands

This section explains all the Critical Resource Management(CRM) show commands and CRM configuation options that are supported in SONiC.

## CRM config commands

This sub-section contains the configuration commands that can configure the CRM related configuration.
1) clear - Removes the CRM polling and threshold related configuration
2) polling - Configures the CRM polling related configuration
3) thresholds - Configures threshold configuration

**CRM config**  

This command configures the CRM related configuration.

  - Usage:<br>
    crm config [OPTIONS] COMMAND [ARGS]...<br>
    ​     
  ```
  Options:
     --help  Show this message and exit.

  Commands:
    clear       CRM config clear
    polling     CRM polling configuration
    thresholds  CRM thresholds configuration
  ```

**clear**

This command removes the crm polling interval and threshold related configuration for all CRM resources.

- Usage:<br>
    crm config clear

- Example:  
  ```
  admin@sonic:~$ crm config clear
  ```

**polling**

This command configures the polling interval for all CRM resources.


- Usage:<br>
    crm config polling interval <value\>

- Example:  
  ```
  admin@sonic:~$ crm config polling interval 100
  ```

**thresholds [critical resource] high**

These commands configure the high threshold value for a critical resource. 

- Usage:<br>
    crm config thresholds acl group high <value\><br>
    crm config thresholds acl group counter high <value\><br>
    crm config thresholds acl group entry high <value\><br>
    crm config thresholds acl table high <value\><br>
    crm config thresholds fdb high <value\><br>
    crm config thresholds ipv4 neighbor high <value\><br>
    crm config thresholds ipv4 nexthop high <value\><br>
    crm config thresholds ipv4 route high <value\><br>
    crm config thresholds ipv6 neighbor high <value\><br>
    crm config thresholds ipv6 nexthop high <value\><br>
    crm config thresholds ipv6 route high <value\><br>
    crm config thresholds nexthop group object high <value\><br>
    crm config thresholds nexthop group member high <value\><br>


- Example:  
  ```
  admin@sonic:~$ crm config thresholds acl group high 70
  admin@sonic:~$ crm config thresholds acl group counter high 75
  admin@sonic:~$ crm config thresholds acl group table 80
  admin@sonic:~$ crm config thresholds ipv4 route 90
  admin@sonic:~$ crm config thresholds ipv6 nexthop 70
  admin@sonic:~$ crm config thresholds nexthop group object high 95
  admin@sonic:~$ crm config thresholds nexthop group member high 50
  ```

**thresholds [critical resource] low**

These commands configure the low threshold value for a critical resource. 

- Usage:<br>
    crm config thresholds acl group low <value\><br>
    crm config thresholds acl group counter low <value\><br>
    crm config thresholds acl group entry low <value\><br>
    crm config thresholds acl table low <value\><br>
    crm config thresholds fdb low <value\><br>
    crm config thresholds ipv4 neighbor low <value\><br>
    crm config thresholds ipv4 nexthop low <value\><br>
    crm config thresholds ipv4 route low <value\><br>
    crm config thresholds ipv6 neighbor low <value\><br>
    crm config thresholds ipv6 nexthop low <value\><br>
    crm config thresholds ipv6 route low <value\><br>
    crm config thresholds nexthop group object low <value\><br>
    crm config thresholds nexthop group member low <value\><br>

- Example:  
  ```
  admin@sonic:~$ crm config thresholds acl group low 60
  admin@sonic:~$ crm config thresholds acl group counter low 65
  admin@sonic:~$ crm config thresholds acl group table 60
  admin@sonic:~$ crm config thresholds ipv4 route 40
  admin@sonic:~$ crm config thresholds ipv6 nexthop 30
  admin@sonic:~$ crm config thresholds nexthop group object low 20
  admin@sonic:~$ crm config thresholds nexthop group member low 50
  ```

**thresholds [critical resource] type**

These commands configure the thresholds type for a critical resource. Threshold type can be percentage, used or free.

- Usage:<br>
    crm config thresholds acl group type [percentage|used|free]<br>
    crm config thresholds acl group counter type [percentage|used|free]<br>
    crm config thresholds acl group entry type [percentage|used|free]<br>
    crm config thresholds acl table type [percentage|used|free]<br>
    crm config thresholds fdb type [percentage|used|free]<br>
    crm config thresholds ipv4 neighbor type [percentage|used|free]<br>
    crm config thresholds ipv4 nexthop type [percentage|used|free]<br>
    crm config thresholds ipv4 route type [percentage|used|free]<br>
    crm config thresholds ipv6 neighbor type [percentage|used|free]<br>
    crm config thresholds ipv6 nexthop type [percentage|used|free]<br>
    crm config thresholds ipv6 route type [percentage|used|free]<br>
    crm config thresholds nexthop group object type [percentage|used|free]<br>
    crm config thresholds nexthop group member type [percentage|used|free]<br>

- Example:  
  ```
  admin@sonic:~$ crm config thresholds acl group type percentage
  admin@sonic:~$ crm config thresholds acl group counter type used
  admin@sonic:~$ crm config thresholds acl group table free
  admin@sonic:~$ crm config thresholds ipv4 route used
  admin@sonic:~$ crm config thresholds ipv6 nexthop free
  admin@sonic:~$ crm config thresholds nexthop group object type percentage
  admin@sonic:~$ crm config thresholds nexthop group member type used
  ```

## CRM show commands

**CRM show**  

This command displays the CRM related general, resource usage and thresholds information.

  - Usage:<br>
      crm show [OPTIONS] COMMAND [ARGS]...


```
  Options:
   --help  Show this message and exit.

  Commands:
    resources   Show CRM resources information
    summary     Show CRM general information
    thresholds  Show CRM thresholds information
```

**resources [critical resource]**

These commands display currently USED and AVAILABLE number of entries for a critical resource.<br> 
"crm show resources all" displays these entries for all the critical resources.

- Usage:<br>
    crm show resources acl group<br>
    crm show resources acl table<br>
    crm show resources all<br>
    crm show resources fdb<br>
    crm show resources ipv4 neighbor<br>
    crm show resources ipv4 nexthop<br>
    crm show resources ipv4 route<br>
    crm show resources ipv6 neighbor<br>
    crm show resources ipv6 nexthop<br>
    crm show resources ipv6 route<br>
    crm show resources nexthop group object<br>
    crm show resources nexthop group member<br>

- Example:  
  ```
  admin@sonic:$ crm show resources all

  Resource Name           Used Count    Available Count
  --------------------  ------------  -----------------
  ipv4_route                    1007              48145
  ipv6_route                    1004              11284
  ipv4_nexthop                   100              32544
  ipv6_nexthop                   122              32544
  ipv4_neighbor                  100              20180
  ipv6_neighbor                  122              10090
  nexthop_group_member           200              16184
  nexthop_group                    4                124
  fdb_entry                       30              40929

  Stage    Bind Point    Resource Name      Used Count    Available Count
  -------  ------------  ---------------  ------------  -----------------
  INGRESS  PORT          acl_group                   1               1023
  INGRESS  PORT          acl_table                   2                  2
  INGRESS  LAG           acl_group                   0               1023
  INGRESS  LAG           acl_table                   0                  2
  INGRESS  VLAN          acl_group                   0               1023
  INGRESS  VLAN          acl_table                   0                  5
  INGRESS  RIF           acl_group                   0               1023
  INGRESS  RIF           acl_table                   0                  5
  INGRESS  SWITCH        acl_group                   0               1023
  INGRESS  SWITCH        acl_table                   0                  5
  EGRESS   PORT          acl_group                   0               1023
  EGRESS   PORT          acl_table                   0                  2
  EGRESS   LAG           acl_group                   0               1023
  EGRESS   LAG           acl_table                   0                  2
  EGRESS   VLAN          acl_group                   0               1023
  EGRESS   VLAN          acl_table                   0                  2
  EGRESS   RIF           acl_group                   0               1023
  EGRESS   RIF           acl_table                   0                  2
  EGRESS   SWITCH        acl_group                   0               1023
  EGRESS   SWITCH        acl_table                   0                  2

  Table ID         Resource Name      Used Count    Available Count
  ---------------  ---------------  ------------  -----------------
  0x70000000009e3  acl_entry                  20                492
  0x70000000009e3  acl_counter                20              30148

  
  admin@sonic:$ crm show resources acl group
  Stage    Bind Point    Resource Name      Used Count    Available Count
  -------  ------------  ---------------  ------------  -----------------
  INGRESS  PORT          acl_group                   0               1024
  INGRESS  PORT          acl_table                   0                  3
  INGRESS  LAG           acl_group                   0               1024
  INGRESS  LAG           acl_table                   0                  3
  INGRESS  VLAN          acl_group                   0               1024
  INGRESS  VLAN          acl_table                   0                  9
  INGRESS  RIF           acl_group                   0               1024
  INGRESS  RIF           acl_table                   0                  9
  INGRESS  SWITCH        acl_group                   0               1024
  INGRESS  SWITCH        acl_table                   0                  9
  EGRESS   PORT          acl_group                   0               1024
  EGRESS   PORT          acl_table                   0                  2
  EGRESS   LAG           acl_group                   0               1024
  EGRESS   LAG           acl_table                   0                  2
  EGRESS   VLAN          acl_group                   0               1024
  EGRESS   VLAN          acl_table                   0                  2
  EGRESS   RIF           acl_group                   0               1024
  EGRESS   RIF           acl_table                   0                  2
  EGRESS   SWITCH        acl_group                   0               1024
  EGRESS   SWITCH        acl_table                   0                  2
  ```


  admin@sonic:$ crm show resources fdb
  Resource Name      Used Count    Available Count
---------------  ------------  -----------------
  fdb_entry                   2              40957


  admin@sonic:$ crm show resources ipv4 route
  Resource Name      Used Count    Available Count
---------------  ------------  -----------------
  ipv4_route                  1              49151


  admin@sonic:$ crm show resources ipv6 neighbor
  Resource Name      Used Count    Available Count
---------------  ------------  -----------------
  ipv6_neighbor               0              10240


  admin@sonic:$ crm show resources nexthop group object
  Resource Name      Used Count    Available Count
---------------  ------------  -----------------
  nexthop_group               0                128


  admin@sonic:$ crm show resources nexthop group member
  Resource Name           Used Count    Available Count
--------------------  ------------  -----------------
  nexthop_group_member             0              16384

  ```

**summary**

This command displays the polling interval for CRM.

- Usage:<br>
    crm show summary

- Example:  
  ```
  admin@sonic:~$ crm show summary 

  Polling Interval: 100 second(s)
  ```

**thresholds [critical resource]**

These commands display threshold type, low and high thresholds configured for a critical resource.

- Usage:<br>
  crm show thresholds acl group<br>
  crm show thresholds acl table<br>
  crm show thresholds all<br>
  crm show thresholds fdb<br>
  crm show thresholds ipv4 neighbor<br>
  crm show thresholds ipv4 nexthop<br>
  crm show thresholds ipv4 route<br>
  crm show thresholds ipv6 neighbor<br>
  crm show thresholds ipv6 nexthop<br>
  crm show thresholds ipv6 route<br>
  crm show thresholds nexthop group object<br>
  crm show thresholds nexthop group member<br>

- Example:  
  ```
  admin@sonic:$ crm show thresholds acl group
  Resource Name    Threshold Type      Low Threshold    High Threshold
---------------  ----------------  ---------------  ----------------
  acl_group        used                           30                90


  admin@sonic:$ crm show thresholds acl table
  Resource Name    Threshold Type      Low Threshold    High Threshold
---------------  ----------------  ---------------  ----------------
  acl_table        used                           30                90


  admin@sonic:$ crm show thresholds all
  Resource Name         Threshold Type      Low Threshold    High Threshold
--------------------  ----------------  ---------------  ----------------
  ipv4_route            used                           30                90
  ipv6_route            used                           30                90
  ipv4_nexthop          used                           30                90
  ipv6_nexthop          used                           30                90
  ipv4_neighbor         used                           30                90
  ipv6_neighbor         used                           30                90
  nexthop_group_member  used                           30                90
  nexthop_group         used                           30                90
  acl_table             used                           30                90
  acl_group             used                           30                90
  acl_entry             used                           30                90
  acl_counter           used                           30                90
  fdb_entry             used                           30                90


  admin@sonic:$ crm show thresholds fdb
  Resource Name    Threshold Type      Low Threshold    High Threshold
---------------  ----------------  ---------------  ----------------
  fdb_entry        used                           30                90


  admin@sonic:$ crm show thresholds ipv4 nexthop
  Resource Name    Threshold Type      Low Threshold    High Threshold
---------------  ----------------  ---------------  ----------------
  ipv4_nexthop     used                           30                90


  admin@sonic:$ crm show thresholds ipv6 neighbor
  Resource Name    Threshold Type      Low Threshold    High Threshold
---------------  ----------------  ---------------  ----------------
  ipv6_neighbor    used                           30                90


  admin@sonic:$ crm show thresholds nexthop group object
  Resource Name    Threshold Type      Low Threshold    High Threshold
---------------  ----------------  ---------------  ----------------
  nexthop_group    used                           30                90


  admin@sonic:$ crm show thresholds nexthop group member
  Resource Name         Threshold Type      Low Threshold    High Threshold
--------------------  ----------------  ---------------  ----------------
  nexthop_group_member  used                           30                90
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#CRM-Configuration-And-Show-Commands)


# DHCP Relay Commands

This section explains all the DHCP Relay commands that are supported in SONiC.

## DHCP Relay CLICK Configuration Commands

**config interface ip dhcp-relay add <interface_name> <ip_addr1> -vrf-name=<vrf_name> <ip_addr2> <ip_addr3> <ip_addr4> -src-intf=<intf_name> -link-select=<enable/diable> -max-hop-count=<hop_count>**

This command enables user to configure IPv4 DHCP Server address on an interface. One address is mandatory and a maximum of four addresses are allowed. If the command is used multiple times with different addresses, the addresses are appended to the list of server addresses.

If DHCP server is reachable in non-default VRF, the server VRF must be specified using -vrf-name option. If the -vrf-name is not specified, the DHCP server is assumed to be reachable in default VRF. Even in case of client and server are in the same non-default VRF, the server VRF must be specified explicitly (else it is assumed to be reachable in default VRF).

- Usage:
    sudo config interface ip dhcp-relay add <interface_name> -vrf-name=<vrf_name> <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4> -src-intf=<intf_name> -link-select=<enable/diable> -max-hop-count=<hop_count>

- Examples:
  ```
    admin@sonic:~$ sudo config interface ip dhcp-relay add Vlan206 1.2.0.1 3.4.0.1
    admin@sonic:~$ sudo config interface ip dhcp-relay add PortChannel007 192.168.0.1
    admin@sonic:~$ sudo config interface ip dhcp-relay add Ethernet52 1.2.0.1 3.4.0.1 192.168.0.1 192.168.5.1
    admin@sonic:~$ sudo config interface ip dhcp-relay add Ethernet0 172.16.0.1 -vrf-name=VrfRed
    admin@sonic:~$ sudo config interface ip dhcp-relay add Vlan206 1.2.0.1 -src-intf=Loopback1 -link-select=enable -max-hop-count=12
  ```

**config interface ipv6 dhcp-relay add <interface_name> <ip_addr1> -vrf-name=<vrf_name> <ip_addr2> <ip_addr3> <ip_addr4> -src-intf=<intf_name> -max-hop-count=<hop_count>**

This command enables user to configure IPv6 DHCP Server address on an interface. One address is mandatory and a maximum of four addresses are allowed. If the command is used multiple times with different addresses, the addresses are appended to the list of server addresses.

If DHCP server is reachable in non-default VRF, the server VRF must be specified using -vrf-name option. If the -vrf-name is not specified, the DHCP server is assumed to be reachable in default VRF. Even in case of client and server are in the same non-default VRF, the server VRF must be specified explicitly (else it is assumed to be reachable in default VRF).

- Usage:
    sudo config interface ipv6 dhcp-relay add <interface_name> <ip_addr1> -vrf-name=<vrf_name> <ip_addr2> <ip_addr3> <ip_addr4> -src-intf=<intf_name> -max-hop-count=<hop_count>

- Examples:
  ```
    admin@sonic:~$ sudo config interface ipv6 dhcp-relay add Vlan206 1122::1 -src-intf=Loopback1 -max-hop-count=12
    admin@sonic:~$ sudo config interface ipv6 dhcp-relay add PortChannel007 3366::1 1112::1
    admin@sonic:~$ sudo config interface ipv6 dhcp-relay add PortChannel007 9977::1 -vrf-name=Vrf9
    admin@sonic:~$ sudo config interface ipv6 dhcp-relay add Ethernet52 1122::1 3366::1 1112::1
  ```

**config interface [ip|ipv6] dhcp-relay remove <interface_name> <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>**

This command enables user to remove the configured DHCP Server address on an interface. One address is mandatory and a maximum of four addresses are allowed. A single server address can be deleted from the list of configured addresses by providing that address as argument.

- Usage:
    sudo config interface ip dhcp-relay remove <interface_name> <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>
    sudo config interface ipv6 dhcp-relay remove <interface_name> <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip dhcp-relay remove Vlan206 1.2.0.1 3.4.0.1
  admin@sonic:~$ sudo config interface ip dhcp-relay remove PortChannel007 192.168.0.1
  admin@sonic:~$ sudo config interface ip dhcp-relay remove Ethernet52 1.2.0.1 3.4.0.1 192.168.0.1 192.168.5.1
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay remove Vlan206 1122::1
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay remove PortChannel007 3366::1 1112::1
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay remove Ethernet52 1122::1 3366::1 1112::1
  ```

**config interface [ip|ipv6] dhcp-relay src-intf [add|remove] <interface_name> <source_interface>**

This command adds or removes source interface selection on the given interface. Only one interface can be specified as the source interface. To update the existing source interface, a new interface needs to be added. If the link-selection option is enabled, the source interface cannot be removed. The link-selection must be disabled before removing the source interface configuration.

- Usage:
    sudo config interface ip dhcp-relay src-intf add <interface_name> <source_interface>
    sudo config interface ip dhcp-relay src-intf remove <interface_name>
    sudo config interface ipv6 dhcp-relay src-intf add <interface_name> <source_interface>
    sudo config interface ipv6 dhcp-relay src-intf remove <interface_name>

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip dhcp-relay src-intf add Vlan206 Looback1
  admin@sonic:~$ sudo config interface ip dhcp-relay src-intf remove Vlan206
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay src-intf add Ethernet12 Looback1
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay src-intf remove Ethernet12
  ```

**config interface [ip|ipv6] dhcp-relay max-hop-count [add|remove] <interface_name> <max_hop_count>**

This command sets the maximum hop count value on the given interface. Use 'add' option to configure a non-default value. Use 'remove' option to reset the hop count to default value. The 'add' option can also be used to update currently configured value. The range for hop count is 1 to 16. The default value is 10.

- Usage:
    sudo config interface ip dhcp-relay max-hop-count add <interface_name> <max_hop_count>
    sudo config interface ip dhcp-relay max-hop-count remove <interface_name>
    sudo config interface ipv6 dhcp-relay max-hop-count add <interface_name> <max_hop_count>
    sudo config interface ipv6 dhcp-relay max-hop-count remove <interface_name>

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip dhcp-relay max-hop-count add Vlan206 5
  admin@sonic:~$ sudo config interface ip dhcp-relay max-hop-count remove Vlan206
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay max-hop-count add Ethernet12 5
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay max-hop-count remove Ethernet12
  ```

**config interface ip dhcp-relay link-select [add|remove] <interface_name>**

Thiscommand adds or removes link-selection sub option on the given IPv4 DHCP Relay interface. The link-selection sub option is disabled by default. The source interface must be configured before enabling link-selection sub option. If the source interface is not configured, the link-select command fails with error message.

- Usage:
    sudo config interface ip dhcp-relay link-select add <interface_name>
    sudo config interface ip dhcp-relay link-select remove <interface_name>

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip dhcp-relay link-select add Vlan206
  admin@sonic:~$ sudo config interface ip dhcp-relay link-select remove Vlan206
  ```
**config interface [ip|ipv6] dhcp-relay vrf-select [add|remove] <interface_name>**
This command enables or disables VRF selection sub option 151/152 on the given IPv4 DHCP Relay interface. The vrf-select sub option is disabled by default. If the interface belongs to default/global VRF, the sub option is not added to the relayed packet.

- Usage:
    sudo config interface ip dhcp-relay vrf-select add <interface_name>
    sudo config interface ip dhcp-relay vrf-select remove <interface_name>

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip dhcp-relay vrf-select add Vlan206
  admin@sonic:~$ sudo config interface ip dhcp-relay vrf-select remove Vlan206

  admin@sonic:~$ sudo config interface ipv6 dhcp-relay vrf-select add Vlan206
  admin@sonic:~$ sudo config interface ipv6 dhcp-relay vrf-select remove Vlan206
  ```
**config interface ip dhcp-relay policy-action [discard|append|replace] <interface_name>**

This command controls handling of relay agent options on the given interface. The default is to discard incoming packet with agent options. This command is applicable for DHCPv4 packets only.

- Usage:
    sudo config interface ip dhcp-relay policy-action [discard|append|replace] Ethernet36

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip dhcp-relay policy-action append Ethernet36
  ```

## DHCP Relay CLICK Show Commands

**show [ip|ipv6] dhcp-relay brief**

This command displays all the configured DHCP Server address configurations.

- Usage:
    sudo show ip dhcp-relay brief
    sudo show ipv6 dhcp-relay brief

- Sample Output:
  ```
    admin@sonic:~$ sudo show ip dhcp-relay brief
    
    +------------------+-----------------------+
    | Interface Name   | DHCP Helper Address   |
    +==================+=======================+
    | Vlan10           | 20.20.1.2             |
    |                  | 1.2.0.1               |
    +------------------+-----------------------+
    | PortChannel10    | 40.20.1.2             |
    +------------------+-----------------------+
    | Ethernet52       | 10.10.1.2             |
    +------------------+-----------------------+
    
    admin@sonic:~$ sudo show ipv6 dhcp-relay brief
    
    +------------------+-----------------------+
    | Interface Name   | DHCP Helper Address   |
    +==================+=======================+
    | Vlan10           | 2002::2               |
    |                  | 1122::1               |
    +------------------+-----------------------+
    | PortChannel10    | 4422::2               |
    +------------------+-----------------------+
    | Ethernet52       | 1100::2               |
    +------------------+-----------------------+
  ```

**show [ip|ipv6] dhcp-relay detailed  [OPTIONS] <interface_name>**

This command displays the relay configuration on the given interface. If the interface name is not specified, the command displays relay configuration for all the interfaces enabled for DHCP relay. If the given interface is not enabled for DHCP relay, the command returns an error message. If the source interface is not configured, the command output indicates it as 'Not Configured'.

- Usage:
    sudo show ip dhcp-relay detailed
    sudo show ip dhcp-relay detailed <interface_name>
    sudo show ipv6 dhcp-relay detailed
    sudo show ipv6 dhcp-relay detailed <interface_name>

- Sample output:
  ```
    admin@sonic:~$ sudo show ip dhcp-relay detailed Vlan200
    Server Address: 114.0.0.2
    Server VRF: Not Configured
    Source Interface: Vlan100
    Link Select: enable
    Max Hop Count: 12
    VRF Select: disable
    Policy Action: append
    
    admin@sonic:~$ sudo show ipv6 dhcp-relay detailed Vlan200
    Server Address: 1122::1
    Server VRF: VrfRed
    Source Interface: Not Configured
    Max Hop Count: 10
    VRF Select: disable
  ```

**show [ip|ipv6] dhcp-relay statistics <interface_name>**

This command displays the DHCP Relay statistics. If interface name is not specified, the command displays statistics of all the DHCP Relay processes running.

- Usage:
    sudo show ip dhcp-relay statistics
    sudo show ip dhcp-relay statistics <interface_name>
    sudo show ipv6 dhcp-relay statistics
    sudo show ipv6 dhcp-relay statistics <interface_name>

- Sample Output:
  ```
    admin@sonic:~$ sudo show ip dhcp-relay statistics Vlan10
    BOOTREQUEST messages received by the relay agent: 4
    BOOTREQUEST messages forwarded by the relay agent: 2
    BOOTREPLY messages forwarded by the relay agent: 0
    DHCP DISCOVER messages received by the relay agent: 1
    DHCP OFFER messages sent by the relay agent: 0
    DHCP REQUEST messages received by the relay agent: 1
    DHCP ACK messages sent by the relay agent: 0
    DHCP RELEASE messages received by the relay agent: 0
    DHCP DECLINE messages received by the relay agent: 0
    DHCP INFORM messages received by the relay agent: 0
    DHCP NACK messages sent by the relay agent: 0
    Total number of DHCP packets dropped by the relay agent: 2
    Number of DHCP packets dropped due to an invalid opcode: 0
    Number of DHCP packets dropped due to an invalid option: 0
    Errors relaying packets from clients: 0
    Errors relaying packets from servers: 0
    Packets dropped with bogus GIADDR: 0
    Packets dropped due to bad relay info: 0
    Packets dropped due to missing relay info: 0
    Packets dropped due to invalid hdr length: 0
    Packets dropped on interface with no IP: 0
    Replies dropped on downstream interface: 0
    Requests dropped on upstream interface: 2
    DHCPv4 OFFER packets received from server: 0
    DHCPv4 ACK packets received from server: 0
    DHCPv4 NACK packets received from server: 0
    Packets dropped on exceeding the max hop count: 0
    DHCPv4 OFFER packets relayed to client on other downstream interface: 0
    DHCPv4 ACK packets relayed to client on other downstream interface: 0
    DHCPv4 NACK packets relayed to client on other downstream interface: 0
    
    admin@sonic:~$ sudo show ipv6 dhcp-relay statistics Vlan10
    DHCPv6 SOLICIT messages received by the relay agent: 1
    DHCPv6 ADVERTISEMENT messages sent by the relay agent: 1
    DHCPv6 REQUEST messages received by the relay agent: 1
    DHCPv6 REPLY messages sent by the relay agent: 1
    DHCPv6 CONFIRM messages received by the relay agent: 0
    DHCPv6 RELEASE messages received by the relay agent: 0
    DHCPv6 DECLINE messages received by the relay agent: 0
    DHCPv6 REBIND messages received by the relay agent: 0
    DHCPv6 RECONFIGURE messages sent by the relay agent: 0
    DHCPv6 INFO-REQUEST messages received by the relay agent: 0
    DHCPv6 RELAY-REPLY messages received by the relay agent: 2
    DHCPv6 RELAY-FORWARD messages sent by the relay agent: 2
    Total number of DHCPv6 packets dropped by the relay agent: 0
    Number of DHCPv6 packets dropped due to an invalid opcode: 0
    Number of DHCPv6 packets dropped due to an invalid option: 0
    Packets relayed from server to client: 2
    Errors relaying packets from servers: 0
    Errors relaying packets from clients: 0
    Packets with wrong message type dropped on downstream interface: 0
    Packets with wrong message type dropped on upstream interface: 0
    DHCPv6 RENEW packets received from client: 0
    DHCPv6 LEASE-QUERY packets received from client: 0
    DHCPv6 DHCPV4-QUERY packets received from client: 0
    DHCPv6 INFORM-REQUEST packets received from downstream: 0
    DHCPv6 LEASE QUERY packets sent to client: 0
    DHCPv6 DHCPV4 RESPONSE packets sent to client: 0
    Packets dropped on exceeding the max hop count: 0
    DHCPv6 ADVERTISE packets sent to client on other downstream interface: 0
    DHCPv6 REPLY packets sent to client on other downstream interface: 0
    DHCPv6 RECONFIGURE packets sent to client on other downstream interface: 0
    DHCPv6 LEASE QUERY packets sent to client on other downstream interface: 0
    DHCPv6 DHCPV4 RESPONSE packets sent to client on other downstream interface: 0
  ```

## DHCP Relay CLICK Debug Commands

**debug dhcp-relay**

This command enables the user to toggle between DEBUG and INFO logging levels of syslog for all the DHCP Relay processes.

- Usage:
    sudo debug dhcp-relay

**debug [ip|ipv6] dhcp-relay <interface_name>**

This command toggles the syslog level for the DHCP Relay process the relay interface.

- Usage:
    sudo debug ip dhcp-relay <interface_name>
    sudo debug ipv6 dhcp-relay <interface_name>

- Example:
  ```
    admin@sonic:~$ sudo debug ip dhcp-relay Vlan10
    admin@sonic:~$ sudo debug ip dhcp-relay Ethernet52
    admin@sonic:~$ sudo debug ipv6 dhcp-relay Vlan10
    admin@sonic:~$ sudo debug ipv6 dhcp-relay Ethernet52
    ```

## DHCP Relay CLICK Clear Commands

**sonic-clear [ip|ipv6] dhcp-relay statistics <interface_name>**

This command clears the DHCP Relay statistics on a relay interface.

- Usage:
    sudo sonic-clear ip dhcp-relay statistics <interface_name>
    sudo sonic-clear ipv6 dhcp-relay statistics <interface_name>

- Example:
    ```
    admin@sonic:~$ sudo sonic-clear ip dhcp-relay statistics Vlan10
    admin@sonic:~$ sudo sonic-clear ip dhcp-relay statistics Ethernet52
    admin@sonic:~$ sudo sonic-clear ipv6 dhcp-relay statistics Vlan10
    admin@sonic:~$ sudo sonic-clear ipv6 dhcp-relay statistics Ethernet52
    ```

## DHCP Relay KLISH Configuration Commands

### DHCP Relay KLISH Interface Configuration Commands

DHCP Relay configurations for specific interface are executed in interface-configuration-view.

  ```
  sonic# configure terminal
  sonic(config)# interface Vlan100
  sonic(conf-if-Vlan100)#
  ```

**[ip|ipv6] dhcp-relay <ip_addr1> vrf-name <vrf_name> <ip_addr2> <ip_addr3> <ip_addr4>**

This command adds DHCP Relay addresses on the given interface. Up to 4 addresses can be specified at a time (separated by space). No form of this command removes the DHCP Relay addresses. If no addresses are specified for the no form, all the configured relay addresses are removed from the interface configuration.

If DHCP server is reachable in non-default VRF, the server VRF must be specified using -vrf-name option. If the -vrf-name is not specified, the DHCP server is assumed to be reachable in default VRF. Even in case of client and server are in the same non-default VRF, the server VRF must be specified explicitly (else it is assumed to be reachable in default VRF).

- Usage:
    ip dhcp-relay <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>
    no ip dhcp-relay <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>
    ipv6 dhcp-relay <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>
    no ipv6 dhcp-relay <ip_addr1> <ip_addr2> <ip_addr3> <ip_addr4>
    ip dhcp-relay <ip_addr1> vrf-name <vrf_name>

- Example:
  ```
    sonic# configure terminal
    sonic(config)# interface Vlan100
    sonic(conf-if-Vlan100)# ip dhcp-relay 11.0.0.1
    sonic(conf-if-Vlan100)# no ip dhcp-relay 11.0.0.1
    sonic(conf-if-Vlan100)# ip dhcp-relay 11.0.0.1 12.0.0.1 13.0.0.1 14.0.0.1

    sonic# configure terminal
    sonic(config)# interface Vlan100
    sonic(conf-if-Vlan100)# ipv6 dhcp-relay 1111::1
    sonic(conf-if-Vlan100)# no ipv6 dhcp-relay 1111::1
    sonic(conf-if-Vlan100)# ipv6 dhcp-relay 1111::1 2222::2 3333::3 4444::4

    sonic# configure terminal
    sonic(config)# interface Ethernet27
    sonic(conf-if-Vlan100)# ip dhcp-relay 11.0.0.1 vrf-name Vrf9

  ```
  

**[ip|ipv6] dhcp-relay source-interface <source_interface>**

This command adds or removes source interface selection on the given interface. Only one interface can be specified as the source interface. To update the existing source interface, a new interface needs to be added. If the link-selection option is enabled, the source interface cannot be removed. The link-selection must be disabled before removing the source interface configuration.

- Usage:
    ip dhcp-relay source-interface <source_interface>
    no ip dhcp-relay source-interface
    ipv6 dhcp-relay source-interface <source_interface>
    no ipv6 dhcp-relay source-interface

- Examples:
  ```
    # Set source interface to Loopback1 on VLAN100
    sonic(conf-if-Vlan100)# ip dhcp-relay source-interface Loopback1
    sonic(conf-if-Vlan100)# ipv6 dhcp-relay source-interface Loopback1

    # Disable source interface selection on VLAN100
    sonic(conf-if-Vlan100)# no ip dhcp-relay source-interface
    sonic(conf-if-Vlan100)# no ipv6 dhcp-relay source-interface
  ```

**[ip|ipv6] dhcp-relay max-hop-count <max_hop_count>**

This command sets the maximum hop count value on the given interface. Use 'add' option to configure a non-default value. Use 'remove' option to reset the hop count to default value. The 'add' option can also be used to update currently configured value. The range for hop count is 1 to 16. The default value is 10.

- Usage:
    ip dhcp-relay max-hop-count <max_hop_count>
    no ip dhcp-relay max-hop-count
    ipv6 dhcp-relay max-hop-count <max_hop_count>
    no ipv6 dhcp-relay max-hop-count

- Examples:
  ```
    #Set the max hop count to 15 on VLAN100
    sonic(conf-if-Vlan100)# ip dhcp-relay max-hop-count 15
    sonic(conf-if-Vlan100)# ipv6 dhcp-relay max-hop-count 15

    # Reset the max hop count value to default on VLAN100
    sonic(conf-if-Vlan100)# no ip dhcp-relay max-hop-count
    sonic(conf-if-Vlan100)# no ipv6 dhcp-relay max-hop-count
  ```

**ip dhcp-relay link-select**

Thiscommand adds or removes link-selection sub option on the given IPv4 DHCP Relay interface. The link-selection sub option is disabled by default. The source interface must be configured before enabling link-selection sub option. If the source interface is not configured, the link-select command fails with error message.

- Usage:
    ip dhcp-relay link-select
    no ip dhcp-relay link-select

- Examples:
  ```
    #Enable link selection suboption on VLAN100
    sonic(conf-if-Vlan100)# ip dhcp-relay link-select

    #Disable link selection suboption on VLAN100
    sonic(conf-if-Vlan100)# no ip dhcp-relay link-select
  ```
** [ip|ipv6] dhcp-relay vrf-select **
This command enables or disables VRF selection sub option 151/152 on the given IPv4 DHCP Relay interface. The vrf-select sub option is disabled by default. If the interface belongs to default/global VRF, the sub option is not added to the relayed packet.

- Usage:
    ip dhcp-relay vrf-select
    ipv6 dhcp-relay vrf-select

- Examples:
  ```
    #Enable VRF selection suboption on VLAN100
    sonic(conf-if-Vlan100)# ip dhcp-relay vrf-select
    sonic(conf-if-Vlan100)# ipv6 dhcp-relay vrf-select

    #Disable VRF selection suboption on VLAN100
    sonic(conf-if-Vlan100)# no ip dhcp-relay vrf-select
    sonic(conf-if-Vlan100)# no ipv6 dhcp-relay vrf-select

  ```
**ip dhcp-relay policy-action [discard|append|replace]**

This command controls handling of relay agent options on the given interface. The default is to discard incoming packet with agent options. This command is applicable for DHCPv4 packets only.

- Usage:
    ip dhcp-relay policy-action [discard|append|replace]

- Examples:
  ```
    #Set DHCP-Relay policy-action on VLAN100
    sonic(conf-if-Vlan100)# ip dhcp-relay policy-action append

    #Set default DHCP-Relay policy-action on VLAN100
    sonic(conf-if-Vlan100)# no ip dhcp-relay policy-action
  ```

## DHCP Relay KLISH Show Commands

**show [ip|ipv6] dhcp-relay brief**

This command displays all the configured DHCP Server address configurations.

- Usage:
    show ip dhcp-relay brief
    show ipv6 dhcp-relay brief

- Sample Output:
  ```
    sonic# show ip dhcp-relay brief
    ------------------------------------------------
    Interface Name    DHCP Helper Address
    ------------------------------------------------
    Ethernet52        1.2.0.1
    Vlan100           1.2.0.1
                      2.4.0.1
    Vlan200           1.2.0.1

    sonic# show ipv6 dhcp-relay brief
    ------------------------------------------------
    Interface Name    DHCP Helper Address
    ------------------------------------------------
    Ethernet52        1122::1
    Vlan100           1122::1
  ```

**show [ip|ipv6] dhcp-relay detailed <interface_name>**

This command displays the detailed relay configuration on the given interface. If the interface name is not specified, the command displays relay configuration for all the interfaces enabled for DHCP relay. If the source interface is not configured, the command output indicates it as 'Not Configured'.

- Usage:
    show ip dhcp-relay detailed
    show ip dhcp-relay detailed <interface_name>
    show ipv6 dhcp-relay detailed
    show ipv6 dhcp-relay detailed <interface_name>

- Sample output:
  ```
    sonic# show ip dhcp-relay detailed Vlan100
    Server Address: 114.0.0.2
    Source Interface: Vlan100
    Link Select: enable
    Max Hop Count: 12
    VRF Select: disable
    Policy Action: replace

    sonic# show ipv6 dhcp-relay detailed Vlan100
    Server Address: 1122::1
    Source Interface: Not Configured
    Max Hop Count: 10
    VRF Select: disable
  ```

**show [ip|ipv6] dhcp-relay statistics <interface_name>**

This command displays the DHCP Relay statistics.

- Usage:
    show ip dhcp-relay statistics <interface_name>
    show ipv6 dhcp-relay statistics <interface_name>

- Sample Output:
  ```
    sonic# show ip dhcp-relay statistics Vlan100
    BOOTREQUEST messages received by the relay agent        : 4
    BOOTREQUEST messages forwarded by the relay agent       : 2
    BOOTREPLY messages forwarded by the relay agent         : 0
    DHCP DISCOVER messages received by the relay agent      : 1
    DHCP OFFER messages sent by the relay agent             : 0
    DHCP REQUEST messages received by the relay agent       : 1
    DHCP ACK messages sent by the relay agent               : 0
    DHCP RELEASE messages received by the relay agent       : 0
    DHCP DECLINE messages received by the relay agent       : 0
    DHCP INFORM messages received by the relay agent        : 0
    DHCP NACK messages sent by the relay agent              : 0
    Total number of DHCP packets dropped by the relay agent : 2
    Number of DHCP packets dropped due to an invalid opcode : 0
    Number of DHCP packets dropped due to an invalid option : 0

    sonic# show ipv6 dhcp-relay statistics Vlan100
    DHCPv6 SOLICIT messages received by the relay agent       : 1
    DHCPv6 ADVERTISEMENT messages sent by the relay agent     : 1
    DHCPv6 REQUEST messages received by the relay agent       : 1
    DHCPv6 REPLY messages sent by the relay agent             : 1
    DHCPv6 CONFIRM messages received by the relay agent       : 0
    DHCPv6 RELEASE messages received by the relay agent       : 0
    DHCPv6 DECLINE messages received by the relay agent       : 0
    DHCPv6 REBIND messages received by the relay agent        : 0
    DHCPv6 RECONFIGURE messages sent by the relay agent       : 0
    DHCPv6 INFO-REQUEST messages received by the relay agent  : 0
    DHCPv6 RELAY-REPLY messages received by the relay agent   : 2
    DHCPv6 RELAY-FORWARD messages sent by the relay agent     : 2
    Total number of DHCPv6 packets dropped by the relay agent : 0
    Number of DHCPv6 packets dropped due to an invalid opcode : 0
    Number of DHCPv6 packets dropped due to an invalid option : 0
  ```

## DHCP Relay KLISH Clear Commands

**clear [ip|ipv6] dhcp-relay statistics <interface_name>**

This command clears the DHCP Relay statistics on a relay interface.

- Usage:
    clear ip dhcp-relay statistics <interface_name>
    clear ipv6 dhcp-relay statistics <interface_name>

- Example:
    ```
      sonic# clear ip dhcp-relay statistics Vlan100
      sonic# clear ipv6 dhcp-relay statistics Vlan100
    ```

Go Back To [Beginning of the document](#command-line-interface-guide) or [Beginning of this section](#dhcp-relay-commands)

# ECN Configuration And Show Commands

This section explains all the Explicit Congestion Notification (ECN) show commands and ECN configuation options that are supported in SONiC.

## ECN show commands
This sub-section contains the show commands that are supported in ECN.

**show ecn**  

This command displays all the WRED profiles that are configured in the device.

  - Usage:
    show ecn

- Example:
  ```
    show ecn
    Profile: **AZURE_LOSSLESS**
    -----------------------  -------
    red_max_threshold        2097152
    red_drop_probability     5
    yellow_max_threshold     2097152
    ecn                      ecn_all
    green_min_threshold      1048576
    red_min_threshold        1048576
    wred_yellow_enable       true
    yellow_min_threshold     1048576
    green_max_threshold      2097152
    green_drop_probability   5
    wred_green_enable        true
    yellow_drop_probability  5
    wred_red_enable          true
    -----------------------  -------
    
    Profile: **wredprofileabcd**
    -----------------  ---
    red_max_threshold  100
    -----------------  ---
  ```

## ECN config commands

This sub-section contains the configuration commands that can configure the WRED profiles.

**config ecn**  

This command configures the possible fields in a particular WRED profile that is specified using "-profile <profilename>" argument. 
The list of the WRED profile fields that are configurable is listed in the below "Usage".

  - Usage:   
    config ecn [OPTIONS]

  ```
    ECN Config OPTIONS:
      -profile <profile_name>       Profile name  [required] - Even though the profile_name is specified as optional parameter, it is a mandatory parameter.
      -rmax <red threshold max>     Set red max threshold
      -rmin <red threshold min>     Set red min threshold
      -ymax <yellow threshold max>  Set yellow max threshold
      -ymin <yellow threshold min>  Set yellow min threshold
      -gmax <green threshold max>   Set green max threshold
      -gmin <green threshold min>   Set green min threshold
      -v, --verbose                 Enable verbose output
      --help                        Show this message and exit.
  ```


- Example:
  ```
  root@T1-2:~# config ecn -profile wredprofileabcd -rmax 100
  This command configures the "red max threshold" for the WRED profile name "wredprofileabcd". It will create the WRED profile if it does not exist.
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#ECN-Configuration-And-Show-Commands)

# Interface Configuration And Show-Commands

## Interface Show Commands

This sub-section lists all the possible show commands for the interfaces available in the device. Following example gives the list of possible shows on interfaces.
Subsequent pages explain each of these commands in detail.

- Example:
  ```
  user@debug:~$ show interfaces -?

  Show details of the network interfaces

  Options:
    -?, -h, --help  Show this message and exit.

  Commands:
  counters     Show interface counters
  description  Show interface status, protocol and...
  naming_mode  Show interface naming_mode status
  neighbor     Show neighbor related information
  pktdrops     Show interface packet drops
  portchannel  Show PortChannel information
  status       Show Interface status information
  transceiver  Show SFP Transceiver information
  ```

**show interfaces counters**  

This show command displays packet counters for all interfaces since the last time the counters were cleared.  
Optional argument "-a" will display two additional counters - RX_PPS and TX_PPS.  
Optional argument "-c" can be used to clear the counters for all interfaces. 
Optional argument "-i" can be used to apply this command per interface. 
Optional argument "-p" specify a period (in seconds) with which to gather counters over. 

  - Usage:  
    show interfaces counters [OPTIONS]  
      OPTIONS:  
      -a, --printall  
      -c, --clear  
      -i, --interface TEXT  
      -p, --period TEXT


- Example:
  ```
  admin@sonic:~$ show interfaces counters
        IFACE    STATE            RX_OK       RX_BPS    RX_UTIL    RX_ERR    RX_DRP    RX_OVR            TX_OK       TX_BPS    TX_UTIL    TX_ERR    TX_DRP    TX_OVR
  -----------  -------  ---------------  -----------  ---------  --------  --------  --------  ---------------  -----------  ---------  --------  --------  --------
    Ethernet0        U  471,729,839,997  653.87 MB/s     12.77%         0    18,682         0  409,682,385,925  556.84 MB/s     10.88%         0         0         0
    Ethernet4        U  453,838,006,636  632.97 MB/s     12.36%         0     1,636         0  388,299,875,056  529.34 MB/s     10.34%         0         0         0
    Ethernet8        U  549,034,764,539  761.15 MB/s     14.87%         0    18,274         0  457,603,227,659  615.20 MB/s     12.02%         0         0         0
   Ethernet12        U  458,052,204,029  636.84 MB/s     12.44%         0    17,614         0  388,341,776,615  527.37 MB/s     10.30%         0         0         0
   Ethernet16        U   16,679,692,972   13.83 MB/s      0.27%         0    17,605         0   18,206,586,265   17.51 MB/s      0.34%         0         0         0
   Ethernet20        U   47,983,339,172   35.89 MB/s      0.70%         0     2,174         0   58,986,354,359   51.83 MB/s      1.01%         0         0         0
   Ethernet24        U   33,543,533,441   36.59 MB/s      0.71%         0     1,613         0   43,066,076,370   49.92 MB/s      0.97%         0         0         0
  ```
  
  - Optionally, you can specify a period (in seconds) with which to gather counters over. Note that this function will take `<period>` seconds to execute.

- Example:
  ```
  admin@sonic:~$ show interfaces counters -p 5
        IFACE    STATE    RX_OK       RX_BPS    RX_UTIL    RX_ERR    RX_DRP    RX_OVR    TX_OK       TX_BPS    TX_UTIL    TX_ERR    TX_DRP    TX_OVR
  -----------  -------  -------  -----------  ---------  --------  --------  --------  -------  -----------  ---------  --------  --------  --------
  Ethernet0         U      515   59.14 KB/s      0.00%         0         0         0    1,305  127.60 KB/s      0.00%         0         0         0
  Ethernet4         U      305   26.54 KB/s      0.00%         0         0         0      279   39.12 KB/s      0.00%         0         0         0
  Ethernet8         U      437   42.96 KB/s      0.00%         0         0         0      182   18.37 KB/s      0.00%         0         0         0
  Ethernet12        U      284   40.79 KB/s      0.00%         0         0         0      160   13.03 KB/s      0.00%         0         0         0
  Ethernet16        U      377   32.64 KB/s      0.00%         0         0         0      214   18.01 KB/s      0.00%         0         0         0
  Ethernet20        U      284   36.81 KB/s      0.00%         0         0         0      138  8758.25 B/s      0.00%         0         0         0
  Ethernet24        U      173   16.09 KB/s      0.00%         0         0         0      169   11.39 KB/s      0.00%         0         0         0
  ```

  - The option to display the detailed counters of an interface is also available. 
- Example:
  ```
  admin@sonic:~$ show interfaces counters detailed Ethernet48
  Last rate cached time was 2019-09-18 21:31:58.094196
  Packets Received 64 Octets..................... 4
  Packets Received 65-127 Octets................. 0
  Packets Received 128-255 Octets................ 842
  Packets Received 256-511 Octets................ 6
  Packets Received 512-1023 Octets............... 0
  Packets Received 1024-1518 Octets.............. 0
  Packets Received 1519-2047 Octets.............. 0
  Packets Received 2048-4095 Octets.............. 0
  Packets Received 4096-9216 Octets.............. 0
  Packets Received 9217-16383 Octets............. 0

  Total Packets Received Without Errors.......... 852
  Unicast Packets Received....................... 0
  Multicast Packets Received..................... 846
  Broadcast Packets Received..................... 6

  Jabbers Received............................... 0
  Fragments Received............................. 0
  Undersize Received............................. 0
  Overruns Received.............................. 0

  Packets Transmitted 64 Octets.................. 1
  Packets Transmitted 65-127 Octets.............. 0
  Packets Transmitted 128-255 Octets............. 835
  Packets Transmitted 256-511 Octets............. 0
  Packets Transmitted 512-1023 Octets............ 0
  Packets Transmitted 1024-1518 Octets........... 0
  Packets Transmitted 1519-2047 Octets........... 0
  Packets Transmitted 2048-4095 Octets........... 0
  Packets Transmitted 4096-9216 Octets........... 0
  Packets Transmitted 9217-16383 Octets.......... 0

  Total Packets Transmitted Successfully......... 836
  Unicast Packets Transmitted.................... 0
  Multicast Packets Transmitted.................. 836
  Broadcast Packets Transmitted.................. 0
  Time Since Counters Last Cleared............... None
  admin@sonic:~$
  ```

**show interfaces counters in Klish**  

  This show command lists packet counters for all interfaces.  
- Usage:  
    show interface counters [INTERFACENAME]
- Example:
  ```
  sonic# show interface counters
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Interface      State     RX_OK                 RX_ERR                RX_DRP                RX_OVERSIZE           TX_OK                 TX_ERR                TX_DRP                TX_OVERSIZE
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Ethernet0      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet1      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet2      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet3      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet4      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet5      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet6      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet7      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet8      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet9      D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet10     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet11     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet12     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet13     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet14     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet15     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet16     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet17     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet18     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet19     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet20     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet21     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet22     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet23     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet24     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet25     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet26     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet27     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet28     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet29     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet30     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet31     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet32     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet33     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet34     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet35     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet36     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet37     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet38     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet39     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet40     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet41     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet42     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet43     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet44     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet45     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet46     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet47     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet48     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet52     U         8395607123            0                     0                     0                     30                    0                     0                     0
  Ethernet56     U         0                     0                     0                     0                     3821090159722         0                     0                     0
  Ethernet60     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet64     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet68     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet72     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet76     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet80     D         0                     0                     0                     0                     0                     0                     0                     0
  Ethernet81     D         0                     0                     0                     0                     0                     0                     0                     0
  sonic#
  ```
  
  There an option to display the detailed counters of an interface for different packet size distribution. 
  - Usage:  
    show interface counters [INTERFACENAME]
  - Example:
  ```
  sonic# show interface counters Ethernet 52
  Packets Received 64 Octets              0
  Packets Received 65-127 Octets          0
  Packets Received 128-255 Octets         0
  Packets Received 256-511 Octets         0
  Packets Received 512-1023 Octets        201378378
  Packets Received 1024-1518 Octets       0
  Packets Received 1519-2047 Octets       0
  Packets Received 2048-4095 Octets       0
  Packets Received 4096-9216 Octets       0
  Packets Received 9217-16383 Octets      0
  Total Packets Received Without Errors   201378378
  Unicast Packets Received                201378561
  Multicast Packets Received              0
  Broadcast Packets Received              0
  Jabbers Received                        0
  Fragments Received                      0
  Undersize Received                      0
  Overruns Received                       0
  Packets Transmitted 64 Octets           0
  Packets Transmitted 65-127 Octets       0
  Packets Transmitted 128-255 Octets      0
  Packets Transmitted 256-511 Octets      0
  Packets Transmitted 512-1023 Octets     0
  Packets Transmitted 1024-1518 Octets    0
  Packets Transmitted 1519-2047 Octets    0
  Packets Transmitted 2048-4095 Octets    0
  Packets Transmitted 4096-9216 Octets    0
  Packets Transmitted 9217-16383 Octets   0
  Total Packets Transmitted Successfully  0
  Unicast Packets Transmitted             0
  Multicast Packets Transmitted           0
  Broadcast Packets Transmitted           0
  Time Since Counters Last Cleared        2020-09-14 07:06:47
  sonic#
  ```

**show interfaces description**  

This command displays the key fields of the interfaces such as Operational Status, Administrative Status, Alias and Description.

  - Usage:  
    show interfaces description [INTERFACENAME]
  
- Example:
  ```
  admin@sonic:~$ show interfaces description 
  Interface    Oper    Admin            Alias           Description
  -----------  ------  -------  ---------------  --------------------
  Ethernet0    down       up   hundredGigE1/1  T0-1:hundredGigE1/30
  Ethernet4    down       up   hundredGigE1/2  T0-2:hundredGigE1/30
  Ethernet8    down     down   hundredGigE1/3        hundredGigE1/3
  Ethernet12   down     down   hundredGigE1/4        hundredGigE1/4
  ```

  ```
  show the description for one particular interface.  

  admin@sonic:~$ show interfaces description Ethernet4
  Interface    Oper    Admin           Alias           Description
  -----------  ------  -------  --------------  --------------------
  Ethernet4    down       up  hundredGigE1/2  T0-2:hundredGigE1/30

  ```


**show interfaces naming_mode**  

Refer sub-section [Interface-Naming-Mode](#Interface-Naming-Mode)


**show interfaces neighbor**  

This command is used to display the list of expected neighbors for all interfaces (or for a particular interface) that is configured.

  - Usage:  
    show interfaces neighbor expected [INTERFACENAME]

- Example:
  ```
  root@sonic-z9264f-9251:~# show interfaces neighbor expected
	LocalPort    Neighbor    NeighborPort    NeighborLoopback    NeighborMgmt    NeighborType
	-----------  ----------  --------------  ------------------  --------------  --------------
	Ethernet112  ARISTA01T1  Ethernet1       None                10.16.205.100   ToRRouter
	Ethernet116  ARISTA02T1  Ethernet1       None                10.16.205.101   SpineRouter
	Ethernet120  ARISTA03T1  Ethernet1       None                10.16.205.102   LeafRouter
	Ethernet124  ARISTA04T1  Ethernet1       None                10.16.205.103   LeafRouter

  ```

**show interfaces pktdrops**  

This command displays detailed packet drop counters for all interfaces. The optional "nonzero" argument displays only nonzero counters.

  - Usage:  
    show interfaces pktdrops [nonzero]

- Example:
  ```
  admin@sonic:~$ show interfaces pktdrops

    IFACE      COUNTER                    COUNT    CHANGE
    ---------  -----------------------  -------  --------
    Ethernet0  RIPD4                          0        +0
    Ethernet0  RIPHE4                         0        +0
    Ethernet0  RIPD6                          0        +0
    Ethernet0  RIPHE6                         0        +0
    Ethernet0  RPORTD                         0        +0
    Ethernet0  RPARITYD                       0        +0
    Ethernet0  ING_NIV_RX_FRAMES_ER           0        +0
    Ethernet0  ING_NIV_RX_FRAMES_FO           0        +0
    Ethernet0  ING_ECN_COUNTER_64             0        +0
    Ethernet0  EGR_ECN_COUNTER_64             0        +0
    Ethernet0  TPCE_64                        0        +0
    Ethernet0  RFCS                           0        +0
    Ethernet0  RXUO                           0        +0
    Ethernet0  RXUDA                          0        +0
    Ethernet0  RXWSA                          0        +0
    Ethernet0  RALN                           0        +0
    Ethernet0  RFLR                           0        +0
    Ethernet0  RERPKT                         0        +0
    Ethernet0  RFCR                           0        +0
    Ethernet0  ROVR                           0        +0
    Ethernet0  RJBR                           0        +0
    Ethernet0  RMTUE                          0        +0
    Ethernet0  RTRFU                          0        +0
    Ethernet0  CLMIB_RSCHCRC                  0        +0
    Ethernet0  RUND                           0        +0
    Ethernet0  RFRG                           0        +0
    Ethernet0  RRPKT                          0        +0
    Ethernet0  TJBR                           0        +0
    Ethernet0  TFCS                           0        +0
    Ethernet0  TEDF                           0        +0
    Ethernet0  TSCL                           0        +0
    Ethernet0  TMCL                           0        +0
    Ethernet0  TLCL                           0        +0
    Ethernet0  TXCL                           0        +0
    Ethernet0  TFRG                           0        +0
    Ethernet0  TERR                           0        +0
    Ethernet0  TRPKT                          0        +0
    Ethernet0  TUFL                           0        +0
    Ethernet0  CLMIB_XTHOL                    0        +0
    Ethernet0  PERQ_DROP_PKT(0)               0        +0
    Ethernet0  PERQ_DROP_PKT(1)               0        +0
    Ethernet0  PERQ_DROP_PKT(2)               0        +0
    Ethernet0  PERQ_DROP_PKT(3)               0        +0
    Ethernet0  PERQ_DROP_PKT(4)               0        +0
    Ethernet0  PERQ_DROP_PKT(5)               0        +0
    Ethernet0  PERQ_DROP_PKT(6)               0        +0
    Ethernet0  PERQ_DROP_PKT(7)               0        +0
    Ethernet0  PERQ_DROP_PKT(8)               0        +0
    Ethernet0  PERQ_DROP_PKT(9)               0        +0
    Ethernet0  DROP_PKT_ING                   0        +0
    Ethernet0  DROP_PKT_YEL                   0        +0
    Ethernet0  DROP_PKT_RED                   0        +0
    Ethernet0  OBM_LOSSY_LO_DRP_PKT(0)        0        +0
    Ethernet0  OBM_LOSSY_HI_DRP_PKT(0)        0        +0
    Ethernet0  OBM_LOSSLESS0_DRP_PK(0)        0        +0
    Ethernet0  OBM_LOSSLESS1_DRP_PK(0)        0        +0
  ```

The command also displays a brief description of each drop counter before displaying the packet drop counters.

- Example:
  ```
  admin@sonic:~$ show interfaces pktdrops

    COUNTER                  DESCRIPTION
    -----------------------  --------------------------------------
    RIPD4                    Rx IPv4 L3 discards
    RIPHE4                   Rx IPv4 L3 IP Header Errors
    RIPD6                    Rx IPv6 L3 discards
    RIPHE6                   Rx IPv6 L3 IP Header Errors
    RDISC                    Rx discards
    RPORTD                   Rx PortInDiscards
    RPARITYD                 Rx Parity errors
    IUNHGI_64                Rx unknown HGI pkts
    ING_NIV_RX_FRAMES_ER     VNTAG/ETAG format errors
    ING_NIV_RX_FRAMES_FO     NIV/PE forwarding errors
    ING_ECN_COUNTER_64       Ingress ECN packets
    EGR_ECN_COUNTER_64       ECN errors
    TPCE_64                  Egress purge and cell Error drops
    RFCS                     Rx FCS error frames
    RXUO                     Rx unsupported Opcode frames
    RXUDA                    Rx unsupported DA for PAUSE/PFC
    RXWSA                    Rx wrong SA frames
    RALN                     Rx Alignment Errors
    RFLR                     Rx length out of range
    RERPKT                   Rx code errors
    RFCR                     Rx False carrier
    ROVR                     Rx oversized
    RJBR                     Rx Jabber frames
    RMTUE                    Rx MTU check error frames
    RTRFU                    Rx trauncated frames
    CLMIB_RSCHCRC            Rx SCH CRC Error
    RUND                     Rx undersize
    RFRG                     Rx framents
    RRPKT                    Rx RUNT frames
    TJBR                     Tx Jabbers
    TFCS                     Tx FCS errors
    TEDF                     Tx Multiple Deferral frames
    TSCL                     Tx single collision frames
    TMCL                     Tx Multiple collision frames
    TLCL                     Tx Late collision frames
    TXCL                     Tx Excessive collision frames
    TFRG                     Tx Fragments
    TERR                     Tx Errors
    TRPKT                    Tx RUNT frames
    TUFL                     Tx FIFO underrun
    CLMIB_XTHOL              Tx End-to-End HOL packets
    PERQ_DROP_PKT(0)         Packet drops on queue #0
    PERQ_DROP_PKT(1)         Packet drops on queue #1
    PERQ_DROP_PKT(2)         Packet drops on queue #2
    PERQ_DROP_PKT(3)         Packet drops on queue #3
    PERQ_DROP_PKT(4)         Packet drops on queue #4
    PERQ_DROP_PKT(5)         Packet drops on queue #5
    PERQ_DROP_PKT(6)         Packet drops on queue #6
    PERQ_DROP_PKT(7)         Packet drops on queue #7
    PERQ_DROP_PKT(8)         Packet drops on queue #8
    PERQ_DROP_PKT(9)         Packet drops on queue #9
    PERQ_WRED_DROP_PKT_U(0)  WRED Drops on queue #0
    PERQ_WRED_DROP_PKT_U(1)  WRED Drops on queue #1
    PERQ_WRED_DROP_PKT_U(2)  WRED Drops on queue #2
    PERQ_WRED_DROP_PKT_U(3)  WRED Drops on queue #3
    PERQ_WRED_DROP_PKT_U(4)  WRED Drops on queue #4
    PERQ_WRED_DROP_PKT_U(5)  WRED Drops on queue #5
    PERQ_WRED_DROP_PKT_U(6)  WRED Drops on queue #6
    PERQ_WRED_DROP_PKT_U(7)  WRED Drops on queue #7
    PERQ_WRED_DROP_PKT_U(8)  WRED Drops on queue #8
    PERQ_WRED_DROP_PKT_U(9)  WRED Drops on queue #9
    DROP_PKT_ING             MMU drops due to THDI(input threshold)
    DROP_PKT_YEL             MMU YELLOW drops
    DROP_PKT_RED             MMU RED drops
    OBM_LOSSY_LO_DRP_PKT(0)  OBM Lossy Low drops
    OBM_LOSSY_HI_DRP_PKT(0)  OBM Lossy high drops
    OBM_LOSSLESS0_DRP_PK(0)  OBM lossless0 drops
    OBM_LOSSLESS1_DRP_PK(0)  OBM lossless1 drops
  ```

**show interfaces portchannel**  

This command displays information regarding port-channel interfaces

  - Usage:  
    show interfaces portchannel

- Example:
  ```
  admin@sonic:~$ show interfaces portchannel
  Flags: A - active, I - inactive, Up - up, Dw - Down, N/A - not available, S - selected, D - deselected
    No.  Team Dev       Protocol     Ports
  -----  -------------  -----------  ---------------------------
     24  PortChannel24  LACP(A)(Up)  Ethernet28(S) Ethernet24(S)
     48  PortChannel48  LACP(A)(Up)  Ethernet52(S) Ethernet48(S)
     40  PortChannel40  LACP(A)(Up)  Ethernet44(S) Ethernet40(S)
      0  PortChannel0   LACP(A)(Up)  Ethernet0(S) Ethernet4(S)
      8  PortChannel8   LACP(A)(Up)  Ethernet8(S) Ethernet12(S)
  ```
  

**show interfaces portchannel <portchannel_name> fallback**

This command displays portchannel fallback information

  - Usage:
    show interfaces portchannel <portchannel_name> fallback

- Example:
  ```
  admin@sonic:~$ show interfaces portchannel PortChannel30 fallback
  Port Channel                :  PortChannel30
  Fallback Configured Value   :  Enabled
  Fallback Operational Status :  Enabled
  ```


**show interface status**  

This command displays some more fields such as Lanes, Speed, MTU, Type, Asymmetric PFC status and also the operational and administrative status of the interfaces 

- Usage:  
  show interfaces status [INTERFACENAME]

- Example:
  ```
  show interface status of all interfaces

  admin@sonic:~$ show interfaces status
  Interface            Lanes    Speed    MTU            Alias    Oper    Admin    Type    Asym PFC
  -----------  ---------------  -------  -----  ---------------  ------  -------  ------  ----------
  Ethernet0      49,50,51,52     100G   9100   hundredGigE1/1    down       up     N/A         off
  Ethernet4      53,54,55,56     100G   9100   hundredGigE1/2    down       up     N/A         off
  Ethernet8      57,58,59,60     100G   9100   hundredGigE1/3    down     down     N/A         off
  <contiues to display all the interfaces>

  ```
  
  ```
  show interface status for one particular interface

  admin@sonic:~$ show interface status Ethernet0
  Interface     Lanes    Speed    MTU            Alias    Oper    Admin
  -----------  --------  -------  -----   --------------  ------  -------
  Ethernet0   101,102      40G   9100   fortyGigE1/1/1      up       up

  ```

**show interfaces transceiver**  

This command is already explained [here](#Transceivers)

## Interface Config Commands
### Interface Config Commands in Click
This sub-section explains the following list of configuration on the interfaces.
1) ip - to add or remove IP address for the interface.
2) pfc - to set the PFC configuration for the interface.
3) shutdown - to administratively shut down the interface.
4) speed - to set the interface speed.
5) startup - to bring up the administratively shutdown interface.
6) description - to set a description for the interface.
7) fec - to set the forward-error-correction mode to rs, fc or none.
8) mtu - to set the MTU to a value in the range 1548-9216. 
9) vrf - to bind or unbind interface to a VRF.
10) vrrp - to apply VRRP configurations on interface.
11) ipv6 - to enable or disable using link-local address only on the interface.

From 201904 release onwards, the ?config interface? command syntax is changed and the format is as follows    

- config interface  interface_subcommand <interface_name>   
i.e Interface name comes after the subcommand  
- Example: config interface startup Ethernet63  

The syntax for all such interface_subcommands are given below under each command  

NOTE: In older versions of SONiC until 201811 release, the command syntax was  
      "config interface <interface_name> interface_subcommand"

**config interface ip add <interface-name> <ip_addr> (for 201904+ version)**  
**config interface <interface-name> ip add <ip_addr> (for 201811- version)**  

This command is used for adding the IP address for an interface.  
IP address for either physical interface or for portchannel or for VLAN interface can be configured using this command. 


- Usage:  
    config interface ip add <interface-name> <ip_addr> (for 201904+ version)
    config interface <interface-name> ip add <ip_addr> (for 201811- version)  

- Example:  
  ```
  admin@sonic:~$ sudo config interface ip add Ethernet63 10.11.12.13/24 
  ```
NOTE: In SONiC versions until 201811, syntax was "config <interface_name> ip add <ip_addr>"
  
  

**IP Address Configuration for Vlan Interface**  

- Usage:  
    config interface ip add <ip_addr> <vlan_IDName>

- Example:  
  ```
  admin@sonic:~$ sudo config interface ip add vlan100 10.11.12.13/24 
  ```
NOTE: In versions until 201811, syntax was "config interface <vlan_IDName> ip add <ip_addr>"

  
  

**config interface ip remove <interface_name> <ip_addr> (for 201904+ version)**  
**config interface <interface_name> ip remove <ip_addr> (for 201811- version)**  

- Usage:  
    config interface ip remove <interface_name> <ip_addr> (for 201904+ version)
    config interface ip remove <interface_name> <ip_addr> (for 201811- version)

- Example:
  ```
  admin@sonic:~$ sudo config interface ip remove Ethernet63 10.11.12.13/24 
  ```
NOTE: In versions until 201811, syntax is "config  interface <interface_name> ip remove <ip_addr>"
  
  

**IP Address Removal for Vlan Interface**  

- Usage:  
    config interface ip remove <vlan_IDName> <ip_addr> 

- Example:
  ```
  admin@sonic:~$ sudo config interface ip remove vlan100 10.11.12.13/24 
  ```
NOTE: In versions until 201811, syntax is "config interface <vlan_ID> ip remove <ip_addr>"




**Loopback interface creation**  

Introduction of VRF feature in SONIC allows user to have per VRF loopback interface. With this, new commands have been introduced to create and delete Loopback interfaces. Loopback interface must start with "Loopback" with "L" in upper case. Once Loopback interface is created, user can bind it to a VRF, can assign IP address etc. like a regular interface.

- Usage:  
  config loopback add Loopback<0-999> 

- Example:

  ```
  admin@sonic:~$ sudo config loopback add Loopback7 
  ```

**Loopback interface deletion**  

This command allows user to delete an already existing loopback interface

- Usage:  
  config loopback del Loopback<0-999> 

- Example:

  ```
  admin@sonic:~$ sudo config loopback del Loopback7 
  ```

  **config interface pfc asymmetric <interface_name> (for 201904+ version)**  
  **config interface <interface_name> pfc asymmetric (for 201811- version)**  
  This command is used for setting the asymmetric PFC for an interface to either "on" or "off". Once if it is configured, use "show interfaces status" to check the same.

- Usage:  
    config interface pfc asymmetric <interface_name> on/off (for 201904+ version)
    config interface <interface_name> pfc asymmetric on/off (for 201811- version)

- Example:  
  ```
  admin@sonic:~$ sudo config interface pfc asymmetric Ethernet60 on 
  ```

**config interface shutdown <interface_name> (for 201904+ version)**  
**config interface <interface_name> shutdown (for 201811- version)**  

This command is used to administratively shut down either the Physical interface or port channel interface. Once if it is configured, use "show interfaces status" to check the same.

- Usage:   
    config interface shutdown <interface_name> (for 201904+ version)  
    config interface <interface_name> shutdown (for 201811- version)

- Example:
  ```
  admin@sonic:~$ sudo config interface shutdown Ethernet63
  ```
NOTE: In versions until 201811, syntax is "config interface <interface_name> shutdown"



**config interface startup <interface_name> (for 201904+ version)**  
**config interface <interface_name> startup (for 201811- version)**  

This command is used for administratively bringing up the physical interface or port channel interface.  
The  bring-up of multiple physical interfaces can be done  by providing  range of interfac names, as shown in the example.
The command, "show interfaces status" can be used to check the status after issuing startup command.

- Usage:   
    config interface startup <interface_name(s)> (for 201904+ version)  
    config interface <interface_name> startup (for 201811- version)

- Example:
  ```
  admin@sonic:~$ sudo config interface startup Ethernet63
  admin@sonic:~$ sudo config interface startup Ethernet0-62
  ```
NOTE: In versions until 201811, syntax is "config interface <interface_name> startup"



**config interface speed <interface_name> (for 201904+ version)**  
**config interface <interface_name> speed (for 201811- version)**  

This command is used to configure the speed for the Physical interface. Use the value 40000 for setting it to 40G and 100000 for 100G. Users need to know the device to configure it properly. 

- Usage:  
    config interface speed <interface_name> <speed_value>  (for 201904+ version)  
    config interface <interface_name> speed <speed_value>  (for 201811- version)

- Example:
  ```
  admin@sonic:~$ sudo config interface speed Ethernet63 40000
  ```

NOTE: In versions until 201811, syntax is "config interface <interface_name> speed <4000>"

**config interface description <interface_name> <interface_description>**  

This command is used to configure a user defined description to an interface. The description can be cleared by giving value for description as "None".  
The command, "show interface description", can be used to view the descriptions of interfaces. 

- Usage:  
    config interface description <interface_name> <interface_description>  

- Example:
  ```
  admin@sonic:~$ sudo config interface description Ethernet60 South-bound  
  admin@sonic:~$ sudo config interface description Ethernet64 "North-bound interface to Spine1"  
  show interfaces description
  show interfaces description Ethernet64
  admin@sonic:~$ show interfaces description Ethernet64
    Interface    Oper    Admin          Alias                      Description
  -----------  ------  -------  -------------  -------------------------------
   Ethernet64    down       up  hundredGigE53  North-bound interface to Spine1
  admin@sonic:~$ sudo config interface description Ethernet60 None
  ```

NOTE: This command is not supported in  201904 or prior versions.

**config interface mtu <interface_name> <1548-9216>**  

This command is used to the maximum transmission unit(MTU) for the physical interface.  
The permitted range of MTU value is 1548B to 9216B.

- Usage:  
    config interface mtu <interface_name> <1548-9216>  

- Example:
  ```
  admin@sonic:~$ sudo config interface mtu Ethernet48 9200
  ```

**config interface fec <interface_name> <fec_mode>**  

This command is used to configure the forward error correction mode for the physical interface.  
The permitted modes are rs(RS-FEC) , fc(FC-FEC)  and none. The default mode is none.  
The FEC mode can be changed for multiple interfaces using a single command by providing range of interface names as shown in the example.

- Usage:  
    config interface fec <interface_name(s)> <fec_mode>  

- Example:
  ```
  admin@sonic:~$ sudo config interface fec Ethernet63 rs
  admin@sonic:~$ sudo config interface fec Ethernet0-76 none
  ```

NOTE: This command is not supported in  201904 or prior versions.  

### Interface Config Commands in Klish
The following list of configurations are supported on the interfaces using Klish framework.  
1) speed - to set the interface speed. 

**speed command**  
This command configures port speed. "no" option can be used to revert the speed to default.
- Usage:
    speed <speed in Mbps>

- Example:
  ```
  sonic(config)# interface Ethernet 60
  sonic(conf-if-Ethernet60)#
  sonic(conf-if-Ethernet60)# speed 40000
  sonic(conf-if-Ethernet60)# no speed
  sonic(conf-if-Ethernet60)#
  ```
Some platforms supports port-groups. port-group speed command has to be used for the member ports of port groups except when multiple valid speeds for member port are supported for a port group speed. For example, when port-group speed is 10G, valid speeds for member port can be 10G and 1G. In this case, speed command on member port to set the speed to 1G will be accepted when the port-group speed is 10G. And "no speed" will revert the speed to port group speed, 10G in this scenario.

- Example:
  ```
  sonic(conf-if-Ethernet0)# speed 10000
  %Error: Port group member. Please use port group command to change the speed
  sonic(conf-if-Ethernet0)#
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#interface-configuration-and-show-commands)

# Associating/disassociating an Interface to/from a VRF

## Binding an interface to a VRF

This command allows user to bind an interface to a particular VRF. By default, all interfaces are part of default VRF. 

If an interface is already associated with a user VRF, user can execute this command to move the interface to a different VRF without disassociating the interface from the prior VRF. However, when an interface is moved from one VRF to another, SONIC will automatically delete all the IP/IPv6 address configured on that interface. In order to remove an interface from user VRF to default VRF, please use "vrf unbind" command. The interface and VRF must exist in order for this command to succeed. Interface can be Ethernet or Vlan or PortChannel or Loopback.

In order to successfully bind an interface to VRF, interface should not be a member of a PortChannel or a Vlan

**sudo config interface vrf bind <interface_name> <vrf_name>**  

- Usage:  
  config interface vrf bind <interface_name> <vrf_name>

- Example:

  ```
  admin@sonic:~$ sudo config interface vrf bind Ethernet12 Vrf-Green
  admin@sonic:~$ sudo config interface vrf bind PortChannel017 Vrf-Red
  admin@sonic:~$ sudo config interface vrf bind Vlan27 Vrf-Green
  admin@sonic:~$ sudo config interface vrf bind Loopback7 Vrf-Green
  ```

## Unbinding an interface from a VRF

This command allows user to disassociate an interface from a particular VRF and move the interface to default VRF.  While disassociating the interface, the IP/IPv6 addresses configured on that interface will automatically be deleted.

**sudo config interface vrf unbind <interface_name> <vrf_name>**  

- Usage:  
  config interface vrf unbind <interface_name> <vrf_name>

- Example:

  ```
  admin@sonic:~$ sudo config interface vrf unbind Ethernet12 Vrf-Green
  admin@sonic:~$ sudo config interface vrf unbind PortChannel017 Vrf-Red
  admin@sonic:~$ sudo config interface vrf unbind Vlan27 Vrf-Green
  admin@sonic:~$ sudo config interface vrf unbind Loopback7 Vrf-Green
  ```

## Portgroup configuration and show commands
Some platforms have limitation that certain ports have to be setup at same speed together. The ports for which the speed has to be set together are grouped into multiple port groups, for the platforms that have this limitation. The portgroup configuration command can be used to configure speed of ports in same portgroup, and interface configuration command will not able to use.

**portgroup show commands in Click**  
This command displays portgroup information of current platform. It displays each portgroup in current platform and ports belong to each portgroup and valid speeds for each portgroup.

- Usage:
    show portgroup

- Example:
  For platform does not have portgroup
  ```
  admin@sonic:~/logs$ show portgroup
  Platform does not support portgroup.
  ```

  For platform does have portgroup
  ```
  admin@sonic:~$ show portgroup
    portgroup          ports      valid speeds
  -----------  -------------  ----------------
            1   Ethernet0-11  25000,10000,1000
            2  Ethernet12-23  25000,10000,1000
            3  Ethernet24-35  25000,10000,1000
            4  Ethernet36-47  25000,10000,1000
  ```

**portgroup config commands in Click**  
This command configures portgroup speed.
- Usage:
    config portgroup speed <portgroup> <speed>

- Example:
  ```
  admin@sonic:~$ sudo config portgroup speed 1 10000
  Config portgroup 1 speed 10000
  ```

**portgroup show commands in Klick**  
This command displays portgroup information of the current platform. It will list portgroups along with member interface range, valid speeds and default speed of each portgroup.

- Usage:
    show port-group

- Example:
  ```
  sonic# show port-group
  -----------------------------------------------------------------------
  Port-group  Interface range            Valid speeds      Default Speed
  -----------------------------------------------------------------------
  1           Ethernet0 - Ethernet11     10G, 25G          25G
  2           Ethernet12 - Ethernet23    10G, 25G          25G
  3           Ethernet24 - Ethernet35    10G, 25G          25G
  4           Ethernet36 - Ethernet47    10G, 25G          25G
  sonic#
  ```
  An error will be thrown if the hardware platform does not support port-group.
  ```
  sonic# show port-group
  %Error: Port-group is not supported
  sonic#
  ```

**portgroup config commands in Klish**  
The following command configures port-group speed.
- Usage:
    port-group <port group id> speed <speed in Mbps>

- Example:
  ```
  sonic(config)# port-group 1 speed 10000
  sonic(config)#
  ```
  An error will be thrown if the hardware platform does not support port-group.
  ```
  sonic(config)# port-group 1 speed 10000
  %Error: Port-group is not supported
  sonic(config)#
  ```

## Interface naming mode config commands

**config interface naming mode**  
This command is used to change the interface naming mode. 

# Interface Naming Mode

## Interface naming mode show commands
This command displays the current interface naming mode. Interface naming mode originally set to 'default'. Interfaces are referenced by default SONiC interface names. 
Users can change the naming_mode using "config interface_naming_mode" command. 

**show interfce naming mode**  
This command displays the current interface naming mode

- Usage:
    show interfaces naming_mode 
    
- Example:
  ```
  admin@sonic:~$ show interfaces naming_mode 
  **default**
  - "default" is the name of the default naming_mode since users have not modified it in this example.

  Following example shows the modified interface_naming_mode
  admin@sonic:~$ show interfaces naming_mode 
  **alias**
  ```


## Interface naming mode config commands

**config interface naming mode**  
This command is used to change the interface naming mode. 
Users can select between default mode (SONiC interface names) or alias mode (Hardware vendor names). 
The user must log out and log back in for changes to take effect. Note that the newly-applied interface mode will affect all interface-related show/config commands.



NOTE: Some platforms do not support alias mapping. In such cases, this command is not applicable. Such platforms always use the same SONiC interface names.

  - Usage:  
    config interface_naming_mode (default | alias)

  - Interface naming mode originally set to 'default'. Interfaces are referenced by default SONiC interface names:

- Example:    
  ```
    admin@sonic:~$ show interfaces naming_mode 
    default
    
    admin@sonic:~$ show interface status Ethernet0
      Interface     Lanes    Speed    MTU            Alias    Oper    Admin
    -----------  --------  -------  -----   --------------  ------  -------
      Ethernet0   101,102      40G   9100   fortyGigE1/1/1      up       up
    
    admin@sonic:~$ sudo config interface_naming_mode alias
    Please logout and log back in for changes take effect.
  ```

    - After user logs out and back in again, interfaces now referenced by hardware vendor aliases:  

  ```
    admin@sonic:~$ show interfaces naming_mode 
    alias
    
    admin@sonic:~$ sudo config interface fortyGigE1/1/1 shutdown
    admin@sonic:~$ show interface status fortyGigE1/1/1
      Interface     Lanes    Speed    MTU            Alias    Oper    Admin
    -----------  --------  -------  -----   --------------  ------  -------
      Ethernet0   101,102      40G   9100   fortyGigE1/1/1    down     down
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Interface-Naming-Mode)


# IP 

## IP show commands

This sub-section explains the various IP protocol specific show commands that are used to display the following.
1) routes
2) bgp details - Explained in the [bgp section](#show-bgp)
3) IP interfaces
4) prefix-list
5) protocol

**show ip route**  

This command displays either all the route entries from the routing table or a specific route. VRF is an optional keyword for this CLI that allows user to view IP routes in a given VRF or all VRFs. If VRF parameter is not entered by user, then routes from default VRF are displayed.

  - Usage:  
    show ip route [\<ip_address\>] [vrf {all | <vrf_name>}]


- Example:
  ```
  admin@sonic:~$ show ip route
  Codes: K - kernel route, C - connected, S - static, R - RIP,
         O - OSPF, I - IS-IS, B - BGP, P - PIM, A - Babel,
         > - selected route, * - FIB route
    S>* 0.0.0.0/0 [200/0] via 10.11.162.254, eth0
    C>* 1.1.0.0/16 is directly connected, Vlan100
    C>* 10.1.0.1/32 is directly connected, lo
    C>* 10.1.0.32/32 is directly connected, lo
    C>* 10.1.1.0/31 is directly connected, Ethernet112
    C>* 10.1.1.2/31 is directly connected, Ethernet116
    C>* 10.11.162.0/24 is directly connected, eth0
    C>* 10.12.0.102/32 is directly connected, lo
    C>* 127.0.0.0/8 is directly connected, lo
    C>* 240.127.1.0/24 is directly connected, docker0

  ```
 - Optionally, you can specify an IP address in order to display only routes to that particular IP address

- Example:
  ```
	admin@sonic:~$ show ip route 10.1.1.0
	Routing entry for 10.1.1.0/31
	  Known via "connected", distance 0, metric 0, best
	  * directly connected, Ethernet112
  ```

- Optionally, user can specify a VRF name in order to display routes in that particular VRF

- Example:

  ```
  admin@sonic:~$ show ip route vrf Vrf-Core
  Codes: K - kernel route, C - connected, S - static, R - RIP,
         O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
         T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
         F - PBR, f - OpenFabric,
         > - selected route, * - FIB route # - not installed in hardware

  VRF Vrf-Core:
  C>*  179.13.79.0/24 is directly connected, Ethernet204, 00:00:40
  C>*  192.168.42.0/24 is directly connected, Ethernet208, 00:00:37
  ```

**show ip interfaces**  

This command displays the details about all the Layer3 IP interfaces in the device for which IP address has been assigned. This command output has been extnded to display the asscoiated VRF for each L3 interface. A new column has been inserted to display the associated VRF name. If an interface belongs to default VRF, the VRF name displayed is blank. Flags Column field displays any flags assosiated with the interface.
The type of interfaces include the following.
1) Front panel physical ports.
2) PortChannel.
3) VLAN interface.
4) Loopback interfaces
5) docker interface and
6) management interface
7) IPv4 Unnumbered interface (Denoted by 'U' in the Flags column)

  - Usage:  
    show ip interfaces

- Example:
  ```
    admin@sonic:~$ show ip interfaces
    Interface       IPv4 address/mask    Master    Admin/Oper       Flags
    --------------  -------------------  --------  ------------     -------
    Ethernet100     161.29.39.25/27      Vrf-Edge  up/down
                    12.46.83.58/29
    Ethernet200     64.27.33.48/21       Vrf-Core  up/down
    Ethernet204     179.13.79.31/24      Vrf-Core  up/up
    Ethernet208     192.168.42.91/24     Vrf-Core  up/up
    Ethernet212     27.135.72.19/24      Vrf-Core  up/down
    Ethernet220  	3.3.3.3/32                     down/down        U
    Loopback1    	3.3.3.3/32                     up/up 
    PortChannel213  71.141.26.9/24       Vrf-Core  up/down
    Vlan234         10.27.22.219/31      Vrf-Core  down/down
    docker0         240.127.1.1/24                 up/down
    eth0            10.59.143.45/20                up/up
    lo              127.0.0.1/8                    up/up
  ```

**show ip protocol**  

This command displays the route-map that is configured for the routing protocol.
Refer the routing stack [Quagga Command Reference](https://www.quagga.net/docs/quagga.pdf) or [FRR Command Reference](https://buildmedia.readthedocs.org/media/pdf/frrouting/latest/frrouting.pdf) to know more about this command.

  - Usage:  
    show ip protocol


- Example:
  ```
    show ip protocol
    Protocol    : route-map 
    ------------------------
    system      : none
    kernel      : none
    connected   : none
    static      : none
    rip         : none
    ripng       : none
    ospf        : none
    ospf6       : none
    isis        : none
    bgp         : RM_SET_SRC
    pim         : none
    hsls        : none
    olsr        : none
    babel       : none
    any         : none
  ```

## IPv6 show commands

This sub-section explains the various IPv6 protocol specific show commands that are used to display the following.
1) routes
2) IPv6 bgp details - Explained in the [bgp section](#show-bgp)
3) IP interfaces
4) protocol

**show ipv6 route**  

This command displays either all the IPv6 route entries from the routing table or a specific IPv6 route. This command has been extended to display IPv6 routes from a particular VRF or all VRFs by entering optional VRF parameter on the CLI. If VRF parameter is not entered by user, the routes are displayed from default VRF.

  - Usage:  
    show ipv6 route [\<ipv6_address\>] [vrf {all | <vrf_name>}]

- Example:
  ```
  admin@sonic:~$ show ipv6 route 
	Codes: K - kernel route, C - connected, S - static, R - RIPng,
		   O - OSPFv6, I - IS-IS, B - BGP, A - Babel,
		   > - selected route, * - FIB route

	C>* ::1/128 is directly connected, lo
	C>* 2018:2001::/126 is directly connected, Ethernet112
	C>* 2018:2002::/126 is directly connected, Ethernet116
	C>* fc00:1::32/128 is directly connected, lo
	C>* fc00:1::102/128 is directly connected, lo
	C>* fc00:2::102/128 is directly connected, eth0
	C * fe80::/64 is directly connected, Vlan100
	C * fe80::/64 is directly connected, Ethernet112
	C * fe80::/64 is directly connected, Ethernet116
	C * fe80::/64 is directly connected, Bridge
	C * fe80::/64 is directly connected, PortChannel0011
	C>* fe80::/64 is directly connected, eth0

  ```
 - Optionally, you can specify an IPv6 address in order to display only routes to that particular IPv6 address


- Example:
  ```
    admin@sonic:~$ show ipv6 route  fc00:1::32
    Routing entry for fc00:1::32/128
      Known via "connected", distance 0, metric 0, best
      * directly connected, lo
  ```

- Optionally, user can specify VRF parameter on CLI in order to display routes from that particular VRF

- Example:

  ```
  admin@sonic:~$ show ipv6 route vrf Vrf-Core
  Codes: K - kernel route, C - connected, S - static, R - RIPng,
         O - OSPFv3, I - IS-IS, B - BGP, N - NHRP, T - Table,
         v - VNC, V - VNC-Direct, A - Babel, D - SHARP, F - PBR,
         f - OpenFabric,
         > - selected route, * - FIB route # - not installed in hardware
  ```


  VRF Vrf-Core:
  C *  fe80::/64 is directly connected, Ethernet208, 00:17:01
  C>*  fe80::/64 is directly connected, Ethernet204, 00:17:04
  K>*  ff00::/8 [0/256] is directly connected, Ethernet208, 00:17:01
  ```

**show ipv6 interfaces**  

This command displays the details about all the Layer3 IPv6 interfaces in the device for which IPv6 address has been assigned. The comand output has been extended to display each interface's associated VRF. A new column has been inserted in command output to display the associated VRF. A blan VRF name indicate that the interface belongs to default VRF.
The type of interfaces include the following.
1) Front panel physical ports.
2) PortChannel.
3) VLAN interface.
4) Loopback interfaces
5) management interface

  - Usage:  
    show ipv6 interfaces


- Example:
  ```
    admin@sonic:~$ show ipv6 interfaces
    Interface       IPv6 address/mask                         Master     Admin/Oper
    --------------  ----------------------------------------  ---------  ------------
    Bridge          fe80::d494:dcff:fe37:535e%Bridge/64                  up/down
    Ethernet200     3001::1/64                                Vrf-Core   up/down
    Ethernet204     2001::1/64                                Vrf-Core   up/up
                    fe80::3e2c:99ff:fe2d:8235%Ethernet204/64
    Ethernet208     fe80::3e2c:99ff:fe2d:8235%Ethernet208/64  Vrf-Core   up/up
    Ethernet112     2018:2001::1/126                          Vrf-Red    up/up
                    fe80::3617:ebff:fe38:100%Ethernet112/64
    Ethernet116     2018:2002::1/126                          Vrf-Green  up/up
                    fe80::3617:ebff:fe38:100%Ethernet116/64
    PortChannel213  4001::1/64                                Vrf-Core   up/down
    Vlan27          6001::1/64                                Vrf-Green  down/down
    Vlan100         5001::1/64                                Vrf-Edge   down/down
    eth0            fe80::3e2c:99ff:fe2d:8235%eth0/64                    up/up
    lo              ::1/128                                              up/up
    ```



**show ipv6 protocol**  

This command displays the route-map that is configured for the IPv6 routing protocol.
Refer the routing stack [Quagga Command Reference](https://www.quagga.net/docs/quagga.pdf) or [FRR Command Reference](https://buildmedia.readthedocs.org/media/pdf/frrouting/latest/frrouting.pdf) to know more about this command.


  - Usage:  
    show ipv6 protocol


- Example:
  ``` 
  show ipv6 protocol
  Protocol    : route-map 
  ------------------------
  system      : none
  kernel      : none
  connected   : none
  static      : none
  rip         : none
  ripng       : none
  ospf        : none
  ospf6       : none
  isis        : none
  bgp         : RM_SET_SRC6
  pim         : none
  hsls        : none
  olsr        : none
  babel       : none
  any         : none
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#IP)

# Flow based services commands

## Flow based services config commands

**Create classifier**

```
root@sonic:~# config classifier add --help
Usage: config classifier add [OPTIONS] <name>

  Add a Classifier

Options:
  -m, --match-type <match-type>   Set match type to acl or fields  [required]
  -d, --description <description>
                                  Set classifier description
  --help                          Show this message and exit.

```

**Update classifier with match parameters**

```
root@sonic:~# config classifier update --help
Usage: config classifier update [OPTIONS] <name>

  Update a Classifier with flow identification criterion/criteria.

Options:
  --acl <acl-name>                Add ACL table name for flow classification
  --no-acl                        Delete ACL Table name used for flow
                                  classification
  --src-mac <src-mac>             Match source mac address (xx-xx-xx-xx-xx-xx
                                  [/xx-xx-xx-xx-xx-xx] format)
  --no-src-mac                    Delete match on source mac address
  --dst-mac <dst-mac>             Match destination mac address (xx-xx-xx-xx-
                                  xx-xx[/xx-xx-xx-xx-xx-xx] format)
  --no-dst-mac                    Delete match on destination mac address
  --ether-type <ether-type>       Match ethertype (hex or decimal format in
                                  1536-65535 range)
  --no-ether-type                 Delete match on ethertype
  --pcp <pcp>                     Match on PCP (0-7)
  --no-pcp                        Delete match on PCP
  --src-ip <src-ip>               Match source IP address (A.B.C.D[/M] format)
  --no-src-ip                     Delete match on source IP address
  --dst-ip <dst-ip>               Match on destination IP address (A.B.C.D[/M]
                                  format)
  --no-dst-ip                     Delete match on destination IP address
  --src-ipv6 <src-ipv6>           Match source IPv6 address (X:X::X.X[/M]
                                  format)
  --no-src-ipv6                   Delete match on source IPv6 address
  --dst-ipv6 <dst-ipv6>           Match on destination IPv6 address
                                  (X:X::X.X[/M] format)
  --no-dst-ipv6                   Delete match on destination IPv6 address
  --ip-proto <ip-proto>           Match on IP protocol/next Header (0-255)
  --no-ip-proto                   Delete match on IP protocol/next header
  --src-port <value or begin-end>
                                  Match on source port or source port range
                                  (0-65535)
  --no-src-port                   Delete match on source port or source port
                                  range
  --dst-port <dst-port>           Match on destination port or destination
                                  port range (0-65535)
  --no-dst-port                   Delete match on destination port or
                                  destination port range
  --tcp-flags <tcp-flags>         Match on TCP flags (fin not-fin syn not-syn
                                  rst not-rst psh not-psh ack not-ack urg not-
                                  urg ece not-ece cwr not-cwr in comma
                                  separated format)
  --no-tcp-flags <tcp-flags>      Delete match on TCP flags (fin not-fin syn
                                  not-syn rst not-rst psh not-psh ack not-ack
                                  urg not-urg ece not-ece cwr not-cwr in comma
                                  separated format)
  --dscp <dscp>                   Match on DSCP (0-63)
  --no-dscp                       Delete match on DSCP
  -d, --description <description>
  --help                          Show this message and exit.
```

**Delete classifier**

```
root@sonic:~# config classifier del --help
Usage: config classifier del [OPTIONS] <name>

  Delete a Classifier

Options:
  --help  Show this message and exit.
```

**Add policy**

```
root@sonic:~# config policy add --help
Usage: config policy add [OPTIONS] <name>

  Add a Flow Based Services Policy

Options:
  -t, --type <type>               Policy type (qos or monitoring)  [required]
  -d, --description <description>
                                  Set policy description
  --help                          Show this message and exit.
```

**Delete policy**

```
root@sonic:~# config policy del --help
Usage: config policy del [OPTIONS] <name>

  Delete a Flow Based Services Policy

Options:
  --help  Show this message and exit.
```

**Add flow identified by a classifier to a policy**

```
root@sonic:~# config flow add --help
Usage: config flow add [OPTIONS] <policy_name> <classifier_name>

  Add a Flow to Policy

Options:
  -p, --priority <type>           Set flow priority (0-1023)  [required]
  -d, --description <description>
                                  Set flow description
  --help                          Show this message and exit.
```

**Delete flow identified by a classifier to a policy**

```
root@sonic:~# config flow del --help
Usage: config flow del [OPTIONS] <policy_name> <classifier_name>

  Delete a Flow from Policy

Options:
  --help  Show this message and exit.
```

**Add action(s) to flows**

```
root@sonic:~# config flow update --help
Usage: config flow update [OPTIONS] <policy_name> <classifier_name>

  Add or Update flow results

Options:
  -p, --priority <type>           Set flow priority (0-1023)
  --set-dscp <Value>              DSCP remark to value (0-63)
  --no-set-dscp                   Delete DSCP remarking action
  --set-pcp <Value>               PCP remark to value (0-7)
  --no-set-pcp                    Delete PCP Remarking action
  --police                        Add rate limiting action
  --cir <cir>                     Conform rate
  --cbs <cbs>                     Conform burst size
  --pir <pir>                     Peak rate
  --pbs <pbs>                     Peak burst size
  --no-police                     Delete rate limiting action
  --mirror-session <session_name>
                                  Set mirror destination
  --no-mirror-session             Delete mirror destination
  -d, --description <description>
                                  Set flow description
  --help                          Show this message and exit.
```

The policers are implicitly configured as TRTCM policers of type bytes in color blind mode and drop as default action for packets of color red. 

**Apply and remove the policy to interface**

```
root@sonic:~# config service-policy bind --help
Usage: config service-policy bind [OPTIONS] <interface_name> <type> <stage>
                                  <policy_name>

  Apply policy to interface

Options:
  --help  Show this message and exit.


root@sonic:~# config service-policy unbind --help
Usage: config service-policy unbind [OPTIONS] <interface_name> <type> <stage>

  Remove policy from interface

Options:
  --help  Show this message and exit.
```

**Examples**

```
# Create classifier class0
config classifier add class0 -m acl
config classifier update class0 -a l3_ACL_0

# Create classifier class1
config classifier add class1 -m acl
config classifier update class1 -a l2_ACL_0

# Create policy policy0
config policy add policy0 -t qos

# Create flow using classifier class0 and set results
config flow add policy0 class0 -p 200
config flow update policy0 class0 --set-dscp 15 --set-pcp 5

# Create flow using classifier class0 and set results
config flow add policy0 class1 -p 100
config flow update policy0 class1 --set-dscp 30 --set-pcp 2

# Apply policy to required interfaces
config service-policy bind Ethernet0 qos in policy0
config service-policy bind Ethernet4 qos in policy0
config service-policy bind Ethernet8 qos out policy0
```

## Flow based services show and clear commands

**Show classifier details**

```
root@sonic:~# show classifier --help
Usage: show classifier [OPTIONS] [NAME]

  Show flow based services classifiers related information

Options:
  -m, --match-type <match-type>  Classifier type acl or fields
  -?, -h, --help                 Show this message and exit.
```

The following is the sample output

```
root@sonic:~# show classifier class0
classifier class0 match-type acl
  match-acl l3_ACL_0
  Referenced in flows:
    policy policy0 at priority 200
    
root@sonic:~# show classifier fields_class_0
Classifier fields_class_0 match-type fields
  Description:
  Match:
    src-ip 40.1.1.100/32
  Referenced in flows:
    policy mon_policy_0 at priority 999
    policy qos_policy_0 at priority 999
```

**Show policy details**

```
root@sonic:~# show policy --help
Usage: show policy [OPTIONS] [NAME]

  Show flow based services policies related information

Options:
  -f, --flow <flow>  Show information only for flow identified by classifer
  -t, --type <type>  Policy type (qos)
  -?, -h, --help     Show this message and exit.
```

The following is the sample output

```
root@sonic:~# show policy qos_policy_0
Policy qos_policy_0 Type qos
  Description: 
  Flow fields_class_0 at priority 999
    Description: 
    set-pcp 1
    set-pcp 1
    police cir 10000000 cbs 1000000 pir 0 pbs 0
  Flow fields_class_1 at priority 998
    Description: 
    set-pcp 2
    set-pcp 2
    police cir 20000000 cbs 2000000 pir 0 pbs 0
  Flow fields_class_2 at priority 997
    Description: 
    set-pcp 3
    set-pcp 3
    police cir 30000000 cbs 3000000 pir 0 pbs 0
  Flow fields_class_3 at priority 996
    Description: 
    set-pcp 4
    set-pcp 4
    police cir 40000000 cbs 4000000 pir 0 pbs 0
  Applied to:
    Ethernet0 at ingress

root@sonic:~# show policy mon_policy_0
Policy mon_policy_0 Type monitoring
  Description: 
  Flow fields_class_0 at priority 999
    Description: 
    mirror-session ERSPAN_DestIP_50.1.1.2
  Flow fields_class_1 at priority 998
    Description: 
    mirror-session ERSPAN_DestIP_60.1.1.2
  Flow fields_class_2 at priority 997
    Description: 
    mirror-session ERSPAN_DestIP_50.1.1.2
  Flow fields_class_3 at priority 996
    Description: 
    mirror-session ERSPAN_DestIP_60.1.1.2
  Applied to:
    Ethernet0 at ingress
```

**Show policy binding summary**

```
root@sonic:~# show service-policy summary --help
Usage: show service-policy summary [OPTIONS]

  Show summary of applied flow based services policies

Options:
  -i, --interface <interface>  Interface name
  -t, --type <type>            Policy type (qos)
  -?, -h, --help               Show this message and exit.
```

The following is the sample output

```
root@sonic:~# show service-policy summary
Ethernet0
  qos policy policy0 at ingress
  monitoring policy mon_policy_0 at ingress
Ethernet4
  qos policy policy0 at ingress
Ethernet8
  qos policy policy0 at egress

root@sonic:~# show service-policy summary -i Ethernet0
Ethernet0
  qos policy policy0 at ingress
```

**Show/Clear policy binding and counters for an interface**

```
root@sonic:~# show service-policy interface --help
Usage: show service-policy interface [OPTIONS] <interface_name>

  Show details by interface name

Options:
  -t, --type <type>    Policy type (qos or monitoring)
  -s, --stage <stage>  Stage (in or out)
  -c, --clear          Clear statistics
  -?, -h, --help       Show this message and exit.
```

The following is the sample output

```
root@sonic:~# show service-policy interface Ethernet0
Ethernet0
  Policy qos_policy_0 Type qos at ingress
  Description: 
    Flow fields_class_3 at priority 996 (Active)
      Description: 
      set-pcp 4
      set-dscp 4
      police: cir 40000000 cbs 4000000 pir 0 pbs 0
        type bytes mode color-blind
        operational cir 40000000 cbs 4000000 pir 0 pbs 0
        conformed 0 packets 0 bytes action forward
        exceed 0 frames 0 bytes action forward
        violated 0 frames 0 bytes action drop
      Packet matches: 0 frames 0 bytes
    Flow fields_class_2 at priority 997 (Active)
      Description: 
      set-pcp 3
      set-dscp 3
      police: cir 30000000 cbs 3000000 pir 0 pbs 0
        type bytes mode color-blind
        operational cir 30000000 cbs 3000000 pir 0 pbs 0
        conformed 0 packets 0 bytes action forward
        exceed 0 frames 0 bytes action forward
        violated 0 frames 0 bytes action drop
      Packet matches: 0 frames 0 bytes
```
**Show/Clear policy binding and counters for a policy**

```
root@sonic:~# show service-policy policy --help
Usage: show service-policy policy [OPTIONS] <policy_name>

  Show flow based services applied polcies information by policies name

Options:
  -t, --type <type>  Policy type (qos)
  -c, --clear        Clear statistics
  -?, -h, --help     Show this message and exit.
```

The following is the sample output 

```
root@sonic:~# show service-policy policy mon_policy_0
Ethernet0
  Policy mon_policy_0 Type monitoring at ingress
  Description: 
    Flow fields_class_3 at priority 996 (Active)
      Description: 
      mirror-session ERSPAN_DestIP_60.1.1.2
      Packet matches: 0 frames 0 bytes
    Flow fields_class_2 at priority 997 (Active)
      Description: 
      mirror-session ERSPAN_DestIP_50.1.1.2
      Packet matches: 0 frames 0 bytes
    Flow fields_class_1 at priority 998 (Active)
      Description: 
      mirror-session ERSPAN_DestIP_60.1.1.2
      Packet matches: 0 frames 0 bytes
    Flow fields_class_0 at priority 999 (Active)
      Description: 
      mirror-session ERSPAN_DestIP_50.1.1.2
      Packet matches: 0 frames 0 bytes
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Flow-based-services-commands)

# IP-Helper Commands

This section explains all the IP-Helper show, configuration and clear commands that are supported in SONiC.

Note: User can configure IP-Helper and execute IP-Helper show commands from KLISH CLI too. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about IP-Helper configuration and show CLIs.

## IP-Helper show commands 

**show ip forward_protocol config** 

This command displays the IP Helper Global configuration.

- Usage:  
  show ip forward_protocol config

- Example:
```
root@sonic:/home/admin# show ip forward_protocol config

    UDP forwarding: Enabled

    UDP rate limit: 600 pps

    UDP forwarding enabled on the ports: TFTP , NTP , TACACS , 330 , 234, 1000

    UDP forwarding disabled on the ports: DNS , NetBios-Name-server , NetBios-datagram-server
```

**show ip helper-address config** 

This command displays the IP Helper address configuration on all the interfaces.

- Usage:  
  show ip helper-address config

- Example:
```
root@sonic:/home/admin# show ip helper-address config

    Interface   Vrf         Relay address
    -------------------------------------
    Ethernet24              31.1.0.2
                            2.2.2.3
                vrf20       11.19.0.144
    Ethernet28              31.1.0.2
```

**show ip helper-address config {interface}**

This command displays the IP Helper address configuration on the interface.

- Usage:  
  show ip helper-address config Ethernet24

- Example:
```
root@sonic:/home/admin# show ip helper-address config Ethernet24

    Interface   Vrf         Relay address
    -------------------------------------
    Ethernet24              31.1.0.2
                            2.2.2.3
                vrf20       11.19.0.144 
```

**show ip helper-address statistics** 

This command displays the IP Helper statistics on all the interfaces.

- Usage:  
  show ip helper-address statistics

- Example:

```
root@sonic:/home/admin# show ip helper-address statistics

Ethernet24
-----------
  Packets received                              : 1098
  Packets relayed                               :  980
  Packets dropped                               :  118
  Invalid TTL packets                           :   22
  All ones broadcast packets received           :  602
  Net directed broadcast packets received       :  496


Ethernet28
-----------
  Packets received                              : 100
  Packets relayed                               : 90
  Packets dropped                               : 10
  Invalid TTL packets                           : 5
  All ones broadcast packets received           : 50
  Net directed broadcast packets received       : 50
```

**show ip helper-address statistics {interface}**

This command displays the IP Helper statistics on the interface.

- Usage:  
  show ip helper-address statistics Ethernet24

- Example:
```
root@sonic:/home/admin# show ip helper-address statistics Ethernet24

    Packets received                              : 1098
    Packets relayed                               :  980  
    Packets dropped                               :  118
    Invalid TTL packets                           :   22
    All ones broadcast packets received           :  602
    Net directed broadcast packets received       :  496
```

## IP-Helper configuration commands 
This sub-section explains the list of configuration options available for IP-Helper module.

**config interface ip helper-address add {interface-name} {ip-address} [-vrf <vrf-name]**

Use this command to add IP helper address on an interface.

- Example:
```
root@sonic:/home/admin# config interface ip helper_address add Ethernet28 22.22.22.22
root@sonic:/home/admin# config interface ip helper_address add Ethernet24 22.22.22.22 -vrf VrfBlue
```

**config interface ip helper-address remove {interface-name} {ip-address} [-vrf <vrf-name]**

Use this command to remove IP helper address on an interface.

- Example:
```
root@sonic:/home/admin# config interface ip helper_address remove Ethernet28 22.22.22.22
root@sonic:/home/admin# config interface ip helper_address remove Ethernet24 22.22.22.22 -vrf VrfBlue
```

**config ip forward_protocol udp enable**

Use this command to enable UDP Broadcast forwarding.

- Example:
```
root@sonic:/home/admin# config ip forward_protocol udp disable
```

**config ip forward_protocol udp disable**

Use this command to disable UDP Broadcast forwarding.

- Example:
```
root@sonic:/home/admin# config ip forward_protocol udp disable
```

**config ip forward_protocol udp add {[tftp/dns/ntp/netbios-name-server/netbios-datagram-server/tacacs] | {port}}**

Use this command to add UDP port to the list of forwarding ports.

- Example:
```
root@sonic:/home/admin# config ip forward_protocol udp add 330
root@sonic:/home/admin# config ip forward_protocol udp add ntp
```

**config ip forward_protocol udp remove {[tftp/dns/ntp/netbios-name-server/netbios-datagram-server/tacacs] | {port}}**

Use this command to remove UDP port from the list of forwarding ports.

- Example:
```
root@sonic:/home/admin# config ip forward_protocol udp remove 330
root@sonic:/home/admin# config ip forward_protocol udp remove dns
```

**config ip forward_protocol udp rate_limit {value-in-pps}**

Use this command to configure the UDP broadcast packet rate limiting value in the range 600 - 10000 pps. The default value is 600 pps.

- Example:
```
root@sonic:/home/admin# config ip forward_protocol udp rate_limit 5000
```

## IP-Helper clear commands 

**sonic-clear ip helper-address statistics**

Use this command to clear the relay statistics on all the interfaces.
- Example:

```
root@sonic:/home/admin# sonic-clear ip helper-address statistics  

IpHelper Address Statistics are cleared.

```

**sonic-clear ip helper-address statistics {interface}**

Use this command to clear the relay statistics on an interface.
- Example:

```
root@sonic:/home/admin# sonic-clear ip helper-address statistics Ethernet28

IpHelper Address Statistics are cleared on Ethernet28

```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#ip-helper-commands)

# IPv4 Unnumbered Interface Commands

## IPv4 Unnumbered Interface configuration commands 

Note: IPv4 Unnumbered Interface can be configured from KLISH CLI too. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring IPv4 Unnumbered Interface using KLISH CLIs.


This section explains all the commands that are supported in SONiC to configure IPv4 Unnumbered Interface. 

**config interface ip unnumbered add <interface_name> <donor_interface_name>**

This command can be used to configure an IPv4 Unnumbered interface by specifying the Donor interface from which the IPv4 address will be borrowed.

  - Usage:
    sudo config interface ip unnumbered add <interface_name> <donor_interface_name> 

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip unnumbered add Ethernet0 Loopback1 
  admin@sonic:~$ sudo config interface ip unnumbered add PortChannel1 Loopback1
  ```

**config interface ip unnumbered del <interface_name>**

This command can be used to unconfigure an IPv4 Unnumbered interface.

  - Usage:
    sudo config interface ip unnumbered del <interface_name>

- Examples:
  ```
  admin@sonic:~$ sudo config interface ip unnumbered del Ethernet0
  admin@sonic:~$ sudo config interface ip unnumbered del PortChannel1
  ```
  Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#ipv4-unnumbered-interface-commands)

# Link state tracking

## Link state tracking show commands

**show link state group**

```
sonic# show link state group
Name: MclagLinkTracking
Description: Interface tracking for all MCLAGs
Timeout: 120 seconds

sonic# show link state group MclagLinkTracking
Name: MclagLinkTracking
Description: Interface tracking for all MCLAGs
Timeout: 120 seconds
Upstream Interfaces:
    Ethernet0 (Up)
    Ethernet4 (Up)
    Vlan100   (Up)
Downstream Interfaces:
    PortChannel1 (Up)
    PortChannel2 (Up)
```
**show link state group *NAME***

```
sonic# show link state group MclagLinkTracking
Name: MclagLinkTracking
Description: Interface tracking for all MCLAGs
Timeout: 60 seconds
Upstream:
    Ethernet0 (Down)
    Ethernet4 (Down)
    Vlan100   (Down)
Downstream:
    PortChannel1 (Disabled)
    PortChannel2 (Disabled)
```

## Link state tracking config commands

**Through Klish**

```
sonic(config)# [no] link state track NAME
sonic(config-link-track)# [no] description STRING
sonic(config-link-track)# [no] timeout <1-999>
sonic(config-link-track)# [no] downstream all-mclag

sonic(config)# interface (Ethernet|PortChannel|Vlan) PORTNUM
sonic(config-if) [no] link state group NAME upstream
```

**Through Click**

**config linktrack add**

```
admin@sonic:~$ sudo config linktrack add --help
Usage: config linktrack add [OPTIONS] <name>

  Add Interface tracking group

Options:
  -u, --upstream <upstream>      Set upstream interfaces
  -d, --downstream <downstream>  Set downstream ports. Use port/lag names or 
                                 all-mclag
  -t, --timeout <timeout>        Set timeout value in seconds
  --description <description>    Set group description
  -?, -h, --help                 Show this message and exit.
```

**config linktrack del**

```
admin@sonic:~$ sudo config linktrack del --help
Usage: config linktrack del [OPTIONS] <name>

  Delete Interface tracking group

Options:
  -?, -h, --help  Show this message and exit.
```

**config linktrack update**

```
admin@sonic:~$ sudo config linktrack update --help
Usage: config linktrack update [OPTIONS] <name>

  Update Interface tracking group

Options:
  -u, --upstream <upstream>       Set upstream interfaces
  -nu, --no-upstream <upstream>   Remove upstream interfaces
  -d, --downstream <downstream>   Set downstream ports. Use port/lag names or
                                  all-mclag
  -nd, --no-downstream <downstream>
                                  Remove downstream ports
  -t, --timeout <timeout>         Set timeout value in seconds
  --description <description>     Set group description
  -?, -h, --help                  Show this message and exit.
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Link-state-tracking)

# LLDP

Note: User can configure LLDP and execute LLDP show commands from KLISH CLI. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about LLDP configuration and show CLIs.

## LLDP show commands

**show lldp table**  

This command displays the brief summary of all LLDP neighbors.

  - Usage:  
    show lldp table


- Example:
  ```   
  admin@sonic:~$ show lldp table
  Capability codes: (R) Router, (B) Bridge, (O) Other
  LocalPort    RemoteDevice       RemotePortID         Capability    RemotePortDescr
  -----------  -----------------  -------------------  ------------  --------------------
  Ethernet112  T1-1               hundredGigE1/2       BR            T0-2:hundredGigE1/29
  Ethernet116  T1-2               hundredGigE1/2       BR            T0-2:hundredGigE1/30
  eth0         swtor-b2lab2-1610  GigabitEthernet 0/2  OBR
  --------------------------------------------------
  Total entries displayed:  3
  ```

**show lldp neighbors**  

This command displays more details about all LLDP neighbors or only the neighbors connected to a specific interface. 

  - Usage:  
    show lldp neighbors [INTERFACENAME]


- Example1: To display all neighbors in all interfaces
  ```
  admin@sonic:~$ show lldp neighbors
  -------------------------------------------------------------------------------
  LLDP neighbors:
  -------------------------------------------------------------------------------
  Interface:    eth0, via: LLDP, RID: 1, Time: 0 day, 12:21:21
    Chassis:     
  	ChassisID:    mac 00:01:e8:81:e3:45
  	SysName:      swtor-b2lab2-1610
  	SysDescr:     Dell Force10 Networks Real Time Operating System Software. Dell Force10 Operating System Version: 1.0. Dell Force10 Application Software Version: 8.3.3.10d. Copyright (c) 1999-2012 by Dell Inc. All Rights Reserved.Build Time: Tue Sep 22 11:21:54 PDT 2015
  	TTL:          20
  	Capability:   Repeater, on
  	Capability:   Bridge, on
  	Capability:   Router, on
    Port:        
  	PortID:       ifname GigabitEthernet 0/2
    VLAN:         162, pvid: yes
  -------------------------------------------------------------------------------
  Interface:    Ethernet116, via: LLDP, RID: 3, Time: 0 day, 12:20:49
    Chassis:     
  	ChassisID:    mac 4c:76:25:e7:f0:c0
  	SysName:      T1-2
  	SysDescr:     Debian GNU/Linux 8 (jessie) Linux 4.9.0-8-amd64 #1 SMP Debian 4.9.110-3+deb9u6 (2015-12-19) x86_64
  	TTL:          120
  	MgmtIP:       10.11.162.40
  	Capability:   Bridge, on
  	Capability:   Router, on
  	Capability:   Wlan, off
  	Capability:   Station, off
    Port:        
  	PortID:       local hundredGigE1/2
  	PortDescr:    T0-2:hundredGigE1/30
  -------------------------------------------------------------------------------
  ```


  - Optionally, you can specify an interface name in order to display only that particular interface

- Example2:
  ```
  admin@sonic:~$ show lldp neighbors Ethernet112
  show lldp neighbors Ethernet112
  -------------------------------------------------------------------------------
  LLDP neighbors:
  -------------------------------------------------------------------------------
  Interface:    Ethernet112, via: LLDP, RID: 2, Time: 0 day, 19:24:17
    Chassis:     
  	ChassisID:    mac 4c:76:25:e5:e6:c0
  	SysName:      T1-1
  	SysDescr:     Debian GNU/Linux 8 (jessie) Linux 4.9.0-8-amd64 #1 SMP Debian 4.9.110-3+deb9u6 (2015-12-19) x86_64
  	TTL:          120
  	MgmtIP:       10.11.162.41
  	Capability:   Bridge, on
  	Capability:   Router, on
  	Capability:   Wlan, off
  	Capability:   Station, off
    Port:        
  	PortID:       local hundredGigE1/2
  	PortDescr:    T0-2:hundredGigE1/29
  -------------------------------------------------------------------------------

  ```


**show lldp statistics**  

This command displays LLDP packet statistics for all interfaces or a specific interface. 

  - Usage:  
    show lldp statistics [INTERFACENAME]


- Example1: To display statistics for all interfaces
  ```
    show lldp statistics 
    -------------------------------------------------------------------------------
    LLDP statistics:
    -------------------------------------------------------------------------------
    Interface:    Ethernet0
    Transmitted:  150
    Received:     145
    Discarded:    1
    Unrecognized: 0
    Ageout:       0
    Inserted:     0
    Deleted:      0
    -------------------------------------------------------------------------------
    Interface:    Ethernet1
    Transmitted:  0
    Received:     0
    Discarded:    1
    Unrecognized: 0
    Ageout:       0
    Inserted:     0
    Deleted:      0
    -------------------------------------------------------------------------------
    Interface:    Ethernet2
    Transmitted:  0
    Received:     0
    Discarded:    1
    Unrecognized: 0
    Ageout:       0
    Inserted:     0
    Deleted:      0
    -------------------------------------------------------------------------------
    
  ```


  - Optionally, you can specify an interface name in order to display only that particular interface

- Example2:
  ```
  admin@sonic:~$ show lldp statistics Ethernet6
    -------------------------------------------------------------------------------
    LLDP statistics:
    -------------------------------------------------------------------------------
    Interface:    Ethernet6
    Transmitted:  1460
    Received:     1465
    Discarded:    1
    Unrecognized: 2
    Ageout:       0
    Inserted:     2
    Deleted:      0
    -------------------------------------------------------------------------------
  ```
  Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#LLDP)



# UDLD

UDLD is a layer 2 protocol that allows detection of uni-directional link failures which is required in spanning-tree topologies for avoiding loops caused due to uni-directional link failures.

## UDLD config CLIs:
UDLD supports folloiwng global config CLIs. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details.
```
[no] udld enable
[no] udld aggressive
[no] udld message-time <value>
[no] udld multiplier <value>
```

UDLD supports folloiwng interface config CLIs. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details.
```
[no] udld enable
[no] udld aggressive
```

## UDLD show CLIs:
UDLD supports folloiwng show CLIs. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details.
```
show udld global
show udld neighbors
show udld interface <ifname>
show udld statistics [interface <ifname>] 
```

## UDLD debug CLIs:
Following debug commands will be supported for enabling additional logging, all the logs in the context of UDLDd and UDLDMgr will be stored in /var/log/udldd.log. Any logs in orchagent context will be available in /var/log/syslog. These debug commands are available in CLICK CLI only. 
```
debug udld packet [tx|rx|both] <ifname>
debug udld [set|reset] trace_lvl <module>
debug udld set log_lvl <level>
```

Following debug commands will be supported to display internal information.
```
debug udld dump all
debug udld dump global
debug udld dump interface <ifname>
```

## UDLD exec CLIs:
Below command allows to clear the UDLD statistics
```
sonic-clear udld statistics [ interface <ifname>]
```

Below command allows to reset the interfaces which are shutdown by UDLD.
```
udld reset
```

Note: These exec commands are available in CLICK CLI only. 

## UDLD errdisable CLIs:
Errdisable recovery functionality allows automatic recovery of ports that are shutdown due to specific conditions detected by a feature like uni-directional failures in case of UDLD. When errdisable recovery is enabled, post shutdown of a port a recovery timer is started as per the configured recovery interval, on expiry of this timer the port is enabled back. This helps in automatic recovery of the port without user intervention. 

Note: User can configure errdisable and execute errdisable show commands from KLISH CLI too. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about errdisable configuration and show CLIs.


### CLICK CLIs to configure UDLD errdisable:
The below command allows configuring the recovery interval. Default - 300sec, Range - 30-65535
```
config errdisable recovery interval <value>
```

The below command allows enabling/disabling of errdisable recovery functionality for UDLD. By default it is disabled.
```
config errdisable recovery cause {enable|disable} {udld}
```

The below command can be used to display the errdisable recovery status of UDLD and the recovery interval configured
```
show errdisable recovery
```

# Loading, Reloading And Saving Configuration

This section explains the commands that are used to load the configuration from either the ConfigDB or from the minigraph.

## Load config command

This command is used to load the configuration from configDB. 
This command loads the configuration from the input file (if user specifies this optional filename, it will use that input file. Or else, it will use the /etc/sonic/config_db.json as the input file) into CONFIG_DB.
The configurations present in the input file are applied on top of the already running configuration. 
This command does not flush the config DB before loading the new configuration. 
i.e. If the configuration present in the input file is same as the current running-configuration, nothing happens.
If the config present in the input file is not present in running-configuration, it will be added.
If the config present in the input file matches (when key matches) with the running-configuration, it will be modified as per the new values for those keys.

When user specifies the optional argument "-y" or "--yes", this command forces the loading without prompting the user for confirmation.
If the argument is not specified, it prompts the user to confirm whether user really wants to load this configuration file.

  - Usage:  
    config load [OPTIONS] [FILENAME]
    OPTIONS : -y, --yes

- Example:
   ```
   root@T1-2:~# config load
   Load config from the file /etc/sonic/config_db.json? [y/N]: y
   Running command: /usr/local/bin/sonic-cfggen -j /etc/sonic/config_db.json --write-to-db
   root@T1-2:~# 
   ```

## Load_mgmt_config command

This command is used to reconfigure hostname and mgmt interface based on device description file. 
This command either uses the optional file specified as arguement or looks for the file "/etc/sonic/device_desc.xml". 
If the file does not exist or if the file does not have valid fields for "hostname" and "ManagementAddress", it fails.

When user specifies the optional argument "-y" or "--yes", this command forces the loading without prompting the user for confirmation.
If the argument is not specified, it prompts the user to confirm whether user really wants to load this configuration file.

  - Usage:  
    config load_mgmt_config [OPTIONS] [FILENAME]
    OPTIONS : -y, --yes

- Example:
   ```
   root@T1-2:~# config load_mgmt_config
   Reload config from minigraph? [y/N]: y
   Running command: /usr/local/bin/sonic-cfggen -M /etc/sonic/device_desc.xml --write-to-db
   root@T1-2:~# 
   ```


## Load_minigraph config command

This command is used to load the configuration from /etc/sonic/minigraph.xml. 
When users do not want to use configuration from config_db.json, they can copy the minigraph.xml configuration file to the device and load it using this command.
This command restarts various services running in the device and it takes some time to complete the command.  

NOTE: If the user had logged in using SSH, users might get disconnected and some configuration failures might happen which might be hard to recover. Users need to reconnect their SSH sessions after configuring the management IP address. It is recommended to execute this command from console port  
NOTE: Management interface IP address and default route (or specific route) may require reconfiguration in case if those parameters are not part of the minigraph.xml.

When user specifies the optional argument "-y" or "--yes", this command forces the loading without prompting the user for confirmation.
If the argument is not specified, it prompts the user to confirm whether user really wants to load this configuration file.

  - Usage:  
    config load_minigraph [OPTIONS]
    OPTIONS : -y, --yes

- Example:
   ```
   root@T1-2:~# config load_minigraph
   Reload config from minigraph? [y/N]: y
   Running command: /usr/local/bin/sonic-cfggen -j /etc/sonic/config_db.json --write-to-db
   root@T1-2:~# 
   ```

## Reload config command

This command is used to clear current configuration and import new configurationn from the input file or from /etc/sonic/config_db.json.
This command shall stop all services before clearing the configuration and it then restarts those services.

This command restarts various services running in the device and it takes some time to complete the command.
NOTE: If the user had logged in using SSH, users **might get disconnected** depending upon the new management IP address. Users need to reconnect their SSH sessions.
In general, it is recommended to execute this command from console port after disconnecting all SSH sessions to the device.
When users to do ?config reload? the newly loaded config may have management IP address, or it may not have management IP address. 
If mgmtIP is there in the newly loaded config file, that mgmtIP might be same as previously configured value or it might be different. 
This difference in mgmtIP address values results in following possible behaviours.

Case1: Previously configured mgmtIP is same as newly loaded mgmtIP. The SSH session may not be affected at all, but it?s possible that there will be a brief interruption in the SSH session. But, assuming the client?s timeout value isn?t on the order of a couple of seconds, the session would most likely just resume again as soon as the interface is reconfigured and up with the same IP.
Case2: Previously configured mgmtIP is different from newly loaded mgmtIP. Users will lose their SSH connections.
Case3: Newly loaded config does not have any mgmtIP. Users will lose their SSH connections.

NOTE: Management interface IP address and default route (or specific route) may require reconfiguration in case if those parameters are not part of the minigraph.xml.

When user specifies the optional argument "-y" or "--yes", this command forces the loading without prompting the user for confirmation.
If the argument is not specified, it prompts the user to confirm whether user really wants to load this configuration file.

  - Usage:  
    config reload [-y|--yes] [-l | --load-sysinfo] [FILENAME]

- Example:
   ```
   root@T1-2:~# config reload
   Clear current config and reload config from the file /etc/sonic/config_db.json? [y/N]: y
   Running command: systemctl stop dhcp_relay
   Running command: systemctl stop swss
   Running command: systemctl stop snmp
   Warning: Stopping snmp.service, but it can still be activated by:
     snmp.timer
   Running command: systemctl stop lldp
   Running command: systemctl stop pmon
   Running command: systemctl stop bgp
   Running command: systemctl stop teamd
   Running command: /usr/local/bin/sonic-cfggen -H -k Force10-Z9100-C32 --write-to-db
   Running command: /usr/local/bin/sonic-cfggen -j /etc/sonic/config_db.json --write-to-db
   Running command: systemctl restart hostname-config
   Running command: systemctl restart interfaces-config
   Timeout, server 10.11.162.42 not responding.
   root@T1-2:~# 
   ```

## Save config  command

This command is to save the config DB configuration into the user-specified filename or into the default /etc/sonic/config_db.json. This saves the configuration into the disk which is available even after reboots.
Saved file can be transferred to remote machines for debugging. If users wants to load the configuration from this new file at any point of time, they can use "config load" command and provide this newly generated file as input. If users wants this newly generated file to be used during reboot, they need to copy this file to /etc/sonic/config_db.json.

  - Usage:  
    config save [OPTIONS] [FILENAME]
    OPTIONS : -y, --yes

- Example:
   ```
   root@T1-2:~# config save -y /etc/sonic/config2.json - this saves to the filename specified.
   root@T1-2:~# config save -y - this saves to /etc/sonic/config_db.json.
   ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#loading-reloading-and-saving-configuration)

## Configuration Erase command

This command is used to remove configuration changes performed by the user and restore the switch to a factory default configuration state. For the changes to take effect,
a switch reboot is required after executing the command.

  - Usage:
    config erase [OPTIONS]
    OPTIONS : boot, cancel, install, -y, --yes

- Example:
   ```
   root@T1-2:~# config erase
   Existing switch configuration files except management interface configuration will be removed, continue? [y/N] y
   SONiC configuration files will be restored to default values on next reboot

   root@T1-2:~# config erase cancel -y
   Configuration erase operation is cancelled

   root@T1-2:~# config erase install -y
   All SONiC switch content will be restored to default values on next reboot

   ```

- Description:
  - **config erase** : This command deletes the startup configuration JSON file /etc/sonic/config_db.json and all other application configuration files in the /etc/sonic directory. 
           The management interface configuration in the startup configuration file is retained so that the user can access the 
           switch using the same management address along with other factory default configuration after the switch reboots.
  - **config erase boot** : This command deletes the startup configuration JSON file and all other application configuration files in the /etc/sonic directory. 
           The management interface configuration in the startup configuration JSON file is also removed. 
           The SONiC switch boots with a factory default configuration file.
  - **config erase install** : This command removes all changes made by the user. All user installed packages and file changes are removed. It also deletes the startup configuration JSON file and the files in /etc/sonic directory. The SONiC switch is reverted to a state similar to a newly installed image. After the SONiC switch is rebooted, if the Zero Touch Provisioning (ZTP) feature is enabled, the SONIC switch will start performing ZTP to discover and download the switch configuration.
  - **config erase cancel** : For the *config erase* command operation to take effect, the user has to reboot the switch after issuing the command. If the user wishes to not proceed with the configuration removal operation, the *config erase cancel* command can be used to undo the previously issued *config erase* command.

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#configuration-erase-command)



# MCLAG Configuration And Show

## MCLAG Config commands  

Following section covers Click config commands required for adding/deleting mclag domains and adding/delting mclag interface to domains. 

Note:-*Only one MCLAG domain can be created in a device.* 

**MCLAG Domain configuration**

To create a new mclag domain, users need to configure the following fields 
1) domain id  - should be in the range of < 1 to 4095> 
2) local ip address - source IPv4 address to be used for MCLAG session. This is local address. Can be loopback, router interface or ve address. 
3) peer ip address - Peer IPv4 address to be used for MCLAG session

4) peer interface name -  mandatory for L2 config this links is used to carray data traffic when MCLAG interface is down; optional - for L3 MCLAG, data forwarding depends on routing information. peer link is not required.

***Note:-***

1) to bring up MCLAG session between two MCLAG peer nodes configure mclag domain on both ends. one end's source/local ip address becomes peer address in other end and vice versa.

- Usage:  
  config mclag add  <domain_id> <local_ip_addr> <peer_ip_addr> [<peer-ifname>]

  Example:

  ```
  root@sonic:/home/admin# config mclag add 10 10.1.1.1 10.1.1.2 PortChannel2
  ```

**MCLAG Member Addition**

To add mclag interface(s) to mclag domain user needs to use below command with following fields

1) domain id

2) list of mclag portchannel names seperated by comma

*Note:- mclag interfaces needs to be portchannels. Multiple mclag interfaces can be added by providing all MCLAG Interfaces seperated by comma.*  

- Usage:  
  config mclag member add  <domain_id>  <mclag-portchannel-names>

  Example:

  ```
  root@sonic:/home/admin# config mclag member add 10 PortChannel10,PortChannel20                
  ```



**MCLAG Member Deletion**

To delete mclag interface(s) from mclag domain user needs to use below command with following fields. To delete entire MCLAG domain including all mclag member interfaces use mclag domain deletion command

1) domain id

2) list of mclag portchannel names seperated by comma

*Note:- mclag interfaces needs to be portchannels. Multiple mclag interfaces can be added by providing all MCLAG Interfaces seperated by comma.*  

- Usage:  
  config mclag member del  <domain_id>  <mclag-portchannel-names>

  Example:

  ```
  root@sonic:/home/admin# config mclag member del 10 PortChannel10,PortChannel20                
  ```



**MCLAG Domain Deletion**

To delete mclag domain,  user needs to use below command with domain id field. MCLAG domain deletion takes care of deleting mclag interfaces from the MCLAG Domain before deleting the mclag domain. To delete individual mclag member interfaces use mclag member deletion command.

- Usage:  
  config mclag del  <domain_id> 

- Example:

  ```
  root@sonic:/home/admin# config mclag del 10
  MCLAG Domain delete takes care of deleting all associated MCLAG Interfaces
  ```



**Modifying MCLAG Keepalive/Session timer values**

To modify mclag keepalive timer or session timeout use the following commands. By default keepalive interval is 1 secs and session timeout is 15 secs

Take care of following requirement while configuring the timeout values

1) keepalive interval range is < 1 to 60 secs> - default 1secs

2) session timeout range is < 1 to 3600 secs> - default 15 secs

3) Session timeout should be at least 3 times greater than keepalive timer value. Also session timeout should be a multiple of keepalive timer value.

- Usage:  
  config mclag keepalive-interval  <domain_id>  <keepalive-interval in secs>

  Example:
  ```
  root@sonic:/home/admin# config mclag keepalive-interval 10 5            
  ```


- Usage:  
  config mclag session-timeout  <domain_id>  <session-timeout in secs>

  Example:

  ```
  root@sonic:/home/admin# config mclag session-timeout 10 20           
  ```

**MCLAG SYSTEM MAC Configuration**

Configuration is supported to add or delete MCLAG system MAC. By default MCLAG system MAC is not configured. User to use following MCLAG system-mac command for configuration.

- Usage:  
  
   config mclag system-mac {add | del} \<domain-id> <mac-address>
  
    - add  : Configure MCLAG system MAC.
    - del  : Remove MCLAG system MAC.
    - mac-address : MAC Address in xx:xx:xx:xx:xx:xx format

  Example:
  ```
  root@sonic:/home/admin# config mclag system-mac add 100 00:11:22:33:44:55
  
  root@sonic:/home/admin# config mclag system-mac del 100
  ```

**MCLAG GATEWAY MAC Configuration**

Configuration is supported to add or delete MCLAG gateway MAC. By default MCLAG gateway MAC is not configured. User to use following MCLAG gw-mac command for configuration.

- Usage:

   config mclag gw-mac {add | del} <mac-address>

    - add  : Configure MCLAG gateway MAC.
    - del  : Remove MCLAG gateway MAC.
    - mac-address : MAC Address in xx:xx:xx:xx:xx:xx format

  Example:
  ```
  root@sonic:/home/admin# config mclag gw-mac add 00:01:02:03:04:05

  root@sonic:/home/admin# config mclag gw-mac del 00:01:02:03:04:05
  ```

**MCLAG unique IP Configuration**

Configuration is supported to add or delete MCLAG unique IP. By default MCLAG unique IP is not configured. User to use following MCLAG unique IP command for configuration.

- Usage:

   config mclag  unique-ip {add | del} <Vlan-interface's>

    - add  : Configure MCLAG unique IP.
    - del  : Remove MCLAG unique IP.
    - Vlan-interface : VLAN interface for which unique IP can be configured.

  Example:
  ```
  root@sonic:/home/admin# config mclag unique-ip add Vlan100

  root@sonic:/home/admin# config mclag unique-ip del Vlan100
  ```

## MCLAG Show commands 

This section covers all mclagdctl show command for mclag

**mclag brief**

This commands displays brief information about the MCLAG domain configured in the system. 

Note:- There can be only mclag domain configured in the system.

- Usage:  
  mclagdctl dump state 


- Example:

  ```
  root@sonic:/home/admin# mclagdctl dump state
  The MCLAG's keepalive is: OK
  MCLAG info sync is: completed
  Domain id: 10
  Local Ip: 10.1.1.1
  Peer Ip: 10.1.1.2
  Peer Link Interface: PortChannel2
  Keepalive time: 1
  sesssion Timeout : 15
  Peer Link Mac: b8:6a:97:73:6c:96
  System Mac: 1c:ea:0b:06:9b:b5
  Mclag System Mac: 00:11:22:33:44:55
  Gateway Mac: 00:01:02:03:04:05
  Role: Active
  MCLAG Interface: PortChannel10
  ```

**mclag local interface state**

This commands displays information about local interface state. It shows all mclag portchannel interface states and it also shows underlying interface state of the mclag portchannel interfaces

- Usage:  
  mclagdctl dump portlist local -i  <domain_id>


- Example:

  ```
  root@sonic:/home/admin# mclagdctl dump  portlist local -i 10
  ------------------------------------------------------------
  Ifindex: 66
  Type: Ethernet
  PortName: Ethernet48
  State: Up
  ------------------------------------------------------------

  ------------------------------------------------------------
  Ifindex: 83
  Type: PortChannel
  PortName: PortChannel10
  MAC: 1c:ea:0b:06:9b:b5
  IPv4Address: 0.0.0.0
  Prefixlen: 32
  State: Up
  Admin State: Up
  IsL3Interface: No
  MemberPorts: Ethernet48
  PortchannelIsUp: 1
  IsIsolateWithPeerlink: Yes
  IsTrafficDisable: No
  VlanList:10,20
  ------------------------------------------------------------
  ```



**mclag peer interface state**

This commands displays information about peer interface state. This information is based on the peer information synced between mclag peer nodes.

- Usage:  
  mclagdctl dump portlist remote -i  <domain_id>


- Example:

  ```
  root@sonic:/home/admin# mclagdctl dump  portlist peer -i 10
  ------------------------------------------------------------
  Ifindex: 10
  Type: PortChannel
  PortName: PortChannel10
  MAC: 1c:ea:0b:06:9b:b5
  State: Up
  ------------------------------------------------------------
  ```



**mclag debug counters**

This commands displays information about peer interface state. This information is based on the peer information synced between mclag peer nodes.

- Usage:  
  mclagdctl dump debug counters 


- Example:

  ```
  root@sonic:/home/admin# mclagdctl -i 10 dump debug counters
  ICCP session down:  1
  Peer link down:     0
  Warmboot:           0

  ICCP to MclagSyncd      TX_OK       TX_ERROR
  ------------------      -----       --------
  PortIsolation           13          0
  MacLearnMode            1           0
  FlushFdb                1           0
  SetIfMac                0           0
  SetFdb                  226003      0
  SetL2mc                 0           0
  TrafficDistEnable       1           0
  TrafficDistDisable      1           0
  SetIccpState            2           0
  SetIccpRole             1           0
  SetSystemId             0           0
  DelIccpInfo             0           0
  SetRemoteIntfSts        9           0
  DelRemoteIntf           0           0

  MclagSyncd to          ICCP  RX_OK       RX_ERROR
  ------------------     -----------       --------
  FdbChange               161616           0
  CfgMclag                1                0
  CfgMclagIface           1                0

  ICCP to Peer   TX_OK        RX_OK        TX_ERROR    RX_ERROR
  ------------   -----        -----        --------    --------
  SysConfig      2            2            0           0
  AggrConfig     2            1            0           0
  AggrState      7            9            0           0 
  MacInfo        160896       142183       0           0
  ArpInfo        0            0            0           0
  L2mcInfo       0            0            0           0
  PoInfo         2            1            0           0
  PeerLinkInfo   2            2            0           0
  Heartbeat      2533         2533         0           0
  Nak            0            0            0           0
  SyncData       2            2            0           0
  SyncReq        1            0            0           0
  Warmboot       0            0            0           0
  IfUpAck        4            3            0           0            
  ```

**mclag debug mac**

This command is used to dump local and remote MAC addresses

- Usage:  
  mclagdctl dump mac 


- Example:
```
root@sonic:/home/admin# mclagdctl -i 2 dump mac
TYPE: S-STATIC, D-DYNAMIC; AGE: L-Local age, P-Peer age
No.   TYPE MAC                 VID  DEV                 ORIGIN-DEV          AGE
1     D    00:02:01:0a:00:01   54   Ethernet76          Ethernet76          P
2     D    00:02:01:0b:00:01   54   Ethernet76          Ethernet76          P
3     D    00:03:01:0a:00:01   54   Ethernet72          Ethernet76          L
4     D    00:03:01:0b:00:01   54   Ethernet72          Ethernet76          L
5     D    00:02:01:0a:00:01   55   Ethernet76          Ethernet76          P
6     D    00:02:01:0b:00:01   55   Ethernet76          Ethernet76          P
7     D    00:03:01:0a:00:01   55   Ethernet72          Ethernet76          L
8     D    00:03:01:0b:00:01   55   Ethernet72          Ethernet76          L
9     D    3c:2c:99:2d:7d:35   3509 PortChannel10       PortChannel10       L
10    D    3c:2c:99:2d:7d:35   3510 PortChannel10       PortChannel10       L
```

**mclag unique IP dump**

This commands displays information about Unique IP configuration.

- Usage:
  mclagdctl dump unique_ip -i  <domain_id>


- Example:

  ```
  root@sonic:/home/admin# mclagdctl dump unique_ip -i 10
  ------------------------------------------------------------
  Ifname              Active
  ------------------------------------------------------------
  Vlan100              Yes
  ------------------------------------------------------------
  ```


## MCLAG Config commands - KLISH

Following section covers Klick CLI config commands required for adding/deleting mclag domains and adding/delting mclag interface to domains. 



**MCLAG Domain configuration**

To configure MCLAG domain at global configuration level use below command.  

Config command enters MCLAG domain configuration mode and when any MCLAG attribute is configured then the MCLAG domain is configured in the system. 

Note:-*Only one MCLAG domain can be created in a device.* 

Usage:  
  ```
  sonic(config)#[no] mclag domain <domain-id>

  domain-id is a integer in the range of 1 to 4095
  ```

To configure MCLAG attributes enter MCLAG domain configuration mode and enter following command

Notes:- 
1) Peer link configuration is mandatory for L2 config this links is used to carray data traffic when MCLAG interface is down; optional - for L3 MCLAG, data forwarding depends on routing information. peer link is not required. Peer link can be either portchannel or Ethernet interface.
2) no form of each command would end up delete the config. 
3) to bring up MCLAG session between two MCLAG peer nodes configure mclag domain on both ends. one end's source/local ip address becomes peer address in other end and vice versa.

Usage:  

  ```
  sonic(config-mclag-domain)#[no] source-address  <source-ipv4-address>
  sonic(config-mclag-domain)#[no] peer-address <peer-ipv4-address>
  sonic(config-mclag-domain)#[no] peer-link  <peer-if-name>
  ```

- MCLAG domain add Example:

  ```
  sonic(config)# mclag domain 10
  sonic(config-mclag-domain-10)# source-ip 10.1.1.1
  sonic(config-mclag-domain-10)# peer-ip 10.1.1.2
  sonic(config-mclag-domain-10)# peer-link PortChannel 2
  sonic(config-mclag-domain-10)# session-timeout 20
  sonic(config-mclag-domain-10)# keepalive-interval 5      
  ```



**MCLAG  Keepalive/session timer configuration**

To configure/unconfigure MCLAG session keepalive interval and session timeout use following commands

Usage:  

```
sonic(config-mclag-domain)#[no] keepalive-interval <keepalive interval in secs>

sonic(config-mclag-domain)#[no] session-timeout <keepalive interval in secs>

- Keepalive interval in range of <1 to 60> seconds with default value of 1 seconds. 

- session-timeout in the range of <1 to 3600> seconds with default value of 15 seconds

- session-timeout should be atleast 3 times of keepalive interval. 

- session-timeout should be multiple of keepalive interval 
```

- Example:

  ```
  sonic(config)# mclag domain 10
  sonic(config-mclag-domain-10)# keepalive-interval 5
  sonic(config-mclag-domain-10)# session-timeout 20
  ```
  

**MCLAG  system MAC configuration**

Use below commands  to configure/unconfigure MCLAG system MAC 

Usage:  

   ```
   sonic(config-mclag-domain)#[no] system-mac <mac-address>
   - mac-address : MAC Address in xx:xx:xx:xx:xx:xx format
   ```

- Example:

  ```
  sonic(config)# mclag domain 10
  sonic(config-mclag-domain-10)# mclag-system-mac 00:11:22:33:44:55
  sonic(config-mclag-domain-10)# no mclag-system-mac
  ```

**MCLAG Interface add/delete **

To add or delete MCLAG interface use following command. MCLAG interfaces can only be portchannels. Configure MCLAG interface under the portchannel interface configuration mode.

Usage:
```
sonic(config-if-PortChannelx)# [no] mclag [domain-id]
```

-  MCLAG interface add Example:
```
sonic(config)# interface PortChannel 10
sonic(conf-if-po10)# mclag 10  
```

- MCLAG interface delete Example:
```
sonic(config)# interface PortChannel 10
sonic(conf-if-po10)#no mclag 10  
```

**MCLAG Domain deletion**

To delete MCLAG domain, use the below command. 

Note:- first delete the mclag interfaces using mclag interface deletion command for all mclag interfaces associated with this domain before deleting the MCLAG Domain. 

Usage:  

```
sonic(config)#[no] mclag domain <domain-id>

domain-id is a integer in the range of 1 to 4095
```

-  Example:

```
sonic(config)# no mclag domain 10    
```

**Enabling Delay Restore under MCLAG Domain**

Configure delay restore timer in seconds under MCLAG domain configuration

```
sonic(config)# mclag domain 5
sonic-cli(config-mclag-domain-5)# delay-restore <seconds>
```

**MCLAG Gateway MAC configuration**

Use below commands to configure/unconfigure MCLAG Gateway MAC

Usage:

   ```
   sonic(config)#[no] gateway-mac <mac-address>
   - mac-address : MAC Address in xx:xx:xx:xx:xx:xx format
   ```

- Example:

  ```
  sonic(config)# mclag gateway-mac 00:01:02:03:04:05
  sonic(config)# [no] mclag gateway-mac 00:01:02:03:04:05
  ```

**MCLAG Separate IP configuration**

Use below commands to configure/unconfigure MCLAG Separate IP

Usage:

   ```
   sonic(conf-if-Vlan<id>)# [no] mclag-separate-ip
   ```

- Example:

  ```
  sonic(config)# interface Vlan 100
  sonic(conf-if-Vlan100)# mclag-separate-ip
  sonic(conf-if-Vlan100)# [no] mclag-separate-ip
  ```

## MCLAG Show commands - KLISH

This section covers all mclag show commands. Following MCLAG commands are through Klish CLI interface

**show mclag brief **

This commands displays brief information about the MCLAG domain configured in the system. It display MCLAG domain information and also gives local/remote port status information for all mclag interfaces configured in the mclag domain.

Note:- There can be only mclag domain configured in the system.

- Usage:  
  show mclag brief


- Example:

  ```
  Sonic#show mclag brief
  
      Domain ID                   : 10
      Role                        : Active
      Session Status              : Up
      Peer Link Status            : Up
      Source Address              : 10.1.1.1
      Peer Address                : 10.1.1.2
      Peer Link                   : PortChannel2
      Keepalive Interval          : 1 secs
      Session Timeout             : 15 secs
      Delay Restore               : 300 secs (12 secs left on timer)
      System MAC                  : b8:6a:97:73:6c:96
      MCLAG System MAC            : 00:11:22:33:44:55
      Gateway Mac                 : 00:01:02:03:04:05
      Number of MCLAG Interfaces  : 2
      MCLAG Interface             Local/Remote Status
      PortChannel10               Up/Up
      PortChannel20               Up/Up
  ```

**show mclag interface **

This commands displays information about a given mclag interface of given mclag domain. 

note:- this commands take only portchannel id and not portchannel name

- Usage:  
  show mclag interface <domain id> <portchannel id>


- Example:

  ```
  sonic# show mclag interface 10 10

  Local/Remote Status  : up/up
  TrafficDisable       : No
  IsolateWithPeerLink  : Yes
  ```

**show mclag separate-ip-interfaces **

This commands displays information about mclag separate ip configuration.

- Usage:
  show mclag separate-ip-interfaces

- Example:

  ```
  sonic# show mclag separate-ip-interfaces

  Interface Name
  ==============
  Vlan100
  ==============
  Total count :    1
  ==============
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#mclag-configuration-and-show)

# Mirroring Configuration And Show

## Mirroring Show command

**show mirror_session**  

This command displays all the mirror sessions that are configured. 

- Usage:  
  show mirror_session [session_name]


- Example:
  ```
  admin@sonic:~$ show mirror_session
  ERSPAN Sessions
  ---------------------------------------------------------------------------------------------------------
  Name      Status    SRC IP    DST IP       GRE       DSCP    TTL    Queue  Policer    Monitor Port        SRC Port    Direction
  --------  --------  --------  -----------  ------  ------  -----  -------  ---------  ------------------  ----------  -----------
  mrr_ers   active  1.2.3.4   20.21.22.23  0x6558       8    100        0             oid:0x7f620b159b00  Ethernet0   both

  SPAN Sessions
  --------------------------------------------------------
  Name      Status    DST Port    SRC Port    Direction
  --------  --------  ----------  ----------  -----------
  mrr_span  active    Ethernet4   Ethernet8   rx
  ```

## Mirroring Config command

This command is used to add or remove mirroring sessions. Mirror session is identified by "session_name". 

While adding a new SPAN session, users need to configure the following fields that are used while forwarding the mirrored packets.
1) Destination Port, 
2) optional - Source Port, 
3) optional - Direction. Supports rx/tx/both directions

Note: Following are some of known restrictions on mirror configuration.
```
Destination port cant have any L2 config. If the port has any L2 config, then mirror command will fail.
Maximum 4 unique destination port mirror sessions and 8 ERSPAN sessions can be supported.
Maximum 8 mirror sessions can be active (ERSPAN + SPAN)
Single destination port can be shared across any number of sessions.
Once Maximum sessions are active, mirror command will fail with appropriate error.
Egress CPU TX mirroring is not supported.
```

While adding a new ERSPAN session, users need to configure the following fields that are used while forwarding the mirrored packets.

1) source IP address, 
2) destination IP address, 
3) DSCP (QoS) value with which mirrored packets are forwarded
4) TTL value
5) optional - GRE Type in case if user wants to send the packet via GRE tunnel. GRE type could be anything; it could also be left as empty; by default, it is 0x8949 for Mellanox; and 0x88be for the rest of the chips.
6) optional - Queue in which packets shall be sent out of the device. Valid values 0 to 7 for most of the devices. Users need to know their device and the number of queues supported in that device.
7) optional - Source Port, 
8) optional - Direction. Supports rx/tx/both directions

  - Usage:  
    config mirror_session add span <session_name> <dst_port>  
                                      [src_port] [direction]  

    config mirror_session add erspan <session_name> <src_ip> <dst_ip> <dscp> <ttl>  
                                      [gre_type] [queue] [src_port] [direction]  

- Example:
  ```
    root@sonic:/home/admin# config mirror_session add erspan mrr_erspan 1.2.3.4 20.21.22.23 8 100 0x6558 0 Ethernet0 both
    root@sonic:/home/admin# config mirror_session add span mrr_span Ethernet4 Ethernet8 rx
    root@sonic:/home/admin# config mirror_session add erspan mrr_erspan_1 1.2.3.5 20.21.22.24 8 100 0x6558 0
    root@sonic:/home/admin# config mirror_session add span mrr_span_1 Ethernet16
    root@sonic:/home/admin# show mirror_session
    ERSPAN Sessions
    -------------------------------------------------------------------------------------------------------------------------------------
    Name          Status    SRC IP    DST IP       GRE       DSCP    TTL    Queue  Policer    Monitor Port        SRC Port    Direction
    ------------  --------  --------  -----------  ------  ------  -----  -------  ---------  ------------------  ----------  -----------
    mrr_erspan_1  active  1.2.3.5   20.21.22.24  0x6558       8    100        0             oid:0x7f620b159b00
    mrr_erspan      active  1.2.3.4   20.21.22.23  0x6558       8    100        0             oid:0x7f620b159b00  Ethernet0   both
    
    SPAN Sessions
    --------------------------------------------------------
    Name        Status    DST Port    SRC Port    Direction
    ----------  --------  ----------  ----------  ----------
    mrr_span    active    Ethernet4   Ethernet8   rx
    mrr_span_1  active    Ethernet16

  ```


## Mirroring configuration commands - KLISH

The details of these commands are available in KLISH CLI reference guide.

 **switch(config-mirror-<session-name>)# [no] destination <dest_ifName> [source <src_ifName> direction <rx/tx/both>]**  
Create SPAN mirror session to mirror source port traffic to destination port.  
The source port and direction are optional and allows destination only mirror session for ACL programming.  

 **switch(config-mirror-<session-name>)# [no] destination erspan src_ip <src_ip> dst_ip <dst_ip> dscp < dscp > ttl < ttl > [ gre < gre >] [queue <queue>] [source <src_ifName> direction <rx/tx>]**  
Create ERSPAN mirror session to mirror source port traffic to destination host.  

 **switch(config)# [no] mirror-session <session-name>**   
Remove mirror session configuration.  


## Mirroring show commands - KLISH

**show mirror-session**

Example output:
```
sonic# show mirror-session
ERSPAN Sessions
---------------------------------------------------------------------------------------------------------------
Name      Status    SRC-IP          DST-IP          GRE   DSCP  TTL   Queue   Policer   SRC-Port        Direction
---------------------------------------------------------------------------------------------------------------
mrr_1     active    1.2.3.4         20.21.22.23     0x65588     100   0                 Ethernet0       both
mrr_2     active    1.2.3.5         20.21.22.24     0x65588     100   0
SPAN Sessions
---------------------------------------------------------------
Name      Status    DST-Port        SRC-Port        Direction
---------------------------------------------------------------
mrr_sp    active    Ethernet4       Ethernet8       rx
mrr_sp_1  active    Ethernet16
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Mirroring-Configuration-And-Show)

# NAT Commands

This section explains all the NAT show, configuation and clear commands that are supported in SONiC.

## NAT show commands 

**show nat config [static | pool | bindings | zones]**  

This command displays the configuration of the NAT module. 

- Usage:  
  show nat config

- Example:

```
root@sonic:/home/admin# show nat config

Global Values

Admin Mode     : enabled
Global Timeout : 600 secs
TCP Timeout    : 86400 secs
UDP Timeout    : 300 secs  

Static Entries

Nat Type    IP Protocol    Global IP    Global Port    Local IP    Local Port  Twice-NAT Id
----------  -------------  -----------  -------------  ----------  ----------  ------------
snat        all            112.0.0.2    ---            111.0.0.3   ---               1

Pool Entries

Pool Name    Global IP Range    Global Port Range
-----------  -----------------  -------------------
nat1         2.0.0.5            10-200

NAT Bindings

Binding Name    Pool Name    Access-List    Nat Type    Twice-NAT Id
--------------  -----------  -------------  ----------  --------------
bind1           nat1                        snat        ---

NAT Zones

Port         Zone
---------  ------
Ethernet0       1
Ethernet2       2
```

**show nat statistics**  

This command displays the statistics per NAT entry.

- Usage:  
  show nat statistics

- Example:

```
root@sonic:/home/admin#show nat statistics

Protocol Source           Destination          Packets          Bytes
-------- ---------        --------------       -----------    ----------
all      10.0.0.1         ---                          802      1009280     
all      10.0.0.2         ---                           23         5590            
tcp      20.0.0.1:4500    ---                          110        12460         
udp      20.0.0.1:4000    ---                         1156       789028            
tcp      20.0.0.1:6000    ---                           30        34800         
tcp      20.0.0.1:5000    65.55.42.1:2000              128       110204     
tcp      20.0.0.1:5500    65.55.42.1:2000                8         3806
```

**show nat translations [count]**  

This command displays all the NAT translations entries.

- Usage:  
  show nat translations

- Example:

```
#show nat translations

Static NAT Entries        ................. 4
Static NAPT Entries       ................. 2
Dynamic NAT Entries       ................. 0
Dynamic NAPT Entries      ................. 4
Static Twice NAT Entries  ................. 0
Static Twice NAPT Entries ................. 2
Total Entries             ................. 12

Protocol Source           Destination       Translated Source  Translated Destination
-------- ---------        --------------    -----------------  ----------------------
all      10.0.0.1         ---               65.55.42.2         ---
all      ---              65.55.42.2        ---                10.0.0.1
all      10.0.0.2         ---               65.55.42.3         ---
all      ---              65.55.42.3        ---                10.0.0.2
tcp      20.0.0.1:4500    ---               65.55.42.1:2000    ---
tcp      ---              65.55.42.1:2000   ---                20.0.0.1:4500
udp      20.0.0.1:4000    ---               65.55.42.1:1030    ---
udp      ---              65.55.42.1:1030   ---                20.0.0.1:4000
tcp      20.0.0.1:6000    ---               65.55.42.1:1024    ---
tcp      ---              65.55.42.1:1024   ---                20.0.0.1:6000
tcp      20.0.0.1:5000    65.55.42.1:2000   65.55.42.1:1025    20.0.0.1:4500
tcp      20.0.0.1:4500    65.55.42.1:1025   65.55.42.1:2000    20.0.0.1:5000
tcp      20.0.0.1:5500    65.55.42.1:2000   65.55.42.1:1026    20.0.0.1:4500
tcp      20.0.0.1:4500    65.55.42.1:1026   65.55.42.1:2000    20.0.0.1:5500

Router#show nat translations count

Static NAT Entries        ................. 4
Static NAPT Entries       ................. 2
Dynamic NAT Entries       ................. 0
Dynamic NAPT Entries      ................. 4
Static Twice NAT Entries  ................. 0
Static Twice NAPT Entries ................. 2
Total Entries             ................. 12
```

## NAT configuration commands 
This sub-section explains the list of configuration options available for NAT module.

**config nat add static basic {global-ip} {local-ip} -nat_type {snat/dnat} -twice_nat_id {value}**

Use this command to add basic static NAT entry

- Example:

```
root@sonic:/home/admin# config nat add static basic 65.54.0.1 10.0.0.1
root@sonic:/home/admin# config nat add static basic 112.0.0.1 111.0.0.2 -nat_type dnat -twice_nat_id 1
root@sonic:/home/admin# config nat add static basic 112.0.0.2 111.0.0.3 -nat_type snat -twice_nat_id 1
```

**config nat remove static basic {global-ip} {local-ip}**

Use this command to remove basic static NAT entry

- Example:
```
root@sonic:/home/admin# config nat remove static basic 65.54.0.1 10.0.0.1
```

**config nat add static {tcp | udp} {global-ip} {global-port} {local-ip} {local-port} -nat_type {snat/dnat} -twice_nat_id {value}**

Use this command to add a static NAPT entry

- Example:
```
root@sonic:/home/admin# config nat add static udp 112.0.0.1 250 10.0.0.1 111 
```

**config nat remove static {tcp | udp} {global-ip} {global-port} {local-ip} {local-port}**

Use this command to remove a static NAPT entry

- Example:
```
root@sonic:/home/admin# config nat remove static udp 112.0.0.1 250 10.0.0.1 111
```

**config nat remove static all**

Use this command to remove all the static NAT/NAPT configuration

**config nat add pool {pool-name} {global-ip-range} {global-port-range}**

Use this command to create a NAT pool

- Example:
```
root@sonic:/home/admin# config nat add pool nat1 2.0.0.5 10-200
```

**config nat remove pool {pool-name}**

Use this command to remove a NAT pool

- Example:
```
root@sonic:/home/admin# config nat remove pool nat1 
```

**config nat remove pools**

Use this command to remove all the NAT pool configuration

**config nat add binding {binding-name} {pool-name} {acl-name} -nat_type {snat/dnat} -twice_nat_id {value}**

Create a binding between an ACL and a NAT pool

- Example:
```
root@sonic:/home/admin# config nat add binding bind1 nat1              
```

**config nat remove binding {binding-name}**

Remove a binding between an ACL and a NAT pool

**config nat remove bindings**

Use this command to remove all the NAT binding configuration

**config nat add interface {interface-name} {-nat_zone {zone-value}}**

Use this command to configure the NAT zone value on an interface

- Example:
```
root@sonic:/home/admin# config nat add interface Ethernet1 -nat_zone 2
root@sonic:/home/admin# config nat add interface Ethernet3 -nat_zone 1
```

**config nat remove interface {interface-name}**

Use this command to remove the NAT configuration on the interface

**config nat remove interfaces**

Use this command to remove the NAT configuration on all the L3 interfaces

**config nat set timeout {secs}**

Use this command to configure the Basic NAT entry aging timeout in seconds.

**config nat reset timeout**

Use this command to reset the Basic NAT entry aging timeout to default value.

**config nat feature {enable/disable}**

Use this command to enable or disable the NAT feature.

**config nat set udp-timeout {secs}**

Use this command to configure the UDP NAT entry aging timeout in seconds.

**config nat reset udp-timeout**

Use this command to reset the UDP NAT entry aging timeout to default value.

**config nat set tcp-timeout {secs}**

Use this command to configure the TCP NAT entry aging timeout in seconds.

**config nat reset tcp-timeout**

Use this command to reset the TCP NAT entry aging timeout to default value.

## NAT clear commands 

**sonic-clear nat statistics**

Use this command to clear all NAT statistics.
- Example:

```
root@sonic:/home/admin# sonic-clear nat statistics  

NAT statistics are cleared.
```

**sonic-clear nat translations**

Use this command to clear all dynamic NAT translations.
- Example:

```
root@sonic:/home/admin# sonic-clear nat translations        

Dynamic NAT entries are cleared.
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#nat-commands)


# NTP

## NTP show command

**show ntp**  

This command displays a list of NTP peers known to the server as well as a summary of their state.

  - Usage:  
    show ntp


- Example:

```
  admin@sonic:~$ show ntp
  	 remote           refid      st t when poll reach   delay   offset  jitter
  ==============================================================================
   23.92.29.245    .XFAC.          16 u    - 1024    0    0.000    0.000   0.000
  *204.2.134.164   46.233.231.73    2 u  916 1024  377    3.079    0.394   0.128
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#NTP)

# IGMP Snooping Commands

This section explains all the IGMP Snooping configuration and show commands that are supported in SONiC.


## IGMP Snooping configuration commands 
This section explains the list of configuration options available for IGMP snooping. Use `no` version of these commands to remove non-default values configured. 

- Usage:
```
ip igmp snooping { [ querier ] | [ fast-leave ] | { [ query-interval ] <query-interval-val> } | { [ last-member-query-interval ] <last-mem-query-interval-val> } | { [ query-max-response-time ] <query-max-response-val> } | { [ version ] <igmps-version-val> } | { [ mrouter ] { interface <mrouter-if-name> } } | { [ static-group ] { <group-addr> { interface <grp-if-name> } } } }
no ip igmp snooping { [ querier ] | [ fast-leave ] | [ query-interval ] | [ last-member-query-interval ] | [ query-max-response-time ] | [ version ] | { [ mrouter ] { interface <mrouter-if-name> } } | { [ static-group ] { <group-addr> { interface <grp-if-name> } } } }
```

**ip igmp snooping** 
Use this command to configure IGMP Snooping on a VLAN.
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping
```

**ip igmp snooping { [ querier ]}**
Use this command to enable IGMP querier on VLAN, by default querier is disabled.
```
sonic(conf-if-Vlan)# ip igmp snooping querier
```

**ip igmp snooping {[ fast-leave ]}**
Use this command to enable IGMP fast-leave on VLAN, by default fast-leave is disabled.
```
sonic(conf-if-Vlan)# ip igmp snooping fast-leave
```

**ip igmp snooping { [ query-interval ] \<query-interval-val> }**
Use this command to configure IGMP query-interval, default interval is 125 seconds, range is from 1 to 18000 seconds.
```
sonic(conf-if-Vlan)# ip igmp snooping query-interval 20
```

**ip igmp snooping { [ last-member-query-interval ] \<last-mem-query-interval-val> }**
Use this command to configure  last member query interval, default is 1000ms, the valid range is from 100ms to 25500ms.
```
sonic(conf-if-Vlan200)# ip igmp snooping last-member-query-interval 2000
```

**ip igmp snooping { [ query-max-response-time ] \<query-max-response-val> }**
Use this command to configure max response interval, default is 10s, the valid range is from 1 to 25s.
```
sonic(conf-if-Vlan)# ip igmp snooping query-max-response-time 12
```

**ip igmp snooping { [ version ] \<igmps-version-val> }**
Use this command to configure IGMP version, default is Version2, the valid range is from 1 to 3. 
```
sonic(conf-if-Vlan)# ip igmp snooping version 3
```

**ip igmp snooping { [ mrouter ] { interface \<mrouter-if-name> } }**
Use this command to configure static multicast router(mrouter) port.

```
sonic(conf-if-Vlan2)# ip igmp snooping mrouter interface Ethernet4
```

**ip igmp snooping { [ static-group ] { \<group-addr> { interface \<grp-if-name> } } }**
Use this command to configure static multicast group.
```
sonic(conf-if-Vlan)# ip igmp snooping static-group 225.0.0.1 interface PortChannel2
```

## IGMP Snooping show commands

**show ip igmp snooping { [ vlan ] \<vlan-id> }**  

This commands displays IGMP snooping configuration across all the VLANs or specified VLAN.


- Usage:  
  show ip igmp snooping

- Example:
```
sonic# show ip igmp snooping
Vlan ID: 100
Querier: Disabled
IGMP Operation mode: IGMPv1
Is Fast-Leave Enabled: Disabled
Query interval: 125
Last Member Query Interval: 1000
Max Response time: 10

Vlan ID: 200
Querier: Enabled
IGMP Operation mode: IGMPv2
Is Fast-Leave Enabled: Disabled
Query interval: 125
Last Member Query Interval: 1000
Max Response time: 10

Vlan ID: 300
Querier: Enabled
IGMP Operation mode: IGMPv3
Is Fast-Leave Enabled: Disabled
Query interval: 20
Last Member Query Interval: 1000
Max Response time: 10


sonic# show ip igmp snooping vlan 200
Vlan ID: 200
Querier: Enabled
IGMP Operation mode: IGMPv2
Is Fast-Leave Enabled: Disabled
Query interval: 125
Last Member Query Interval: 1000
Max Response time: 10

```
**show ip igmp snooping  groups {[ vlan ] \<vlan-id>}**  

This commands displays IGMP snooping groups learnt  across all the VLANs or specified VLAN.

- Usage:  
  show ip igmp snooping

- Example:

```
sonic# show ip igmp snooping groups
Vlan ID: 100
-----------
1 ( *, 225.1.1.1)
  Outgoing Ports: Ethernet4,PortChannel3
2 ( *, 225.1.1.2)
  Outgoing Ports: Ethernet8
Total number of entries: 2

Vlan ID : 300
-------------
1 (100.10.2.3, 226.0.0.1 )
  Outgoing Ports: Ethernet8,Portchannel2
Total number of entries: 1


sonic# show ip igmp snooping groups vlan 100
Vlan ID: 100
-----------
1 ( *, 225.1.1.1)
  Outgoing Ports: Ethernet4, PortChannel3
2 ( *, 225.1.1.2)
  Outgoing Ports: Ethernet8
Total number of entries: 2
```
Go Back To [Beginning of the document](#table-of-contents) or [Beginning of this section](#igmp-snooping-commands)


# KDUMP Configuration and Show Commands

This section provides details about various configuration and show commands for the kernel core dump feaure supported in SONiC.

## KDUMP Configuration commands
This sub-section explains the list of the configuration options available for using the kernel core dump feature. Some of these configuration options in turn change the configuration files used by the *kdump-tools* service which provides the actual kdump functionality.

All changes done to kdump configuration are automatically saved to the startup configration file, */etc/sonic/config_db.json*

**config kdump enable**

Use this command to administratively enable the capability of kernel core file generation.

- Usage:
       config kdump enable
- Example:
```
root@sonic:/home/admin# config kdump enable
```

Since this command requires changing the kernel parameters to specify the amount of memory reserved for the capture kernel (the kernel parameters which are exported through */proc/cmdline*), a reboot is necessary. By default, the command displays a message showing that kdump functionality will be either enabled or disabled following the next reboot. The optional parameter *-y* allows to automatically perform the reboot without user requiring to execute a reboot command.

**config kdump disable**

Use this command to administratively disable the capability of kernel core file generation.

- Usage:
         config kdump disable
- Example:
```
root@sonic:/home/admin# config kdump disable
```

By default, kdump is administratively disabled.

**config kdump memory string**

Use this command to set the amount of memory reserved for the capture kernel called with kexec.

- Usage: 
         config kdump memory <memory-string>
- Example:
```
root@sonic:/home/admin# config kdump memory 0M-2G:256M,2G-4G:320M,4G-8G:512M,8G-:1024M
```

The amount of memory should always be specified in MB. For instance, the command *config kdump memory 512M* allocates 512MB for the capture kernel. If the memory amount is changed and kdump is currently enabled, the command displays a message showing that the newly provided memory size will be used following a reboot.

If the amount of memory is set too low, kdump will not be able to either store the capture kernel and initramfs image, or store the core dump information. If this value is changed, the user needs to choose a value that has been verified to to be sufficient.

The default value for the memory allocated for the capture memory is set to:
*0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M*

The above memory setting translates to:

| RAM size | crashkernel<br>parameter |
| -------- | ------------------------ |
| <= 2GB   | 384 MB                   |
| <= 4GB   | 512 MB                   |
| <= 8GB   | 576 MB                   |
| > 8GB    | 640 MB                   |

For instance, if the system has 8GB of RAM, the kernel will allocate 576MB of memory for the capture kernel.

**config kdump num_dumps**

Use this command to control the number of kernel core files that can be stored locally on the disk. 

- Usage: 
      config kdump num_dumps <number>
- Example:
```
root@sonic:/home/admin# config kdump num_dumps 4
```

When a kernel core file is generated, the kdump service stores the generated core file in  compressed form. The number of the kernel core files that can be stored can be configured through this command.

If there are already N kernel core dumps files stored locally and a new value which is less than N is specified, existing core files files are deleted on the local storage to satisfy the new  configuration. For instance, if there are already 6 kernel core dumps saved, and the user specifies keeping only 3 kernel core dump files, 4 of the oldest files are removed from the local storage to make room for 2 existing dump files plus the new core dump file to be generated. It is to be noted that this file pruning only happens when a new kernel crash occurs.

The default value is 3 and the acceptable range is from 1 to 9.

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#kdump-configuration-commands)

## KDUMP show commands

This sub-section lists all the show commands for the KDUMP configuration and status. Subsequent sections explain each of these commands in detail.

**show kdump status**

This command displays complete information about the configuration settings of the kdump feature, its operational state and the list of kernel core files stored locally on the disk:
- Usage: 
       show kdump status

- Example:

```
root@sonic:/home/admin# show kdump status
Kdump Administrative Mode:  Enabled
Kdump Operational State:    Ready
Memory Reserved: 0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M
Maximum number of Kernel Core files Stored: 3

Record Key           Filename
-------------------------------------------------------------
     1 202002182141 /var/crash/202002182141/dmesg.202002182141
                    /var/crash/202002182141/kdump.202002182141
     2 202002182133 /var/crash/202002182133/dmesg.202002182133
                    /var/crash/202002182133/kdump.202002182133
     3 202002182123 /var/crash/202002182123/dmesg.202002182123
                    /var/crash/202002182123/kdump.202002182123
```

- Description:
   - **Kdump Adminstrative Mode**: Displays if the kdump feature is enabled or disabled
   - **Kdump Operational State**: When kdump is enabled, display whether the kdump feature is operationally ready to perform a kdump in the event of a kernel crash
   - **Memory Reserved**: Displays the amount of memory reserved for the kdump capture kernel
   - **Maximum number of kernel core files stored**: Displays the allowed maximum number of kernel core files that are stored locally on the disk
   - **List of kernel core files:** This command shows the kernel core dump files currently saved on the local storage. They are displayed in reverse chronological order allow with a key value which specifies exact date and time when the kernel crash has happened and the file path where the kernel core file is stored at

**show kdump files**

This command is used to display the kernel core files stored locally.

- Usage: 
      show kdump log <record> <number of lines>
- Example:
```
root@sonic:/home/admin# show kdump files
Record Key           Filename
-------------------------------------------------------------
     1 202002182141 /var/crash/202002182141/dmesg.202002182141
                    /var/crash/202002182141/kdump.202002182141
     2 202002182133 /var/crash/202002182133/dmesg.202002182133
                    /var/crash/202002182133/kdump.202002182133
     3 202002182123 /var/crash/202002182123/dmesg.202002182123
                    /var/crash/202002182123/kdump.202002182123
root@sonic:/home/admin#
```

**show kdump log**

This command is used to display a specified number of lines of the kernel log ring buffer which are extracted from the stored kernel core file. When no value is not provided, a default value of 75 lines is used. The kernel log buffer typically contains the kernel back-trace which provides information about what event caused the kernel crash. The corresponding kernel core file can be specified by using either the Record number or the the Key name. The filenames indicating where the kernel core file is stored locally on the disk are also listed.

- Usage: 
      show kdump log <record> <number of lines>
- Example:

```
root@sonic:/home/admin# show kdump log 1 10
File: /var/crash/202002182133/dmesg.202002182133
[  520.658277]  [<ffffffffbc02b0db>] ? write_sysrq_trigger+0x2b/0x30
[  520.664436]  [<ffffffffbbe7ab90>] ? proc_reg_write+0x40/0x70
[  520.670163]  [<ffffffffbbe0c430>] ? vfs_write+0xb0/0x190
[  520.675540]  [<ffffffffbbe0d8ca>] ? SyS_write+0x5a/0xd0
[  520.680826]  [<ffffffffbbc03b7d>] ? do_syscall_64+0x8d/0x100
[  520.686547]  [<ffffffffbc20484e>] ? entry_SYSCALL_64_after_swapgs+0x58/0xc6
[  520.693568] Code: 41 5c 41 5d 41 5e 41 5f e9 6c 2f cf ff 66 2e 0f 1f 84 00 00 00 00 00 66 90 0f 1f 44 00 00 c7 05 29 28 a8 00 01 00 00 00 0f ae f8 <c6> 04 25 00 00 00 00 01 c3 0f 1f 44 00 00 0f 1f 44 00 00 53 8d
[  520.716174] RIP  [<ffffffffbc02a562>] sysrq_handle_crash+0x12/0x20
[  520.722470]  RSP <ffffa8ce415e7e78>
[  520.726018] CR2: 0000000000000000
```

**show kdump memory**
This command is used to display the amount of memory that is reserved for the catpure kernel to be used in the event of a kernel crash and the subsequent kernel core file generation.  

- Usage: 
      show kdump memory
- Example:


```
root@sonic:/home/admin# show kdump memory
Memory Reserved: 0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M
```

**show kdump num_dumps**

This command is used to display the maximum number of kernel core files that can be stored locally on disk.  

- Usage: 
      show kdump num_dumps

- Example:
```
root@sonic:/home/admin# show kdump num_dumps
Maximum number of Kernel Core files Stored: 3
root@sonic:/home/admin#
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#kdump-show-commands)

# Platform Specific Commands

There are few commands that are platform specific. Mellanox has used this feature and implemented Mellanox specific commands as follows.

**show platform mlnx sniffer**  

This command shows the SDK sniffer status

  - Usage:  
    show platform mlnx sniffer


- Example:

```
  admin@arc-switch1004:~$ show platform mlnx sniffer
  sdk sniffer is disabled
```

**show platform mlnx sniffer**  

Another show command available on ?show platform mlnx? which is the issu status.
This means if ISSU is enabled on this SKU or not. A warm boot command can be executed only when ISSU is enabled on the SKU.

  - Usage:  
    show platform mlnx issu


  - Example:

  ```
  admin@arc-switch1004:~$ show platform mlnx issu
  ISSU is enabled
  ```

In the case ISSU is disabled and warm-boot is called, the user will get a notification message explaining that the command cannot be invoked.

Example:

  ```
admin@arc-switch1038:~$ sudo warm-reboot
ISSU is not enabled on this HWSKU
Warm reboot is not supported
  ```

**config platform mlnx**  
This command is valid only on mellanox devices. The sub-commands for "config platform" gets populated only on mellanox platforms.
There are no other subcommands on non-Mellanox devices and hence this command appears empty and useless in other platforms. 
The platform mellanox command currently includes a single sub command which is the SDK sniffer.
The SDK sniffer is a troubleshooting tool which records the RPC calls from the Mellanox SDK user API library to the sx_sdk task into a .pcap file. 
This .pcap file can be replayed afterward to get the exact same configuration state on SDK and FW to reproduce and investigate issues.

A new folder will be created to store the sniffer files: "/var/log/mellanox/sniffer/". The result file will be stored in a .pcap file, which includes a time stamp of the starting time in the file name, for example, "sx_sdk_sniffer_20180224081306.pcap"
In order to have a complete .pcap file with all the RPC calls, the user should disable the SDK sniffer. Swss service will be restarted and no capturing is taken place from that moment.
It is recommended to review the .pcap file while sniffing is disabled.
Once SDK sniffer is enabled/disabled, the user is requested to approve that swss service will be restarted. 
For example: To change SDK sniffer status, swss service will be restarted, continue? [y/N]:
In order to avoid that confirmation the -y / --yes option should be used.

  - Usage:  
    config platform mlnx sniffer sdk [OPTIONS] OPTION  
    Options:
    -y, --yes
    --help     Show this message and exit.

  - Example:

```
  admin@arc-switch1038:~$ config platform mlnx sniffer sdk
  To change SDK sniffer status, swss service will be restarted, continue? [y/N]: y
  NOTE: In order to avoid that confirmation the -y / --yes option should be used.
```

# PFC Configuration And Show Commands
This section explains Priority Flow Control configuation and show options that are supported in SONiC.

## PFC config commands
**PFC config**

This command enables/disables the asymmmetric mode on an interface or all interfaces.
- Usage:<br>
    pfc config asymmetric <on|off> {<interface_name>|all}

- Example:  
  ```
  admin@sonic:~$ sudo pfc config asymmetric on Ethernet9
  admin@sonic:~$ sudo pfc config asymmetric on all
  admin@sonic:~$ sudo pfc config asymmetric off Ethernet8

  ```

## PFC show commands
This command displays the configured PFC mode on an interface or all interfaces. By default all the interfaces are configured in symmetric PFC mode.
- Usage:<br>
    pfc show asymmetric <interface_name>

- Example:  

 ```
  admin@sonic:~$ sudo pfc show asymmetric Ethernet9


  Interface    Asymmetric
  -----------  ------------
  Ethernet9    on


  admin@sonic:~$ sudo pfc show asymmetric 


  Interface    Asymmetric
  -----------  ------------
  Ethernet0    N/A
  Ethernet4    on
  Ethernet5    N/A
  Ethernet6    N/A
  Ethernet7    N/A
  Ethernet8    off
  Ethernet9    on
  Ethernet10   N/A
  Ethernet11   N/A
  Ethernet12   on
  Ethernet16   N/A
  Ethernet20   N/A
  Ethernet24   N/A
  Ethernet28   N/A
  Ethernet32   N/A
  Ethernet36   N/A
  Ethernet40   N/A
  Ethernet44   N/A
  Ethernet48   N/A
  Ethernet52   N/A
  Ethernet56   N/A
  Ethernet60   N/A
  Ethernet64   N/A
  Ethernet68   N/A
  Ethernet72   N/A
  Ethernet76   N/A
  Ethernet80   N/A
  Ethernet84   N/A
  Ethernet88   N/A
  Ethernet92   N/A
  Ethernet96   N/A
  Ethernet100  N/A
 ```

## pfcstat command
This command displays/removes the Pause Frames statistics for Rx and Tx priority queues of all interfaces.

- Usage:<br>
  pfcstat [-h] [-v] [-c] [-d]

  Display the pfc counters

  optional arguments:<br>
    -h, --help     show this help message and exit<br>
    -v, --version  show program's version number and exit<br>
    -c, --clear    Clear previous stats and save new ones<br>
    -d, --delete   Delete saved stats<br>

- Example:  

  ```
    admin@sonic:~$ sudo pfcstat 
      Port Rx    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
   -----------  ------  ------  ------  ------  ------  ------  ------  ------
          CPU       0       0       0       0       0       0       0       0
    Ethernet0       0       0       0       0       0       0       0       0
    Ethernet4       0       0       0       0       0       0       0       0
    Ethernet5       0       0       0       0       0       0       0       0
    Ethernet6       0       0       0       0       0       0       0       0
    Ethernet7       0       0       0       0       0       0       0       0
    Ethernet8       0       0       0       0       0       0       0       0
    Ethernet9       0       0       0       0       0       0       0       0
   Ethernet10       0       0       0       0       0       0       0       0
   Ethernet11       0       0       0       0       0       0       0       0
   Ethernet12       0       0       0       0       0       0       0       0
   Ethernet16       0       0       0       0       0       0       0       0
   Ethernet20       0       0       0       0       0       0       0       0
   Ethernet24       0       0       0       0       0       0       0       0
   Ethernet28       0       0       0       0       0       0       0       0
   Ethernet32       0       0       0       0       0       0       0       0
   Ethernet36       0       0       0       0       0       0       0       0
   Ethernet40       0       0       0       0       0       0       0       0
   Ethernet44       0       0       0       0       0       0       0       0
   Ethernet48       0       0       0       0       0       0       0       0
   Ethernet52       0       0       0       0       0       0       0       0
   Ethernet56       0       0       0       0       0       0       0       0
   Ethernet60       0       0       0       0       0       0       0       0
   Ethernet64       0       0       0       0       0       0       0       0
   Ethernet68       0       0       0       0       0       0       0       0
   Ethernet72       0       0       0       0       0       0       0       0
   Ethernet76       0       0       0       0       0       0       0       0
   Ethernet80       0       0       0       0       0       0       0       0
   Ethernet84       0       0       0       0       0       0       0       0
   Ethernet88       0       0       0       0       0       0       0       0
   Ethernet92       0       0       0       0       0       0       0       0
   Ethernet96       0       0       0       0       0       0       0       0
  Ethernet100       0       0       0       0       0       0       0       0
  Ethernet104       0       0       0       0       0       0       0       0
  Ethernet108       0       0       0       0       0       0       0       0
  Ethernet112       0       0       0       0       0       0       0       0
  Ethernet116       0       0       0       0       0       0       0       0
  Ethernet120       0       0       0       0       0       0       0       0
  Ethernet124       0       0       0       0       0       0       0       0

      Port Tx    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
   -----------  ------  ------  ------  ------  ------  ------  ------  ------
          CPU       0       0       0       0       0       0       0       0
    Ethernet0       0       0       0       0       0       0       0       0
    Ethernet4       0       0       0   11805       0       0       0       0
    Ethernet5       0       0       0   11805       0       0       0       0
    Ethernet6       0       0       0       0       0       0       0       0
    Ethernet7       0       0       0       0       0       0       0       0
    Ethernet8       0       0       0       0       0       0       0       0
    Ethernet9       0       0       0       0       0       0       0       0
   Ethernet10       0       0       0       0       0       0       0       0
   Ethernet11       0       0       0       0       0       0       0       0
   Ethernet12       0       0       0       0       0       0       0       0
   Ethernet16       0       0       0       0       0       0       0       0
   Ethernet20       0       0       0       0       0       0       0       0
   Ethernet24       0       0       0       0       0       0       0       0
   Ethernet28       0       0       0       0       0       0       0       0
   Ethernet32       0       0       0       0       0       0       0       0
   Ethernet36       0       0       0       0       0       0       0       0
   Ethernet40       0       0       0       0       0       0       0       0
   Ethernet44       0       0       0       0       0       0       0       0
   Ethernet48       0       0       0       0       0       0       0       0
   Ethernet52       0       0       0       0       0       0       0       0
   Ethernet56       0       0       0       0       0       0       0       0
   Ethernet60       0       0       0       0       0       0       0       0
   Ethernet64       0       0       0       0       0       0       0       0
   Ethernet68       0       0       0       0       0       0       0       0
   Ethernet72       0       0       0       0       0       0       0       0
   Ethernet76       0       0       0       0       0       0       0       0
   Ethernet80       0       0       0       0       0       0       0       0
   Ethernet84       0       0       0       0       0       0       0       0
   Ethernet88       0       0       0       0       0       0       0       0
   Ethernet92       0       0       0       0       0       0       0       0
   Ethernet96       0       0       0       0       0       0       0       0
  Ethernet100       0       0       0       0       0       0       0       0
  Ethernet104       0       0       0       0       0       0       0       0
  Ethernet108       0       0       0       0       0       0       0       0
  Ethernet112       0       0       0       0       0       0       0       0
  Ethernet116       0       0       0       0       0       0       0       0
  Ethernet120       0       0       0       0       0       0       0       0
  Ethernet124       0       0       0       0       0       0       0       0

  admin@sonic:~$ sudo pfcstat -c
  Clear saved counters       
  ```

## PFC watchdog commands
This section explains PFC Watchdog related commands that are supported in SONiC. 

**pfcwd counter_poll**

This command enables/disables the PFC watchdog counter monitoring.
- Usage:<br>
    pfcwd counter_poll <enable/disable>

- Example:  

```
  admin@sonic:~$ sudo pfcwd counter_poll disable
  admin@sonic:~$ sudo pfcwd counter_poll enable
```
  **pfcwd interval**

This command configures the PFC watchdog counter monitoring interval(in msecs). 
- Usage:<br>
    pfcwd interval <value\>

- Example:  

  ```
  admin@sonic:~$ sudo pfcwd interval 100
  ```
  
  **pfcwd start**

This command configures the detection period of PFC storm detection and enables watchdog on a specified port(s) and can specify the action [drop|forward|alert] to be performed on detection of storm. Restoration time is 2 times the detection time (if not specified). 


- Usage:<br>
    pfcwd start --action [drop|forward|alert] ports <interface_name> detection-time <value\> --restoration-time <value\>

- Example:  

  ```
  admin@sonic:~$ sudo pfcwd start --action drop ports Ethernet9 detection-time 100
  restoration time not defined; default to 2 times detection time: 200 ms

  admin@sonic:~$ sudo pfcwd start --action drop ports all detection-time 400 --restoration-time 3000

  admin@sonic:~$ sudo pfcwd start --action drop ports all detection-time 400 --restoration-time 300000
  Usage: pfcwd start [OPTIONS] [PORTS]... DETECTION_TIME

  Error: Invalid value for "--restoration-time" / "-r": 300000 is not in the valid range of 100 to 60000.
  admin@sonic:~$ sudo pfcwd start --action drop ports all detection-time 30000 --restoration-time 3000
  Usage: pfcwd start [OPTIONS] [PORTS]... DETECTION_TIME

  Error: Invalid value for "detection-time": 30000 is not in the valid range of 100 to 5000.
  ```

**pfcwd stop**

This command disables PFC watchdog on a specified port(s). If the interface_name is not specified, PFC watchdog is disabled for all the interfaces.


- Usage:<br>
    pfcwd stop [<interface_name>]

- Example:  

  ```
  admin@sonic:~$ sudo pfcwd stop Ethernet10
  admin@sonic:~$ sudo pfcwd stop
  ```
  **pfcwd show config**

This command displays the PFC watchdog configuration like action, detection time and restoration time for a port(s). If the interface_name is not specified, it displays the PFC watchdog configuration for all the interfaces.
- Usage:<br>
    pfcwd show config <interface_name>

- Example:  

  ```
  admin@sonic:~$ sudo pfcwd show config Ethernet8
  Changed polling interval to 100ms
       PORT    ACTION    DETECTION TIME    RESTORATION TIME
  ---------  --------  ----------------  ------------------
  Ethernet8      drop               400                3000

  admin@sonic:~$ sudo pfcwd show config 
  Changed polling interval to 100ms
         PORT    ACTION    DETECTION TIME    RESTORATION TIME
  -----------  --------  ----------------  ------------------
    Ethernet0      drop               400                3000
    Ethernet4      drop               400                3000
    Ethernet5      drop               400                3000
    Ethernet6      drop               400                3000
    Ethernet7      drop               400                3000
    Ethernet8      drop               400                3000
    Ethernet9      drop               400                3000
   Ethernet10      drop               400                3000
   Ethernet11      drop               400                3000
   Ethernet12      drop               400                3000
   Ethernet16      drop               400                3000
   Ethernet20      drop               400                3000
   Ethernet24      drop               400                3000
   Ethernet28      drop               400                3000
   Ethernet32      drop               400                3000
   Ethernet36      drop               400                3000
   Ethernet40      drop               400                3000
   Ethernet44      drop               400                3000
   Ethernet48      drop               400                3000
   Ethernet52      drop               400                3000
   Ethernet56      drop               400                3000
   Ethernet60      drop               400                3000
   Ethernet64      drop               400                3000
   Ethernet68      drop               400                3000
   Ethernet72      drop               400                3000
   Ethernet76      drop               400                3000
   Ethernet80      drop               400                3000
   Ethernet84      drop               400                3000
   Ethernet88      drop               400                3000
   Ethernet92      drop               400                3000
   Ethernet96      drop               400                3000
  Ethernet100      drop               400                3000
  Ethernet104      drop               400                3000
  Ethernet108      drop               400                3000
  Ethernet112      drop               400                3000
  Ethernet116      drop               400                3000
  Ethernet120      drop               400                3000
  Ethernet124      drop               400                3000
  ```

**pfcwd show stats**

This command displays the PFC watchdog stats on the system.
If the interface_name is not specified, it displays the PFC watchdog configuration for all the interfaces.
- Usage:<br>
    pfcwd show stats <interface_name>

- Example:  

  ```
  admin@sonic:~$ sudo pfcwd show config Ethernet32
           QUEUE       STATUS    STORM DETECTED/RESTORED       TX OK/DROP    RX OK/DROP    TX LAST OK/DROP    RX LAST OK/DROP
    ------------  -----------  -------------------------  ---------------  ------------  -----------------  -----------------
    Ethernet32:3  operational                        4/4  1024381/1444477           0/0       32333/459167                0/0
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#PFC-Configuration-And-Show-Commands)

# PIM Source Specific Multicast

PIM source specific multicast (PIM-SSM) is supported in this SONiC release. PIM sparse mode functionality is not available yet. The following multicast features are supported in this SONiC release:
  ```
 1. PIM-SSM,
 2. IGMP versions v2 and v3.
  ```


PIM and IGMP configuration and show commands are available only via FRR vtysh shell. FRR split mode configuration (config routing_config_mode split) is required in SONiC. And FRR configuration is required to be saved (write memory) from vtysh in order to retain across reloads.

## IGMP Configuration Commands

All IGMP configuration is done in the VTY shell. When IGMP is enabled, PIM recieves IGMP reports and Queries. IGMP Configuration is done under an interface, where pim is also configured. IGMP configuration example is provided below.

```
ip pim ssm prefix-list ssm_list
!
interface Vlan20
 ip igmp
 ip igmp join 238.1.1.1 7.7.7.7
 ip igmp last-member-query-count 3
 ip igmp last-member-query-interval 11
 ip igmp query-max-response-time 20
 ip pim
!
interface Vlan200 vrf Vrf_RED
 ip igmp
 ip igmp join 224.3.3.4 0.0.0.0
 ip igmp version 2
 ip pim
!
ip prefix-list ssm_list seq 5 permit any
!
```

IGMP can be configured from SONIC Management IS-CLI as well. Please refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring IGMP using IS-CLIs.

## PIM Configuration Commands

PIM SSM configuration example is provided below.

```
ip pim join-prune-interval 90
ip pim ssm prefix-list ssm_list
ip pim ecmp rebalance
service integrated-vtysh-config
!
vrf Vrf_RED
 ip pim ecmp rebalance
 ip pim join-prune-interval 90
 ip pim ssm prefix-list ssm_list
 exit-vrf
!
interface vlan10
 ip pim
 ip pim bfd
 ip pim drpriority 100
 ip pim hello 15
!
interface Vlan100 vrf Vrf_RED
 ip pim
 ip pim bfd
 ip pim drpriority 100
 ip pim hello 15
!
interface Vlan20
 ip igmp
 ip pim
 ip pim bfd
 ip pim drpriority 100
 ip pim hello 15
!
interface Vlan200 vrf Vrf_RED
 ip igmp
 ip pim
 ip pim bfd
 ip pim drpriority 100
 ip pim hello 15
!
ip prefix-list ssm_list seq 5 permit 232.0.0.0/24
!
```

The above configuration example shows the PIM-SSM/IGMP configuration for the default VRF and the non-default VRF (Vrf_RED). PIM is enabled on the upstream interface (Vlan10 and Vlan100, the interfaces towards the multicast source, called IIF) and IGMP and PIM are enabled on the downstream interface (Vlan20 and Vlan200, the interfaces towards the multicast host or a downstream PIM router).  PIM adjacency is established on on the upstream interfaces, Vlan10 and Vlan100.  The example also shows that PIM is enabled for BFD.



## PIM/IGMP Show Commands

### FRR VTYSH Shell

PIM show commands listed below are available only from FRR vtysh shell.

PIM global configuration parameters, total multicast route count and other PIM related information is displayed using the below command. 

```
sonic# show ip multicast
Router MLAG Role: NONE
Mroute socket descriptor: 7(default)
Mroute socket uptime: 01:36:44

Zclient update socket: 11 failures=0
Zclient lookup socket: 12 failures=0

Maximum highest VifIndex: 31
Total number of PIM/IGMP enabled interfaces: 3

Total Dynamic Multicast routes in VRF default: 1
Total Dynamic Uninstalled Multicast routes in VRF default: 0
Total Static Multicast routes in VRF default: 0
Total Static Uninstalled Multicast routes in VRF default: 0
Total Static Failed Multicast routes in VRF default: 0
Total Uninstalled Multicast routes in VRF default: 0
Total Multicast routes in VRF default: 1

Total Dynamic Multicast routes across all VRFs: 2
Total Dynamic Uninstalled Multicast routes across all VRFs: 0
Total Static Multicast routes across all VRFs: 0
Total Static Uninstalled Multicast routes across all VRFs: 0
Total Uninstalled Multicast routes across all VRFs: 0
Total Multicast routes across all VRFs: 2

Upstream Join Timer: 90 secs
Join/Prune Holdtime: 315 secs
PIM ECMP: Enable
PIM ECMP Rebalance: Enable

RPF Cache Refresh Delay:    50 msecs
RPF Cache Refresh Timer:    0 msecs
RPF Cache Refresh Requests: 0
RPF Cache Refresh Events:   0
RPF Cache Refresh Last:     --:--:--
Nexthop Lookups:            6
Nexthop Lookups Avoided:    0

Scan OIL - Last: 01:08:42  Events: 7
MFC Add  - Last: 01:08:42  Events: 3
MFC Del  - Last: 01:09:04  Events: 4

Interface        Address            ifi Vif  PktsIn PktsOut    BytesIn   BytesOut
Vlan10           192.168.10.1     66   2       0       0          0          0
Vlan20           192.168.20.1     68   1       0       0          0          0
vlan10           0.0.0.0           0  -1       0       0          0          0
sonic#
```



PIM interface status is displayed using the below command.

```
sonic# show ip pim vrf all interface
VRF: Vrf_RED
Interface         State          Address  PIM Nbrs           PIM DR  FHR IfChannels
Vlan100              up    192.168.100.1         1            local    0          0
Vlan200              up    192.168.200.1         0            local    0          1
VRF: default
Interface         State          Address  PIM Nbrs           PIM DR  FHR IfChannels
Vlan10               up     192.168.10.1         1            local    0          0
Vlan20               up     192.168.20.1         0            local    0          1
```



PIM interface related operational information in detail is displayed using the below command.

```
sonic# show ip pim interface Vlan10
Interface  : Vlan10
State      : up
Address    : 192.168.10.1 (primary)

Designated Router
-----------------
Address                : 192.168.10.1
Local DR Priority      : 1
Neighbors that didn't
 advertise DR Priority : 0
Uptime                 : --:--:--
Elections              : 0
Changes                : 0


FHR - First Hop Router
----------------------
232.1.2.1 : 192.168.10.10 is a source, uptime is 01:29:05


Hellos
------
Period         : 30
Timer          : 00:00:14
StatStart      : 01:30:46
Receive        : 27
Receive Failed : 0
Send           : 182
Send Failed    : 0
Generation ID  : 4b375a30


Flags
-----
All Multicast   : no
Broadcast       : yes
Deleted         : no
Interface Index : 66
Multicast       : yes
Multicast Loop  : 0
Promiscuous     : no


Join Prune Interval
-------------------
LAN Delay                    : yes
Effective Propagation Delay  : 0 msec
Effective Override Interval  : 0 msec
Join Prune Override Interval : 0 msec


LAN Prune Delay
---------------
Propagation Delay           : 500 msec
Propagation Delay (Highest) : 0 msec
Override Interval           : 2500 msec
Override Interval (Highest) : 0 msec


sonic#
```



IGMP interface status is displayed using the below command.

```
sonic# show ip igmp vrf all interface
VRF: Vrf_RED
Interface         State          Address  V  Querier  Query Timer    Uptime
Vlan100              up    192.168.100.1  3    local     00:01:33  115:09:34
Vlan200              up    192.168.200.1  3    local     00:02:01  115:06:56
VRF: default
Interface         State          Address  V  Querier  Query Timer    Uptime
Vlan10             mtrc     192.168.10.1  3    other     --:--:--  115:08:42
Vlan20               up     192.168.20.1  3    local     00:02:01  115:07:01
sonic#
```



PIM packet send/receive statistics per interface are displayed using the below command.

```
sonic# show ip pim vrf all interface traffic
VRF: Vrf_RED

Interface              HELLO            JOIN            PRUNE         REGISTER      REGISTER-STOP      ASSERT           BSM
                       Rx/Tx            Rx/Tx           Rx/Tx            Rx/Tx           Rx/Tx           Rx/Tx           Rx/Tx
--------------------------------------------------------------------------------------------------------------------------------
Vlan100                 39/42            0/19            0/0             0/0             0/0             0/0             0/0
Vlan200                 0/407            27/0            0/0             0/0             0/0             0/0             0/0
VRF: default

Interface              HELLO            JOIN            PRUNE         REGISTER      REGISTER-STOP      ASSERT           BSM
                       Rx/Tx            Rx/Tx           Rx/Tx            Rx/Tx           Rx/Tx           Rx/Tx           Rx/Tx
--------------------------------------------------------------------------------------------------------------------------------
Vlan10                  41/38            0/17            0/0             0/0             0/0             0/0             0/0
Vlan20                  0/408            11/0            0/0             0/0             0/0             0/0             0/0
sonic#
```



PIM neighbors are displayed using the below command.

```
sonic# show ip pim neighbor
Interface         Neighbor    Uptime  Holdtime  DR Pri
Vlan10        192.168.10.2  00:00:37  00:01:38       1
sonic#

sonic# show ip pim vrf Vrf_RED neighbor
Interface         Neighbor    Uptime  Holdtime  DR Pri
Vlan100       192.168.100.2 00:00:37  00:01:38       1
sonic#
```



IGMP group membership information is displayed using the below command.

```
sonic# show ip igmp vrf all groups
VRF: Vrf_RED
Interface        Address         Group           Mode Timer    Srcs V Uptime
Vlan200          192.168.200.1   232.1.2.1       INCL --:--:--    1 3 94:57:43
VRF: default
Interface        Address         Group           Mode Timer    Srcs V Uptime
Vlan20           192.168.20.1    232.1.2.1       INCL --:--:--    1 3 94:57:51
sonic#
```



IGMP group and source related membership information is displayed using the below command.

```
sonic# show ip igmp vrf all sources
VRF: Vrf_RED
Interface        Address         Group           Source          Timer Fwd Uptime
Vlan200          192.168.200.1   232.1.2.1       192.168.100.10  02:26   Y 94:57:52
VRF: default
Interface        Address         Group           Source          Timer Fwd Uptime
Vlan20           192.168.20.1    232.1.2.1       192.168.10.10   02:19   Y 94:58:00
sonic#
```



Multicast routes are displayed using below command.

```
sonic# show ip mroute vrf all
VRF: Vrf_RED
  * -> indicates installed route
    Source          Group           Proto  Input            Output           TTL  Uptime
* 192.168.100.10  232.1.2.1       IGMP   Vlan100          Vlan200          1    00:59:25
VRF: default
  * -> indicates installed route
  Source          Group           Proto  Input            Output           TTL  Uptime
* 192.168.10.10   232.1.2.1       IGMP   Vlan10           Vlan20           1    00:59:52
sonic#
```



The below command displays the number of multicast routes in the MRIB and if they are installed in the linux kernel.

```
sonic# show ip mroute vrf all summary
VRF: Vrf_RED
Mroute Type    Installed/Total
(*, G)               0/0
(S, G)               1/1
------
Total                1/1
VRF: default
Mroute Type    Installed/Total
(*, G)               0/0
(S, G)               1/1
------
Total                1/1
sonic#
```



The below command displays the software forwarded multicast data packets by the linux kernel.

```
sonic# show ip mroute vrf all count
VRF: Vrf_RED

Source          Group           LastUsed Packets Bytes WrongIf
192.168.100.10  232.1.2.1       0        0       0          0
VRF: default

Source          Group           LastUsed Packets Bytes WrongIf
192.168.10.10   232.1.2.1       0        0       0          0
sonic#
```



PIM SSM range prefix list details are displayed using the below command.

```
sonic# show ip pim vrf all group-type
VRF: Vrf_RED
SSM group range : ssm_list
VRF: default
SSM group range : ssm_list
sonic#
```



PIM upstream Join state information is displayed using the below command.

```
sonic# show ip pim vrf all join
VRF: Vrf_RED
Interface        Address         Source          Group           State      Uptime   Expire Prune
Vlan200          192.168.200.1   192.168.100.10  232.1.2.1       NOINFO     --:--:-- --:--  --:--
VRF: default
Interface        Address         Source          Group           State      Uptime   Expire Prune
Vlan20           192.168.20.1    192.168.10.10   232.1.2.1       NOINFO     --:--:-- --:--  --:--
sonic#
```



PIM local membership details are displayed using the below command.

```
sonic# show ip pim vrf all local-membership
VRF: Vrf_RED
Interface         Address          Source           Group            Membership
Vlan200           192.168.200.1    192.168.100.10   232.1.2.1        INCLUDE
VRF: default
Interface         Address          Source           Group            Membership
Vlan20            192.168.20.1     192.168.10.10    232.1.2.1        INCLUDE
sonic#
```



PIM RPF nexthop information registered with Zebra is displayed using the below command.

```
sonic# show ip pim vrf all nexthop
VRF: Vrf_RED
Number of registered addresses: 1
Address         Interface        Nexthop
---------------------------------------------
192.168.100.10  Vlan100          192.168.100.10
VRF: default
Number of registered addresses: 1
Address         Interface        Nexthop
---------------------------------------------
192.168.10.10   Vlan10           192.168.10.10
sonic#
```



PIM upstream RPF related information is displayed using the below command.

```
sonic# show ip pim vrf all rpf
VRF: Vrf_RED
RPF Cache Refresh Delay:    50 msecs
RPF Cache Refresh Timer:    0 msecs
RPF Cache Refresh Requests: 0
RPF Cache Refresh Events:   0
RPF Cache Refresh Last:     --:--:--
Nexthop Lookups:            4
Nexthop Lookups Avoided:    0

Source          Group           RpfIface         RpfAddress      RibNextHop      Metric Pref
192.168.100.10  232.1.2.1       Vlan100          0.0.0.0         192.168.100.10       0    0
VRF: default
RPF Cache Refresh Delay:    50 msecs
RPF Cache Refresh Timer:    0 msecs
RPF Cache Refresh Requests: 0
RPF Cache Refresh Events:   0
RPF Cache Refresh Last:     --:--:--
Nexthop Lookups:            6
Nexthop Lookups Avoided:    0

Source          Group           RpfIface         RpfAddress      RibNextHop      Metric Pref
192.168.10.10   232.1.2.1       Vlan10           0.0.0.0         192.168.10.10        0    0
sonic#
```



PIM downstream state information is displayed using the below command.

```
sonic# show ip pim vrf all state
VRF: Vrf_RED
Codes: J -> Pim Join, I -> IGMP Report, S -> Source, * -> Inherited from (*,G), V -> VxLAN
Installed Source           Group            IIF               OIL
1         192.168.100.10   232.1.2.1        Vlan100           Vlan200(IJ  )

VRF: default
Codes: J -> Pim Join, I -> IGMP Report, S -> Source, * -> Inherited from (*,G), V -> VxLAN
Installed Source           Group            IIF               OIL
1         192.168.10.10    232.1.2.1        Vlan10            Vlan20(IJ  )

sonic#
```



PIM upstream state and timers related information is displayed using the below command.

```
sonic# show ip pim vrf all upstream
VRF: Vrf_RED
Iif             Source          Group           State       Uptime   JoinTimer RSTimer   KATimer   RefCnt
Vlan100         192.168.100.10  232.1.2.1       J           01:23:08 --:--:--  --:--:--  00:02:59       2
VRF: default
Iif             Source          Group           State       Uptime   JoinTimer RSTimer   KATimer   RefCnt
Vlan10          192.168.10.10   232.1.2.1       J           01:23:35 --:--:--  --:--:--  00:03:20       2
sonic#
```



PIM upstream state JoinDesired information is displayed using the below command.

```
sonic# show ip pim vrf all upstream-join-desired
VRF: Vrf_RED
Interface        Source          Group           LostAssert Joins PimInclude JoinDesired EvalJD
Vlan200          192.168.100.10  232.1.2.1       no         no    yes        yes         yes
VRF: default
Interface        Source          Group           LostAssert Joins PimInclude JoinDesired EvalJD
Vlan20           192.168.10.10   232.1.2.1       no         no    yes        yes         yes
sonic#
```

### SONiC Click CLI Shell
The below show commands are available only in SONiC Click CLI shell

The below command fetches the data from the Multicast OrchAgent and shows the multicast routes, IPMC groups returned by SAI, IPMC RPF groups returned by SAI and some counters related to the above.

```
root@sonic:/home/admin# show debug ipmcorch -?
Usage: show debug ipmcorch [OPTIONS] COMMAND [ARGS]...

  Active debugging for IpmcOrch

Options:
  -?, -h, --help  Show this message and exit.

Commands:
  all               Dump all ipmcorch debugs
  counters          Dump IPMC counters
  ipmc-groups       Dump IPMC groups
  ipmc-routes       Dump ipmcorch routes
  ipmcvlan-l2oifs   Dump snooping vlan oifs
  rpf-groups        Dump RPF groups
  saved-mroutes     Dump Saved mroutes
root@sonic:/home/admin#
```

The below example shows all the above options:

```
root@sonic:/home/admin# show debug ipmcorch all
VRF name "Default", VRF object ID 0x300000000003a

Source IP        Group IP         Incoming Interface   Outgoing Interface(s)
---------------- ---------------- -------------------- ---------------------
192.168.10.10    232.1.2.1        Vlan10               Vlan20

Total number of IPMC entries in VRF "Default" : 1

VRF name "Vrf_RED", VRF object ID 0x30000000009ff

Source IP        Group IP         Incoming Interface   Outgoing Interface(s)
---------------- ---------------- -------------------- ---------------------
192.168.100.10   232.1.2.1        Vlan100              Vlan200

Total number of IPMC entries in VRF "Vrf_RED" : 1

Total number of IPMC entries : 2

IPMC Group ID      Ref Count Group Members (interface - object ID)
------------------ --------- -------------------------------------
0x0033000000000a25 1         Vlan20 - 0x34000000000a26
0x0033000000000a29 1         Vlan200 - 0x34000000000a2a

Total number of IPMC groups : 2 Group sharing:off

RPF Group ID       Ref Count Group Members (interface - object ID)
------------------ --------- -------------------------------------
0x002f000000000a23 1         Vlan10 - 0x30000000000a24
0x002f000000000a27 1         Vlan100 - 0x30000000000a28

Total number of RPF groups : 2

IPMC Interface     Ref Count
------------------ ---------
Vlan10             1
Vlan100            1
Vlan20             1
Vlan200            1
root@sonic:/home/admin#
```

The below example shows IPMC and associated L2 Vlan

```
root@sonic:/home/admin# show debug ipmcorch ipmcvlan-l2oifs
IPMC Group          Vlan-OIFs
----------------    ---------------------------------------
0x0033000000000af0  Vlan10
root@sonic:/home/admin#
```

The below example shows Saved mroutes

```
root@sonic:/home/admin# show debug ipmcorch saved-mroutes
IPMC Group         Source          Group           VRF
----------------  ---------------- ---------------  ---------------
0x0033000000000a30 3.3.3.10        232.0.0.1        0x300000000003a
root@sonic:/home/admin#
```

The below command fetches the data from the Error database and displays all the multicast route entries that failed to get added to the ASIC and also the entries that hit the TABLE FULL condition.

```
root@sonic:/home/admin# show error_database
VRF Name    Source IP    Group IP      In Interface    Out Interface(s)    Error Code          Operation
----------  -----------  ------------  --------------  ------------------  ------------------  -----------
Default     112.0.0.2    232.0.31.64   Ethernet2       Ethernet0           SWSS_RC_TABLE_FULL  create
Default     112.0.0.2    232.0.31.63   Ethernet2       Ethernet0           SWSS_RC_TABLE_FULL  create
Default     112.0.0.2    232.0.31.62   Ethernet2       Ethernet0           SWSS_RC_TABLE_FULL  create
```

## IGMP clear Commands
IGMP dynamic group information under an interface can be reset using the below command
```
sonic# clear ip igmp vrf Vrf_RED interfaces 
  <cr>  
sonic# clear ip igmp interfaces             
  <cr>   
```
## PIM clear Commands

PIM information can be reset using below command.
```
sonic# clear ip pim             
  interface   Reset PIM interfaces
  interfaces  Reset PIM interfaces
  oil         Rescan PIM OIL (output interface list)
  statistics  Specify the VRF
  vrf         Specify the VRF
```
## IGMP Debug Commands

IP igmp commands are listed below and can be enabled from VTY shell.
```
sonic# debug igmp    
  <cr>     
  events   IGMP protocol events
  packets  IGMP protocol packets
  trace    IGMP internal daemon activity
```

## PIM Debug Commands

PIM debug commands listed below are available only from FRR vtysh shell.


The below listed debug commands enables tracing of PIM events, packets, mroute, PIM's interaction with Zebra etc.,

```
sonic# debug pim
  <cr>
  bsm          BSR message processing activity
  events       PIM protocol events
  nht          Nexthop Tracking
  packet-dump  PIM packet dump
  packets      PIM protocol packets
  static       PIM Static Multicast Route activity
  trace        PIM internal daemon activity
  vxlan        PIM VxLAN events
  zebra        ZEBRA protocol activity

sonic# debug mroute
  <cr>
  detail  detailed
sonic#
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#pim-source-specific-multicast)
# PortChannel Configuration And Show

## PortChannel Show commands

**show interfaces portchannel**  

This command displays all the port channels that are configured in the device and its current status.

  - Usage:  
    show interfaces portchannel

- Example:

```
  admin@sonic:~$ show interfaces portchannel
  Flags: A - active, I - inactive, Up - up, Dw - Down, N/A - not available, S - selected, D - deselected
    No.  Team Dev       Protocol     Ports
-----  -------------  -----------  ---------------------------
     24  PortChannel24  LACP(A)(Up)  Ethernet28(S) Ethernet24(S)
     48  PortChannel48  LACP(A)(Up)  Ethernet52(S) Ethernet48(S)
     40  PortChannel40  LACP(A)(Up)  Ethernet44(S) Ethernet40(S)
      0  PortChannel0   LACP(A)(Up)  Ethernet0(S) Ethernet4(S)
      8  PortChannel8   LACP(A)(Up)  Ethernet8(S) Ethernet12(S)
      1  PortChannel1   NONE(A)(Up)  Ethernet19(S) Ethernet18(S)
```


## PortChannel Config commands

This sub-section explains how to configure the portchannel and its member ports.

**config portchannel add/del <portchannel_name>**  

This command is used to add or delete the portchannel.  
It is recommended to use portchannel names in the format "PortChannelxxxx", where "xxxx" is number of 1 to 4 digits. Ex: "PortChannel0002".

NOTE: If users specify any other name like "pc99", command will succeed, but such names are not supported. Such names are not printed properly in the "show interface portchannel" command. It is recommended not to use such names.

When any port is already member of any other portchannel and if user tries to add the same port in some other portchannel (without deleting it from the current portchannel), the command fails internally. But, it does not print any error message. In such cases, remove the member from current portchannel and then add it to new portchannel.

Command takes following optional arguments:
1) min-links  - minimum number of links required to bring up the portchannel
2) fallback - true/false. LACP fallback feature can be enabled / disabled.  When it is set to true, only one member port will be selected as active per portchannel during fallback mode. Refer https://github.com/Azure/SONiC/blob/master/doc/lag/LACP%20Fallback%20Feature%20for%20SONiC_v0.5.md for more details about fallback feature.
3) static - configures the portchannel as static.

  - Usage:  
    config portchannel add/del <portchannel_name> [min-links INTEGER] [fallback true/false] [static true/false]

- Example:
  ```
  admin@sonic:~$ sudo config portchannel add PortChannel0011
  This command will create the portchannel with name "PortChannel0011".

  admin@sonic:~$ sudo config portchannel add PortChannel0011 --static=true
  This command will create a static portchannel with name "PortChannel0011".

  ```

**config portchannel member add/del <portchannel_name> <member_portname>**  

This command is to add or delete a member port into the already created portchannel.

  - Usage:  
    config portchannel member add/del <portchannel_name> <member_portname>

- Example:
  ```
  admin@sonic:~$ sudo config portchannel member add PortChannel0011 Ethernet4
  This command will add Ethernet4 as member of the portchannel "PortChannel0011".
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#PortChannel-Configuration-And-Show)

# QoS Configuration And Show

## QoS Maps

### QoS Maps Show commands
QoS Map CLI are available as part of KLISH-CLI (sonic-cli)

**show qos map dot1p-tc [\<map-name\>]**

This command displays the details about dot1p-tc qos map. If map-name is not provided, It will provide details about all the dot1p-tc qos maps.

  - Usage:
    
    sonic#show qos map dot1p-tc

- Example:
  ```
  sonic# show qos map dot1p-tc
  DOT1P-TC-MAP: dot1p_tc
  ----------------------------
    DOT1P  TC   
  ----------------------------
    5      0    
  ----------------------------
  ```

**show qos map tc-dot1p [\<map-name\>]**

This command displays the details about tc-dot1p qos map. 
If map-name is not provided, It will provide details about all the tc-dot1p qos maps.

  - Usage:
  
      sonic#show qos map tc-dot1p

- Example:
  ```
  sonic# show qos map tc-dot1p 
  Traffic-Class-Dot1P-MAP: tc_dot1p
  ----------------------------
    TC   DOT1P
  ----------------------------
    5    4    
  ----------------------------
  ```

**show qos map dscp-tc [\<map-name\>]**

This command displays the details about dscp-tc qos map. If map-name is not provided, It will provide details about all the dscp-tc qos maps.

  - Usage:
    
    sonic#show qos map dscp-tc

- Example:
  ```
  sonic# show qos map dscp-tc 
  DSCP-TC-MAP: dscp_tc
  ----------------------------
    DSCP TC   
  ----------------------------
    9    1    
  ----------------------------
  ```

**show qos map tc-dscp [\<map-name\>]**

This command displays the details about tc-dscp qos map. If map-name is not provided, It will provide details about all the tc-dscp qos maps.

  - Usage:
    
    sonic#show qos map tc-dscp

- Example:
  ```
  sonic# show qos map tc-dscp
  Traffic-Class-DSCP-MAP: tc_dscp
  ----------------------------
    TC   DSCP 
  ----------------------------
    6    55   
  ----------------------------
  ```

### QoS Maps Config Commands

Qos Map configuration CLI are available as part of KLISH-CLI (sonic-cli) config mode.

**qos map {dot1p-tc | dscp-tc | tc-dot1p | tc-dscp} \<mapname\>**

This command creates a given type of qos map.
CLI context will change to the qos-map mode and user will need to create individual mappings.

- Config dot1p-tc map
````
  sonic(config)# qos map dot1p-tc <mapname>
  sonic(conf-dot1p-tc-map-mapname)# dot1p <0..7> traffic-class <0..7>
  
  -Remove mappings and Map 
  sonic(conf-dot1p-tc-map-mapname)# no dot1p <0..7>
  sonic(config)# no qos map tc-dscp <mapname>
````

- Config tc-dot1p map
````
  sonic(config)# qos map tc-dot1p <mapname>
  sonic(conf-tc-dot1p-map-mapname)# traffic-class <0..7> dot1p <0..7>
  
  -Remove mappings and Map 
  sonic(conf-tc-dot1p-map-mapname)# no traffic-class <0..7>
  sonic(config)# no qos map tc-dot1p <mapname>
````

- Config dscp-tc map
````
  sonic(config)# qos map dscp-tc <mapname>
  sonic(conf-dscp-tc-map-mapname)# dscp <0..63> traffic-class <0..7>
  
  -Remove mappings and Map 
  sonic(conf-dscp-tc-map-mapname)# no dscp <0..63>
  sonic(config)# no qos map dscp-tc <mapname>
````

- Config tc-dscp map
````
  sonic(config)# qos map tc-dscp <mapname>
  sonic(conf-tc-dscp-map-mapname)# traffic-class <0..7> dscp <0..63>
  
  -Remove mappings and Map 
  sonic(conf-tc-dscp-map-mapname)# no traffic-class <0..7>
  sonic(config)# no qos map tc-dscp <mapname>
````


Qos Maps can be applied on an interface from interface context using the following command.

**qos-map {dot1p-tc | dscp-tc | tc-dot1p | tc-dscp} \<map-name\>**

Ethernet and Port-Channel interface can be associated with all of the given type of qos maps.

Vlan interface can be associated with only dscp-tc and tc-dscp type of qos map, moreover these maps will only be effective if Vlan interface is an L3 interface.

- Config interface QoS
````
  sonic(config)# interface <ifname>
  sonic(conf-if-ifname)# qos-map tc-dscp <mapname>
  sonic(conf-if-ifname)# qos-map tc-dot1p <mapname>
  sonic(conf-if-ifname)# qos-map dscp-tc <mapname>
  sonic(conf-if-ifname)# qos-map dot1p-tc <mapname>

  sonic(conf-if-ifname)# no qos-map tc-dscp
  sonic(conf-if-ifname)# no qos-map tc-dot1p
  sonic(conf-if-ifname)# no qos-map dscp-tc
  sonic(conf-if-ifname)# no qos-map dot1p-tc
````

## QoS Scheduler and Shaper

### QoS Scheduler and Shaper Show Commands

QoS Scheduling and Shaping CLI are available as part of KLISH-CLI (sonic-cli)

**show qos scheduler-policy [\<policy-name\>]**

This command displays the details about given scheduler-policy. 

- Usage:   
sonic# show qos scheduler-policy [\<policy-name\>]

- Example: 
```
sonic# show qos scheduler-policy spolicy
   Queue: 0
              type: wrr
              weight: 1
              pir: 60000       Kbps
   Queue: 1
              type: wrr
              weight: 2
              pir: 70000       Kbps
   Queue: 2
              type: strict
              pir: 80000       Kbps
   Port: port
              pir: 1000000     Kbps
sonic#
```

**show qos scheduler-policy**

This command displays the all the scheduler-policies in the system 

- Usage:   
sonic# show qos scheduler-policy 

**show qos interface \<interface_name\>**

This command displays policy binded to an interface

- Usage:   
sonic# show qos interface \<interface_name\>

- Example:   
```
sonic# show qos interface Ethernet 0
          scheduler policy: spolicy
```

### QoS Scheduler and Shaper Config Commands

QoS Scheduling and Shaping CLI are available as part of KLISH-CLI (sonic-cli) config mode

**Create/Delete a scheduler-policy**

- Usage:  
sonic(config)# [no] qos scheduler-policy \<policy-name\>

- Example to create a scheduler-policy :   
```
sonic(config)# qos scheduler-policy spolicy
```

- Example to delete a scheduler-policy :   
```
sonic(config)# no qos scheduler-policy spolicy
```
**Configure/Remove PIR for a queue**

PIR - Peak Information Rate in kilo bits per second

- Usage:  
sonic(conf-scheduler-spolicy-queue-0)# [no] pir \<pir value\>

- Example to configure PIR:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# pir 60000
```
- Example to remove PIR:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# no pir
```

**Configure/Remove PBS for a queue**

PBS  - Peak Burst Size in bytes

- Usage:  
sonic(conf-scheduler-spolicy-queue-0)# [no] pbs \<pbs value\>

- Example to configure PBS:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# pbs 100000
```
- Example to remove  PBS:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# no pbs
```

**Configure/Remove CIR for a queue**

CIR - Committed Information Rate in kilo bits per second

- Usage:  
sonic(conf-scheduler-spolicy-queue-0)# [no] cir \<cir value\>

- Example to configure CIR:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# cir 50000
```

- Example to remove CIR:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# no cir
```

**Configure/Remove CBS for a queue**

CBS  - Committed Burst Size in bytes

- Usage:  
sonic(conf-scheduler-spolicy-queue-0)# [no] cbs \<cbs value\>

- Example to configure CBS:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# cbs 10000
```

- Example to remove CBS:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 0
sonic(conf-scheduler-spolicy-queue-0)# no cbs
```

**Configure/Remove scheduler type for a queue**

Physical port queues 0 to 6 are scheduled in WRR fashion, 7 to 9 are scheduler in strict priority.

- Usage:  
sonic(conf-scheduler-spolicy-queue-0)# [no] type \<strict/dwrr/wrr\)>

- Example to configure strict-priority:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 2
sonic(conf-scheduler-spolicy-queue-2)# type strict
```
- Example to remove  strict-priority:   
```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# queue 2
sonic(conf-scheduler-spolicy-queue-2)# no type 
```
**Configure/Remove scheduler weight for a queue**

Default weight is 1

- Usage:  
sonic(conf-scheduler-spolicy-queue-0)# [no] weight \<1..100\>

- Example to configure weight:   
```
sonic(conf-sched-policy-spolicy)# queue 1
sonic(conf-scheduler-spolicy-queue-1)# type wrr
sonic(conf-scheduler-spolicy-queue-1)# weight 2
```
- Example to remove weight:   

```
sonic(conf-sched-policy-spolicy)# queue 1
sonic(conf-scheduler-spolicy-queue-1)# type wrr
sonic(conf-scheduler-spolicy-queue-1)# no weight
```

**Configure/Remove PIR for a port**

PIR - Peak Information Rate in kilo bits per second

- Usage:  
sonic(conf-scheduler-spolicy-port)# [no] pir \<pir value\>

- Example to configure PIR:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# port
sonic(conf-scheduler-spolicy-port)# pir 80000
```
- Example to remove PIR:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# port
sonic(conf-scheduler-spolicy-port)# no pir
```

**Configure/Remove PBS for a queue**

PBS  - Peak Burst Size in bytes

- Usage:  
sonic(conf-scheduler-spolicy-port)# [no] pbs \<pbs value\>

- Example to configure PBS:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# port
sonic(conf-scheduler-spolicy-port)# pbs 100000
```
- Example to remove PBS:   

```
sonic(config)# qos scheduler-policy spolicy
sonic(conf-sched-policy-spolicy)# port
sonic(conf-scheduler-spolicy-port)# no pbs
```

**Binding/Unbinding scheduler-policy to interface**

- Usage:   
sonic(conf-if-Ethernet0)# [no] scheduler-policy \<policy-name\>

- Example to bind scheduler-policy to interface:
```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# scheduler-policy spolicy
```
- Example to unbind scheduler-policy from interface:
```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# no scheduler-policy 
```

## QoS WRED

## QoS WRED Show Commands

QoS WRED show CLI are available as part of KLISH-CLI (sonic-cli)

**show qos wred-policy [\<policy-name\>]**

This command displays the details about given wred-policy. 

- Usage:   
sonic# show qos wred-policy [\<policy-name\>]

- Example: 
```
sonic# show qos wred-policy wpolicy
---------------------------------------------------
Policy                 : wpolicy
---------------------------------------------------
ecn                    : ecn_green
green-min-threshold    : 1000        KBytes
green-max-threshold    : 1024        KBytes
green-drop-probability : 90
sonic#
```

**show qos wred-policy**

This command displays the all the wred-policies in the system 

- Usage:   
sonic# show qos wred-policy 

**show qos interface \<interface_name\> queue \<queue_id\>**

This command displays policy binded to a queue 

- Usage:
show qos interface \<interface_name\> queue \<queue_id\>

- Example:
```
sonic# show qos interface Ethernet 0 queue 0
interface queue 0 config:
    WRED profile: wpolicy
```

### QoS WRED Config Commands

QoS WRED Config CLI are available as part of KLISH-CLI (sonic-cli) config mode

**Create/Delete a wred-policy**

- Usage:  
sonic(config)# [no] qos wred-policy \<policy-name\>

- Example to create a wred-policy :   
```
sonic(config)# qos wred-policy wpolicy
```

- Example to delete a wred-policy :   
```
sonic(config)# no qos wred-policy wpolicy
```
**Configure/Remove Color profile**

- Usage:  
sonic(conf-wred-wpolicy)# [no] green minimum-threshold \<value\> maximum-threshold \<value\> drop-probability \<value\>

- Example to configure color:   

```
sonic(config)# qos wred-policy wpolicy
sonic(conf-wred-wpolicy)# green minimum-threshold 1000 maximum-threshold 1024 drop-probability 90
```
- Example to remove color :   

```
sonic(config)# qos wred-policy wpolicy
sonic(conf-wred-wpolicy)# no green
```
**Configure/Remove ECN**

- Usage:  
sonic(conf-wred-wpolicy)# [no] ecn \<none/green/all\>

- Example to configure ECN:   

```
sonic(config)# qos wred-policy wpolicy
sonic(conf-wred-wpolicy)# ecn green
```
- Example to remove ECN :   

```
sonic(config)# qos wred-policy wpolicy
sonic(conf-wred-wpolicy)# no ecn
```
**Binding/Unbinding wred-policy to queue**

- Usage:  
sonic(conf-if-Ethernet0)# [no] queue \<queue_id\> wred-policy \<policy-name\>

- Example to bind policy to queue:
```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# queue 0 wred-policy wpolicy
```
- Example to unbind policy from queue:

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# no queue 0 wred-policy
```
### PFC

**show pfc counters**  
This command displays the details of Rx & Tx priority-flow-control (pfc) for all ports. This command can be used to clear the counters using -c option.

  - Usage:  
    show pfc counters [-c or --clear]

- Example:
  ```
   admin@sonic:~$ show pfc counters
      Port Rx    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
    -----------  ------  ------  ------  ------  ------  ------  ------  ------
    Ethernet0       0       0       0       0       0       0       0       0
    Ethernet4       0       0       0       0       0       0       0       0
    Ethernet8       0       0       0       0       0       0       0       0
   Ethernet12       0       0       0       0       0       0       0       0

      Port Tx    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
    -----------  ------  ------  ------  ------  ------  ------  ------  ------
    Ethernet0       0       0       0       0       0       0       0       0
    Ethernet4       0       0       0       0       0       0       0       0
    Ethernet8       0       0       0       0       0       0       0       0
   Ethernet12       0       0       0       0       0       0       0       0
  ```

### Queue And Priority-Group

This sub-section explains the following queue parameters that can be displayed using "show queue" command.
1) queue counters
2) queue watermark
3) priority-group  watermark
4) queue persistent-watermark


**show queue counters**  

This command displays packet and byte counters for all queues of all ports or one specific-port given as arguement.
The command also displays counters for CPU queues.
This command can be used to clear the counters for all queues of all ports or one specific-port given as an argument.

  - Usage:  
    show queue counters [-c or --clear] [<interface_name>]

- Example:
   ```
    This example gives the sample output from two ports Ethernet0 and Ethernet4.
    
    admin@sonic:~$ show queue counters 
         Port    TxQ    Counter/pkts    Counter/bytes    Drop/pkts    Drop/bytes
    ---------  -----  --------------  ---------------  -----------  ------------
    Ethernet0    UC0               0                0            0             0
    Ethernet0    UC1               0                0            0             0
    Ethernet0    UC2               0                0            0             0
    Ethernet0    UC3               0                0            0             0
    Ethernet0    UC4               0                0            0             0
    Ethernet0    UC5               0                0            0             0
    Ethernet0    UC6               0                0            0             0
    Ethernet0    UC7               0                0            0             0
    Ethernet0    UC8               0                0            0             0
    Ethernet0    UC9               0                0            0             0
    Ethernet0    MC0               0                0            0             0
    Ethernet0    MC1               0                0            0             0
    Ethernet0    MC2               0                0            0             0
    Ethernet0    MC3               0                0            0             0
    Ethernet0    MC4               0                0            0             0
    Ethernet0    MC5               0                0            0             0
    Ethernet0    MC6               0                0            0             0
    Ethernet0    MC7               0                0            0             0
    Ethernet0    MC8               0                0            0             0
    Ethernet0    MC9               0                0            0             0
    
         Port    TxQ    Counter/pkts    Counter/bytes    Drop/pkts    Drop/bytes
    ---------  -----  --------------  ---------------  -----------  ------------
    Ethernet4    UC0               0                0            0             0
    Ethernet4    UC1               0                0            0             0
    Ethernet4    UC2               0                0            0             0
    Ethernet4    UC3               0                0            0             0
    Ethernet4    UC4               0                0            0             0
    Ethernet4    UC5               0                0            0             0
    Ethernet4    UC6               0                0            0             0
    Ethernet4    UC7               0                0            0             0
    Ethernet4    UC8               0                0            0             0
    Ethernet4    UC9               0                0            0             0
    Ethernet4    MC0               0                0            0             0
    Ethernet4    MC1               0                0            0             0
    Ethernet4    MC2               0                0            0             0
    Ethernet4    MC3               0                0            0             0
    Ethernet4    MC4               0                0            0             0
    Ethernet4    MC5               0                0            0             0
    Ethernet4    MC6               0                0            0             0
    Ethernet4    MC7               0                0            0             0
    Ethernet4    MC8               0                0            0             0
    Ethernet4    MC9               0                0            0             0
  ```
  - Optionally, you can specify an interface name in order to display only that particular interface

- Examples:
  ```
  admin@sonic:~$ show queue counters Ethernet4
         Port    TxQ    Counter/pkts    Counter/bytes    Drop/pkts    Drop/bytes
    ---------  -----  --------------  ---------------  -----------  ------------
    Ethernet4    UC0               0                0            0             0
    Ethernet4    UC1               0                0            0             0
    Ethernet4    UC2               0                0            0             0
    Ethernet4    UC3               0                0            0             0
    Ethernet4    UC4               0                0            0             0
    Ethernet4    UC5               0                0            0             0
    Ethernet4    UC6               0                0            0             0
    Ethernet4    UC7               0                0            0             0
    Ethernet4    UC8               0                0            0             0
    Ethernet4    UC9               0                0            0             0
    Ethernet4    MC0               0                0            0             0
    Ethernet4    MC1               0                0            0             0
    Ethernet4    MC2               0                0            0             0
    Ethernet4    MC3               0                0            0             0
    Ethernet4    MC4               0                0            0             0
    Ethernet4    MC5               0                0            0             0
    Ethernet4    MC6               0                0            0             0
    Ethernet4    MC7               0                0            0             0
    Ethernet4    MC8               0                0            0             0
    Ethernet4    MC9               0                0            0             0

  admin@sonic:~$ show queue counters CPU
     Port    TxQ    Counter/pkts    Counter/bytes    Drop/pkts    Drop/bytes
   ------  -----  --------------  ---------------  -----------  ------------
      CPU    MC0               0                0            0             0
      CPU    MC1               0                0            0             0
      CPU    MC2               0                0            0             0
      CPU    MC3               0                0            0             0
      CPU    MC4               0                0            0             0
      CPU    MC5               0                0            0             0
      CPU    MC6               0                0            0             0
      CPU    MC7               0                0            0             0
      ...
  ```


**show queue watermark**  

This command displays the user watermark for the queues (Egress shared pool occupancy per queue) for either the unicast queues or multicast queues for all ports

  - Usage:  
    show queue watermark <multicast|unicast>

- Example:
  ```
  admin@sonic:~$ show queue  watermark unicast
  Egress shared pool occupancy per unicast queue:
         Port    UC0    UC1    UC2    UC3    UC4    UC5    UC6    UC7
  -----------  -----  -----  -----  -----  -----  -----  -----  -----
    Ethernet0      0      0      0      0      0      0      0      0
    Ethernet4      0      0      0      0      0      0      0      0
    Ethernet8      0      0      0      0      0      0      0      0
    Ethernet12     0      0      0      0      0      0      0      0

  admin@sonic:~$ show queue  watermark multicast (Egress shared pool occupancy per multicast queue)

  ```

**show priority-group watermark|persistent-watermark**  
This command displays the user watermark or persistent-watermark for the Ingress "headroom" or "shared pool occupancy" per priority-group for  all ports 

  - Usage:  
    show priority-group <watermark|persistent-watermark> <headroom|shared>

- Example:
  ```
  admin@sonic:~$ show priority-group  watermark shared
  Ingress shared pool occupancy per PG:
         Port    PG0    PG1    PG2    PG3    PG4    PG5    PG6    PG7
  -----------  -----  -----  -----  -----  -----  -----  -----  -----
    Ethernet0      0      0      0      0      0      0      0      0
    Ethernet4      0      0      0      0      0      0      0      0
    Ethernet8      0      0      0      0      0      0      0      0
    Ethernet12     0      0      0      0      0      0      0      0

  admin@sonic:~$ show priority-group watermark headroom	(Ingress headroom per PG)
  admin@sonic:~$ show priority-group persistent-watermark shared (Ingress shared pool occupancy per PG)
  admin@sonic:~$ show priority-group persistent-watermark headroom (Ingress headroom per PG)
  ```

In addition to user watermark("show queue|priority-group watermark ..."), a persistent watermark is available.
It hold values independently of user watermark. This way user can use "user watermark" for debugging, clear it, etc, but the "persistent watermark" will not be affected. 

**show queue persistent-watermark**  
This command displays the user persistet-watermark for the queues (Egress shared pool occupancy per queue) for either the unicast queues or multicast queues for all ports
  - Usage:  
    show queue persistent-watermark <unicast|multicast>

- Example:
  ```
  admin@sonic:~$ show queue persistent-watermark unicast
  Egress shared pool occupancy per unicast queue:
         Port    UC0    UC1    UC2    UC3    UC4    UC5    UC6    UC7
  -----------  -----  -----  -----  -----  -----  -----  -----  -----
    Ethernet0    N/A    N/A    N/A    N/A    N/A    N/A    N/A    N/A
    Ethernet4    N/A    N/A    N/A    N/A    N/A    N/A    N/A    N/A
    Ethernet8    N/A    N/A    N/A    N/A    N/A    N/A    N/A    N/A
    Ethernet12   N/A    N/A    N/A    N/A    N/A    N/A    N/A    N/A

  admin@sonic:~$ show queue persistent-watermark multicast (Egress shared pool occupancy per multicast queue)

  ```

  Both "user watermark" and "persistent watermark" can be cleared by user: 
  ```
  root@sonic:~# sonic-clear queue persistent-watermark unicast

  root@sonic:~# sonic-clear queue persistent-watermark multicast

  root@sonic:~# sonic-clear priority-group persistent-watermark shared

  root@sonic:~# sonic-clear priority-group persistent-watermark headroom
  ```


## QoS Global commands

**config qos clear**  

This command is used to clear all the QoS configuration from all the following QOS Tables in ConfigDB. 

1) TC_TO_PRIORITY_GROUP_MAP,
2) MAP_PFC_PRIORITY_TO_QUEUE,
3) TC_TO_QUEUE_MAP,
4) DOT1P_TO_TC_MAP,
5) DSCP_TO_TC_MAP,
6) TC_TO_DOT1P_MAP,
7) TC_TO_DSCP_MAP,
8) SCHEDULER,
9) PFC_PRIORITY_TO_PRIORITY_GROUP_MAP,
10) PORT_QOS_MAP,
11) WRED_PROFILE,
12) QUEUE,
13) CABLE_LENGTH,
14) BUFFER_POOL,
15) BUFFER_PROFILE,
16) BUFFER_PG,
17) BUFFER_QUEUE

   - Usage:  
     config qos clear 

- Example:
  ```
  admin@sonic:~$ sudo config qos clear 

  ```

**config qos reload**  

This command is used to reload the QoS configuration. 
QoS configuration has got two sets of configurations.
1) Generic QOS Configuration - This gives complete list of all possible QOS configuration. Its given in the file /usr/share/sonic/templates/qos_config.j2 in the device.
   Reference: https://github.com/Azure/sonic-buildimage/blob/master/files/build_templates/qos_config.j2
   Users have flexibility to have platform specific qos configuration by placing the qos_config.j2 file at /usr/share/sonic/device/<platform>/<hwsku>/.
   If users want to modify any of this loaded QOS configuration, they can modify this file in the device and then issue the "config qos reload" command.

2) Platform specific buffer configuration. Every platform has got platform specific and topology specific (T0 or T1 or T2) buffer configuration at /usr/share/sonic/device/<platform>/<hwsku>/buffers_defaults_tx.j2
   In addition to platform specific configuration file, a generic configuration file is also present at /usr/share/sonic/templates/buffers_config.j2.
   Reference: https://github.com/Azure/sonic-buildimage/blob/master/files/build_templates/buffers_config.j2
   Users can either modify the platform specific configuration file, or the generic configuration file and then issue this "config qos reload" command.

These configuration files are already loaded in the device as part of the reboot process. In case if users wants to modify any of these configurations, they need to modify the appropriate QOS tables and fields in these files and then use this reload command.
This command uses those modified buffers.json.j2 file & qos.json.j2 file and reloads the new QOS configuration.
If users have not made any changes in these configuration files, this command need not be executed.

Some of the example QOS configurations that users can modify are given below.
1) TC_TO_PRIORITY_GROUP_MAP
2) MAP_PFC_PRIORITY_TO_QUEUE
3) TC_TO_QUEUE_MAP
4) DOT1P_TO_TC_MAP
5) DSCP_TO_TC_MAP
6) TC_TO_DSCP_MAP
7) TC_TO_DOT1P_MAP
8) SCHEDULER
9) PFC_PRIORITY_TO_PRIORITY_GROUP_MAP
10) PORT_QOS_MAP
11) WRED_PROFILE
12) CABLE_LENGTH
13) BUFFER_QUEUE

- Usage:  
  config qos reload 

- Example:
  ```
  root@T1-2:~# config qos reload 
  Running command: /usr/local/bin/sonic-cfggen -d -t /usr/share/sonic/device/x86_64-dell_z9100_c2538-r0/Force10-Z9100-C32/buffers.json.j2 >/tmp/buffers.json
  Running command: /usr/local/bin/sonic-cfggen -d -t /usr/share/sonic/device/x86_64-dell_z9100_c2538-r0/Force10-Z9100-C32/qos.json.j2 -y /etc/sonic/sonic_version.yml >/tmp/qos.json
  Running command: /usr/local/bin/sonic-cfggen -j /tmp/buffers.json --write-to-db
  Running command: /usr/local/bin/sonic-cfggen -j /tmp/qos.json --write-to-db
  root@T1-2:~# 
  In this example, it uses the buffers.json.j2 file and qos.json.j2 file from platform specific folders. 
  When there are no changes in the platform specific configutation files, they internally use the file "/usr/share/sonic/templates/buffers_config.j2" and "/usr/share/sonic/templates/qos_config.j2" to generate the configuration.
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#QoS-Configuration-And-Show)

# Spanning Tree

The Spanning Tree Protocol (STP) prevents Layer 2 loops in a network and provides redundant links. If a primary link fails, the backup link is activated and network traffic is not affected. STP also ensures that the least cost path is taken when multiple paths exist between the devices.

When spanning tree is used, the network switches transform the real network topology into a spanning tree topology. In an STP topology any LAN in the network can be reached from any other LAN through a unique path. The network switches recalculate a new spanning tree topology whenever there is a change to the network topology.

Two modes of spanning tree protocol are supported: Per VLAN Spanning Tree and Rapid Per VLAN Spanning Tree. At any given time, only one of the modes is configurable. Most of the commands listed below are applicable to both the modes. Commands not applicable to a particular mode are explicitly called out.

Note: Spanning Tree Protocol can be configured from KLISH CLI. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring STP using KLISH CLIs.


## Per VLAN Spanning Tree (PVST)

PVST+ allows for running multiple instances of spanning tree on per VLAN basis. 

## Rapid Per VLAN Spanning Tree (RPVST)

RPVST+ allows for running multiple instances of rapid spanning tree on per VLAN basis. 

Following sections explain PVST and RPVST configuration and show commands. The details of the equivalent PVST and RPVST KLISH CLI commands commands are available in KLISH CLI reference guide.


## STP(PVST/RPVST) MC-LAG support
SONiC MC-LAG implementation uses the Inter Chassis Communication Protocol (ICCP) for establishing and running a MultiChassis LAG (MC-LAG) across two MC-LAG capable peer devices.
SONIC supports STP over MC-LAG peer devices by implementing STP extensions for ICCP protocol as described in RFC 7275. 

When STP is enabled on MC-LAG peer devices, 
- Both MC-LAG peer devices are virtualized to acts as a single bridge
- The virtualized Bridge Identifier i.e. MAC address of the Active MC-LAG device is used for STP communications. 
- When STP-ICCP is operational, MC-LAG peer link will not participate in STP topology
- When STP-ICCP is not operational, peer link will participate in STP topology and both MCLAG peers will run STP independently i.e. use their own Bridge Identifier instead of virtualized Bridge Identifier.
- STP needs to be configured on both MC-LAG peers and same STP configuration should be used. For example, bridge priority value configured on both MC-LAG peers must be same.
- It is recommended to use either RPVST or PVST (but not mix of both) in STP network. 

#### Limitations
- Maximum number of STP instances supported is hardware dependent. For example TH3 platforms supports upto 62 STP instances. Pelase refer to STP scale matrix in SONIC 3.1.0 release notes for more details. 
- PVST or RPVST is supported with maximum of 3500 per vlan ports.
- STP loop guard is supported with RPVST only. 


### Configuration commands 

#### Global commands

Following commands allow to enable or disable PVST or RPVST globally and to configure STP parameters globally. 

**config spanning_tree enable or disable pvst or rpvst**

This command allows enabling or disabling spanning tree mode (pvst or rpvst) for the device globally. 

Note: When global pvst or rpvst mode is enabled, by default spanning tree will be enabled on the VLANs for VLANs up to maximum PVST or RPVST instances supported on the hardware, for rest of the VLANs spanning tree will be disabled.

- Usage: 
  config spanning_tree {enable|disable} {pvst|rpvst}
- Example:

  ```
  admin@sonic:~$ sudo config spanning_tree enable pvst
  admin@sonic:~$ sudo config spanning_tree enable rpvst
  ```


**config spanning_tree root_guard_timeout**

This command allows configuring a root guard timeout value. Once superior BPDUs stop coming on the port, device will wait for a period until root guard timeout before moving the port to forwarding state(default = 30 secs), range 5-600.

- Usage: 
  config spanning_tree root_guard_timeout <value\>
- Example:

  ```
  admin@sonic:~$ sudo config spanning_tree root_guard_timeout 40
  ```

**config spanning_tree forward_delay**

This command allows configuring the forward delay time in seconds (default = 15), range 4-30.

- Usage: 
  config spanning_tree forward_delay <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree forward_delay 20
```

**config spanning_tree hello**

This command allows configuring hello interval in secs for transmission of BPDUs (default = 2), range 1-10.

- Usage: 
  config spanning_tree hello <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree hello 3
```

**config spanning_tree max_age**

This command allows configuring the maximum time to listen for root bridge in seconds (default = 20), range 6-40.

- Usage: 
  config spanning_tree max_age <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree max_age 25
```

**config spanning_tree priority**

This command allows configuring the bridge priority in increments of 4096 (default = 32768), range 0-61440.

- Usage: 
  config spanning_tree priority <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree priority 4096
```

#### VLAN commands

Following commands allow to enable or disable PVST or RPVST per VLAN and configure STP parameters per VLAN.

**config spanning_tree vlan enable or disable**

This command allows enabling or disabling spanning-tree on a particular VLAN.

- Usage: 
  config spanning_tree vlan {enable|disable} <vlan\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree vlan enable 100
```

**STP VLAN parameters**

Below commands are similar to the global level commands but allow configuring STP parameters on per VLAN basis.

- Usage: 
  config spanning_tree vlan {forward_delay|hello|max_age|priority} <vlan\> <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree vlan forward_delay 100 20
  admin@sonic:~$ sudo config spanning_tree vlan hello 100 3
  admin@sonic:~$ sudo config spanning_tree vlan max_age 100 25
  admin@sonic:~$ sudo config spanning_tree vlan priority 100 4096  
```

#### Interface commands

Following command allow to enable or disable PVST or RPVST per interface and configure STP parameters per interface.

**config spanning_tree interface enable or disable** 

This command allows enabling or disabling of STP on an interface, by default STP will be enabled on the interface if global STP mode is configured.

- Usage: 
  config spanning_tree interface {enable|disable} <if-name\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface enable Ethernet0
  admin@sonic:~$ sudo config spanning_tree interface enable PortChannel100
```

**config spanning_tree interface priority**

This command allows to configure the port level priority value, range 0 - 240 (default 128)

- Usage: 
  config spanning_tree interface priority <if-name\> <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface priority Ethernet0 64
  admin@sonic:~$ sudo config spanning_tree interface priority PortChannel100 64
```

**configure spanning_tree interface cost**

This command allows to configure the port level cost value, range 1 - 200000000

- Usage: 
  configure spanning_tree interface cost <if-name\> <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface cost Ethernet0 100
  admin@sonic:~$ sudo config spanning_tree interface cost PortChannel100 100
```

**config spanning_tree interface root_guard **

This command allow enabling or disabling of root guard on an interface.

- Usage: 
  config spanning_tree interface root_guard {enable|disable} <if-name\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface root_guard enable Ethernet0
```

**config spanning_tree interface bpdu_guard**

This command allows enabling or disabling of bpdu guard on an interface. By default, BPDU guard will only generate a syslog indicating the condition, for taking an action like disabling the port use shutdown option. 

- Usage: 
  config spanning_tree interface bpdu_guard {enable|disable} <ifname\> [--shutdown | -s]
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface bpdu_guard enable Ethernet0
  admin@sonic:~$ sudo config spanning_tree interface bpdu_guard enable Ethernet0 --shutdown  
```

**config spanning_tree interface portfast**

This command allows enabling or disabling of portfast on an interface. Portfast command is enabled by default on all ports. This command is not applicable to RPVST.  

- Usage: 
  config spanning_tree interface portfast {enable|disable} <if-name\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface portfast disable Ethernet0 
```

**config spanning_tree interface uplink_fast**

This command allows enabling or disabling of uplink_fast on an interface. uplink_fast command is disabled by default on all ports. This command is not applicable to RPVST. 

- Usage: 
  config spanning_tree interface uplink_fast {enable|disable} <if-name\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree interface uplink_fast enable Ethernet0 
```

#### VLAN, Interface commands

Following command allow to configure port cost and port priority per VLAN, interface port.

**config spanning_tree vlan interface cost**

This command allows to configure the port cost value per VLAN, interface basis, range 1 - 200000000

- Usage: 
  config spanning_tree vlan interface cost <vlan\> <if-name\> <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree vlan interface cost 100 Ethernet0 100
  admin@sonic:~$ sudo config spanning_tree vlan interface cost 100 PortChannel100 100
```

**config spanning_tree vlan interface priority**

This command allows to configure the port priority value VLAN, interface basis, range 0 - 240 (default 128)

- Usage: 
  config spanning_tree vlan interface priority <vlan\> <if-name\> <value\>
- Example:

```
  admin@sonic:~$ sudo config spanning_tree vlan interface priority 100 Ethernet0 100
  admin@sonic:~$ sudo config spanning_tree vlan interface priority 100 PortChannel100 100
```

### Show commands 

Use following command to display information about spanning tree in the system.

**show spanning_tree**

This command shows information about spanning tree state information. 

- Usage: 
  show spanning_tree vlan <vlanid\> interface <if-name\>

- Examples:

```
  admin@sonic:~$ show spanning_tree
  Spanning-tree Mode: PVST
  VLAN 100 - STP instance 3
  --------------------------------------------------------------------
  STP Bridge Parameters:

  Bridge           Bridge Bridge Bridge Hold  LastTopology Topology
  Identifier       MaxAge Hello  FwdDly Time  Change       Change
  hex              sec    sec    sec    sec   sec          cnt
  8000002438eefbc3 20     2      15     1     0            0       

  RootBridge       RootPath  DesignatedBridge Root  Max Hel Fwd
  Identifier       Cost      Identifier       Port  Age lo  Dly
  hex                        hex                    sec sec sec
  8000002438eefbc3 0         8000002438eefbc3 Root  20  2   15  

  STP Port Parameters:

  Port        Prio Path Port Uplink   State      Designated  Designated       Designated
  Num         rity Cost Fast Fast                Cost        Root             Bridge
  Ethernet13  128  4    Y    N        FORWARDING 0           8000002438eefbc3 8000002438eefbc3 
```

```
  admin@sonic:~$ show spanning_tree
  Spanning-tree Mode: RPVST
  VLAN 100 - STP instance 3
  --------------------------------------------------------------------
  STP Bridge Parameters:

  Bridge           Bridge Bridge Bridge Hold  LastTopology Topology
  Identifier       MaxAge Hello  FwdDly Time  Change       Change
  hex              sec    sec    sec    sec   sec          cnt
  8000002438eefbc3 20     2      15     1     0            0       

  RootBridge       RootPath  DesignatedBridge Root  Max Hel Fwd
  Identifier       Cost      Identifier       Port  Age lo  Dly
  hex                        hex                    sec sec sec
  8000002438eefbc3 0         8000002438eefbc3 Root  20  2   15  

  STP Port Parameters:

  Port        Prio Path Port Uplink   State      Designated  Designated       Designated
  Num         rity Cost Fast Fast                Cost        Root             Bridge
  Ethernet13  128  4    Y    N        FORWARDING 0           8000002438eefbc3 8000002438eefbc3 
```

**show spanning_tree bpdu_guard**
This command displays the interfaces which are BPDU guard enabled and also the state if the interface is disabled due to BPDU guard.

- Usage: 
  show spanning_tree bpdu_guard
- Example: 

```
admin@sonic:~$ show spanning_tree bpdu_guard
PortNum            Shutdown      Port shut
                   Configured    due to BPDU guard
-------------------------------------------------
Ethernet1            Yes          Yes
Ethernet2            Yes          No
Port-Channel2        No           NA
```

**show spanning_tree root_guard**
This command displays the interfaces where root guard is active and the pending time for root guard timer expiry

- Usage: 
  show spanning_tree root_guard
- Example: 

```
admin@sonic:~$ show spanning_tree root_guard
Root guard timeout: 120 secs

Port         VLAN    Current State
-------------------------------------------------
Ethernet1    1       Inconsistent state (102 seconds left on timer)
Ethernet8    100     Consistent state
```

**show spanning_tree statistics**

This command displays the spanning-tree bpdu statistics. Statistics will be synced to APP DB every 10 seconds.

- Usage: 
  show spanning_tree statistics [vlan <vlanid\>]

- Example: 

```
  admin@sonic:~$ show spanning_tree statistics 
  VLAN 100 - STP instance 3
  --------------------------------------------------------------------
  PortNum           BPDU Tx     BPDU Rx     TCN Tx      TCN Rx             
  Ethernet13        10	      4           3          4
  PortChannel15     20	      6           4          1
```

### Debug commands

Following debug commands can be used for enabling additional logging which can be viewed in /var/log/stpd.log 
- debug spanning_tree bpdu [rx/tx]
- debug spanning_tree event
- debug spanning_tree vlan <id\>
- debug spanning_tree interface <if-name\>
- debug spanning_tree verbose

To disable the debugging controls enabled, '-d' or '--disable' option can be used. An example of disabling bpdu debugging is shown below:

```
admin@sonic:~$ sudo debug spanning_tree bpdu -d 
```

Following commands can be used to reset and display the debugging controls enabled respectively
- debug spanning_tree reset
- debug spanning_tree show

Following debug commands will be supported for displaying internal data structures
- debug spanning_tree dump global
- debug spanning_tree dump vlan <vid\>
- debug spanning_tree dump interface <vid\> <if-name\>

### Clear Commands

Following clear commands can use to clear STP counters. 
- sonic-clear spanning_tree statistics
- sonic-clear spanning_tree statistics vlan <vid\>
- sonic-clear spanning_tree statistics vlan-interface <vid\> <if-name\>
- sonic-clear spanning_tree statistics interface <if-name\>

# Startup & Running Configuration

## Startup Configuration command

**show startupconfiguration bgp**  

This command is used to display the startup configuration for the BGP module. 

  - Usage:  
    show startupconfiguration bgp`


- Example:
```
  admin@sonic:~$ show startupconfiguration bgp
  Routing-Stack is: quagga
  !
  ! =========== Managed by sonic-cfggen DO NOT edit manually! ====================
  ! generated by templates/quagga/bgpd.conf.j2 with config DB data
  ! file: bgpd.conf
  !
  !
  hostname T1-2
  password zebra
  log syslog informational
  log facility local4
  ! enable password !
  !
  ! bgp multiple-instance
  !
  route-map FROM_BGP_SPEAKER_V4 permit 10
  !
  route-map TO_BGP_SPEAKER_V4 deny 10
  !
  router bgp 65000
    bgp log-neighbor-changes
    bgp bestpath as-path multipath-relax
    no bgp default ipv4-unicast
    bgp graceful-restart restart-time 180  
    
    <Only the partial output is shown here. In actual command, more configuration information will be displayed> 
```

## Running Configuration command
This sub-section explains the show commands for displaying the running configuration for the following modules.
1) bgp 
2) interfaces
3) ntp
4) snmp
5) all 
6) acl
7) interface

**show runningconfiguration all**  

This command displays the entire running configuration.

  - Usage:  
    show runningconfiguration all


- Example:
  ```
  admin@sonic:~$ show runningconfiguration all
  ```

**show runningconfiguration bgp**  

This command displays the running configuration of the BGP module.

  - Usage:  
    show runningconfiguration bgp


- Example:
  ```
  admin@sonic:~$ show runningconfiguration bgp
  ```

**show runningconfiguration interfaces**  

This command displays the running configuration for the "interfaces".

  - Usage:  
    show runningconfiguration interfaces


- Example:
  ```
  admin@sonic:~$ show runningconfiguration interfaces
  ```

**show runningconfiguration ntp**  

This command displays the running configuration of the ntp module. 

  - Usage:  
    show runningconfiguration ntp


- Example:
  ```
  admin@sonic:~$ show runningconfiguration ntp
  ```

**show runningconfiguration snmp**  

This command displays the running configuration of the snmp module. 

  - Usage:  
    show runningconfiguration snmp


- Example:
  ```
  admin@sonic:~$ show runningconfiguration snmp
  ```

**show runningconfiguration acl**

 This command displays the running configuration of the acls

   - Usage:

    show runningconfiguration acl


 - Example:
  ```
  admin@sonic:~$ show runningconfiguration acl
  ```

 **show runningconfiguration interface <interfacename>**

 This command displays the running configuration of the ports

   - Usage:

    show runningconfiguration interface <interfacename>


 - Example:
  ```
  admin@sonic:~$ show runningconfiguration interface
  ```

  ```
  admin@sonic:~$ show runningconfiguration interface <interfacename>
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Startup--Running-Configuration)



# System State

## Processes show commands

This command is used to determine the CPU utilization. It also lists the active processes along with their corresponding process ID and other relevant parameters.

This sub-section explains the various "processes" specific data that includes the following.
1) cpu      Show processes CPU info
2) memory   Show processes memory info
3) summary  Show processes info

?show processes? commands provide a wrapper over linux?s ?top? command. ?show process cpu? sorts the processes being displayed by cpu-utilization, whereas ?show process memory? does it attending to processes? memory-utilization.

**show processes cpu**  

This command displays the current CPU usage by process. This command uses linux's "top -bn 1 -o %CPU" command to display the output.

  - Usage:  
    show processes cpu

    NOTE that pipe option can be used using " | head -n" to display only the "n" number of lines.


- Example:
   ```
  admin@SONiC:~$ show processes cpu
  top - 23:50:08 up  1:18,  1 user,  load average: 0.25, 0.29, 0.25
  Tasks: 161 total,   1 running, 160 sleeping,   0 stopped,   0 zombie
  %Cpu(s):  3.8 us,  1.0 sy,  0.0 ni, 95.1 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
  KiB Mem:   8181216 total,  1161060 used,  7020156 free,   105656 buffers
  KiB Swap:        0 total,        0 used,        0 free.   557560 cached Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
   2047 root      20   0  683772 109288  39652 S  23.8  1.3   7:44.79 syncd
   1351 root      20   0   43360   5616   2844 S  11.9  0.1   1:41.56 redis-server
  10093 root      20   0   21944   2476   2088 R   5.9  0.0   0:00.03 top
      1 root      20   0   28992   5508   3236 S   0.0  0.1   0:06.42 systemd
      2 root      20   0       0      0      0 S   0.0  0.0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0.0  0.0   0:00.56 ksoftirqd/0
      5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
  ```

**show processes memory**  

This command displays the current memory usage by processes. This command uses linux's "top -bn 1 -o %MEM" command to display the output.

  - Usage:  
    show processes memory

    NOTE that pipe option can be used using " | head -n" to display only the "n" number of lines.


- Example:
  ```
  admin@SONiC:~$  show processes memory 
  top - 23:41:24 up 7 days, 39 min,  2 users,  load average: 1.21, 1.19, 1.18
  Tasks: 191 total,   2 running, 189 sleeping,   0 stopped,   0 zombie
  %Cpu(s):  2.8 us, 20.7 sy,  0.0 ni, 76.3 id,  0.0 wa,  0.0 hi,  0.2 si,  0.0 st
  KiB Mem :  8162264 total,  5720412 free,   945516 used,  1496336 buff/cache
  KiB Swap:        0 total,        0 free,        0 used.  6855632 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
  18051 root      20   0  851540 274784   8344 S   0.0  3.4   0:02.77 syncd
  17760 root      20   0 1293428 259212  58732 S   5.9  3.2  96:46.22 syncd
    508 root      20   0  725364  76244  38220 S   0.0  0.9   4:54.49 dockerd
  30853 root      20   0   96348  56824   7880 S   0.0  0.7   0:00.98 show
  17266 root      20   0  509876  49772  30640 S   0.0  0.6   0:06.36 docker
  24891 admin     20   0  515864  49560  30644 S   0.0  0.6   0:05.54 docker
  17643 admin     20   0  575668  49428  30628 S   0.0  0.6   0:06.29 docker
  23885 admin     20   0  369552  49344  30840 S   0.0  0.6   0:05.57 docker
  18055 root      20   0  509076  49260  30296 S   0.0  0.6   0:06.36 docker
  17268 root      20   0  371120  49052  30372 S   0.0  0.6   0:06.45 docker
   1227 root      20   0  443284  48640  30100 S   0.0  0.6   0:41.91 docker
  23785 admin     20   0  443796  48552  30128 S   0.0  0.6   0:05.58 docker
  17820 admin     20   0  435088  48144  29480 S   0.0  0.6   0:06.33 docker
    506 root      20   0 1151040  43140  23964 S   0.0  0.5   8:51.08 containerd
  18437 root      20   0   84852  26388   7380 S   0.0  0.3  65:59.76 python3.6
  ```


**show processes summary**  

This command displays the current summary information about all the processes

  - Usage:  
    show processes summary


- Example:
  ```
  admin@SONiC:~$  show processes summary 
    PID  PPID CMD                         %MEM %CPU
  	1     0 /sbin/init                   0.0  0.0
  	2     0 [kthreadd]                   0.0  0.0
  	3     2 [ksoftirqd/0]                0.0  0.0
  	5     2 [kworker/0:0H]               0.0  0.0
  ```


## Services & memory show commands

These commands are used to know the services that are running and the memory that is utilized currently.


**show services**  

This command displays the state of all the SONiC processes running inside a docker container. This helps to identify the status of SONiC?s critical processes.

  - Usage:  
    sonic_installer remove <image_name>


- Example:
  ```
  admin@lnos-x1-a-asw02:~$ show services
  dhcp_relay      docker
  ---------------------------
  UID        PID  PPID  C STIME TTY          TIME CMD
  root         1     0  0 05:26 ?        00:00:12 /usr/bin/python /usr/bin/supervi
  root        24     1  0 05:26 ?        00:00:00 /usr/sbin/rsyslogd -n

  snmp    docker
  ---------------------------
  UID        PID  PPID  C STIME TTY          TIME CMD
  root         1     0  0 05:26 ?        00:00:16 /usr/bin/python /usr/bin/supervi
  root        24     1  0 05:26 ?        00:00:02 /usr/sbin/rsyslogd -n
  Debian-+    29     1  0 05:26 ?        00:00:04 /usr/sbin/snmpd -f -LS4d -u Debi
  root        31     1  1 05:26 ?        00:15:10 python3.6 -m sonic_ax_impl

  syncd   docker
  ---------------------------
  UID        PID  PPID  C STIME TTY          TIME CMD
  root         1     0  0 05:26 ?        00:00:13 /usr/bin/python /usr/bin/supervi
  root        12     1  0 05:26 ?        00:00:00 /usr/sbin/rsyslogd -n
  root        17     1  0 05:26 ?        00:00:00 /usr/bin/dsserve /usr/bin/syncd
  root        27    17 22 05:26 ?        04:09:30 /usr/bin/syncd --diag -p /usr/sh
  root        51    27  0 05:26 ?        00:00:01 /usr/bin/syncd --diag -p /usr/sh

  swss    docker
  ---------------------------
  UID        PID  PPID  C STIME TTY          TIME CMD
  root         1     0  0 05:26 ?        00:00:29 /usr/bin/python /usr/bin/supervi
  root        25     1  0 05:26 ?        00:00:00 /usr/sbin/rsyslogd -n
  root        30     1  0 05:26 ?        00:00:13 /usr/bin/orchagent -d /var/log/s
  root        42     1  1 05:26 ?        00:12:40 /usr/bin/portsyncd -p /usr/share
  root        45     1  0 05:26 ?        00:00:00 /usr/bin/intfsyncd
  root        48     1  0 05:26 ?        00:00:03 /usr/bin/neighsyncd
  root        59     1  0 05:26 ?        00:00:01 /usr/bin/vlanmgrd
  root        92     1  0 05:26 ?        00:00:01 /usr/bin/intfmgrd
  root      3606     1  0 23:36 ?        00:00:00 bash -c /usr/bin/arp_update; sle
  root      3621  3606  0 23:36 ?        00:00:00 sleep 300
  ```

**show system-memory**  

This command displays the system-wide memory utilization information ? just a wrapper over linux native ?free? command

  - Usage:  
    show system-memory


- Example:
  ```
  admin@lnos-x1-a-asw02:~$ show system-memory
  Command: free -m -h
  			 total       used       free     shared    buffers     cached
  Mem:          3.9G       2.0G       1.8G        33M       324M       791M
  -/+ buffers/cache:       951M       2.9G
  Swap:           0B         0B         0B
  ```

**show mmu**  

This command displays virtual address to the physical address translation status of the Memory Management Unit (MMU).

  - Usage:  
    show mmu


- Example:
```
  admin@T1-2:~$ show mmu
  Pool: ingress_lossless_pool
  ----  --------
  xoff  4194112
  type  ingress
  mode  dynamic
  size  10875072
  ----  --------

  Pool: egress_lossless_pool
  ----  --------
  type  egress
  mode  static
  size  15982720
  ----  --------

  Pool: egress_lossy_pool
  ----  -------
  type  egress
  mode  dynamic
  size  9243812
  ----  -------

  Profile: egress_lossy_profile
  ----------  -------------------------------
  dynamic_th  3
  pool        [BUFFER_POOL|egress_lossy_pool]
  size        1518
  ----------  -------------------------------

  Profile: pg_lossless_100000_300m_profile
  ----------  -----------------------------------
  xon_offset  2288
  dynamic_th  -3
  xon         2288
  xoff        268736
  pool        [BUFFER_POOL|ingress_lossless_pool]
  size        1248
  ----------  -----------------------------------

  Profile: egress_lossless_profile
  ---------  ----------------------------------
  static_th  3995680
  pool       [BUFFER_POOL|egress_lossless_pool]
  size       1518
  ---------  ----------------------------------

  Profile: pg_lossless_100000_40m_profile
  ----------  -----------------------------------
  xon_offset  2288
  dynamic_th  -3
  xon         2288
  xoff        177632
  pool        [BUFFER_POOL|ingress_lossless_pool]
  size        1248
  ----------  -----------------------------------

  Profile: ingress_lossy_profile
  ----------  -----------------------------------
  dynamic_th  3
  pool        [BUFFER_POOL|ingress_lossless_pool]
  size        0
  ----------  -----------------------------------

  Profile: pg_lossless_40000_40m_profile
  ----------  -----------------------------------
  xon_offset  2288
  dynamic_th  -3
  xon         2288
  xoff        71552
  pool        [BUFFER_POOL|ingress_lossless_pool]
  size        1248
  ----------  -----------------------------------
```


**show line**  

This command displays serial port or a virtual network connection status.
This command is used only when SONiC is used as console switch. 
This command is not applicable when SONiC used as regular switch.
NOTE: This command is not working. It crashes as follows. A bug ticket is opened for this issue.

  - Usage:  
    show line

- Example:   

```
  admin@T1-2:~$ show line

```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#System-State)


# VLAN &amp; FDB

## VLAN

### VLAN show commands

**show vlan brief**  

This command displays brief information about all the vlans configured in the device. It displays the vlan ID, IP address (if configured for the vlan), list of vlan member ports, whether the port is tagged or in untagged mode and the DHCP Helper Address.

  - Usage:  
    show vlan brief


- Example:
  ```
  admin@sonic:~$ show vlan brief 

  +-----------+--------------+-----------+----------------+-----------------------+
  |   VLAN ID | IP Address   | Ports     | Port Tagging   | DHCP Helper Address   |
  +===========+==============+===========+================+=======================+
  |       100 | 1.1.2.2/16   | Ethernet0 | tagged         | 192.0.0.1             |
  |           |              | Ethernet4 | tagged         | 192.0.0.2             |
  |           |              |           |                | 192.0.0.3             |
  +-----------+--------------+-----------+----------------+-----------------------+

  ```

**show vlan config**  

This command displays all the vlan configuration.

  - Usage:  
    show vlan config


- Example:
  ```
  admin@sonic:~$ show vlan config 
  Name       VID  Member     Mode
  -------  -----  ---------  ------
  Vlan100    100  Ethernet0  tagged
  Vlan100    100  Ethernet4  tagged

  ```

**show vlan count**  

This command displays the count of the number  of VLAN


  - Usage:  
    show vlan count


- Example:
  ```
  admin@sonic:~$ show vlan count
  Total Vlan count:1 

  ```

### VLAN Config commands

This sub-section explains how to configure the vlan and its member ports.

**config vlan add/del**  

This command is used to add or delete the vlan. 

  - Usage:  
    config vlan add/del <vlan__id> 


- Example:
  ```
  admin@sonic:~$ sudo config vlan add 100
  This command will create the vlan 100 if not exists.
  ```



**config vlan range add/del**

Use this command to add/del vlan in bulk

- Usage:  
  Config vlan range [add|del]  <first_vlan_id> <last_vlan_id>

- Example:

  ```
  admin@sonic:~$ config vlan range add 100 103
  This command will create vlans from vlan id 100 to vlan id 103. eg, vlan 100, vlan 101, vlan 102, vlan 103 will be created 

  config vlan range del 100 103
  This command will delete vlans from vlan id 100 to vlan id 103. eg, vlan 100, vlan 101, vlan 102, vlan 103 will be removed 
  ```



**config vlan member range add/del**

Use this command to add/del vlan port member in bulk. Valid VLAN id range is from 1 to 4094. 

- Usage:  
  config vlan member range [add|del]  <first_vlan_id> <last_vlan_id> <interface_name>

- Example:

  ```
  admin@sonic:~$ config vlan member range add 100 103 Ethernet0
  This command will add Ethernet0 from vlan id 100 to vlan id 103. eg, Ethernet0 will become the member port of vlan 100, vlan 101, vlan 102, vlan 103  

  admin@sonic:~$ config vlan member range del 100 103 Ethernet0
  This command will delete Ethernet0 from vlan id 100 to vlan id 103. eg, Ethernet0 will be removed from vlan 100, vlan 101, vlan 102, vlan 103 
  ```



**config vlan member add/del**  

This command is to add or delete a member port into the already created vlan. Valid VLAN id is from 1 to 4094. 

  - Usage:  
    config vlan member add/del [-u or --untagged] <vlan_id> <member_portname>   
    -u will set the port in untagged mode.


- Example:
  ```
  admin@sonic:~$ sudo config vlan member add 100 Ethernet0
  This command will add Ethernet0 as member of the vlan 100

  admin@sonic:~$ sudo config vlan member add 100 Ethernet4
  This command will add Ethernet4 as member of the vlan 100.
  ```



## MAC

### MAC age out show command

**show mac aging-time**

This command displays MAC age out interval.

- Usage:  
  show mac aging-time

- Example:

  ```
  admin@sonic:~$ show mac aging_time
  Mac Aging-Time : 3000 seconds
  ```

### MAC age out config command

**config mac aging-time**

Use this command to change MAC ageout time, default MAC ageout is set as 600 sec  

- Usage:  

  config mac aging_time  <ageout_interval> 

  Specifies ageout_internal in secs, range from 0 - 1000000, 0 to disable MAC aging 

- Example: 
  ```
  admin@sonic:~$ config mac aging_time 3000
  This command will set MAC age out to 3000 secs
  ```

### Static MAC config command

**config and delete static mac**

Use this command to configure or delete static MAC   

- Usage:  

  config mac [add|del] <static_mac_address> <vlan_id> [<interface_name>]

  Specifies static mac address to be configured or removed  

- Example: 

  ```
  admin@sonic:~$config mac add 00:10:3a:2b:05:67 100 Ethernet2
  To add a static mac on vlan 100 for Ethernet2

  admin@sonic:~$config mac del 00:10:3a:2b:05:67 100
  To delete a static mac from vlan 100
  ```



## FDB

### FDB show commands

**show mac**  

This command displays the MAC (FDB) entries either in full or partial as given below.
1) show mac - displays the full table
2) show mac -v <vlanid> - displays the MACs learnt on the particular VLAN ID.
3) show mac -p <port>  - displays the MACs learnt on the particular port.


  - Usage:  
    show mac [-v vlan_id] [-p port_name]


- Example:
  ```
  admin@sonic:~$ show mac
  No.    Vlan  MacAddress         Port
  -----  ------  -----------------  -----------
    1    1000  E2:8C:56:85:4A:CD  Ethernet192
    2    1000  A0:1B:5E:47:C9:76  Ethernet192
    3    1000  AA:54:EF:2C:EE:30  Ethernet192
    4    1000  A4:3F:F2:17:A3:FC  Ethernet192
    5    1000  0C:FC:01:72:29:91  Ethernet192
    6    1000  48:6D:01:7E:C9:FD  Ethernet192
    7    1000  1C:6B:7E:34:5F:A6  Ethernet192
    8    1000  EE:81:D9:7B:93:A9  Ethernet192
    9    1000  CC:F8:8D:BB:85:E2  Ethernet192
   10    1000  0A:52:B3:9C:FB:6C  Ethernet192
   11    1000  C6:E2:72:02:D1:23  Ethernet192
   12    1000  8A:C9:5C:25:E9:28  Ethernet192
   13    1000  5E:CD:34:E4:94:18  Ethernet192
   14    1000  7E:49:1F:B5:91:B5  Ethernet192
   15    1000  AE:DD:67:F3:09:5A  Ethernet192
   16    1000  DC:2F:D1:08:4B:DE  Ethernet192
   17    1000  50:96:23:AD:F1:65  Ethernet192
   18    1000  C6:C9:5E:AE:24:42  Ethernet192
  Total number of entries 18 
  ```

  - Optionally, you can specify a VLAN ID or interface name in order to display only that particular entries

- Example:
  ```
  admin@sonic:~$ show mac -v 1000
  No.    Vlan  MacAddress         Port
  -----  ------  -----------------  -----------
    1    1000  E2:8C:56:85:4A:CD  Ethernet192
    2    1000  A0:1B:5E:47:C9:76  Ethernet192
    3    1000  AA:54:EF:2C:EE:30  Ethernet192
    4    1000  A4:3F:F2:17:A3:FC  Ethernet192
    5    1000  0C:FC:01:72:29:91  Ethernet192
    6    1000  48:6D:01:7E:C9:FD  Ethernet192
    7    1000  1C:6B:7E:34:5F:A6  Ethernet192
    8    1000  EE:81:D9:7B:93:A9  Ethernet192
    9    1000  CC:F8:8D:BB:85:E2  Ethernet192
   10    1000  0A:52:B3:9C:FB:6C  Ethernet192
   11    1000  C6:E2:72:02:D1:23  Ethernet192
   12    1000  8A:C9:5C:25:E9:28  Ethernet192
   13    1000  5E:CD:34:E4:94:18  Ethernet192
   14    1000  7E:49:1F:B5:91:B5  Ethernet192
   15    1000  AE:DD:67:F3:09:5A  Ethernet192
   16    1000  DC:2F:D1:08:4B:DE  Ethernet192
   17    1000  50:96:23:AD:F1:65  Ethernet192
   18    1000  C6:C9:5E:AE:24:42  Ethernet192
  Total number of entries 18 

  admin@sonic:~$ show mac -p Ethernet192
  No.    Vlan  MacAddress         Port
  -----  ------  -----------------  -----------
    1    1000  E2:8C:56:85:4A:CD  Ethernet192
    2    1000  A0:1B:5E:47:C9:76  Ethernet192
    3    1000  AA:54:EF:2C:EE:30  Ethernet192
    4    1000  A4:3F:F2:17:A3:FC  Ethernet192
    5    1000  0C:FC:01:72:29:91  Ethernet192
    6    1000  48:6D:01:7E:C9:FD  Ethernet192
    7    1000  1C:6B:7E:34:5F:A6  Ethernet192
    8    1000  EE:81:D9:7B:93:A9  Ethernet192
    9    1000  CC:F8:8D:BB:85:E2  Ethernet192
   10    1000  0A:52:B3:9C:FB:6C  Ethernet192
   11    1000  C6:E2:72:02:D1:23  Ethernet192
   12    1000  8A:C9:5C:25:E9:28  Ethernet192
   13    1000  5E:CD:34:E4:94:18  Ethernet192
   14    1000  7E:49:1F:B5:91:B5  Ethernet192
   15    1000  AE:DD:67:F3:09:5A  Ethernet192
   16    1000  DC:2F:D1:08:4B:DE  Ethernet192
   17    1000  50:96:23:AD:F1:65  Ethernet192
   18    1000  C6:C9:5E:AE:24:42  Ethernet192
  Total number of entries 18 
  ```

**show mac count**  

This command displays the count of the number of MAC (FDB) entries


  - Usage:  
    show mac count


- Example:
  ```
  admin@sonic:~$ show mac count
  Total MAC count:1 
  ```


**sonic-clear fdb**

Command and options to clear MAC entries from FDB. When port or vlan is used, it will clear MAC entries from the specified VLAN or Port.

- Usage:  
  Sonic-clear fdb [all|port <port_id> |vlan <vlan_alias>] 

- Example:

```
admin@sonic:~$ sonic-clear fdb all
All MAC entries are cleared from FDB

admin@sonic:~$ sonic-clear fdb vlan Vlan101
('Dynamic FDB entries are cleared on VLAN.', 'Vlan101')
All MAC entries are cleared from vlan 101
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#vlan--FDB)



# VXLAN

## VXLAN show commands

**show vxlan interface** 

   Displays the name, SIP, PIP, associated NVO name, QoS mode (uniform/pipe) and DSCP value. 
   Displays the loopback interface(s) configured with the VTEP SIP and PIP.

- Usage:  
  show vxlan interface

- Example:
```
admin@sonic:~$ show vxlan interface
VTEP Information:

           VTEP Name : VTEP1, SIP  : 4.4.4.4
           Primary IP  : 2.2.2.2
           QoS Mode  : pipe(dscp:20)
           NVO Name  : nvo1,  VTEP : VTEP1
           Source interface  : Loopback33
           Primary IP interface : Loopback20
```


**show vxlan vlanvnimap**

   Displays all the VLAN VNI mappings along with the count. With the count
   argument displays only the total count of the mappings.

- Usage:  
  show vxlan vlanvnimap [count]

- Example:

```

admin@sonic:~$ show vxlan vlanvnimap
+--------+-------+
| VLAN   |   VNI |
+========+=======+
| Vlan5  |     5 |
+--------+-------+
| Vlan6  |     6 |
+--------+-------+
Total count : 2

admin@sonic:~$ show vxlan vlanvnimap count
Total mapping count:2

```

**show vxlan vrfvnimap**

   Displays all the VRF VNI mappings.

- Usage:  
  show vxlan vrfvnimap 

- Example:

```
admin@sonic:~$ show vxlan vrfvnimap
+--------+-------+
| VRF    |   VNI |
+========+=======+
| Vrf1   |     5 |
+--------+-------+
Total count : 1
```

**show vxlan tunnel**

   Lists all the discovered tunnels. The optional parameter 'count' 
   displays only the total number of tunnels.  

- Usage:  
  show vxlan tunnel [count] 

- Example:

```
admin@sonic:~$ show vxlan tunnel 
   +---------+---------+-------------------+--------------+
   | SIP     | DIP     | Creation Source   | OperStatus   |
   +=========+=========+===================+==============+
   | 2.2.2.2 | 4.4.4.4 | EVPN              | oper_up      |
   +---------+---------+-------------------+--------------+
   | 2.2.2.2 | 3.3.3.3 | EVPN              | oper_up      |
   +---------+---------+-------------------+--------------+
   Total count : 2

admin@sonic:~$ show vxlan tunnel count
Total mapping count:0
```

**show vxlan evpn_remote_mac**

   Lists all the MACs learnt from the specified remote ip or all the remotes 
   for the specified/all vlans. (APP DB view). The optional 'count' argument 
   when given prints only the total number of such MACs.  

- Usage:  
  show vxlan evpn_remote_mac <remoteip/all> [count]

- Example:

```
admin@sonic:~$ show vxlan remote_mac all
   +---------+-------------------+--------------+-------+--------+
   | VLAN    | MAC               | RemoteVTEP   |   VNI | Type   |
   +=========+===================+==============+=======+========+
   | Vlan101 | 00:00:00:00:00:01 | 4.4.4.4      |  1001 | dynamic|
   +---------+-------------------+--------------+-------+--------+
   | Vlan101 | 00:00:00:00:00:02 | 3.3.3.3      |  1001 | static |
   +---------+-------------------+--------------+-------+--------+
   | Vlan101 | 00:00:00:00:00:03 | 4.4.4.4      |  1001 | dynamic|
   +---------+-------------------+--------------+-------+--------+
   | Vlan101 | 00:00:00:00:00:04 | 4.4.4.4      |  1001 | static |
   +---------+-------------------+--------------+-------+--------+
   | Vlan101 | 00:00:00:00:00:05 | 4.4.4.4      |  1001 | static |
   +---------+-------------------+--------------+-------+--------+
   | Vlan101 | 00:00:00:00:00:99 | 3.3.3.3      |  1001 | static |
   +---------+-------------------+--------------+-------+--------+
   Total count : 6

admin@sonic:~$ show vxlan evpn_remote_mac all count
Total mapping count:6

admin@sonic:~$ show vxlan remote_mac 3.3.3.3
   +---------+-------------------+--------------+-------+--------+
   | VLAN    | MAC               | RemoteVTEP   |   VNI | Type   |
   +=========+===================+==============+=======+========+
   | Vlan101 | 00:00:00:00:00:02 | 3.3.3.3      |  1001 | static |
   +---------+-------------------+--------------+-------+--------+
   | Vlan101 | 00:00:00:00:00:99 | 3.3.3.3      |  1001 | static |
   +---------+-------------------+--------------+-------+--------+
   Total count : 2

admin@sonic:~$ show vxlan evpn_remote_mac 3.3.3.3 count
Total mapping count:2

```

**show vxlan evpn_remote_vni**

   Lists all the VLANs learnt from the specified remote ip or all the remotes.(APP DB view) 
   The optional count argument when given prints the total number of such VLANs.

- Usage:  

  show vxlan evpn_remote_vni <remoteip/all> [count]

- Example:

```
admin@sonic:~$ show vxlan evpn_remote_vni all
   +---------+--------------+-------+
   | VLAN    | RemoteVTEP   |   VNI |
   +=========+==============+=======+
   | Vlan101 | 3.3.3.3      |  1001 |
   +---------+--------------+-------+
   | Vlan101 | 4.4.4.4      |  1001 |
   +---------+--------------+-------+
   Total count : 2

admin@sonic:~$ show vxlan evpn_remote_vni all count
Total mapping count:2

admin@sonic:~$ show vxlan evpn_remote_vni 3.3.3.3
   +---------+--------------+-------+
   | VLAN    | RemoteVTEP   |   VNI |
   +=========+==============+=======+
   | Vlan101 | 3.3.3.3      |  1001 |
   +---------+--------------+-------+
   Total count : 1

admin@sonic:~$ show vxlan evpn_remote_vni 3.3.3.3 count
Total mapping count:1

```

## VXLAN config commands

**config vxlan add**

  Creates the VTEP instance. Only a single instance is supported in this release.

- Usage: 

  config vxlan add `<vtepname> <src_ip>`

- Example:

```
admin@sonic:~$ sudo config vxlan add vtep1 1.1.1.1 
```

**config vxlan del**

  Deletes the VTEP instance. There should be no EVPN_NVO object or VLAN/VRF VNI mapping
  for this command to succeed.

- Usage:

    config vxlan del `<vtepname>` 

- Example:

```
admin@sonic:~$ sudo config vxlan del vtep1  
```

**config vxlan evpn_nvo add**

  Creates the EVPN instance and associates with the VTEP instance. 
  Only a single instance is supported in this release.

- Usage:

    config vxlan evpn_nvo add `<nvoname> <vtepname>` 

- Example:

```
admin@sonic:~$ sudo config vxlan evpn_nvo add nvo1 vtep1 
```

**config vxlan evpn_nvo del**

  Deletes the EVPN instance. There should be no VLAN VNI mappings for this command to 
  succeed.

- Usage:

    config vxlan evpn_nvo del `<nvoname>` 

- Example:

```
admin@sonic:~$ sudo config vxlan evpn_nvo del nvo1 
```

**config vxlan primary-ip add**

Configures the VxLAN Primary IP address (PIP). There should be no VLAN VNI mappings for this command to succeed.

- Usage:

  config vxlan primary-ip add <vtepname> <pip_ipv4>

- Example:

```
admin@sonic:~$ sudo config vxlan primary-ip add vtep1 2.2.2.2
```

**config vxlan primary-ip del**

Removes VxLAN Primary IP address (PIP). There should be no VLAN VNI mappings for this command to succeed.

- Usage:

  config vxlan primary-ip del <vtepname> <pip_ipv4>

- Example:

```
admin@sonic:~$ sudo config vxlan primary-ip del vtep1
```



**config vxlan map add**

  Creates the VLAN VNI mapping. Requires that the VTEP, EVPN_NVO and VLAN be created. 

- Usage:

  config vxlan map add `<vtepname> <vlanid> <vnid>`

- Example:

```
admin@sonic:~$ sudo config vxlan map add vtep1 100 1200
```

**config vxlan map del**

  Deletes the VLAN VNI mapping. Requires that there be no VRF-VNI mappings referring to 
  the VNI being deleted.

- Usage:

  config vxlan map del `<vtepname> <vlanid> <vnid>`

- Example:

```
admin@sonic:~$ sudo config vxlan map del vtep1 100 1200
```

**config vxlan map_range add**

  Creates contigous VLAN VNI mappings according to the configured range. 
  Requires that the VTEP, EVPN_NVO and VLAN be created. 
  In case of failures, this command displays an error and continues to the next
  set of VLAN Ids.

- Usage:

    config vxlan map_range add `<vtepname> <start_vlanid> <start_vnid> <end_vlanid>`

- Example:

```
admin@sonic:~$ sudo config vxlan map_range add vtep1 100 1200 200

This creates mappings as 100-1200, 101-1201, .... , 200-1300
```

**config vxlan map_range del**

  Deletes contigous VLAN VNI mappings according to the configured range. 
  Requires that there be no VRF-VNI mappings referring to the VNIs being deleted.
  In case of failures, this command displays an error and continues to the next
  set of VLAN Ids.


- Usage:

    config vxlan map_range del `<vtepname> <start_vlanid> <start_vnid> <end_vlanid>`

- Example:

```
admin@sonic:~$ sudo config vxlan map_range del vtep1 100 1200 200
This deletes mappings as 100-1200, 101-1201, .... , 200-1300
```

**config vrf map add**

  Creates the VRF VNI mapping. Requires that the VTEP, EVPN_NVO, VRF and VLAN-VNI map be created.

- Usage:

  config vrf add_vrf_vni_map `<vrfname> <vnid>`

- Example:

```
admin@sonic:~$ sudo config vrf add_vrf_vni_map Vrf1 1200
```

**config vrf map delete**

  Deletes the VRF VNI mapping.

- Usage:

  config vrf del_vrf_vni_map `<vrfname>`

- Example:

```
admin@sonic:~$ sudo config vrf del_vrf_vni_map Vrf1
```

**config vxlan qos-mode**

  Configures qos-mode for the VTEP instance. Qos-mode can be pipe / uniform. DSCP input is mandatory for pipe mode.

- Usage: 

  config vxlan qos-mode `<vtepname> <uniform/pipe> <dscp_value>`

- Example:

```
admin@sonic:~$ sudo config vxlan qos-mode vtep1 pipe 20
The above command sets the qos-mode for vtep1 to pipe and DSCP value to 20.
admin@sonic:~$ sudo config vxlan qos-mode vtep1 uniform
The above command sets the qos-mode for vtep1 to uniform.
```

# Warm Restart

## Warm Restart show command

**show warm_restart config**  

This command displays all the configuration related to warm_restart.

  - Usage:  
    show warm_restart config


- Example:
```
  admin@sonic:~$ show warm_restart config
  name    enable    timer_name        timer_duration
------  --------  ----------------  ----------------
  bgp     true      bgp_timer         100
  teamd   false     teamsyncd_timer   300
  swss    false     neighsyncd_timer  200
  system  true      NULL              NULL
```

**show warm_restart state**  

This command displays the warm_restart state.

  - Usage:  
    show warm_restart state


- Example:
```
  name          restore_count  state
----------  ---------------  ----------
  orchagent                 0
  vlanmgrd                  0
  bgp                       1  reconciled
  portsyncd                 0
  teammgrd                  1
  neighsyncd                0
  teamsyncd                 1
  syncd                     0

```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#VLAN-Configuration-And-Show)

## Warm Restart Config command

This sub-section explains the various configuration related to warm restart feature. Following parameters can be configured using this command.
1) bgp_timer
2) disable
3) enable
4) neighsyncd_timer
5) teamsyncd_timer
Each of these sub-commands are explained in the following section.

Users can use an optional parameter "-s" to use the unix domain socket for communicating with the RedisDB which will be faster when compared to using the default network sockets. 
All these commands have the following option.

Options:
  -s, --redis-unix-socket-path TEXT
       unix socket path for redis connection


**config warm_restart bgp_timer**  

This command is used to set the bgp_timer value for warm_restart of BGP service. 
bgp_timer holds the time interval utilized by fpmsyncd during warm-restart episodes.
During this interval fpmsyncd will recover all the routing state previously pushed to AppDB, as well as all the new state coming from zebra/bgpd. 
Upon expiration of this timer, fpmsyncd will execute the reconciliation logic to eliminate all the stale entries from AppDB. 
This timer should match the BGP-GR restart-timer configured within the elected routing-stack.
Supported range: 1-3600.

  - Usage:  
    config warm_restart bgp_timer <seconds>
    seconds range 1 to 3600.


- Example:
```
  admin@sonic:~$ sudo config warm_restart bgp_timer 1000
```

**config warm_restart enable/disable**  

This command is used to enable or disable the warm_restart for a particular service that supports warm reboot.
Following four services support warm reboot. When user restarts the particular service using "systemctl restart <service_name>", this configured value will be checked for whether it is enabled or disabled.
If this configuration is enabled for that service, it will perform warm reboot for that service. Otherwise, it will do cold restart of the service.

  - Usage:  
    config warm_restart enable [<module_name>]

       module_name can be either system or swss or bgp or teamd.
       If "module_name" argument is not specified, it will enable "system" module.


- Example:
  ```
  admin@sonic:~$ sudo config warm_restart enable
  The above command will set warm_restart as "enable" for the "system" service.

  admin@sonic:~$ sudo config warm_restart enable swss
  The above command will set warm_restart as "enable" for the "swss" service. When user does "systemctl restart swss", it will perform warm reboot instead of cold reboot.

  admin@sonic:~$ sudo config warm_restart enable teamd
  The above command will set warm_restart as "enable" for the "teamd" service. When user does "systemctl restart teamd", it will perform warm reboot instead of cold reboot.
  ```


  ```


**config warm_restart neighsyncd_timer**  

This command is used to set the neighsyncd_timer value for warm_restart of "swss" service. 
neighsyncd_timer is the timer used for "swss" (neighsyncd) service during the warm restart. 
Timer is started after the neighborTable is restored to internal data structures.
neighborsyncd then starts to read all linux kernel entries and mark the entries in the data structures accordingly. 
Once the timer is expired, reconciliation is done and the delta is pushed to appDB
Valid value is 1-9999. 0 is invalid.

  - Usage:  
    config warm_restart bgp_timerneighsyncd_timer <seconds>
    seconds range 1 to 9999.


- Example:
  ```
  admin@sonic:~$ sudo config warm_restart neighsyncd_timer 2000
  ```


**config warm_restart teamsyncd_timer**  

This command is used to set the teamsyncd_timer value for warm_restart of teamd service. 
teamsyncd_timer holds the time interval utilized by teamsyncd during warm-restart episodes.
The timer is started when teamsyncd starts. During the timer interval, teamsyncd will preserve all LAG interface changes, but it will not apply them. 
The changes will only be applied when the timer expires. 
When the changes are applied, the stale LAG entries will be removed, the new LAG entries will be created.
Supported range: 1-9999. 0 is invalid

  - Usage:  
    config warm_restart teamsyncd_timer <seconds>
    seconds range 1 to 9999.


- Example:
  ```
  admin@sonic:~$ sudo config warm_restart teamsyncd_timer 3000
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Warm-Restart)


# Watermark Configuration And Show

## Watermark Show command

**show watermark telemetry interval**  

This command displays the configured interval for the telemetry. 

  - Usage:  
    show watermark telemetry interval


- Example:
  ```
  admin@sonic:~$ show watermark telemetry interval

      Telemetry interval 120 second(s)

  ```

## Watermark Config command

**config watermark telemetry interval**  

This command is used to configure the interval for telemetry. 
The default interval is 120 seconds. 
There is no regulation on the valid range of values; it leverages linux timer.

  - Usage:   
    config watermark telemetry interval <value>

- Example:
  ```
  admin@sonic:~$ sudo config watermark telemetry interval 999
  ```

## Watermark KLISH Show command

**show watermark telemetry interval**

This command displays the configured interval for the telemetry.

  - Usage:
    show watermark telemetry interval

- Example:
  ```
  sonic# show watermark telemetry interval
         Telemetry interval : 33 seconds
  ```

**show buffer_pool [watermark|persistent-watermark] [-p]**

This command displays the User/Persistent watermark counter values.

  - Usage:
    show buffer_pool watermark
    show buffer_pool watermark percentage
    show buffer_pool persistent-watermark
    show buffer_pool persistent-watermark percentage

- Example:
  ```
  sonic# show buffer_pool persistent-watermark
         ---------------------------------------------------------------
         Pool                                              Bytes
         ---------------------------------------------------------------
         egress_lossless_pool                              745
         egress_lossy_pool                                 0
         ingress_lossless_pool                             445

  sonic# show buffer_pool persistent-watermark percentage
         ---------------------------------------------------------------
         Pool                                              Percentage
         ---------------------------------------------------------------
         egress_lossless_pool                              45
         egress_lossy_pool                                 0
         ingress_lossless_pool                             16

  sonic# show buffer_pool watermark
         ---------------------------------------------------------------
         Pool                                              Bytes
         ---------------------------------------------------------------
         egress_lossless_pool                              75
         egress_lossy_pool                                 0
         ingress_lossless_pool                             48

  sonic# show buffer_pool watermark percentage
         ---------------------------------------------------------------
         Pool                                              Percentage
         ---------------------------------------------------------------
         egress_lossless_pool                              49
         egress_lossy_pool                                 0
         ingress_lossless_pool                             36
  ```

## Watermark KLISH Config command

**watermark telemetry interval <value>**

This command is used to configure the interval for telemetry.
The default interval is 120 seconds.

  - Usage:
    watermark telemetry interval <value>

- Example:
  ```
  sonic(config)# watermark telemetry interval 33
  ```

## Watermark KLISH Clear command

**no watermark telemetry interval <value>**

This command clears any existing telemetry interval configuration.

  - Usage:
    no watermark telemetry interval <value>

- Example:
  ```
  sonic(config)# no watermark telemetry interval
  ```

**clear buffer_pool watermark**

This command clears buffer pool User watermark counters.

  - Usage:
    clear buffer_pool watermark 

- Example:
  ```
  sonic# clear buffer_pool watermark
  ```

**clear buffer_pool persistent-watermark**

This command clears buffer pool persistent watermark counters.

  - Usage:
    clear buffer_pool persistent watermark 

- Example:
  ```
  sonic# clear buffer_pool persistent-watermark
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Watermark-Configuration-And-Show)



# Software Installation Commands

SONiC software can be installed in two methods, viz, "using sonic_installer tool", "ONIE Installer".


## SONiC Installer
This is a command line tool available as part of the SONiC software; If the device is already running the SONiC software, this tool can be used to install an alternate image in the partition.
This tool has facility to install an alternate image, list the available images and to set the next reboot image.

**sonic_installer install**  

This command is used to install a new image on the alternate image partition.  This command takes a path to an installable SONiC image or URL and installs the image.
This command has been enhanced to support sftp/scp protocol for image download as well. In such cases extra arguments like server address, username, password etc need to be provided.

  - Usage:    
    sonic_installer install <URL path/local path to installable SONiC image>
    or 
    sonic_installer install --protocol <scp/sftp> --server <remote server address> --username <username to connect to the remote server> <File path on remote server>


- Examples:
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

  Done
  ```

  ```
  admin@sonic:~$ sudo sonic_installer install --protocol scp --server 10.175.121.155 --username admin /home/admin/sonic-img/sonic-broadcom.bin
  New image will be installed, continue? [y/N]: y
  password:
  Downloading image...
  ...99%, 2 M101 KB 0 onds left...
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



# Troubleshooting Commands

For troubleshooting and debugging purposes, this command gathers pertinent information about the state of the device; information is as diverse as syslog entries, database state, routing-stack state, etc., It then compresses it into an archive file. This archive file can be sent to the SONiC development team for examination.
Resulting archive file is saved as `/var/dump/<DEVICE_HOST_NAME>_YYYYMMDD_HHMMSS.tar.gz`

  - Usage:  
    show techsupport


- Example:
  ```
  admin@sonic:~$ show techsupport
  ```
If the SONiC system was running for quite some time `show techsupport` will produce a large dump file. To reduce the amount of syslog and core files gathered during system dump use `--since` option:

- Example:
  ```
  admin@sonic:~$ show techsupport --since=yesterday  # Will collect syslog and core files for the last 24 hours
  admin@sonic:~$ show techsupport --since='hour ago' # Will collect syslog and core files for the last one hour
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#Troubleshooting-commands)

# Routing Stack Configuration And Show

SONiC software is agnostic of the routing software that is being used in the device. For example, users can use either Quagga or FRR routing stack as per their requirement.
A separate shell (vtysh) is provided to configure such routing stacks.
Once if users go to "vtysh", they can use the routing stack specific commands as given in the following example.

  - Example: Quagga Routing Stack
  ```
	admin@T1-2:~$ vtysh
	
	Hello, this is Quagga (version 0.99.24.1).
	Copyright 1996-2005 Kunihiro Ishiguro, et al.
	
	T1-2# show route-map (This command displays the route-map that is configured for the routing protocol.)
	ZEBRA:
	route-map RM_SET_SRC, permit, sequence 10
	  Match clauses:
	  Set clauses:
		src 10.12.0.102
	  Call clause:
	  Action:
		Exit routemap
  ```

Refer the routing stack [Quagga Command Reference](https://www.quagga.net/docs/quagga.pdf) or [FRR Command Reference](https://buildmedia.readthedocs.org/media/pdf/frrouting/latest/frrouting.pdf) to know more about about the routing stack configuration.


Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) 


# Quagga BGP Show Commands  

**show ip bgp summary**  

This command displays the summary of all IPv4 bgp neighbors that are configured and the corresponding states.

  - Usage:   
    show ip bgp summary

- Example:
  ```
  admin@sonic:~$ show ip bgp summary
  BGP router identifier 1.2.3.4, local AS number 65061
  RIB entries 6124, using 670 KiB of memory
  Peers 2, using 143 KiB of memory

  Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
  192.168.1.161    4 65501   88698  102781        0    0    0 08w5d14h        2
  192.168.1.163    4 65502   88698  102780        0    0    0 08w5d14h        2

  Total number of neighbors 2
  ```

**show ip bgp neighbors**  

This command displays all the details of IPv4 & IPv6 BGP neighbors when no optional argument is specified. 

When the optional argument IPv4_address is specified, it displays the detailed neighbor information about that specific IPv4 neighbor.

Command has got additional optional arguments to display only the advertised routes, or the received routes, or all routes.

In order to get details for an IPv6 neigbor, use "show ipv6 bgp neighbor <ipv6_address>" command.

  - Usage:  
    show ip bgp neighbors [<ipv4-address> [advertised-routes | received-routes | routes]]


- Example:
  ```
  admin@sonic:~$ show ip bgp neighbors
  BGP neighbor is 192.168.1.161, remote AS 65501, local AS 65061, external link
   Description: ARISTA01T0
    BGP version 4, remote router ID 1.2.3.4
    BGP state = Established, up for 08w5d14h
    Last read 00:00:46, hold time is 180, keepalive interval is 60 seconds
    Neighbor capabilities:
      4 Byte AS: advertised and received
      Dynamic: received
      Route refresh: advertised and received(old & new)
      Address family IPv4 Unicast: advertised and received
      Graceful Restart Capabilty: advertised and received
        Remote Restart timer is 120 seconds
        Address families by peer:
          IPv4 Unicast(not preserved)
    Graceful restart informations:
      End-of-RIB send: IPv4 Unicast
      End-of-RIB received: IPv4 Unicast
    Message statistics:
      Inq depth is 0
      Outq depth is 0
                           Sent       Rcvd
      Opens:                  1          1
      Notifications:          0          0
      Updates:            14066          3
      Keepalives:         88718      88698
      Route Refresh:          0          0
      Capability:             0          0
      Total:             102785      88702
    Minimum time between advertisement runs is 30 seconds

   For address family: IPv4 Unicast
    Community attribute sent to this neighbor(both)
    2 accepted prefixes
      
    Connections established 1; dropped 0
    Last reset never
  Local host: 192.168.1.160, Local port: 32961
  Foreign host: 192.168.1.161, Foreign port: 179
  Nexthop: 192.168.1.160
  Nexthop global: fe80::f60f:1bff:fe89:bc00
  Nexthop local: ::
  BGP connection: non shared network
  Read thread: on  Write thread: off
  ```
  
  - Optionally, you can specify an IP address in order to display only that particular neighbor. In this mode, you can optionally specify whether you want to display all routes advertised to the specified neighbor, all routes received from the specified neighbor or all routes (received and accepted) from the specified neighbor.


- Example:
  ```
  admin@sonic:~$ show ip bgp neighbors 192.168.1.161

  admin@sonic:~$ show ip bgp neighbors 192.168.1.161 advertised-routes

  admin@sonic:~$ show ip bgp neighbors 192.168.1.161 received-routes

  admin@sonic:~$ show ip bgp neighbors 192.168.1.161 routes
  ```

**show ipv6 bgp summary**  

This command displays the summary of all IPv4 bgp neighbors that are configured and the corresponding states.

  - Usage:  
     show ipv6 bgp summary


- Example:
  ```
  admin@sonic:~$ show ipv6 bgp summary
  BGP router identifier 10.1.0.32, local AS number 65100
  RIB entries 12809, using 1401 KiB of memory
  Peers 8, using 36 KiB of memory

  Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
  fc00::72        4 64600   12588   12591        0    0    0 06:51:17     6402
  fc00::76        4 64600   12587    6190        0    0    0 06:51:28     6402
  fc00::7a        4 64600   12587    9391        0    0    0 06:51:23     6402
  fc00::7e        4 64600   12589   12592        0    0    0 06:51:25     6402

  Total number of neighbors 4
  ```

**show ipv6 bgp neighbors**  

This command displays all the details of one particular IPv6 Border Gateway Protocol (BGP) neighbor. Option is also available to display only the advertised routes, or the received routes, or all routes.

  - Usage:  
    show ipv6 bgp neighbors <ipv6-address> (advertised-routes | received-routes | routes)`

- Example:
  ```
  admin@sonic:~$ show ipv6 bgp neighbors fc00::72 advertised-routes

  admin@sonic:~$ show ipv6 bgp neighbors fc00::72 received-routes

  admin@sonic:~$ show ipv6 bgp neighbors fc00::72 routes
  ```

**show route-map**  

This command displays the routing policy that takes precedence over the other route processes that are configured.

  - Usage:  
    show route-map
  
  - Example:
  ```
	admin@T1-2:~$ show route-map
	ZEBRA:
	route-map RM_SET_SRC, permit, sequence 10
	  Match clauses:
	  Set clauses:
		src 10.12.0.102
	  Call clause:
	  Action:
		Exit routemap
	ZEBRA:
	route-map RM_SET_SRC6, permit, sequence 10
	  Match clauses:
	  Set clauses:
		src fc00:1::102
	  Call clause:
	  Action:
		Exit routemap
	BGP:
	route-map FROM_BGP_SPEAKER_V4, permit, sequence 10
	  Match clauses:
	  Set clauses:
	  Call clause:
	  Action:
	    Exit routemap
	BGP:
	route-map TO_BGP_SPEAKER_V4, deny, sequence 10
	  Match clauses:
	  Set clauses:
	  Call clause:
	  Action:
	    Exit routemap
	BGP:
	route-map ISOLATE, permit, sequence 10
	  Match clauses:
	  Set clauses:
		as-path prepend 65000
	  Call clause:
	  Action:
		Exit routemap
  ```


Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE)

# Error Handling Framework Configuration and Show Commands

This section explains all the Error Handling Framework show commands and configuation commands that are supported in SONiC.

## Error Handling Framework show commands

**show error_database [tablename]**

This command displays the failure entries logged in the error database.

- Usage:
show error_database [tablename]

- Example:
  ```
admin@sonic:/home/admin# show error_database
Prefix         Nexthop    Interface    Error Code      Operation
-------------  ---------  -----------  --------------  -----------
600::/64       2000::2    Ethernet28   SWSS_RC_EXISTS  create
12.12.12.0/24  10.1.1.2   Ethernet28   SWSS_RC_EXISTS  create
IP address    MAC address        Interface    Error Code          Operation
------------  -----------------  -----------  ------------------  -----------
2000::3       00:00:00:00:00:02  Ethernet28   SWSS_RC_TABLE_FULL  create
2000::4                          Ethernet28   SWSS_RC_NOT_FOUND   remove
10.1.1.3      00:00:00:00:00:01  Ethernet28   SWSS_RC_TABLE_FULL  create
10.1.1.3                         Ethernet28   SWSS_RC_NOT_FOUND   remove

admin@sonic:/home/admin# show error_database ERROR_ROUTE_TABLE
Prefix         Nexthop            Interface              Error Code      Operation
-------------  -----------------  ---------------------  --------------  -----------
12.12.12.0/24  10.1.1.2,20.1.1.2  Ethernet28,Ethernet30  SWSS_RC_EXISTS  create
13.13.13.0/24  10.1.1.2           Ethernet28             SWSS_RC_EXISTS  create
```

## Error Handling Framework clear commands

**sonic-clear error_database [tablename]**

This command clears the failure entries logged in the error database.

- Usage:
sonic-clear error_database [tablename]

- Example:
```
admin@sonic:/home/admin# sudo sonic-clear error_database
ERROR DB entries are cleared.

admin@sonic:/home/admin# sudo sonic-clear error_database ERROR_ROUTE_TABLE
('ERROR DB entries are cleared from Table.', 'ERROR_ROUTE_TABLE')
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE)

# Threshold Configuration and Show Commands

This section explains all the Threshold show and configuration commands that are supported in SONiC.

## Threshold show commands

**show priority-group threshold [shared/headroom]**

This command displays the currently configured thresholds on all port's priority-group shared or headroom buffer according to the command executed.

- Usage:
show priority-group threshold shared
show priority-group threshold headroom

- Example:

```
	root@sonic:/home/admin# show priority-group threshold shared 
	Ingress shared pool threshold per PG:
	       Port    PG0    PG1    PG2    PG3    PG4    PG5    PG6    PG7
	-----------  -----  -----  -----  -----  -----  -----  -----  -----
	        CPU      0      0      0      0      0      0      0      0
	  Ethernet0      0      0      0      0      0      0      0      5
	  Ethernet4      0      0      0      0      0      0      0      0
	  Ethernet8      0      0      0      0      0      0      0      0
	 Ethernet12      0      0      0      0      0      0      0      0
	 Ethernet16      0      0      0      0      0      0      0      0
	 Ethernet20      0      0      0      0      0      0      0      0
	 Ethernet24      0      0      0      0      0      0      0      0
	 Ethernet28      0      0      0      0      0      0      0      0
	 Ethernet32      0      0      0      0      0      0      0      0
	 Ethernet36      0      0      0      0      0      0      0      0
	 Ethernet40      0      0      0      0      0      0      0      0
	 Ethernet44      0      0      0      0      0      0      0      0
	 Ethernet48      0      0      0      0      0      0      0      0
	 Ethernet52      0      0      0      0      0      0      0      0
	 Ethernet56      0      0      0      0      0      20     0      0
	 Ethernet60      0      0      0      0      0      0      0      0
	 Ethernet64      0      0      0      0      0      0      0      0
	 Ethernet68      0      0      0      0      0      0      0      0
	 Ethernet72      0      0      0      0      0      0      0      0
	 Ethernet76      0      0      0      0      0      0      0      0
	 Ethernet80      0      0      0      0      0      0      0      0
	 Ethernet84      0      0      0      0      0      0      0      0
	 Ethernet88      0      0      0      0      0      0      0      0
	 Ethernet92      0      0      0      0      0      0      0      0
	 Ethernet96      0      0      0      0      0      0      0      0
	Ethernet100      0      0      0      0      0      0      0      0
	Ethernet104      0      0      0      0      0      0      0      0
	Ethernet108      0      0      0      0      0      0      0      0
	Ethernet112      0      0      0      0      0      0      0      0
	Ethernet116      0      0      0      0      0      0      0      0
	Ethernet120      0      0      0      0      0      0      0      0
	Ethernet124      0      0      0      0      0      0      0      0
```

**show queue threshold [unicast/multicast]**

This command displays threshold configuration for the unicast/multicast queue buffers of all ports.

- Usage:
show queue threshold unicast
show queue threshold multicast

- Example:
```
root@sonic:/home/admin# show queue threshold unicast 
Egress shared pool threshold per unicast queue:
       Port    UC0    UC1    UC2    UC3    UC4    UC5    UC6    UC7
-----------  -----  -----  -----  -----  -----  -----  -----  -----
        CPU      0      0      0      0      0      0      0      0
  Ethernet0      0      0      0      0      0      0      0      0
  Ethernet4      0      0      0      0      0      0      0      0
  Ethernet8      0      0      0      0      0      0      0      0
 Ethernet12     10      0      0      0      0      0      0      0
 Ethernet16      0      0      0      0      0      0      0      0
 Ethernet20      0      0      0      0      0      0      0      0
 Ethernet24      0      0      0      0      0      0      0      0
 Ethernet28      0      0      0      0      0      0      0      0
 Ethernet32      0      0      0      0      0      0      0      0
 Ethernet36      0      0      0      0      0      0      0      0
 Ethernet40      0      0      0      0      0      0      0      0
 Ethernet44      0      20     0      0      0      0      0      0
 Ethernet48      0      0      0      0      0      0      0      0
 Ethernet52      0      0      0      0      0      0      0      0
 Ethernet56      0      0      0      0      0      0      0      0
 Ethernet60      0      0      0      0      0      0      0      0
 Ethernet64      0      0      0      0      0      0      0      0
 Ethernet68      0      0      0      0      0      0      0      0
 Ethernet72      0      0      0      0      0      0      0      0
 Ethernet76      0      0      0      0      0      0      0      0
 Ethernet80      0      0      0      0      0      0      0      0
 Ethernet84      0      0      0      0      0      0      0      0
 Ethernet88      0      0      0      0      0      0      0      0
 Ethernet92      0      0      0      0      0      0      0      0
 Ethernet96      0      0      0      0      0      0      0      0
Ethernet100      0      0      0      0      0      0      0      0
Ethernet104      0      0      0      0      0      0      0      0
Ethernet108      0      0      0      0      0      0      0      0
Ethernet112      0      0      0      0      0      0      0      0
Ethernet116      0      0      0      0      0      0      0      0
Ethernet120      0      0      0      0      0      0      0      0
Ethernet124      0      0      0      0      0      0      0      0

```

**show threshold breaches <numbreaches>**

This command displays the threshold breaches recorded by the system. Optionally, this command can display only the last "numbreaches" recorded by the system.

- Usage:
show threshold breaches

- Example:
```
root@sonic:/home/admin# show threshold breaches 2
Event-id          Buffer       Type        Port    Index    Breach Value(%)    Breach Value(bytes)           Time-stamp
----------  --------------  ---------  ----------  -------  -----------------  ---------------------  -------------------
75  priority-group     shared   Ethernet8        7                  9                1198288  2019-09-04.05:48:51
74  priority-group     shared   Ethernet8        7                  9                1198288  2019-09-04.05:48:51
```

**show buffer_pool threshold buffer_pool_all**

This command displays the buffer pool threshold configured on ingress and egress buffers.

- Usage:

  show buffer_pool threshold buffer_pool_all

- Example:
```
root@sonic:/home/admin# show buffer_pool threshold buffer_pool_all
                 Pool    threshold
---------------------  -----------
 egress_lossless_pool           77
    egress_lossy_pool           44
ingress_lossless_pool           67
```

## Threshold configuration commands

This sub-section lists the configuration options available for Threshold module.

**config priority-group threshold {port\_alias} {PG\_index} {shared/headroom} {threshold\_value}**

This command is used to configure a threshold on a port's priority-group shared/headroom buffer. The threshold is configured in percentage. The valid values for PG\_index are 0-7.

- Example:
```
root@sonic:/home/admin# config priority-group threshold Ethernet8 7 shared 5
root@sonic:/home/admin# config priority-group threshold Ethernet9 7 headroom 40
```

**config queue threshold {port\_alias} {queue\_index} {unicast/multicast} {threshold\_value}**

This command is used to configure a threshold on a port's unicast/multicast queue buffer. The threshold is configured in percentage. The valid values for queue\_index are 0-7.

- Example:
```
root@sonic:/home/admin# config queue threshold Ethernet8 0 unicast 20
root@sonic:/home/admin# config queue threshold Ethernet10 7 multicast 10
```

**config buffer-pool threshold {pool\_name} {threshold\_value}**

This command is used to configure buffer-pool threshold on ingress and egress buffers. The threshold value is provided in %. Valid values are 1-100.

- Example:
```
root@sonic:/home/admin# config buffer-pool threshold ingress_lossless_pool 67
root@sonic:/home/admin# config buffer-pool threshold egress_lossless_pool 77
root@sonic:/home/admin# config buffer-pool threshold egress_lossy_pool 44
```

## Threshold clear commands

This sub-section lists the clear commands available for Threshold module.

**sonic-clear priority-group threshold {port\_alias} {PGindex} {shared/headroom}**

This command is used to clear the threshold configuration on priority-groups.

- Usage:
sonic-clear priority-group threshold - Clears threshold configuration on all port's priority-groups shared and headroom buffers.
sonic-clear priority-group threshold Ethernet8 7 shared - Clears threshold configuration on Ethernet8's priority-group 7 shared buffer.

- Example:

```
admin@sonic:/home/admin# sudo sonic-clear priority-group threshold
admin@sonic:/home/admin# sudo sonic-clear priority-group threshold Ethernet8 7 shared
```

**sonic-clear queue threshold {port\_alias} {queueindex} {unicast/multicast}**

This command is used to clear the threshold configuration on queues.

- Usage:
sonic-clear queue threshold - Clears threshold configuration on all port's unicast and multicast queue buffers.
sonic-clear queue threshold Ethernet8 7 unicast - Clears threshold configuration on Ethernet8's unicast queue 7 buffer.

- Example:
```
admin@sonic:/home/admin# sudo sonic-clear queue threshold
admin@sonic:/home/admin# sudo sonic-clear queue threshold Ethernet8 7 unicast
```

**sonic-clear threshold breach {eventid}**

This command is used to clear the threshold breaches recorded by the system. A specific breach event can also be cleared by specifying the eventid as a parameter to the clear command.

- Usage:
sonic-clear threshold breach - Clears all threshold breaches recorded by the system.
sonic-clear threshold breach 2 - Clears threshold breach event-id 2.

- Example:
```
admin@sonic:/home/admin# sudo sonic-clear threshold breach
admin@sonic:/home/admin# sudo sonic-clear threshold breach 2
```

**sonic-clear buffer-pool threshold**

This command can be used to remove buffer-pool threshold configured on ingress and egress buffers.

- Usage:

  sonic-clear buffer-pool threshold - Removes buffer-pool threshold configured on ingress and egress buffers.

- Example:
```
root@sonic:/home/admin# show buffer_pool threshold buffer_pool_all
                 Pool    threshold
---------------------  -----------
egress_lossless_pool            0
    egress_lossy_pool            0
ingress_lossless_pool            0
```

## Threshold KLISH show commands

**show threshold priority-group {headroom | shared}**

This command displays the currently configured thresholds on all port's priority-group shared or headroom buffer according to the command executed.

- Usage:

  show threshold priority-group headroom.

  show threshold priority-group shared.

- Example:
```
sonic# show threshold priority-group shared
-----------------------------------------------------------------------
 Port              PG0    PG1    PG2    PG3    PG4    PG5    PG6    PG7
-----------------------------------------------------------------------
 Ethernet0         0      0      77     0      0      0      0      0
 Ethernet4         0      0      0      0      0      0      0      0
 Ethernet8         0      0      0      0      0      0      0      0
 Ethernet12        0      0      0      0      0      0      0      0
 Ethernet16        0      0      0      0      0      0      0      0
 Ethernet20        0      0      0      0      0      0      0      0
 Ethernet24        0      0      0      0      0      0      0      0
 Ethernet28        0      0      0      0      0      0      0      0
 Ethernet32        0      0      0      0      0      0      0      0
 Ethernet36        0      0      0      0      0      0      0      0
 Ethernet40        0      0      0      0      0      0      0      0
 Ethernet44        0      0      0      0      0      0      0      0
 Ethernet48        0      0      0      0      0      0      0      0
 Ethernet52        0      0      0      0      0      0      0      0
 Ethernet56        0      0      0      0      0      0      0      0
 Ethernet60        0      0      0      0      0      0      0      0
 Ethernet64        0      0      0      0      0      0      0      0
 Ethernet68        0      0      0      0      0      0      0      0
 Ethernet72        0      0      0      0      0      0      0      0
 Ethernet76        0      0      0      0      0      0      0      0
 Ethernet80        0      0      0      0      0      0      0      0
--more--
```

**show threshold queue {unicast | multicast | CPU}**

This show command shows the threshold configuration for the unicast/multicast queue buffers of all ports or multicast queue buffers of CPU ports.

- Usage:

  show queue threshold unicast.

  show queue threshold multicast.

  show threshold queue CPU.

- Example:
```
sonic# show threshold queue unicast
-----------------------------------------------------------------------
 Port              UC0    UC1    UC2    UC3    UC4    UC5    UC6    UC7
-----------------------------------------------------------------------
 Ethernet0         0      0      0      0      0      0      33     0
 Ethernet4         0      0      0      0      0      0      0      0
 Ethernet8         0      0      0      0      0      0      0      0
 Ethernet12        0      0      0      0      0      0      0      0
 Ethernet16        0      0      0      0      0      0      0      0
 Ethernet20        0      0      0      0      0      0      0      0
 Ethernet24        0      0      0      0      0      0      0      0
 Ethernet28        0      0      0      0      0      0      0      0
 Ethernet32        0      0      0      0      0      0      0      0
 Ethernet36        0      0      0      0      0      0      0      0
 Ethernet40        0      0      0      0      0      0      0      0
 Ethernet44        0      0      0      0      0      0      0      0
 Ethernet48        0      0      0      0      0      0      0      0
 Ethernet52        0      0      0      0      0      0      0      0
 Ethernet56        0      0      0      0      0      0      0      0
 Ethernet60        0      0      0      0      0      0      0      0
 Ethernet64        0      0      0      0      0      0      0      0
 Ethernet68        0      0      0      0      0      0      0      0
 Ethernet72        0      0      0      0      0      0      0      0
 Ethernet76        0      0      0      0      0      0      0      0
 Ethernet80        0      0      0      0      0      0      0      0
--more--

sonic# show threshold queue CPU
----------------------------------
 Queue               Percent
----------------------------------
 CPU:0               0
 CPU:1               0
 CPU:2               62
 CPU:3               0
 CPU:4               0
 CPU:5               0
 CPU:6               34
 CPU:7               0
 CPU:8               0
 CPU:9               0
 CPU:10              0
 CPU:11              0
 CPU:12              0
 CPU:13              0
 CPU:14              0
 CPU:15              0
 CPU:16              0
 CPU:17              0
 CPU:18              0
 CPU:19              0
 CPU:20              0
--more--
```

**show threshold buffer-pool**

This show command shows the threshold configuration of buffer-pools. Buffer pools will have ingress/egress buffer pools-lossy/lossless.

- Usage:

  show threshold buffer-pool

- Example:

```
sonic# show threshold buffer-pool
---------------------------------------------------------------
pool_name                                         threshold
---------------------------------------------------------------
egress_lossless_pool                              44
egress_lossy_pool                                 77
ingress_lossless_pool                             22
```

## Threshold KLISH configuration commands

This sub-section lists the KLISH CLI configuration options available for Threshold module.

**threshold priority-group {PGindex} {headroom | shared} {threshold_value}**

This command is used to configure a threshold on a port's priority-group shared/headroom buffer. The threshold is configured in percentage. The valid values for PGindex are 0-7.

- Example:
```
sonic(conf-if-Ethernet0)# threshold priority-group 7 headroom 7
sonic(conf-if-Ethernet0)# threshold priority-group 3 shared 73
```

**threshold queue {queueindex} {unicast | multicast} {threshold_value}**

This command is used to configure a threshold on a port's unicast/multicast queue buffer. The threshold is configured in percentage. The valid values for queueindex are 0-7.

- Example:
```
sonic(conf-if-Ethernet0)# threshold queue 5 multicast 5
sonic(conf-if-Ethernet0)# threshold queue 2 unicast 33
```

**threshold queue {queueindex} {multicast} {threshold_value}**

This command is used to configure a threshold for a specific multicast queue of a CPU port. The threshold value is provided in percentage. The valid values for queueindex are 0-47.

- Example:
```
sonic(conf-if-CPU)# threshold queue 47 multicast 47
```

**threshold buffer-pool {bufferpoolname} {threshold_value}**

This command is used to configure buffer-pool threshold on ingress and egress buffers. The threshold value is provided in percentage.

- Example:
```
sonic(config)# threshold buffer-pool ingress_lossless_pool 77
sonic(config)# threshold buffer-pool egress_lossless_pool 55
sonic(config)# threshold buffer-pool egress_lossy_pool 44
```

## Threshold KLISH clear commands

This sub-section lists the clear command options available KLISH CLI Threshold module.

**no threshold priority-group {PGindex} {headroom | shared}**

This command is used to clear the threshold configuration on priority-groups.

- Usage:

  sonic(conf-if-Ethernet0)# no threshold priority-group 7 headroom - Clears threshold configuration on Ethernet0 headroom priority-group 7 buffer.

  sonic(conf-if-Ethernet0)# no threshold priority-group 6 shared - Clears threshold configuration on Ethernet0 shared priority-group 6 buffer.

- Example:

```
sonic(conf-if-Ethernet0)# no threshold priority-group 7 headroom
sonic(conf-if-Ethernet0)# no threshold priority-group 4 shared
```

**no threshold queue {queueindex} {unicast | multicast}**

This command is used to clear the threshold configuration on interface/CPU queues.

- Usage:

  sonic(conf-if-Ethernet0)# no threshold queue 5 multicast - Clears threshold configuration on Ethernet0's multicast queue 5 buffer.

  sonic(conf-if-Ethernet0)# no threshold queue 3 unicast - Clears threshold configuration on Ethernet0's unicast queue 3 buffer.

  sonic(conf-if-CPU)# no threshold queue 0 multicast - Clears threshold configuration on CPU's multicast queue 0 buffer.

- Example:
```
sonic(conf-if-Ethernet0)# no threshold queue 5 multicast
sonic(conf-if-Ethernet0)# no threshold queue 3 unicast
sonic(conf-if-CPU)# no threshold queue 0 multicast
```

**no threshold buffer-pool**

This command can be used to remove buffer-pool threshold configured on ingress and egress buffers.

- Usage:

  sonic(config)# no threshold buffer-pool - Clears buffer-pool threshold configured on ingress and egress buffers.

- Example:
```
sonic(config)# no threshold buffer-pool
```

**clear threshold breach {all/eventid}**

This command can be used to clear all/particular threshold breaches recorded by the system. The event-id when provided specifies the breach event index (index is indicated in the output of show threshold breaches).

- Usage:

  sonic# clear threshold breach all - Clears all the threshold breaches recorded by the system.

  sonic# clear threshold breach 2 - Clears threshold breachi corresponds to event Id 2.

- Example:
```
sonic# clear threshold breach all
sonic# clear threshold breach 2
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE)

# ZTP Configuration And Show Commands

This section explains all the Zero Touch Provisioning commands that are supported in SONiC.

## ZTP show commands 

**show ztp status**

This command displays the current ZTP configuration of the switch. It also displays detailed information about current state of a ZTP session. It displays information related to all configuration sections as defined in the switch provisioning information discovered in a particular ZTP session.

- Usage:  
  show ztp status
  
  show ztp status --verbose

- Example:

```
root@B1-SP1-7712:/home/admin# show ztp status
ZTP Admin Mode : True
ZTP Service    : Inactive
ZTP Status     : SUCCESS
ZTP Source     : dhcp-opt67 (eth0)
Runtime        : 05m 31s
Timestamp      : 2019-09-11 19:12:24 UTC

ZTP Service is not running

01-configdb-json: SUCCESS
02-connectivity-check: SUCCESS
```
Use the verbose option to display more detailed information.

```
root@B1-SP1-7712:/home/admin# show ztp status --verbose
Command: ztp status --verbose
========================================
ZTP
========================================
ZTP Admin Mode : True
ZTP Service    : Inactive
ZTP Status     : SUCCESS
ZTP Source     : dhcp-opt67 (eth0)
Runtime        : 05m 31s
Timestamp      : 2019-09-11 19:12:16 UTC
ZTP JSON Version : 1.0

ZTP Service is not running

----------------------------------------
01-configdb-json
----------------------------------------
Status          : SUCCESS
Runtime         : 02m 48s
Timestamp       : 2019-09-11 19:11:55 UTC
Exit Code       : 0
Ignore Result   : False

----------------------------------------
02-connectivity-check
----------------------------------------
Status          : SUCCESS
Runtime         : 04s
Timestamp       : 2019-09-11 19:12:16 UTC
Exit Code       : 0
Ignore Result   : False
```

- Description

  - **ZTP Admin Mode** - Displays if the ZTP feature is administratively enabled or disabled. Possible values are True or False. This value is configurable using "config ztp enabled" and "config ztp disable" commands.
  - **ZTP Service** - Displays the ZTP service status. The following are possible values this field can display:
    - *Active Discovery*: ZTP service is operational and is performing DHCP discovery to learn switch provisioning information
    - *Processing*: ZTP service has discovered switch provisioning information and is processing it
  - **ZTP Status** - Displays the current state and result of ZTP session. The following are possible values this field can display:
    - *IN-PROGRESS*: ZTP session is currently in progress. ZTP service is processing switch provisioning information.
    - *SUCCESS*: ZTP service has successfully processed the switch provisioning information.
    - *FAILED*:  ZTP service has failed to process the switch provisioning information.
    - *Not Started*: ZTP service has not started processing the discovered switch provisioning information.
  - **ZTP Source** - Displays the DHCP option and then interface name from which switch provisioning information has been discovered.
  - **Runtime** - Displays the time taken for ZTP process to complete from start to finish. For individual configuration sections it indicates the time taken to process the associated configuration section.
  - **Timestamp** - Displays the date/time stamp when the status field has last changed.
  - **ZTP JSON Version** - Version of ZTP JSON file used for describing switch provisioning information.
  - **Status** - Displays the current state and result of a configuration section. The following are possible values this field can display:
    - *IN-PROGRESS*: Corresponding configuration section is currently being processed.
    - *SUCCESS*: Corresponding configuration section was processed successfully.
    - *FAILED*:  Corresponding configuration section failed to execute successfully.
    - *Not Started*: ZTP service has not started processing the corresponding configuration section.
    - *DISABLED*: Corresponding configuration section has been marked as disabled and will not be processed.
  - **Exit Code** - Displays the program exit code of the configuration section executed. Non-zero exit code indicates that the configuration section has failed to execute successfully.
  - **Ignore Result** - If this value is True, the result of the corresponding configuration section is ignored and not used to evaluate the overall ZTP result.
  - **Activity String** - In addition to above information an activity string is displayed indicating the current action being performed by the ZTP service and how much time it has been performing the mentioned activity. Below is an example.
    -    (04m 12s) Discovering provisioning data

## ZTP configuration commands 

This sub-section explains the list of the configuration options available for ZTP.



**config ztp enable**

Use this command to enable ZTP administrative mode

- Example:

```
root@sonic:/home/admin# config ztp enable
Running command: ztp enable
```



**config ztp disable**

Use this command to disable ZTP administrative mode.  This command can also be used to abort a current ZTP session and load the factory default switch configuration.

- Usage:
  config ztp disable

  config ztp disable -y

- Example:

```
root@sonic:/home/admin# config ztp disable
Active ZTP session will be stopped and disabled, continue? [y/N]: y
Running command: ztp disable -y
```



**config ztp run**

Use this command to manually restart a new ZTP session.  This command deletes the existing */etc/sonic/config_db.json* file and stats ZTP service. It also erases the previous ZTP session data. ZTP configuration is loaded on to the switch and ZTP discovery is performed.

- Usage:
  config ztp run

  config ztp run -y

- Example:

```
root@sonic:/home/admin# config ztp run
ZTP will be restarted. You may lose switch data and connectivity, continue? [y/N]: y
Running command: ztp run -y
```


# Debug Framework Commands

## Debug Framework Show Commands

**Show debug all:**

This command will invoke dump routines for all registered components with default action (save to file, path: var/log/<component>.log). Dump routine should provide all debug output required for debugging corresponding components. This command will also be captured as part of "show techsupport".

- Usage: show debug all

**Show debug component:**

This command invokes dump routine of specific component with default action (save to file, path: Docker::var/log/<component>\_debug.log). Component specific dump routine which outputs all required debugs.

- Usage: show debug component < component name >

**Show debug tosyslog:**

This command redirects the dump routine output to syslog.

- Usage: show debug tosyslog [all | component <component name>]

```
Commands:
  all        Collect debugging information from all components
  component  Collect debugging information for a given component name
```

- Example:
```
root@sonic:~# show debug tosyslog component routeorch
root@sonic:~# show logging
Aug 26 04:40:24.154460 sonic DEBUG swss#orchagent: RouteOrch Dump All Start --->
Aug 26 04:40:24.154605 sonic DEBUG swss#orchagent: RouteOrch Dump Route Table --->
Aug 26 04:40:24.154628 sonic DEBUG swss#orchagent: ------------IPv4 Route Table ------------
Aug 26 04:40:24.154720 sonic DEBUG swss#orchagent: VRF_Name = Default VRF_SAI_OID = 0x3000000000047
Aug 26 04:40:24.154748 sonic DEBUG swss#orchagent: Prefix               NextHop                   SAI-OID
Aug 26 04:40:24.154811 sonic DEBUG swss#orchagent: 0.0.0.0/0            DROP                      0xffffffffffffffff
Aug 26 04:40:24.154910 sonic DEBUG swss#orchagent: 100.0.0.0/16         Vlan100                   0x6000000000c0d
Aug 26 04:40:24.154946 sonic DEBUG swss#orchagent:
Aug 26 04:40:24.155540 sonic DEBUG swss#orchagent: ------------IPv6 Route Table ------------
Aug 26 04:40:24.155540 sonic DEBUG swss#orchagent: VRF_Name = Default VRF_SAI_OID = 0x3000000000047
Aug 26 04:40:24.155540 sonic DEBUG swss#orchagent: Prefix               NextHop                   SAI-OID
Aug 26 04:40:24.155540 sonic DEBUG swss#orchagent: ::/0                 DROP                      0xffffffffffffffff
Aug 26 04:40:24.155558 sonic DEBUG swss#orchagent: 1001::/64            Vlan100                   0x6000000000c0d
Aug 26 04:40:24.155558 sonic DEBUG swss#orchagent:
Aug 26 04:40:24.155577 sonic DEBUG swss#orchagent: RouteOrch Dump Route Table END--->
Aug 26 04:40:24.155577 sonic DEBUG swss#orchagent: RouteOrch Dump NexhopGroup Table --->
Aug 26 04:40:24.155577 sonic DEBUG swss#orchagent: Max Nexthop Group - 1024
Aug 26 04:40:24.155577 sonic DEBUG swss#orchagent: NHGrpKey             SAI-OID               NumPath    RefCnt
Aug 26 04:40:24.155588 sonic DEBUG swss#orchagent:
Aug 26 04:40:24.155595 sonic DEBUG swss#orchagent: RouteOrch Dump All END <---
```

## Component Specific Show Commands

### Component: RouteOrch

**Debug Routeorch Routes**

This command displays the routes added in Routeorch. Routes displayed are added to ASIC_DB.

- Usage: show debug routeOrch routes -v <vrf-name> -p <ip-prefix>

```
Options:
  -v, --vrf <name>     Display VRF route table info
  -p, --prefix <addr>  Display Route matching the IP Prefix
```

- Example:

```
root@sonic:~# show debug routeorch routes -v VrfRED
------------IPv4 Route Table ------------

VRF_Name = VrfRED VRF_SAI_OID = 0x30000000005b1
Prefix               NextHop                   SAI-OID
100.100.4.0/24       Ethernet4                 0x60000000005b3
33.33.33.0/24        0x55c1ca5b3b98     (ECMP) 0x50000000005db
33.33.44.0/24        100.120.120.11|Ethernet8  0x40000000005d9
33.33.55.0/24        100.120.120.12|Ethernet8  0x40000000005da
100.102.102.0/24     Ethernet12                0x60000000005d3
100.120.120.0/24     Ethernet8                 0x60000000005b4

------------IPv6 Route Table ------------

VRF_Name = VrfRED VRF_SAI_OID = 0x30000000005b1
Prefix               NextHop                   SAI-OID
2001:100:120:120::/64 Ethernet8                 0x60000000005b4
```

**Debug Routeorch Nexthop Group:**

This command displays the Nexthop group / ECMP info in Routeorch.

- Usage: show debug routeorch nhgrp

- Example:
```
root@sonic:~# show debug routeorch nhgrp
Max Nexthop Group - 512
NHGrpKey             SAI-OID               NumPath    RefCnt
0x55c1ca5b7bd0       0x50000000005db        3          1
                               1: 100.120.120.10|Ethernet8
                               2: 100.120.120.11|Ethernet8
                               3: 100.120.120.12|Ethernet8
```

**Debug Routeorch all:**

This command is the superset of all routeorch debug commands. This command displays 
- All VRF route table info for both IPv4 and IPv6.
- Nexthop Group table

- Usage: show debug routeOrch all

### Component: NeighborOrch 

**Debug NeighborOrch Nexthops:**

This command displays the nexthops added in NeighorOrch. Nexthops displayed are added to ASIC_DB and has their corresponding SAI object ID.

- Usage: show debug NeighOrch nhops

- Example:
```
root@sonic:~# show debug neighorch nhops

NHIP                 Intf             SAI-OID           RefCnt    Flags
100.120.120.10       Ethernet8        0x40000000005d8   1          0
100.120.120.11       Ethernet8        0x40000000005d9   2          0
100.120.120.12       Ethernet8        0x40000000005da   2          0

NHIP                      Intf             SAI-OID           RefCnt    Flags
fe80::648a:79ff:fe5d:6b2a Ethernet4        0x40000000005df   0          0
fe80::fc54:ff:fe44:de2    Ethernet12       0x40000000005d4   0          0
fe80::fc54:ff:fe78:5fac   Ethernet8        0x40000000005d2   0          0
fe80::fc54:ff:fe88:6f80   Ethernet4        0x40000000005d0   0          0
fe80::fc54:ff:fe8e:d91f   Ethernet0        0x40000000005d1   0          0
```

**Debug NeighborOrch Neighbors:**

This command displays the Neighbor entry and their corresponding MAC added in NeighorOrch.

- Usage: show debug NeighOrch neigh

- Example:
```
root@sonic:~# show debug neighorch neigh
NHIP                  Intf             MAC
100.120.120.10        Ethernet8        00:00:11:22:00:10
100.120.120.11        Ethernet8        00:00:11:22:00:11
100.120.120.12        Ethernet8        00:00:11:22:00:12

NHIP                       Intf             MAC
fe80::648a:79ff:fe5d:6b2a  Ethernet4        fe:54:00:35:18:bb
fe80::fc54:ff:fe44:de2     Ethernet12       fe:54:00:44:0d:e2
fe80::fc54:ff:fe78:5fac    Ethernet8        fe:54:00:78:5f:ac
fe80::fc54:ff:fe88:6f80    Ethernet4        fe:54:00:88:6f:80
fe80::fc54:ff:fe8e:d91f    Ethernet0        fe:54:00:8e:d9:1f
```


# Syslog Server Config And Show Commands

## Syslog Server Config Commands

Following commands can be used to configure/manage remote syslog servers & its rules to filter the messages that can be received by them. After configuring the syslog servers, syslog service has to be restarted for these confirgurations to be active.

**config syslog server add**

This command is used to add a syslog server to send the syslog messages generated by this device.

- Usage: 
  config syslog server add <name | ip>

- Example:

```
admin@sonic:~$ config syslog server add 100.1.100.52
Root privileges are required for this operation
root@sonic:~# config syslog server add 100.1.100.52
100.1.100.52 server added to ConfigDB
root@sonic:~# config syslog server add rmt.syslog.srv
rmt.syslog.srv server added to ConfigDB
root@sonic:~#
```

**config syslog server del**

This command is used to delete the configured syslog server in this device. 

- Usage: 
  config syslog server del <name | ip>

- Example:

```
root@sonic:~# config syslog server del rmt.syslog.log
rmt.syslog.log server removed from ConfigDB
root@sonic:~# config syslog server del 221.22.200.21
221.22.200.21 server removed from ConfigDB
root@sonic:~#
```

**config syslog server set**

This command is used to set rules & filter syslog messages sent to syslog server. Rules & Filters are based on rsyslogd selectors.

**config syslog server set priority**

This command is used to set the remote server log level/severity. If a priority is set then all log matching this priority and higher will be logged. To log only a specific priority use "match" option. To exclude only a specific priority use "exclude" option. 

To delete/reset the priority config, execute the same command without the priority.

- Usage: 
  config syslog server set priority [OPTIONS] <name|ip> <prio>

```
  Options:
  -m, --match    log only this priority
  -e, --exclude  exclude this priority
  For <prio> (choose from debug, info, notice, warning, err, crit, alert, emerg)
```

- Example:

```
root@sonic:~# config syslog server set priority 33.33.100.3 err
root@sonic:~# config syslog server set priority 100.1.100.52 -m warning
root@sonic:~# config syslog server set priority 221.22.200.21 -e notice
root@sonic:~# config syslog server set priority 50.50.50.1 -me info
```

**config syslog server set filter**

This command can be used when more advanced log filters are required. Filter rules are free form text and should adhere to rsyslog selector rules.

To delete/reset the filter config, execute the same command without the filter rule.

- Usage: 
  config syslog server set filter [OPTIONS] <name|ip> <rsyslog rules within single quotes>

```
   Options:
   -f, --force  Force this rule, will remove priority config if any
```

- Example:

```
root@sonic:~# config syslog server set filter -f 20.0.0.20 'mail,news.=alert;*.=crit'
```

**config syslog restart**

Use this command to apply the configured syslog configurations and restart the syslog service. Its best to configure all syslog server and their log priority / filter rules and then restart.

- Usage: 
  config syslog restart

- Example:

```
root@sonic:~# config syslog restart
Syslog Config updated and rsyslog service restarted
Please check show runningconfig syslog
root@sonic:~#
```

## Syslog Server Show Commands

**show syslog server**

This command will list all the configured remote syslog servers and the configured priorities/filter_rule.

- Usage: show syslog server

- Example:

```
root@sonic:~# show syslog server
Server          Priority       FilterRules
--------------  -------------  ------------------------
50.50.50.1
rmt.syslog.srv
100.1.100.52    *.=warning
221.22.200.21   *.*;*.!notice
33.33.100.3     *.err
20.0.0.20                      mail,news.=alert;*.=crit

Note: This config may not be active
      To apply, please run rsyslog-config.sh or config syslog restart
```

**show runningconfig syslog**

This command will display the rsyslog.conf file. Verify the rsyslog.conf to confirm whether the configured syslog servers are applied to the rsyslogd or not.

- Usage: show runningconfig syslog

- Example:

```
root@sonic:~# show runningconfiguration syslog
#Set remote syslog server
mail,news.=alert;*.=crit @20.0.0.20:514;SONiCFileFormat
*.err @33.33.100.3:514;SONiCFileFormat
*.* @50.50.50.1:514;SONiCFileFormat
*.=warning @100.1.100.52:514;SONiCFileFormat
*.*;*.!notice @221.22.200.21:514;SONiCFileFormat
*.* @rmt.syslog.srv:514;SONiCFileFormat
```

# IPv6 Link-local Configuration Commands

This section explains all the commands that are supported in SONiC to configure IPv6 Link-local.

**config interface ipv6 enable use-link-local-only <interface_name>**

This command enables user to enable an interface to forward L3 traffic with out configuring an address. This command creates the routing interface based on the auto generated IPv6 link-local address. This command can be used even if an address is configured on the interface.

  - Usage:
    sudo config interface ipv6 enable use-link-local-only <interface_name>

- Examples:
```
  admin@sonic:~$ sudo config interface ipv6 enable use-link-local-only Vlan206
  admin@sonic:~$ sudo config interface ipv6 enable use-link-local-only PortChannel007
  admin@sonic:~$ sudo config interface ipv6 enable use-link-local-only Ethernet52
```

**config interface ipv6 disable use-link-local-only <interface_name>**

This command enables user to disable use-link-local-only configuration on an interface.

  - Usage:
    sudo config interface ipv6 disable use-link-local-only <interface_name>

- Examples:
```
  admin@sonic:~$ sudo config interface ipv6 disable use-link-local-only Vlan206
  admin@sonic:~$ sudo config interface ipv6 disable use-link-local-only PortChannel007
  admin@sonic:~$ sudo config interface ipv6 disable use-link-local-only Ethernet52
  ```

**config ipv6 enable**

This command enables user to enable use-link-local-only command on all the interfaces globally.

  - Usage:
    sudo config ipv6 enable

- Example:
  ```
  admin@sonic:~$ sudo config ipv6 enable
  ```
**config ipv6 disable**

This command enables user to disable use-link-local-only command on all the interfaces globally.

  - Usage:
    sudo config ipv6 disable

- Example:
  ```
  admin@sonic:~$ sudo config ipv6 disable
  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#ipv6-link-local-configuration-commands)

# BGP Unnumbered Configuration Command

Existing FRR CLI supports the configuration of BGP unnumbered.

This command uses link-local address to form BGP adjacency and adds the learned BGP routes with link-local address as nexthop. This command can be used in combination with the ipv6 use-link-local-only option on the interface to form BGP peering with link-local address.

neighbor <interface_name> interface remote-as <as>

Example:
  ```
neighbor Ethernet52 interface remote-as external
address-family ipv6 unicast
  neighbor Ethernet52 activate
exit
  ```
Note: This command is available in FRR BGP container vtysh shell.

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#IFA-configuration-and-show-commands)

# PTP Configuration And Show Commands

This section explains the Precision Time Protocol commands supported in SONiC.
The details of these commands are available in KLISH CLI reference guide.

## PTP configuration commands

**ptp mode <boundary-clock/peer-to-peer-transparent-clock/end-to-end-transparent-clock/disable>**
Configures the device ptp mode as boundary-clock, peer-to-peer-transparent-clock, end-to-end-transparent-clock or disables ptp.

    Default:disable

**ptp network-transport <l2/ipv4/ipv6> <unicast/multicast>**
Configures the network transport used and whether unicast or multicast mode.

    Default:l2

**ptp ipv6-scope <0x0..0xf>**
Configures the desired scope for the IPv6 multicast messages. This will be used as the second byte of the primary address and is relevant only in IPv6 multicast transp

    Default: 0xe

**ptp domain <0..127>**
Configures the domain number to use for this clock.

    Default: 0

**ptp domain-profile <default/g8275.1/g8275.2>**
Configures the method to be used when comparing data sets during the Best Master Clock Algorithm.

    Default: default

**ptp two-step <enable/disable>**
Configures the two-step mode for sync messages. One-step mode can be used only with hardware time stamping.

    Default: enable

**ptp priority1 <0..255>**
Configures the priority1 attribute of the local clock. It is used int he best master selection algorithm. Lower values take precedence.

    Default: 128

**ptp priority2 <0..255>**
Configures the priority2 attribute of the local clock. It is used int he best master selection algorithm. Lower values take precedence.

    Default: 128

**ptp announce-timeout <2..128>**
Configures the number of sync/follow up messages that may go missing before triggering a best master clock election.

    Default: 3

**ptp log-announce-interval <-128..128>**
Configures the mean time interval between announce messages.

    Default: 1

**ptp log-sync-interval <-128..128>**
Configures the mean time interval between sync messages.

    Default: 0

**ptp log-min-delay-req-interval <-128..128>**
Configures the minimum permitted mean time interval between delay_req messages.

    Default: 0

**ptp port add <Interface Name>**
Specifies the interface on which PTP is enabled.

    Default: N/A

**ptp port del <Interface Name>**
Removes the interface on which PTP is enabled.

    Default: N/A

**ptp port master-table <Interface Name> add <master-ip-address>**
Configures the set of master ip addresses that the slave port uses to initiate PTP communication. This is typically the IP address assigned to the interface attached t

    Default: N/A

**ptp port master-table <Interface Name> del <master-ip-address>**
Removes from the set of master ip addresses that the slave port uses to initiate PTP communication. This is typically the IP address assigned to the interface attached

    Default: N/A


## PTP show commands

**show ptp**

Example output:

    sonic# show ptp
    ---------------------
    Interface  State
    ---------------------
    Ethernet52  master
    Ethernet64  slave


**show ptp time-property**

Example output:

    sonic# show ptp time-property
    Curr UTC Offset Vld  false
    Curr UTC Offset      37
    Leap59               false
    Leap61               false
    Time Traceable       false
    Freq Traceable       false
    PTP Timescale        true


**show ptp clock**
Example output:

    sonic# show ptp clock
    Mode                  BC
    Domain Profile        ieee1588
    Network Transport     L2 multicast
    Domain Number         1
    Clock Identity        b86a97.fffe.2ff1ba
    Priority1             128
    Priority2             128
    Two Step              Enabled
    Slave Only            False
    Number Ports          2
    Clock Quality:
      Clock Class         248
      Clock Accuracy      254
      Ofst Scaled Log Var 65535
    Mean Path Delay       0
    Steps Removed         0
    Ofst From Master      0

**show ptp port**

Example output:

    sonic# show ptp port Ethernet 52
    Port Number                    52
    Port State                     master
    Log Min delay Req Intvl        0
    Peer Mean Path Delay           0
    Log Announce Interval          1
    Log Sync Interval              0
    Delay Mechanism                e2e
    Log Min PDelay Req Interval    0
    Version Number                 2
    Unicast Master Table:
                                   192.168.64.1

**show ptp parent**

Example output:

    sonic# show ptp parent
    Parent Clock Identity          b86a97.fffe.2ff1ba
    Port Number                    0
    Grandmaster Clock Class        248
    Grandmaster Off Scaled Log Var 65535
    Grandmaster Clock Accuracy     254
    Grandmaster Identity           b86a97.fffe.2ff1ba
    Grandmaster Priority1          110
    Grandmaster Priority2          128
    Stats Valid                    False
    Observed Off Scaled Log Var    65535
    Observed Clock Phase Chg Rate  2147483647

# sFlow commands
This section explains all the sFlow configuration commands and sFlow show commands that are supported in SONiC.

## sFlow KLISH commands

### sFlow KLISH Global configuration commands

Global sFlow configuration commands are executed in configuration-view.

  ```
sonic# configure terminal
sonic(config)#
```

**sflow enable**      

Globally, sFlow is disabled by default. This command enables global sFlow.

- Usage:     
  sflow enable

- Example:
  
```
  sonic(config)# sflow enable
  ```

**no sflow enable**

This command disables global sFlow.

- Usage:  
  no sflow enable

- Example:
  ```
  sonic(config)# no sflow enable
  ```

**sflow collector {Collector IP address} [{Collector port number}] vrf {vrf name}**

Use this command to add sFlow collector.

- Usage:  
  sflow collector {Collector IP address} [{Collector port number}] vrf {vrf name}
  
    - Collector IP address : IPv4 or IPv6 address of the sFlow collector
    - Collector port number[Optional]: UDP port of the collector (the range is from 0 to 65535. The default is 6343.)
    - VRF name [Optional]

- Example-1 :
  ```
  sonic(config)# sflow collector 10.0.0.1
  ```

- Example-2:
  ```
  sonic(config)# sflow collector  20.0.0.1 9898
  ```
- Note:
  Maximum of 2 collectors is allowed.

**no sflow collector {Collector IP} {Collector port number}] vrf {vrf name}**

Use this command to delete sFlow collector.

- Usage:  
  no sflow collector {Collector IP} {Collector port number}] vrf {vrf name}

  - Collector name: unique name of the sFlow collector

- Example:
  ```
  sonic(config)# no sflow collector 10.0.0.1
  ```

**sflow polling-interval {interval}**

Use this command to configure sFlow polling-interval.

- Usage:  
  sflow polling-interval {interval}

  - interval: [5 .. 300] (0 to disable, default is 20)
- Example:
  ```
  sonic(config)# sflow polling-interval 100
  ```
  

**no sflow polling-interval**

Use this command to reset sFlow polling-interval to default.

- Usage:  
  no sflow polling-interval

- Example:
  ```
  sonic(config)# no sflow polling-interval
  ```

**sflow agent {interface name}**

Use this command to configure sFlow agent interface.

- Usage:  
  sflow agent {interface name}

- Example:
  ```
  sonic(config)# sflow agent-id Ethernet 4
  ```

**no sflow agent-id**

Use this command to reset sFlow agent to default interface.

- Usage:  
  no sflow agent-id

- Example:
  ```
  sonic(config)# no sflow agent-id
  ```

### sFlow KLISH Interface configuration commands

sFlow configurations for specific interface are executed in interface-configuration-view.

  ```
sonic# configure terminal
sonic(config)# interface Ethernet 4
sonic(conf-if-Ethernet4)#
  ```

**sflow sampling-rate {rate}**

The default sample rate for any interface is (ifSpeed / 1e6) where ifSpeed is in bits/sec. 
So, the default sample rate based on interface speed is:

  - 1-in-1000 for a 1G link
  - 1-in-10,000 for a 10G link
  - 1-in-40,000 for a 40G link
  - 1-in-50,000 for a 50G link
  - 1-in-100,000 for a 100G link

Use this command to configure sampling-rate for a specific interface.

- Usage:  
  sflow sampling-rate {rate}
    - rate: [256..8388608]


- Example:
  ```
  sonic(config)# interface Ethernet 4
  sonic(conf-if-Ethernet4)# sflow sampling-rate 10000
```

**no sflow sampling-rate**

Use this command to reset sampling-rate to default for a specific interface.

- Usage:  
  no sflow sampling-rate

- Example:
```
  sonic(config)# interface Ethernet 4
  sonic(conf-if-Ethernet4)# no sflow sampling-rate
  ```

**no sflow enable**

Use this command to explicitly disable sFlow for a specific interface.

- Usage:  
  no sflow enable      

- Example:
  ```
  sonic(config)# interface Ethernet 4
  sonic(conf-if-Ethernet4)# no sflow enable
  ```

**sflow enable**

Use this command to enable sFlow for a specific interface.

- Usage:  
  sflow enable

- Example:
  ```
  sonic(config)# interface Ethernet 4
  sonic(conf-if-Ethernet4)# sflow enable
  ```

### sFlow KLISH Global show commands

**show sflow**

This command displays the current sFlow configuration including admin state, polling-interval, agent-id and collectors information.

- Usage:  
  show sflow  

- Example:
  ```
  sonic# show sflow
  ---------------------------------------------------------
  Global sFlow Information
  ---------------------------------------------------------
	        admin state:            up
	        polling-interval:       100
	        agent-id:               default
	        configured collectors:  2
	            collector1		10.0.0.1   6343   default  
	            collector2		20.0.0.1   9898   default
  ```
### sFlow KLISH Interface show commands

**show sflow interface**

This command displays the current running configuration of sflow on interfaces

- Usage:  
  show sflow interface

- Example:
  ```
    sonic# show sflow interface
    -----------------------------------------------------------
    sFlow interface configurations
    Interface		Admin State		Sampling Rate
    Ethernet0            up                      10000
    Ethernet4            down                    10000
    Ethernet8            up                      10000
    Ethernet12           up                      10000
    Ethernet16           up                      10000
    Ethernet20           up                      10000
    Ethernet24           up                      10000
    Ethernet28           up                      10000
    Ethernet32           up                      10000
    Ethernet36           up                      10000
    Ethernet40           up                      10000
    Ethernet44           up                      10000
    Ethernet48           up                      10000
    Ethernet52           up                      10000
    Ethernet56           up                      10000
    Ethernet60           up                      10000
    Ethernet64           up                      10000
    Ethernet68           up                      10000
    Ethernet72           up                      10000
    Ethernet76           up                      10000
    Ethernet80           up                      10000
    Ethernet84           up                      10000
    Ethernet88           up                      10000
    Ethernet92           up                      10000
    Ethernet96           up                      10000
    Ethernet100          up                      10000
    Ethernet104          up                      10000
    Ethernet108          up                      10000
    Ethernet112          up                      10000
    Ethernet116          up                      10000
    Ethernet120          up                      10000
    Ethernet124          up                      10000
  sonic#
  ```

## sFlow Click commands

### sFlow Click Global configuration commands

**config sflow enable**      

Globally, sFlow is disabled by default. This command enables global sFlow.

- Usage:     
  config sflow enable

- Example:
  
  ```
  root@sonic:/home/admin# config sflow enable
  ```

**config sflow disable**

This command disables global sFlow.

- Usage:  
  config sflow disable

- Example:
  ```
  root@sonic:/home/admin# config sflow disable
  ```

**config sflow collector add**

Use this command to add sFlow collector.

- Usage:  
  config sflow collector add {Collector name} {Collector IP address} [--port {Collector port number}] 
  
    - Collector name: unique name of the sFlow collector
    - Collector IP address : IPv4 or IPv6 address of the sFlow collector
    - Collector port number[Optional]: UDP port of the collector (the range is from 0 to 65535. The default is 6343.)

- Example-1 :
  ```
  root@sonic:/home/admin# config sflow collector add collector1 10.0.0.1
  ```

- Example-2:
  ```
  root@sonic:/home/admin# config sflow collector add collector2 20.0.0.1 --port 9898
  ```
- Note:
  Maximum of 2 collectors is allowed.

**config sflow collector del**

Use this command to delete sFlow collector.

- Usage:  
  config sflow collector del {Collector name} 

  - Collector name: unique name of the sFlow collector

- Example:
  ```
  root@sonic:/home/admin# config sflow collector del collector1
  ```

**config sflow polling-interval**

Use this command to configure sFlow polling-interval.

- Usage:  
  config sflow polling-interval {interval}

  - interval: [5 .. 300] (0 to disable, default is 20)
- Example:
  ```
  root@sonic:/home/admin# config sflow polling-interval 100
  ```

**config sflow agent-id add**

Use this command to configure sFlow agent interface.

- Usage:  
  config sflow agent-id add {interface name}

- Example:
  ```
  root@sonic:/home/admin# config sflow agent-id add Ethernet0
  ```

**config sflow agent-id del**

Use this command to reset sFlow agent to default interface.

- Usage:  
  config sflow agent-id del

- Example:
  ```
  root@sonic:/home/admin# config sflow agent-id del
  ```

### sFlow Click Interface configuration commands

**config sflow interface sample-rate**

The default sample rate for any interface is (ifSpeed / 1e6) where ifSpeed is in bits/sec. 
So, the default sample rate based on interface speed is:

  - 1-in-1000 for a 1G link
  - 1-in-10,000 for a 10G link
  - 1-in-40,000 for a 40G link
  - 1-in-50,000 for a 50G link
  - 1-in-100,000 for a 100G link

Use this command to configure sampling-rate for a specific interface.

- Usage:  
  config sflow interface sample-rate {interface name} {rate}
    - rate: [256..8388608]


- Example:
  ```
  root@sonic:/home/admin# config sflow interface sample-rate Ethernet4 10000
  ```

**config sflow interface disable**

Use this command to explicitly disable sFlow for a specific interface.

- Usage:  
  config sflow interface disable {interface name}     

- Example:
  ```
  root@sonic:/home/admin# config sflow interface disable Ethernet4
  ```

**config sflow interface enable**

Use this command to enable sFlow for a specific interface.

- Usage:  
  config sflow interface enable {interface name}

- Example:
  ```
  root@sonic:/home/admin# config sflow interface enable Ethernet4
  ```

### sFlow Click Global show commands

**show sflow**

This command displays the current sFlow configuration including admin state, polling-interval, agent-id and collectors information.

- Usage:  
  show sflow  

- Example:
  ```
  show root@sonic:/home/admin# show sflow

  sFlow Global Information:
    sFlow Admin State:          up
    sFlow Polling Interval:     100
    sFlow AgentID:              default

    2 Collectors configured:
      Name: collector1          IP addr: 10.0.0.1   UDP port: 6343
      Name: collector2          IP addr: 20.0.0.1   UDP port: 9898
  ```
### sFlow Click Interface show commands

**show sflow interface**

This command displays the current running configuration of sflow on interfaces

- Usage:  
  show sflow interface

- Example:
  ```
root@sonic:/home/admin# show sflow interface

sFlow interface configurations
+-------------+---------------+-----------------+
| Interface   | Admin State   |   Sampling Rate |
+=============+===============+=================+
| Ethernet0   | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet4   | down          |           10000 |
+-------------+---------------+-----------------+
| Ethernet8   | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet12  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet16  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet20  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet24  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet28  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet32  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet36  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet40  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet44  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet48  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet52  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet56  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet60  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet64  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet68  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet72  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet76  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet80  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet84  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet88  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet92  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet96  | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet100 | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet104 | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet108 | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet112 | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet116 | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet120 | up            |           10000 |
+-------------+---------------+-----------------+
| Ethernet124 | up            |           10000 |
+-------------+---------------+-----------------+
root@sonic:/home/admin#
```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#sflow-commands)


# BUM Storm Control Configuration and Show Commands

This section explains the Broadcast, Unknown-unicast and Unknown-multicast commands supported in SONIC.

## BUM Storm Control configuration commands

This sub-section explains the list of configuration commands for BUM storm control on physical interface.

### Enable BUM Storm Control on interface

**config interface storm-control {broadcast | unknown-unicast | unknown-multicast} {add} \<interface_name\> \<kilo_bits_per_second\>**

Use this command to enable BUM Storm Control on interface. 

- Usage:
  - config interface storm-control broadcast add {interface_name} {kbps_value}
  - config interface storm-control unknown-unicast add {interface_name} {kbps_value}
  - config interface storm-control unknown-multicast add {interface_name} {kbps_value}

- Example:

```
  root@sonic:/home/admin# config interface storm-control broadcast add Ethernet0 10000
  root@sonic:/home/admin# config interface storm-control unknown-unicast add Ethernet0 20000
  root@sonic:/home/admin# config interface storm-control unknown-multicast add Ethernet0 30000
  root@sonic:/home/admin# config interface storm-control broadcast add Ethernet1 40000
  root@sonic:/home/admin# config interface storm-control unknown-unicast add Ethernet1 50000
  root@sonic:/home/admin# config interface storm-control unknown-multicast add Ethernet1 60000
  root@sonic:/home/admin# config interface storm-control broadcast add Ethernet2 40000
  root@sonic:/home/admin# config interface storm-control unknown-unicast add Ethernet2 50000
  root@sonic:/home/admin# config interface storm-control unknown-multicast add Ethernet2 60000
  ```

### Update BUM Storm Control on interface

The command used to enable BUM Storm Control is taken as update when storm control is already enabled on the interface but the same command is given with a different value for kbps.

- Example:

  ```
  root@sonic:/home/admin# config interface storm-control broadcast add Ethernet2 10000
  root@sonic:/home/admin# config interface storm-control unknown-unicast add Ethernet2 20000
  root@sonic:/home/admin# config interface storm-control unknown-multicast add Ethernet2 30000
  ```


### Disable BUM Storm Control on interface

**config interface storm-control {broadcast | unknown-unicast | unknown-multicast} {del} \<interface_name\>**

Use this command to disable BUM Storm Control on interface

- Usage:
  - config interface storm-control broadcast del {interface_name}
  - config interface storm-control unknown-unicast del {interface_name}
  - config interface storm-control unknown-multicast del {interface_name}

- Example:

  ```
  root@sonic:/home/admin# config interface storm-control broadcast del Ethernet2
  root@sonic:/home/admin# config interface storm-control unknown-unicast del Ethernet2
  root@sonic:/home/admin# config interface storm-control unknown-multicast del Ethernet2
  ```


## BUM Storm Control show commands

This sub-section explains the list of show commands for BUM storm control

**show storm-control {all | interface \<interface_name\>}**

Use this command to display BUM Storm Control configurations done on all the interfaces in the device or on a particular interface. 

- Usage:
  - show storm-control all
  - show storm-control interface {interface_name}

- Example:

  ```
  admin@sonic:~$ show storm-control all
  +------------------+-------------------+---------------+
  | Interface Name   | Storm Type        |   Rate (kbps) |
  +==================+===================+===============+
  | Ethernet0        | broadcast         |         10000 |
  +------------------+-------------------+---------------+
  | Ethernet0        | unknown-multicast |         30000 |
  +------------------+-------------------+---------------+
  | Ethernet0        | unknown-unicast   |         20000 |
  +------------------+-------------------+---------------+
  | Ethernet1        | broadcast         |         40000 |
  +------------------+-------------------+---------------+
  | Ethernet1        | unknown-multicast |         60000 |
  +------------------+-------------------+---------------+
  | Ethernet1        | unknown-unicast   |         50000 |
  +------------------+-------------------+---------------+

  admin@sonic:~$ show storm-control interface Ethernet1
  +------------------+-------------------+---------------+
  | Interface Name   | Storm Type        |   Rate (kbps) |
  +==================+===================+===============+
  | Ethernet1        | broadcast         |         40000 |
  +------------------+-------------------+---------------+
  | Ethernet1        | unknown-unicast   |         50000 |
  +------------------+-------------------+---------------+
  | Ethernet1        | unknown-multicast |         60000 |
  +------------------+-------------------+---------------+

  ```

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#BUM-Storm-Control-Configuration-and-Show-Commands)



# OSPFv2 Configuration And Show Commands

This section explains all the OSPFv2 show commands and OSPFv2 configuation commands of FRR routing software supported in SONiC.

FRR routing software documentation can be found at: http://docs.frrouting.org/en/latest/

Following features of FRR OSPFv2 proctocol is supported in this release of SONiC.
  - One OSPF process (single instance) per SONiC system in BGP docker container
  - Multi-VRF OSPF (OSPF in default and user VRFs)
  - OSPF over SONiC Ethernet, PortChannel, VLAN IPv4 interfaces.
  - OSPF in MCLAG Peers with unique IPv4 Addresses on LAG IPv4 interfaces.
  - OSPF Type-1 to Type-5 LSAs
  - Multi-Area OSPF
  - OSPF Stub areas
  - OSPF router ABR and ASBR roles
  - OSPF packets simple password authentication
  - OSPF packets authentication using MD5 HMACs
  - OSPF Passive interfaces
  - BFD on OSPF numbered interfaces
  - Type-3 Summary LSA prefix substitution
  - Type-3 Summary LSA filtering (using prefix/import/export lists)
  - Route redistribution to/from OSPF
  - Type-1 and type-2 metric for imported external protocol routes
  - OSPF ECMP routes
  - OSPF Warm reboot 
  - Maximum of 50 OSPFv2 routers per OSPF area
  - Maximum of 128 OSPFv2 enabled L3 interfaces 
  - Maximum of 5000 Intra area routes or prefixes
  - Maximum of 5000 Inter area summary routes
  - Maximum of 40000 external (type-5) routes

Following Configuration and Management features OSPFv2 proctocol is supported.
  - OSPFv2 configuration commands using vtysh
  - OSPFv2 show commands using vtysh
  - SNMP AgentX protocol, providing MIB read-only access
  - SNMP MIB as per RFC 1850 
  - SONiC split docker routing config mode 

Note: User can configure OSPF from KLISH CLI too. Pelase refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring OSPF using KLISH CLIs.

In order to retain OSPFv2 configuration across BGP docker restart, SONiC system reboot and SONIC image upgrade, pelase refer to "routing_config_mode" confiugration section and configure routing mode accordingly. 

OSPF Router ID selection will be as per below preference
 - OSPF router mode configured router id value
 - If user configured router ID value is not present, then choose the most recently used router id value
 - FRR Zebra daemon recommended value of Router ID. Zebra daemon will choose router id in below order
    - FRR global mode configured router id value
    - Highest IPv4 address value among SONiC physical and loopback interface IPv4 addresses.

SONiC OSPFv2 supports only above listed fature capabilities of FRR OSPFv2. Some FRR OSPFv2 feature capabilities like multi-instance (mutiple ospf procesess), MPLS, Segment Routing etc are not supported in this release.

SONiC OSPFv2 is Single inastnce FRR OSPFv2. Hence all OSPFv2 configuration commands shall ommit intance-id command parameter.

SONiC IPv4 interfaces (Ethernet, VLAN, Portchannel) will be auto populated as FRR Zebra IPv4 interfaces. Only these auto populated IPv4 interfaces shall be used for OSPFv2 interface mode configurations

FRR show commands start with command "show ip ospf ", with optional VRF and other command parameters.

OSPFv2 route warm reboot can be enabled by enabling warm restart on four modules namely 'system', 'swss', 'teamd', and 'bgp' using SONiC CLI command 'config warm_restart enable'.  When warm restart is not enabled, upon system or bgp container restart, all the OSPFv2 routes will be newly populated into forwarding plane. When warm restart is enabled, upon warm reboot, system will reconcile all the OSPFv2 routes with forwarding plane.


## OSPFv2 show commands

OSPF show command list is as below. Please refer to FRR User Manual for more details on these commands.

        show ip ospf
        show ip ospf interface [INTERFACE]
        show ip ospf neighbor
        show ip ospf neighbor INTERFACE
        show ip ospf neighbor detail
        show ip ospf neighbor INTERFACE detail
        show ip ospf database
        show ip ospf database (asbr-summary|external|network|router|summary)
        show ip ospf database (asbr-summary|external|network|router|summary) LINK-STATE-ID
        show ip ospf database (asbr-summary|external|network|router|summary)
                        LINK-STATE-ID adv-router show ip ospf database
                        (asbr-summary|external|network|router|summary) adv-router ADV-ROUTER
        show ip ospf database (asbr-summary|external|network|router|summary) LINK-STATE-ID self-originate
        show ip ospf database (asbr-summary|external|network|router|summary) self-originate
        show ip ospf database max-age
        show ip ospf database self-originate
        show ip ospf route


## OSPFv2 config commands

OSPF router configuration command is as below. Please refer to FRR User Manual for more details on these commands.

        [no] router ospf [vrf NAME]

   OSPF router mode config commands are as below 

        [no] ospf router-id A.B.C.D
    
        [no] area <A.B.C.D|(0-4294967295)> authentication message-digest
        [no] area <A.B.C.D|(0-4294967295)> authentication
        [no] area <A.B.C.D|(0-4294967295)> default-cost (0-16777215)
        [no] area <A.B.C.D|(0-4294967295)> export-list NAME
        [no] area <A.B.C.D|(0-4294967295)> filter-list prefix WORD <in|out>
        [no] area <A.B.C.D|(0-4294967295)> import-list NAME
        [no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M [advertise [cost (0-16777215)]]
        [no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M cost (0-16777215)
        [no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M not-advertise
        [no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M substitute A.B.C.D/M
        [no] area <A.B.C.D|(0-4294967295)> shortcut <default|enable|disable>
        [no] area <A.B.C.D|(0-4294967295)> stub no-summary
        [no] area <A.B.C.D|(0-4294967295)> stub
        [no] area <A.B.C.D|(0-4294967295)> virtual-link A.B.C.D [authentication [<message-digest|null>]] 
                                           [<message-digest-key (1-255) md5 KEY|authentication-key AUTH_KEY>]
        [no] area <A.B.C.D|(0-4294967295)> virtual-link A.B.C.D {hello-interval (1-65535)|
                                           retransmit-interval (1-65535)| 
                                           transmit-delay (1-65535)|dead-interval (1-65535)}
    
        [no] auto-cost reference-bandwidth (1-4294967)
        [no] default-information originate [{always|metric (0-16777214)|metric-type (1-2)|route-map WORD}]
        [no] default-metric (0-16777214)
        [no] distribute-list WORD out <kernel|connected|static|bgp>
        [no] distance (1-255)
        [no] distance ospf {intra-area (1-255)|inter-area (1-255)|external (1-255)}
    
        [no] log-adjacency-changes detail
        [no] log-adjacency-changes
        [no] max-metric router-lsa administrative
        [no] max-metric router-lsa on-shutdown (5-100)
        [no] max-metric router-lsa on-startup (5-86400)
    
        [no] neighbor A.B.C.D [priority (0-255) [poll-interval (1-65535)]]
        [no] neighbor A.B.C.D poll-interval (1-65535) [priority (0-255)]
    
        [no] network A.B.C.D/M area <A.B.C.D|(0-4294967295)>
    
        [no] ospf router-id [A.B.C.D]
        [no] passive-interface <IFNAME [A.B.C.D]|default>


        [no] redistribute <kernel|connected|static|bgp> [{metric (0-16777214)|metric-type (1-2)|route-map WORD}]
        [no] redistribute <ospf|table> (1-65535)[{metric (0-16777214)|metric-type (1-2)|route-map WORD}]
        [no] refresh timer [(10-1800)]
        [no] timers lsa min-arrival [(0-600000)]
        [no] timers throttle lsa all [(0-5000)]
        [no] timers throttle spf [(0-600000)(0-600000)(0-600000)]


   OSPF interface mode config commands are as below 

        [no] ip ospf [(1-65535)] area <A.B.C.D|(0-4294967295)> [A.B.C.D]
        [no] ip ospf authentication <null|message-digest> [A.B.C.D]
        [no] ip ospf authentication [A.B.C.D]
        [no] ip ospf authentication-key AUTH_KEY [A.B.C.D]
        [no] ip ospf bfd
        [no] ip ospf cost (1-65535) [A.B.C.D]
        [no] ip ospf dead-interval (1-65535) [A.B.C.D]
        [no] ip ospf dead-interval minimal hello-multiplier (1-10) [A.B.C.D]
        [no] ip ospf hello-interval (1-65535) [A.B.C.D]
        [no] ip ospf message-digest-key (1-255) md5 KEY [A.B.C.D]
        [no] ip ospf network <broadcast|non-broadcast|point-to-multipoint|point-to-point>
        [no] ip ospf priority (0-255) [A.B.C.D]
        [no] ip ospf retransmit-interval (3-65535) [A.B.C.D]
        [no] ip ospf transmit-delay (1-65535) [A.B.C.D]

## OSPFv2 clear commands

OSPF clear commands are as below. Please refer to FRR User Manual for more details on these commands.
Note: These CLIs are available only in FRR vtysh CLI.

        clear ip ospf [vrf NAME] interface IFNAME

## OSPFv2 debug commands

   OSPF debug config commands are as below. Please refer to FRR User Manual for more details on these commands.
   Note: These CLIs are available only in FRR vtysh CLI.

        [no] debug ospf event
        [no] debug ospf ism
        [no] debug ospf lsa
        [no] debug ospf nsm
        [no] debug ospf packet
        [no] debug ospf zebra

   Required log levels (debugging, informational, errors, etc) have to be enabled under "log syslog " to get above enabled logs in log file.

   Debug file path can be configured under configuration mode as below

        log file <file_path_and_name>
    
        Example : log file /var/log/frr/frr.log

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE) or [Beginning of this section](#ospfv2-configuration-and-show-commands)

### Switch Resource configuration and show commands

## Switch Resource configuration commands
Switch resource configurations for drop-monitor are executed in config-switch-resource-view.

  ```
sonic# configure terminal
sonic(config)# switch-resource
sonic(config-switch-resource)#
  ```

**drop-monitor flows**
Configure the scale of drop-monitor flows supported by the drop monitor feature. This command takes an argument as 'none' or 'min'. 'none' can be chosen when the user does not plan to use the drop-monitor feature. It suggests the system not to allocate any exact match resource resource for drop-monitor feature; while 'min' will allocate minimum amount exact match resources for the feature to work.

This configuration modifies the system settings and requires a reboot or 'config reload' to take effect.


  ```
sonic(config-switch-resource)# drop-monitor flows min
 ```

## Switch Resource show commands

**show switch-resource drop-monitor**

Example output:

 ```
show switch-resource drop-monitor

Drop-Monitor flows
----------------------
Configured           : none
In use               : min
Needs reboot         : true
  ```

# SNMP Configuration And Show Commands

This section explains the SNMP commands supported in SONiC. The details of these commands are available in KLISH CLI reference guide.

## SNMP configuration commands

  snmp-server [ contact <contact> ] [ location <location> ] [ engine <octet-byte-string> ]

  snmp-server agentaddress <ip address> [ port <udpPort> ] [interface <vrfname> ]

  snmp-server enable trap

  snmp-server community <community name>  [groupname  <group name>]

  snmp-server group <group name> { any | v2c | v3 { auth | noauth | priv }} [ read <viewname> ] [ write <viewname> ] [ notify <viewname> ]

  snmp-server host <ip-addr> community <community name> {[ traps v2c ] | informs [timeout seconds] [retries retries]}

  snmp-server host <ip-addr> user <username> {[traps  {auth | noauth | priv}] | [informs {auth | noauth | priv} [timeout seconds] [retries retries]]}

  snmp-server user <username> [ group <group-name> ] [ encrypted ] [ auth { md5 | sha | noauth } [ auth-password <password-string>  ] [ priv { DES | AES-128 } [ priv-password <password-string> ] ]

  snmp-server view <view name> <oid-tree> {included | excluded}

## SNMP show commands

  show snmp-server

  show snmp-server community

  show snmp-server group

  show snmp-server host

  show snmp-server user

  show snmp-server view

# BroadView Configuration Commands

This section explains all BroadView configuration commands of BroadView REST interface supported in SONiC.

## BroadView config commands

BroadView collector configuration command is below

        config broadview collector <ip> <port>


<br>
<br>

# Industry Standard CLIs

As part of SONiC 3.0, a new management framework has been introduced. The new management framework provides an industry standard CLIs (IS-CLIs). The commands modelled as IS-CLIs can be referred using a link below<br>
[Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md)
<br>
The Industry Standard CLIs are executed in a SONiC shell which the new management framework provides. The SONiC shell can be accessed from host of the SONiC system as shown below
<br>

  ```
admin@sonic:~$ sonic-cli

sonic#
```

# Snapshot configuration Commands

This section explains snapshot feature configuration commands in SONiC 3.0.

## Snapshot config commands

**config watermark interval <interval>**
This command configures the periodicity of snapshot reports. 

Default: 10s

## Snapshot clear commands

**sonic-clear watermark interval**
This command clears any existing snapshot interval configuration.

## Snapshot KLISH show commands

**show watermark interval**
The above command is to show the interval used for determining periodicity for fetching data from the HW and put it into counter DB using snapshot. It shows default interval(10 sec) if not configured by user.

# Snapshot KLISH config commands

**watermark interval <value>**
This command configures the periodicity of snapshot reports.

Default: 10s

## Snapshot KLISH clear commands

**no watermark interval**
This command clears any existing snapshot interval configuration.

# Dynamic Port Breakout KLISH config commands

**interface breakout port <slot/port> mode \<option\>**
port breakout command

**no interface breakout port <slot/port>**
breakout command to bring back port to default mode.

slot - for future use (fixed to 1 now for rack unit/pizza box)
port - Front panel port number (cage number)
option - break out mode 


Example:
sonic-cli# configure terminal
sonic-cli(config)# interface breakout port 1/49 mode 4x25G

Example:
sonic-cli# configure terminal
sonic-cli(config)# no interface breakout port 1/49

These are the two config commands to breakout port into different modes (speed). 
The new ports are ready for configuration after status is "Completed". The below show command gives breakout status. 
After the config command, CLI prompt will be returned to user immediately and does not wait for completion.  User need to check for the completion before proceeding any configuration on new ports. 
The same sequence i.e. breakout config followed by show to write any custom scripts.


# Dynamic Port Breakout KLISH show commands

**show interface breakout port <slot/port>**

This command displays status of the breakout config command and new port availability.
The ports are ready for further configuration after status is "Completed".
"In-progress" state indicates breakout is not yet completed and in progress.

Example:
sonic# show interface breakout port 1/49
-----------------------------------------------
Port  Breakout Mode  Status        Interfaces          
-----------------------------------------------
1/49   4x25G          Completed    Ethernet48          
                                   Ethernet49          
                                   Ethernet50          
                                   Ethernet51          
sonic#


**show interface breakout modes**
To display available breakout mode on the platform. It also display native and fron panel port number mapping.

Example:
sonic# show interface breakout modes 
-------------------------------------------------------------------------
Port  Interface   Supported Modes                           Default Mode
-------------------------------------------------------------------------
1/49  Ethernet48  1x100G, 1x40G, 4x25G, 4x10G               1x100G
1/56  Ethernet76  1x100G, 1x40G, 4x25G, 4x10G               1x100G
sonic#

sonic(config)# interface-naming standard
sonic# show interface breakout modes 
-------------------------------------------------------------------------
Port  Interface   Supported Modes                           Default Mode
-------------------------------------------------------------------------
1/49  Eth1/49     1x100G, 1x40G, 4x25G, 4x10G               1x100G
1/56  Eth1/56     1x100G, 1x40G, 4x25G, 4x10G               1x100G
sonic#


**show interface breakout dependencies port <slot/port>**
This displays port dependent configurations. These will be erased up as part of breakout.
New ports created wll be in default config (admin down).

Example:
sonic# show interface breakout dependencies port 1/49
----------------------------------
Dependent Configurations
----------------------------------
VLAN|Vlan8
VLAN_MEMBER|Vlan8|Ethernet48
sonic#

Go Back To [Beginning of the document](#SONiC-COMMAND-LINE-INTERFACE-GUIDE)

```

# IP SLA

This section explains IP SLA (ICMP and TCP SLA Track) commands.

Configuration for SLA track will follow FRR configuration design pattern. FRR CLIs will be implemted and acessible via vtysh. SONIC configuration management framework will populate config-db entries for IP SLA track and bgpcfgd will listen to config change events and invoke corresponding ip-sla config commands.

IP SLA track show output will also follow the FRR design pattern. Sonic-management framework transformer code will invoke vtysh command and collect json output. The json output will be parsed and converted to render rest in the management framework modules.

IP SLA can be configured from SONIC Management IS-CLI. Please refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring IP SLA using IS-CLIs.

## IP SLA config commands

The following commands will be used to configure ICMP SLA track.

```
sonic(config)# ip sla 10
sonic(config-ip-sla-10)# icmp-echo <destination> [vrf <vrf>]
sonic(config-ip-sla-10-icmp)# source-address <source-address>
sonic(config-ip-sla-10-icmp)# source-interface <interface>
sonic(config-ip-sla-10-icmp)# request-data-size <28-1472>
sonic(config-ip-sla-10-icmp)# ttl <1-255>
sonic(config-ip-sla-10-icmp)# tos <1-255>

```

The following commands will be used to configure TCP SLA track.


```
sonic(config)# ip sla 20
sonic(config-ip-sla-20)# tcp-connect <destionation> <port> [vrf <vrf>]
sonic(config-ip-sla-20-tcp)# source-address <ip-address>
sonic(config-ip-sla-20-tcp)# source-port <port>
sonic(config-ip-sla-20-tcp)# source-interface <interface>
sonic(config-ip-sla-20-tcp)# ttl <1-255>
sonic(config-ip-sla-20-tcp)# tos <1-255>
sonic(config-ip-sla-20-tcp)# exit
```

Note: In order to avoid potential TCP port conflict, avoid using TCP source port from dynamic (aka private) TCP port range configured in the system (default net.ipv4.ip_local_port_range is 32768-60999).


The following configuration commands are common to ICMP and TCP SLA Track:

```
sonic(config-ip-sla-number)# frequency <1-300>
sonic(config-ip-sla-number)# threshold <1-1000>
sonic(config-ip-sla-number)# timeout 1000
sonic(config-ip-sla-number)# 
```

Static route can be configured to track a given SLA track object:

```
sonic(config)# ip route 0.0.0.0 2.2.2.2 track 10
```

## IP SLA show commands

Summary output listing all of the configured IP SLAs:

```
sonic# show ip sla
SLA#    Type         State Target                    VRF          Transitions  Last change
----    ----         ----- ------------------------  -----------  -----------  -----------
10      TCP-connect  Up    51.52.0.1(22)             default      29           01:16:16 ago
11      ICMP-echo    Up    4090::2                   default      1            2d09h48m ago
12      TCP-connect  Down  4090::2(22)               default      0            2d09h50m ago
20      ICMP-echo    Up    51.52.0.1                 default      1            2d09h48m ago
```

#### **show ICMP SLA track commands**

Show output of specific ICMP-echo IP SLA instance:

```
sonic# show ip sla 20
IP SLA Operation Number: 20
    Type of Operation: ICMP-echo
    ICMP destination IP address: 51.52.0.1
    ICMP source IP address:
    ICMP source interface: Vlan4090
    ICMP request data size: 32
    ICMP Time-To-Live(TTL): 0
    ICMP Type-of-Service(ToS): 0
    Source VRF: default
    Operation frequency (sec): 1
    Operation timeout (sec): 3
    Operation threshold: 3
    Operation state: Up
    Operation state transitions: 1
    Operation last state change: 2d09h49m ago
    ICMP Echo Request counter: 191708
    ICMP Echo Reply counter: 191707
    ICMP Error counter: 114
    ICMP Invalid responses: 0
sonic#
```

Detailed show output of specific ICMP-echo IP SLA instance (available in FRR vtysh):

```
sonic# show ip sla 20 detail
IP SLA Operation Number: 20
    Type of Operation: ICMP-echo
    ICMP destination IP address: 51.52.0.1
    ICMP source IP address:
    ICMP source interface: Vlan4090
    ICMP request data size: 32
    ICMP Time-To-Live(TTL): 0
    ICMP Type-of-Service(ToS): 0
    Source VRF: default
    Operation frequency (sec): 1
    Operation timeout (sec): 3
    Operation threshold: 3
    Operation state: Up
    Operation state transitions: 1
    Operation last state change: 2d09h50m ago
    ICMP Echo Request counter: 191758
    ICMP Echo Reply counter: 191758
    ICMP Error counter: 114
    ICMP Invalid responses: 0
    VRF present: True
    NHT registered: True
    NHT reachable: True
    Source interface present: True
    Socket fd: 14
    Threshold counter: 0
    Last error: Pkt send error(Network is unreachable)
    Last error happened: 2d09h50m ago
sonic#

```

History output of specific IP SLA instance:

```
sonic# show ip sla 20 history
Timestamp                 Event
---------                 -----
Sat Jun 20 10:27:27 2020  Started
Sat Jun 20 10:27:27 2020  Pkt send error(Network is unreachable)
Sat Jun 20 10:29:25 2020  State changed to: Up
sonic#


```

#### **show TCP SLA track commands**

Show output of specific TCP-connect IP SLA instance:

```
sonic# show ip sla 10
IP SLA Operation Number: 10
    Type of Operation: TCP-connect
    TCP destination IP address: 51.52.0.1
    TCP destination port: 22
    TCP source IP address: 40.90.0.0
    TCP source port: 1020
    TCP source interface:
    TCP Time-To-Live(TTL): 0
    TCP ToS (Type-of-Service): 0
    Source VRF: default
    Operation frequency (sec): 1
    Operation timeout (sec): 3
    Operation threshold: 3
    Operation state: Up
    Operation state transitions: 29
    Operation last state change: 01:19:34 ago
    TCP connect request counter: 191876
    TCP connect success counter: 191762
    TCP connect error counter: 59
sonic#
```

Detailed show output of specific TCP-connect IP SLA instance (available in FRR vtysh):

```
sonic# show ip sla 10 detail
IP SLA Operation Number: 10
    Type of Operation: TCP-connect
    TCP destination IP address: 51.52.0.1
    TCP destination port: 22
    TCP source IP address: 40.90.0.0
    TCP source port: 1020
    TCP source interface:
    TCP Time-To-Live(TTL): 0
    TCP ToS (Type-of-Service): 0
    Source VRF: default
    Operation frequency (sec): 1
    Operation timeout (sec): 3
    Operation threshold: 3
    Operation state: Up
    Operation state transitions: 29
    Operation last state change: 01:19:39 ago
    TCP connect request counter: 191881
    TCP connect success counter: 191767
    TCP connect error counter: 59
    VRF present: True
    NHT registered: True
    NHT reachable: True
    Threshold counter: 3
    Last error: Socket connect error(Cannot assign requested address)
    Last error happened: 00:13:20 ago
sonic#

```

### IP SLA Clear commands 

Below commands can be used to clear statistics and history for all of the IP
SLA instances or for a particular one.

```
sonic# clear ip sla all
sonic# clear ip sla <1-255>
```

### IP SLA Debug Commands

Debugging for IP SLA can be enabled using below iptrack commands from FRR vtysh:

```
sonic# debug iptrack sla <1-255>
```

The above command enables debug logs for the specified IP SLA instance.


### 
# Chassis Locator LED 

This section explains chassis locator LED commands.
The chassis locator LED allows you to identify a switch in a multi-switch environment or data center.   

## Enable Chassis Locator LED 

- Examples on supported platforms:
  ```
  sonic# locator-led chassis on
  Success

  sonic# locator-led chassis on
  Failed

  ```

- Examples on unsupported platforms:
  ```
  sonic# locator-led chassis on
  Not supported

  ```

## Disable Chassis Locator LED 

- Examples on supported platforms:
  ```
  sonic# locator-led chassis off
  Success

  sonic# locator-led chassis off
  Failed

  ```
- Examples on unsupported platforms:
  ```
  sonic# locator-led chassis off
  Not supported

  ```

## Show Chassis Locator LED 

- Examples on supported platforms:
  ```
  sonic# show locator-led chassis
  State    Color
  -------  -------
  on      blue

  Note: Chassis Locator LED color is platform specific. The output of color can vary

  sonic# show locator-led chassis
  State    Color
  -------  -------
  off      off

  Note: Chassis Locator LED is turned off

  sonic# show locator-led chassis
  State   Color
  ------- -------
  off     Unknown
  Color may vary with respect to system states

  Note: If the software cannot retrieve the locator LED color, unknown is displayed. The LED color is lit on the
  switch.

  ```

- Examples on unsupported platforms:
  ```
  sonic# show locator-led chassis
  Not supported

  ```

# IP ECMP Loadshare and Symmetric hash
Equal Cost Multi-Path (ECMP) is used in Layer-3 routing to forward traffic to a destination using multiple available paths. Increasing the number of paths, increases the available bandwidth to reach the destination. However, all available paths should be closely load-shared so that no one path is over/under-utilized. It is also required to maintain same path for a given traffic "flow" to avoid any packet re-ordering issue. ASICs achieve this by using an hash algorithm to generate a hash value from packet header and/or static hash configurations. This, generated hash value, is then used to load-share flows across different available paths.

Symmetric hashing allows bidirectional flows with a given Src-IP and Dst-IP and/or given Src-Port and Dst-Port to follow the same path even when they are interchanged. This can be controlled, separately, for IPv4 and IPv6 flows on the device.

This feature allows user to modify the IP ECMP load-share hashing parameters to affect traffic load-sharing across multiple available paths. All the configurations are per-device.

It is important to note here that this feature is not intended to affect port-channel load distribution behaviour

IP ECMP Loadshare and Symmetric hash can be configured from SONIC Management IS-CLI. Please refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring IP ECMP Loadshare and Symmetric hash using IS-CLIs.

### Configuration Commands
```
Syntax:
      [no] ip load-share hash ipv4 {ipv4-src-ip | ipv4-dst-ip | ipv4-ip-proto | ipv4-l4-src-port | ipv4-l4-dst-port | symmetric}
      [no] ip load-share hash ipv6 {ipv6-src-ip | ipv6-dst-ip | ipv6-next-hdr | ipv6-l4-src-port | ipv6-l4-dst-port | symmetric}
      [no] ip load-share hash seed <seed-value>

Example:

      ip load-share hash seed 12

      ip load-share hash ipv4 ipv4-src-ip
      ip load-share hash ipv4 ipv4-dst-ip
      ip load-share hash ipv4 symmetric

      ip load-share hash ipv6 ipv6-l4-src-port
      ip load-share hash ipv6 ipv6-l4-dst-port
      ip load-share hash ipv6 symmetric
```
### Show Commands
```
      show ip load-share

Sample Output:

      IP Hash Mode: Default
      IPv6 Hash Mode: Symmetric
      Packet Header Fields:
      IP: ipv4-src-ip ipv4-dst-ip ipv4-ip-proto ipv4-l4-src-port ipv4-l4-dst-port
      IPv6: ipv6-src-ip ipv6-dst-ip ipv6-next-hdr ipv6-l4-src-port ipv6-l4-dst-port
      Hash seed: 10
```
# IP Sub-interface

A sub port interface is a logical interface that can be created on a physical port or a port channel. A sub port interface serves as an interface to either a .1D bridge or a VRF, but not both. This releae supports the use case of creating a sub port interface on a physical port or a port channel and using it as a router interface to a VRF only. This release does NOT support the case of creating and using sub port as a bridge port to a .1D bridge. 

Multiple L3 sub port interfaces, each characterized by a VLAN id in the 802.1q tag, can be created on a physical port or a port channel. Sub port interfaces attaching to the same physical port or port channel can interface to different VRFs, though they share the same VLAN id space and must have different VLAN ids. Sub port interfaces attaching to different physical ports or port channels can use the same VLAN id, even when they interface to the same VRF. However, there is no L2 bridging between these sub port interfaces; each sub port interface is considered to stay in a separate bridge domain.

All commonly used L3 features like ACL, BGP, OSPF, BFD, MLAG, VxLAN etc are supported on the sub interface. 
Below features are not supported on sub interfaces:
- IP Helper
- NAT
- Hardware RIF counters
- Port based policing rate limit

Sub-interfaces can be configured from SONIC Management IS-CLI as well. Please refer to [Industry Standard CLI's Reference Guide](industry_standard_cli_reference_guide.md) for more details about configuring sub-interfaces using IS-CLIs.

### CLICK CLIs:
Following are CLICK CLIs to configure sub-interfaces:

Adding OR Deleting sub-interface:
```
config subinterface add <sub_port_interface_name> [vlan <1-4094>]
config subinterface del <sub_port_interface_name>

Example:
config subinterface add Ethernet1.100
config subinterface add PortChannel10.150 150

config subinterface del Ethernet1.100
config subinterface del PortChannel10.150
```

Configuring OR deleting IP address on sub-interface:
```
config interface ip add <sub_port_interface_name> <ip_addr>
config interface ip remove <sub_port_interface_name> <ip_addr>

Example:
config interface ip add Ethernet1.100 10.100.1.1/24
config interface ip remove Ethernet1.100 100.1.1.1/24
```

Configuring Admin status on the sub-interface:
```
config interface startup <sub_port_interface_name>
config interface shutdown <sub_port_interface_name>

Example:
config interface startup Ethernet1.100
config interface shutdown Ethernet1.100
```

Show all sub-interfaces configured:
```
show subinterfaces status 

Example output:
  Sub port interface    Speed    MTU    Vlan    Admin                 Type
--------------------  -------  -----  ------  -------  -------------------
            Eth1.100      25G   9100     100       up  dot1q-encapsulation
            Po10.150      50G   9100     150       up  dot1q-encapsulation
```

Configuring all other features like BGP, OSPF etc. is similar to configuring on physical interface or port channel interfaces. 
