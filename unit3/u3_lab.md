# Unit 3 Lab - User Access and System Integreation

## Required Materials

Putty or other connection tool Lab Server
Root or sudo command access

STIG Viewer 2.18 (download from https://public.cyber.mil/stigs/downloads/ )
Download the STIG for RHEL 9 and the import it into your STIG viewer
Create a checklist from the opened STIG for RHEL 9


### Exercises

```
ls -l /etc/pam.d/
# What are the permissions and names of files? Can everyone read them?

-rw-r--r--. 1 root root 232 Apr 15  2024 config-util
-rw-r--r--. 1 root root 701 Apr 15  2024 fingerprint-auth
-rw-r--r--. 1 root root 676 Apr 20  2024 login
-rw-r--r--. 1 root root 154 Apr 15  2024 other
-rw-r--r--. 1 root root 168 May 14  2022 passwd
-rw-r--r--. 1 root root 774 Apr 23 16:26 password-auth
-rw-r--r--. 1 root root 155 Apr 21  2023 polkit-1
-rw-r--r--. 1 root root 398 Apr 15  2024 postlogin
-rw-r--r--. 1 root root 640 Apr 20  2024 remote
-rw-r--r--. 1 root root 143 Apr 20  2024 runuser
-rw-r--r--. 1 root root 138 Apr 20  2024 runuser-l
-rw-r--r--. 1 root root 743 Apr 15  2024 smartcard-auth
-rw-r--r--. 1 root root 727 Apr 17  2024 sshd
-rw-r--r--. 1 root root 566 Apr 20  2024 su
-rw-r--r--. 1 root root 137 Apr 20  2024 su-l
-rw-r--r--. 1 root root 774 Apr 23 16:26 system-auth
-rw-r--r--. 1 root root 414 Apr  7  2024 systemd-user
-rw-r--r--. 1 root root  84 Oct 31  2023 vlock

Everyone can read.


cat /etc/pam.d/sshd

# What information do you see in this file?
# Does any of it look familiar to you?

[root@hammer7 ~]# cat /etc/pam.d/sshd
#%PAM-1.0
auth       substack     password-auth
auth       include      postlogin
account    required     pam_sepermit.so
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open env_params
session    required     pam_namespace.so
session    optional     pam_keyinit.so force revoke
session    optional     pam_motd.so
session    include      password-auth
session    include      postlogin
```

## Pre-Lab Warm-Up

Create a checklist for RHEL 9 - Done


## Lab

This lab is designed to have the engineer practice securing a Linux server or service 
against a set of configuration standards. These standards are sometimes called benchmarks, 
checklists, or guidelines. The engineer will be using STIG Viewer 2.18 to complete this lab.


### Pam Configuration

Connect to a hammer server
Filter by pam and see how many STIGS you have. (Why is it really only 16?)


### Examine STIG V-257986

Rule Title
	- RHEL 9 must enable the Pluggable Authentication Module (PAM) interface for SSHD.

What is the problem?
	- PAM allows for better authentication. Not using PAM can lead to vulnerabilities.

What is the fix?
	- Enable PAM for sshd.

What type of control is being implemented?
	- Technical Preventative

Is it set properly on your system?
	- grep -i pam /etc/ssh/sshd_config
	- No

Can you remediate this finding?
	- in `/etc/ssh/sshd_config`, change the `UsePAM no` line to `UsePAM yes`
	- then restart ssh daemon

### Examine STIG V-258055

Rule Title
	- RHEL 9 must automatically lock the root account until the root account is 
	released by an administrator when three unsuccessful logon attempts occur 
	during a 15-minute time period.

What is the problem?
	- If the account doesn't limit failed log in attempts, then access can be gained
	with password guessing

What is the fix?
	- enable limits on the root account for failed login attempts so that
	the root account is locked after 3 failed attempts in a 15 min period.
	- And admin will need to come in and unlock the account.
	
Are there any major implications to think about with this change on your system?
Why or why not?
	- TODO

What type of control is being implemented?
	- Technical Preventative

Is it set properly on your system?
	- No

How would you go about remediating this on your system?
	- `authselect enable-feature with-faillock`
	- `/etc/security/faillock.conf` uncomment the `even_deny_root` line


### Examine STIG V-258098

Rule Title
	- RHEL 9 must ensure the password complexity module is enabled in the system-auth file.

What is the problem?
	- If password complexity insn't enforced, then accounts can be made with
	very easy passwords.

What is the fix?
	- enable the PAM password complexity module

What type of control is being implemented?
	- Technical Preventative

Is it set properly on your system?
	- Yes

### Filter by "password complexity"

How many are there?
	- 14

What are the password complexity rules?
	- limits on repeating characters
	- passwords must differ from previous passwords by certain number of characters
	and character classes
	- use of alphanumeric and special characters
	- disallow dictionary words
	- minimum of 15 characters

Are there any you haven't seen before?
	- the ones about similar character classes is new.

### Filter by sssd

How many STIGS do you see?
	- 6

What do these STIGs appear to be trying to do?
	- ensure that certificates are properly authenticated for access to the machine

What type of controls are they?
	- Technical Preventative

### OpenLDAP Setup

You will likely not build an LDAP server in a real world environment. 
We are doing it for understanding and ability to complete the lab. 
In a normal corporate environment this is likely Active Directory.

To simplify some of the typing in this lab, there is a file located at 
/lab_work/identity_and_access_management.tar.gz that you can pull down to your system with the correct .ldif files.

```
[root@hammer1 ~]# cp /lab_work/identity_and_access_management.tar.gz .
[root@hammer1 ~]# tar -xzvf identity_and_access_management.tar.gz

```

1. Stop the warefulf client

```
[root@hammer1 ~]# systemctl stop wwclient
```

2. Edit your /etc/hosts file

```
[root@hammer1 ~]# vi /etc/hosts

e.g.
192.168.200.151 hammer1 hammer1-default ldap.prolug.lan ldap
```

3. Setup dnf repo

```
[root@hammer1 ~]# dnf config-manager --set-enabled plus
[root@hammer1 ~]# dnf repolist
[root@hammer1 ~]# dnf -y install openldap-servers openldap-clients openldap

Done
```

4. Start slapd systemctl

```
[root@hammer1 ~]# systemctl start slapd
[root@hammer1 ~]# ss -ntulp | grep slapd

[root@hammer7 ~]# ss -ntulp | grep slapd
tcp   LISTEN 0      2048         0.0.0.0:636        0.0.0.0:*    users:(("slapd",pid=12428,fd=9))   
tcp   LISTEN 0      2048         0.0.0.0:389        0.0.0.0:*    users:(("slapd",pid=12428,fd=7))   
tcp   LISTEN 0      2048            [::]:636           [::]:*    users:(("slapd",pid=12428,fd=10))  
tcp   LISTEN 0      2048            [::]:389           [::]:*    users:(("slapd",pid=12428,fd=8))
```

5. Allow ldap through the firewall

```
[root@hammer1 ~]# firewall-cmd --add-service={ldap,ldaps} --permanent
[root@hammer1 ~]# firewall-cmd --reload
[root@hammer1 ~]# firewall-cmd --list-all


[root@hammer7 ~]# firewall-cmd --add-service={ldap,ldaps} --permanent
success
[root@hammer7 ~]# firewall-cmd --reload
success
[root@hammer7 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: cockpit dhcpv6-client ldap ldaps ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

6. Generate a password (Our example uses testpassword) This will return a salted
SSHA password. Save this password and salted hash for later input

```
[root@hammer1 ~]# slappasswd
```

```
password: testpassword
{SSHA}M/sgAUNgk25Ng1WvNNcUhFjZlGOvG7SE
```

7. Change the password

```
[root@hammer1 ~]# vi changerootpass.ldif


dn: olcDatabase={0}config,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}vKobSZO1HDGxp2OElzli/xfAzY4jSDMZ

[root@hammer1 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f changerootpass.ldif

[root@hammer7 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f changerootpass.ldiff
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={0}config,cn=config"
```

8. Generate basic schemas

```
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif

[root@hammer7 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldiff
/etc/openldap/schema/cosine.ldiff: No such file or directory
[root@hammer7 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
adding new entry "cn=cosine,cn=schema,cn=config"

[root@hammer7 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
adding new entry "cn=nis,cn=schema,cn=config"

[root@hammer7 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
adding new entry "cn=inetorgperson,cn=schema,cn=config"
```

9. Set up the domain (USE THE PASSWORD YOU GENERATED EARLIER)

```
[root@hammer1 ~]# vi setdomain.ldif
```

```
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
read by dn.base="cn=Manager,dc=prolug,dc=lan" read by * none

dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=prolug,dc=lan

dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=Manager,dc=prolug,dc=lan

dn: olcDatabase={2}mdb,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}s4x6uAxcAPZN/4e3pGnU7UEIiADY0/Ob

dn: olcDatabase={2}mdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {0}to attrs=userPassword,shadowLastChange by
dn="cn=Manager,dc=prolug,dc=lan" write by anonymous auth by self write by * none
olcAccess: {1}to dn.base="" by * read
olcAccess: {2}to * by dn="cn=Manager,dc=prolug,dc=lan" write by * read
```

DONE

10. Run it

```
[root@hammer1 ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f setdomain.ldif
```

```
[root@hammer7 ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f ./setdomain.ldif
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={1}monitor,cn=config"

modifying entry "olcDatabase={2}mdb,cn=config"

modifying entry "olcDatabase={2}mdb,cn=config"

modifying entry "olcDatabase={2}mdb,cn=config"
ldap_modify: Type or value exists (20)
        additional info: modify/add: olcRootPW: value #0 already exists
```

11. Search and verify the domain is working.

```
[root@hammer1 ~]# ldapsearch -H ldap:// -x -s base -b "" -LLL "namingContexts"
```

```
[root@hammer7 ~]# ldapsearch -H ldap:// -x -s base -b "" -LLL "namingContexts"
dn:
namingContexts: dc=prolug,dc=lan
```

12. Add the base group and organization.

```
[root@hammer1 ~]# vi addou.ldif

dn: dc=prolug,dc=lan
objectClass: top
objectClass: dcObject
objectclass: organization
o: My prolug Organisation
dc: prolug

dn: cn=Manager,dc=prolug,dc=lan
objectClass: organizationalRole
cn: Manager
description: OpenLDAP Manager

dn: ou=People,dc=prolug,dc=lan
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=prolug,dc=lan
objectClass: organizationalUnit
ou: Group


[root@hammer1 ~]# ldapadd -x -D cn=Manager,dc=prolug,dc=lan -W -f addou.ldif
```

```
[root@hammer7 ~]# ldapadd -x -D cn=Manager,dc=prolug,dc=lan -W -f addou.ldif
Enter LDAP Password:
adding new entry "dc=prolug,dc=lan"

adding new entry "cn=Manager,dc=prolug,dc=lan"

adding new entry "ou=People,dc=prolug,dc=lan"

adding new entry "ou=Group,dc=prolug,dc=lan"
```

13. Verifying

```
[root@hammer1 ~]# ldapsearch -H ldap:// -x -s base -b "" -LLL "+"
[root@hammer1 ~]# ldapsearch -x -b "dc=prolug,dc=lan" ou


[root@hammer7 ~]# ldapsearch -H ldap:// -x -s base -b "" -LLL "+"
dn:
structuralObjectClass: OpenLDAProotDSE
configContext: cn=config
monitorContext: cn=Monitor
namingContexts: dc=prolug,dc=lan
supportedControl: 2.16.840.1.113730.3.4.18
supportedControl: 2.16.840.1.113730.3.4.2
supportedControl: 1.3.6.1.4.1.4203.1.10.1
supportedControl: 1.3.6.1.1.22
supportedControl: 1.2.840.113556.1.4.319
supportedControl: 1.2.826.0.1.3344810.2.3
supportedControl: 1.3.6.1.1.13.2
supportedControl: 1.3.6.1.1.13.1
supportedControl: 1.3.6.1.1.12
supportedExtension: 1.3.6.1.4.1.4203.1.11.1
supportedExtension: 1.3.6.1.4.1.4203.1.11.3
supportedExtension: 1.3.6.1.1.8
supportedExtension: 1.3.6.1.1.21.3
supportedExtension: 1.3.6.1.1.21.1
supportedFeatures: 1.3.6.1.1.14
supportedFeatures: 1.3.6.1.4.1.4203.1.5.1
supportedFeatures: 1.3.6.1.4.1.4203.1.5.2
supportedFeatures: 1.3.6.1.4.1.4203.1.5.3
supportedFeatures: 1.3.6.1.4.1.4203.1.5.4
supportedFeatures: 1.3.6.1.4.1.4203.1.5.5
supportedLDAPVersion: 3
entryDN:
subschemaSubentry: cn=Subschema

[root@hammer7 ~]# ldapsearch -x -b "dc=prolug,dc=lan" ou
# extended LDIF
#
# LDAPv3
# base <dc=prolug,dc=lan> with scope subtree
# filter: (objectclass=*)
# requesting: ou
#

# prolug.lan
dn: dc=prolug,dc=lan

# Manager, prolug.lan
dn: cn=Manager,dc=prolug,dc=lan

# People, prolug.lan
dn: ou=People,dc=prolug,dc=lan
ou: People

# Group, prolug.lan
dn: ou=Group,dc=prolug,dc=lan
ou: Group

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 4
```

14. Add a user

Generate a password (use testuser124)
{SSHA}VFqVLpsSiNtcNGHospIyuCjrbVWZtzuz

```
[root@hammer1 ~]# slappasswd

[root@hammer1 ~]# vi adduser.ldif

dn: uid=testuser,ou=People,dc=prolug,dc=lan
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
cn: testuser
sn: temp
userPassword: {SSHA}yb6e0ICSdlZaMef3zizvysEzXRGozQOK
loginShell: /bin/bash
uidNumber: 15000
gidNumber: 15000
homeDirectory: /home/testuser
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0

dn: cn=testuser,ou=Group,dc=prolug,dc=lan
objectClass: posixGroup
cn: testuser
gidNumber: 15000
memberUid: testuser

[root@hammer1 ~]# ldapadd -x -D cn=Manager,dc=prolug,dc=lan -W -f adduser.ldif


[root@hammer7 ~]# ldapadd -x -D cn=Manager,dc=prolug,dc=lan -W -f adduser.ldif
Enter LDAP Password:
adding new entry "uid=testuser,ou=People,dc=prolug,dc=lan"

adding new entry "cn=testuser,ou=Group,dc=prolug,dc=lan"
```

16. Verify that your user is in the system.

```
[root@hammer1 ~]# ldapsearch -x -b "ou=People,dc=prolug,dc=lan"


[root@hammer7 ~]# ldapsearch -x -b "ou=People,dc=prolug,dc=lan"
# extended LDIF
#
# LDAPv3
# base <ou=People,dc=prolug,dc=lan> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# People, prolug.lan
dn: ou=People,dc=prolug,dc=lan
objectClass: organizationalUnit
ou: People

# testuser, People, prolug.lan
dn: uid=testuser,ou=People,dc=prolug,dc=lan
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
cn: testuser
sn: temp
userPassword:: e1NTSEF9VkZxVkxwc1NpTnRjTkdIb3NwSXl1Q2pyYlZXWnR6dXo=
loginShell: /bin/bash
uidNumber: 15000
gidNumber: 15000
homeDirectory: /home/testuser
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0
uid: testuser

# search result
search: 2
result: 0 Success

# numResponses: 3
# numEntries: 2
```

17. Secure the system with TLS (accept all defaults)

```
[root@hammer1 ~]# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/pki/tls/ldapserver.key -out /etc/pki/tls/ldapserver.crt
[root@hammer1 ~]# chown ldap:ldap /etc/pki/tls/{ldapserver.crt,ldapserver.key}
```

```
[root@hammer1 ~]# ls -l /etc/pki/tls/ldap*


[root@hammer7 ~]# ls -l /etc/pki/tls/ldap*
-rw-r--r--. 1 ldap ldap 1237 Apr 24 17:56 /etc/pki/tls/ldapserver.crt
-rw-------. 1 ldap ldap 1704 Apr 24 17:55 /etc/pki/tls/ldapserver.key
```

18. Fix the /etc/openldap/ldap.conf to allow for certs

```
[root@hammer1 ~]# vi /etc/openldap/ldap.conf


#
# LDAP Defaults
#

# See ldap.conf(5) for details
# This file should be world readable but not world writable.

#BASE dc=example,dc=com
#URI ldap://ldap.example.com ldap://ldap-master.example.com:666

#SIZELIMIT 12
#TIMELIMIT 15
#DEREF never

# When no CA certificates are specified the Shared System Certificates
# are in use. In order to have these available along with the ones specified # by TLS_CACERTDIR one has to include them explicitly:

TLS_CACERT /etc/pki/tls/ldapserver.crt
TLS_REQCERT never

# System-wide Crypto Policies provide up to date cipher suite which should
# be used unless one needs a finer grinded selection of ciphers. Hence, the
# PROFILE=SYSTEM value represents the default behavior which is in place
# when no explicit setting is used. (see openssl-ciphers(1) for more info)
#TLS_CIPHER_SUITE PROFILE=SYSTEM

# Turning this off breaks GSSAPI used with krb5 when rdns = false
SASL_NOCANON on



[root@hammer1 ~]# systemctl restart slapd
```

DONE


### SSSD Configuration and Realmd join to LDAP

SSSD can connect a server to a trusted LDAP system and authenticate users for 
access to local resources. You will likely do this during your career and it is a 
valuable skill to work with.

1. Install ssd, configure, and validate that the user is seen by the system

```
[root@hammer1 ~]# dnf install openldap-clients sssd sssd-ldap oddjob-mkhomedir authselect
[root@hammer1 ~]# authselect select sssd with-mkhomedir --force
[root@hammer1 ~]# systemctl enable --now oddjobd.service
[root@hammer1 ~]# systemctl status oddjobd.service

[root@hammer7 ~]# systemctl status oddjobd.service
● oddjobd.service - privileged operations for unprivileged applications
     Loaded: loaded (/usr/lib/systemd/system/oddjobd.service; enabled; preset: disabled)
     Active: active (running) since Thu 2025-04-24 17:59:49 MST; 3s ago
```

2. Uncomment and fix the lines in /etc/openldap/ldap.conf

```
[root@hammer1 ~]# vi /etc/openldap/ldap.conf

BASE dc=prolug,dc=lan
URI ldap://ldap.ldap.lan/
```

DONE

3. edit the sssd.conf file

```
[root@hammer1 ~]# vi /etc/sssd/sssd.conf

[domain/default]
id_provider = ldap
autofs_provider = ldap
auth_provider = ldap
chpass_provider = ldap
ldap_uri = ldap://ldap.prolug.lan/
ldap_search_base = dc=prolug,dc=lan
#ldap_id_use_start_tls = True
#ldap_tls_cacertdir = /etc/openldap/certs
cache_credentials = True
#ldap_tls_reqcert = allow

[sssd]
services = nss, pam, autofs
domains = default

[nss]
homedir_substring = /home


[root@hammer1 ~]# chmod 0600 /etc/sssd/sssd.conf
[root@hammer1 ~]# systemctl start sssd
[root@hammer1 ~]# systemctl status sssd

[root@hammer7 ~]# systemctl status sssd
● sssd.service - System Security Services Daemon
     Loaded: loaded (/usr/lib/systemd/system/sssd.service; enabled; preset: enabled)
     Active: active (running) since Thu 2025-04-24 18:01:38 MST; 4s ago

```

4. Validate that the user can be seen

```
[root@hammer1 ~]# id testuser


[root@hammer7 ~]# id testuser
uid=15000(testuser) gid=15000(testuser) groups=15000(testuser)
```

