# Unit 2 Lab - Securing the Network Connection

## Required Materials

Putty or other connection tool Lab Server
Root or sudo command access
STIG Viewer

## Pre-Lab Warm Up

**EXERCISES**

```
sysctl -a | grep -i ipv4 | grep -i forward

net.ipv4.conf.all.bc_forwarding = 0 # forwarding of broadcast packets on all interfaces
net.ipv4.conf.all.forwarding = 0 # ip forwarding for all interfaces globally
net.ipv4.conf.all.mc_forwarding = 0 # multicast packet forwarding on all interfaces
net.ipv4.conf.default.bc_forwarding = 0 # default for new interfaces for broadcast packet forwarding
net.ipv4.conf.default.forwarding = 0 # default for new interfaces to allow IP forwarding
net.ipv4.conf.default.mc_forwarding = 0 # default for new interfaces for multicast forwarding
net.ipv4.conf.eth0.bc_forwarding = 0 # forwarding for broadcast packets on eth0 interface
net.ipv4.conf.eth0.forwarding = 0 # IP forwarding on eth0 interface
net.ipv4.conf.eth0.mc_forwarding = 0 # multicast forwarding on eth0 interface
net.ipv4.conf.lo.bc_forwarding = 0 # broadcast forwarding on loopback interface
net.ipv4.conf.lo.forwarding = 0 # ip forwarding on loopback interface
net.ipv4.conf.lo.mc_forwarding = 0 # multicast forwarding on loopback interface
net.ipv4.ip_forward = 0 # global switch for ip forwarding. 1 means system can route packets between interfaces
net.ipv4.ip_forward_update_priority = 1 # priority of routing table updates due to ip forwarding. 1 means high priority
net.ipv4.ip_forward_use_pmtu = 0 # Path MTU discovery when forwarding packets.

Path MTU Discovery: 
  - Maxiumum transmission unit (MTU)
  - 0 or 1 determines if the system will try to dynamically determine the best MTU size for 
  forwarding packets, potentially avoiding issues with some routers/firewalls.

# Does this system appear to be set to forward? Why or why not?
# no, it does not appear to be forwarding. All values are set to 0 (not counting update_priority)

systctl -a | grep -i ipv4 | grep -i martian

net.ipv4.conf.all.log_martians = 0 # logging of martian packets
net.ipv4.conf.default.log_martians = 0 # default for new interfaces whether to log martian packets
net.ipv4.conf.eth0.log_martians = 0 # enable/disable martian logging on eth0
net.ipv4.conf.lo.log_martians = 0 # enable/disable martian logging on loopback

# What are martians and is this system allowing them?
# Martians are packets that should not exist or should not be routed. 
# They are impossible or from suspicious IP addresses
# System allows them and does not log them.

sysctl -a | grep -i panic

kernel.hardlockup_panic = 1 # if the system will panic (crash) if a hard lockup (CPU freeze) is detected.
kernel.hung_task_panic = 0 # panic if task is hung (stuck without progress)
kernel.max_rcu_stall_to_panic = 0 # panic on RCU stalls (Read-Copy-Update deadlocks).
kernel.panic = 0 # after a panic, whether to automatically reboot
kernel.panic_on_io_nmi = 0 # panic if a Non-Maskable Interrupt (NMI) occurs during I/O operations
kernel.panic_on_oops = 1 # panic on "oops" (a recoverable kernel bug).
kernel.panic_on_rcu_stall = 0 # pain on RCU stalls (deadlocks in kernel's synchronization mechanism)
kernel.panic_on_unrecovered_nmi = 0 # panic on unrecoverable non-maskable interrupts
kernel.panic_on_warn = 0 # panic on kernel warning is triggered
kernel.panic_print = 0 # additional debug info printed on panic
kernel.softlockup_panic = 0 # panic on soft lockup (CPU detected to be running but not making progress)
kernel.unknown_nmi_panic = 0 # panic if unknown NMI is received
vm.panic_on_oom = 0 # panic on an Out-Of_memory (OOM) condition

# How does this system handle panics?

sysctl -a | grep -i crypto
crypto.fips_enabled = 0
crypto.fips_name = Rocky Linux 9 - Kernel Cryptographic API
crypto.fips_version = 5.14.0-427.18.1.el9_4.x86_64
# What are the settings you see? Is FIPS enabled?
# FIPS is disabled

cat /proc/cmdline

initrd=initramfs  rd.neednet=1 root=wwinit wwinit.container=http://192.168.200.25:9873/provision/ae:1a:50:a0:f3:2d?assetkey=&uuid=ad77e27d-06f7-6886-5d48-72afc66e555b&stage=container&compress=gz wwinit.system=http://192.168.200.25:9873/provision/ae:1a:50:a0:f3:2d?assetkey=&uuid=ad77e27d-06f7-6886-5d48-72afc66e555b&stage=system&compress=gz wwinit.runtime=http://192.168.200.25:9873/provision/ae:1a:50:a0:f3:2d?assetkey=&uuid=ad77e27d-06f7-6886-5d48-72afc66e555b&stage=runtime&compress=gz init=/init wwid=ae:1a:50:a0:f3:2d quiet crashkernel=no vga=791 net.naming-scheme=v238

fips-mode-setup --check

Installation of FIPS modules is not completed.
FIPS mode is disabled.

sestatus

SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33

cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=permissive
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

What information about the security posture of the system can you see here?
Can you verify SELINUX status?
Can you verify FIPS status?

Download STIG Viewer - done
Download STIG for RHEL 9 - done
Create a checklist from the opened STIG for RHEL 9 - done

## Lab

This lab is designed to have the engineer practice securing a Linux server or service against
a set of configuration standards. These standards are sometimes called benchmarks, checklists, 
or guidelines. The engineer will be using STIG Viewer 2.18 to complete this lab.


### Network Service Configuration

Connect to hammer server.
Filter by ipv4 and see how many stigs you have.

**Examine STIG V-257957**

Rule Title
  - RHEL 9 must be configured to use TCP syncookies.

What is the problem?
  - Denial of Service can happen by an actor flooding a system with TCP SYN (connection
  requests) without completing the handshake (never sending back an ACK)
  -This normally would mean the system would allocate resources for every SYN
  it recieves, and eventually all resources would be used up.
  - Syncookies mean that the system will send back a SYN-ACK with a special
  Initial Squence Number (ISN) that encodes info. If the client is legit, then it
  will send back an ACK, to which the server will then verify the handshake and 
  allocate resources.

What is the fix?
  - Verify that RHEL 9 is configured to use syncookies.
  - ensure that `sysctl net.ipv4.tcp_syncookies` is set to 1

What type of control is being implemented?
  - Technical Preventative

Is it set properly on your system?
  - Yes

`sysctl -a | grep -i ipv4 | grep -i syncookies`

Can you remediate this finding?
  - Nothing to remediate

**Check and remediate V-257958 STIG**

Rule Title
  - RHEL 9 must ignore Internet Protocol version 4 (IPv4) Internet Control 
  Message Protocol (ICMP) redirect messages.

What is the problem?
  - ipv4 redirect messages modify the host's route table and are unauthenticated.
  - this means ilicit ICMP redirect messages could result in a man-in-the-middle attack

What is the fix?
  - check the value of accept_redirects. if 0, then redirects are not allowed.
  - sysctl net.ipv4.conf.all.accept_redirects

What type of control is being implemented?
  - Technical Preventative

Is it set properly on your system?
  - Yes

How would you go about remediating this on your system?
  - If it was not set properly, you would need to set `net.ipv4.conf.all.accept_redirects = 0`
  in a config file in `/etc/sysctl.d/`
  - This system is already set properly

**Check and remediate V-257960 STIG**

Rule Title
  - RHEL 9 must log IPv4 packets with impossible addresses.

What is the problem?
  - martian packets, spoofed packets, source-routed packets, and redirects
  could be a sign of nefarious network activity. These should be logged.

What is the fix?
  - Ensure logging of martian packets.

What type of control is being implemented?
  - Technical Detective

Is it set properly on your system?
  - No

How would you go about remediating this on your system?
  - Add the following line to the sysctl.conf file in /etc/sysctl.d/
  `net.ipv4.conf.all.log_martians = 1`
  - then run `sysctl --system` to reload conf files.

**Check and remediate V-257961 STIG**

Rule Title
  - RHEL 9 must log IPv4 packets with impossible addresses by default.

What is the problem?
  - martian packets, spoofed packets, source-routed packets, and redirects
  could be a sign of nefarious network activity. These should be logged. 

What is the fix?
  - Check if `net.ipv4.conf.default.log_martians` is set to 1
  - if not, change the setting

What type of control is being implemented?
  - Technical Detective

Is it set properly on your system?
  - No

How would you go about remediating this on your system?
  - Add the following line to the sysctl.conf file in /etc/sysctl.d/
  `net.ipv4.conf.default.log_martians = 1`
  - then run `sysctl --system` to reload conf files.

### Firewall

**Filter by firewall**

How many STIGS do you see?
  - 6

What do these STIGs appear to be trying to do?
  - Ensure that a firewall is installed and running on the system.
  - Ensure that the firewall be configured properly
  
What type of controls are they?
  - Technical Preventative

#### Firewall port exposure

Scenario:
```
Your team needs to use node_exporter with Prometheus to allow scraping of system information 
back to your network monitoring solution. You are running a firewall, so you need to expose the 
port that node_exporter runs on to the network outside of your system.
```

**Expose a network port through your firewall**

```
# Verify that your firewall is running
systemctl status firewalld


[root@hammer7 sysctl.d]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
     Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-04-23 15:47:25 MST; 20min ago

---

# Verify that your firewall has the service defined
firewall-cmd --get-services | grep -i node
ls /usr/lib/firewalld/services | grep -i node

[root@hammer7 sysctl.d]# ls /usr/lib/firewalld/services/ | grep -i node
kube-nodeport-services.xml
prometheus-node-exporter.xml

---

# Verify that the service is not currently enabled for node_exporter
firewall-cmd --list-services

[root@hammer7 sysctl.d]# firewall-cmd --list-services
cockpit dhcpv6-client ssh

---

# Examine the structure of the firewall .xml file
cat /usr/lib/firewalld/services/prometheus-node-exporter.xml

[root@hammer7 sysctl.d]# cat /usr/lib/firewalld/services/prometheus-node-exporter.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>prometheus-node-exporter</short>
  <description>The node-exporter agent for Prometheus monitoring system.</description>
  <port protocol="tcp" port="9100"/>
</service>

---

# Enable the service through your firewall
firewall-cmd --permanent --add-service=prometheus-node-exporter

[root@hammer7 sysctl.d]# firewall-cmd --permanent --add-service=prometheus-node-exporter
success

---

# Reload so the changes take effect
firewall-cmd --reload

[root@hammer7 sysctl.d]# firewall-cmd --reload
success

---

# Verify that the service is currently enabled for node_exporter
firewall-cmd --list-services

[root@hammer7 sysctl.d]# firewall-cmd --list-services
cockpit dhcpv6-client prometheus-node-exporter ssh
```

### Automate STIG remediation on a system

There are many options and the STIG remediation steps are well known. Here the learner will examine
a few ways to generate ansible and shell fixes to your system. Then one can apply
all of them, or just some of them. This is the real value of a security engineer
focused Linux engineer, the trade-off between security and productivity.

**Download and extract a STIG remediation tool**

```
cd /root
mkdir stigs
cd stigs
wget -O U_RHEL_9_V2R4_STIG_Ansible.zip https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_RHEL_9_V2R4_STIG_Ansible.zip
unzip U_RHEL_9_V2R4_STIG_Ansible.zip
mkdir ansible
cp rhel9STIG-ansible.zip ansible/
cd ansible
unzip rhel9STIG-ansible.zip

[root@hammer7 ansible]# ll
total 1536
-rw-------. 1 root root      40 Mar  7 10:49 ansible.cfg
-rw-------. 1 root root      45 Mar  7 10:49 enforce.sh
-rw-r--r--. 1 root root 1559634 Apr 23 16:14 rhel9STIG-ansible.zip
drwxrwxr-x. 3 root root      60 Mar  7 10:49 roles
-rw-------. 1 root root      63 Mar  7 10:49 site.yml
```

**Examine the default values for STIGS**

```
cd /root/stigs/ansible/roles/rhel9STIG/defaults/
vim main.yml
```

Search for a few of the STIG numbers you used earlier and see their default values.

```
# R-257957 RHEL-09-253010
rhel9STIG_stigrule_257957_Manage: True
rhel9STIG_stigrule_257957_net_ipv4_tcp_syncookies_Value: 1
rhel9STIG_stigrule_257957_net_ipv4_tcp_syncookies_File: /etc/sysctl.d/99-sysctl.conf
# R-257958 RHEL-09-253015
rhel9STIG_stigrule_257958_Manage: True
rhel9STIG_stigrule_257958_net_ipv4_conf_all_accept_redirects_Value: 0
rhel9STIG_stigrule_257958_net_ipv4_conf_all_accept_redirects_File: /etc/sysctl.d/99-sysctl.conf

# R-257960 RHEL-09-253025
rhel9STIG_stigrule_257960_Manage: True
rhel9STIG_stigrule_257960_net_ipv4_conf_all_log_martians_Value: 1
rhel9STIG_stigrule_257960_net_ipv4_conf_all_log_martians_File: /etc/sysctl.d/99-sysctl.conf
# R-257961 RHEL-09-253030
rhel9STIG_stigrule_257961_Manage: True
rhel9STIG_stigrule_257961_net_ipv4_conf_default_log_martians_Value: 1
rhel9STIG_stigrule_257961_net_ipv4_conf_default_log_martians_File: /etc/sysctl.d/99-sysctl.conf
```

**Examine the playbook to see how those are applied to a running system.**

```
vim /root/stigs/ansible/roles/rhel9STIG/tasks/main.yml


# R-257957 RHEL-09-253010
- name: stigrule_257957_net_ipv4_tcp_syncookies
  sysctl:
    name: net.ipv4.tcp_syncookies
    value: "{{ rhel9STIG_stigrule_257957_net_ipv4_tcp_syncookies_Value }}"
    sysctl_file: "{{ rhel9STIG_stigrule_257957_net_ipv4_tcp_syncookies_File }}"
  notify: sysctl_load_settings
  when:
    - rhel9STIG_stigrule_257957_Manage
# R-257958 RHEL-09-253015
- name: stigrule_257958_net_ipv4_conf_all_accept_redirects
  sysctl:
    name: net.ipv4.conf.all.accept_redirects
    value: "{{ rhel9STIG_stigrule_257958_net_ipv4_conf_all_accept_redirects_Value }}"
    sysctl_file: "{{ rhel9STIG_stigrule_257958_net_ipv4_conf_all_accept_redirects_File }}"
  notify: sysctl_load_settings
  when:
    - rhel9STIG_stigrule_257958_Manage
	

# R-257960 RHEL-09-253025
- name: stigrule_257960_net_ipv4_conf_all_log_martians
  sysctl:
    name: net.ipv4.conf.all.log_martians
    value: "{{ rhel9STIG_stigrule_257960_net_ipv4_conf_all_log_martians_Value }}"
    sysctl_file: "{{ rhel9STIG_stigrule_257960_net_ipv4_conf_all_log_martians_File }}"
  notify: sysctl_load_settings
  when:
    - rhel9STIG_stigrule_257960_Manage
# R-257961 RHEL-09-253030
- name: stigrule_257961_net_ipv4_conf_default_log_martians
  sysctl:
    name: net.ipv4.conf.default.log_martians
    value: "{{ rhel9STIG_stigrule_257961_net_ipv4_conf_default_log_martians_Value }}"
    sysctl_file: "{{ rhel9STIG_stigrule_257961_net_ipv4_conf_default_log_martians_File }}"
  notify: sysctl_load_settings
  when:
    - rhel9STIG_stigrule_257961_Manage
```

**Create an Ansible playbook from OpenSCAP**

```
dnf -y install openscap-scanner openscap-utils openscap-scanner scap-security-guide
cd /root
mkdir openscap
cd openscap

# Generate the Ansible
oscap xccdf generate fix --profile ospp --fix-type ansible /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml > draft-disa-remediate.yml

# Examine the file
vim draft-disa-remediate.yml

# Generate a BASH version
oscap xccdf generate fix --profile ospp --fix-type bash /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml > draft-disa-remediate.sh

# Examine the file
vim draf-disa-remediate.sh


[root@hammer7 openscap]# ll
total 604
-rw-r--r--. 1 root root 238130 Apr 23 16:23 draft-disa-remediate.sh
-rw-r--r--. 1 root root 376028 Apr 23 16:22 draft-disa-remediate.yml
```



