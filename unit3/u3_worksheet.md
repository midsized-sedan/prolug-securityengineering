# Unit 3 Worksheet - User Access and System Integration

## Resources / Important Links

(SANS Security Policy Templates)[https://www.sans.org/information-security-policy/]
(SANS list of cheat sheets)[https://www.sans.org/blog/the-ultimate-list-of-sans-cheat-sheets/]
(Rocky PAM Authentication Modules)[https://docs.rockylinux.org/guides/security/pam/]
(Rocky Active Directory Authentication)[https://docs.rockylinux.org/guides/security/authentication/active_directory_authentication/]
(Rocky User Management)[https://docs.rockylinux.org/books/admin_guide/06-users/]

## Discussions Posts

### 1 

There are 16 STIGs that involve PAM for RHEL 9. Read the guide from Rocky Linux
here: [https://docs.rockylinux.org/guides/security/pam/]

1. What are the mechanisms and how do they affect PAM functionality?
  - review `/etc/pam.d/sshd` on a Linux system.
  - What is happening in that file relative to these functionalities?

```md
The Mechanisms are:
  - auth
    - handles authentication of the requester and establishes the rights of the account
    - usually authenticates with a password either stored in a database or using an authentication server
    - establishes account settings: uid, gid, groups, and resource limits
  - account
    - checks that the requested account is available
    - relates to account availability for reasons other than authentication
  - session
    - session setup and termination
  - password
    - used to modify the authentication token associated with an account (expiration or change)
    - change the token and possibly verifies that the token is robust enough or that it has not already been used.

The Control Indicators are:
  - required
    - successful completion of all required modules is necessary.
    - If Pass: the rest of the chain is executed. The request is allowed unless other modules fail.
    - If Fail: the rest of the chain is executed. Request is rejected.
  - requisite
    - successful completion of all requisite modules is necessary.
    - If Pass: the rest of the chain is executed. The request is allowed unless other modules fail.
    - If Fail: the request is immediately rejected.
  - sufficient
    - can be used to let a user in "early" under certain conditions
    - If Pass: the authentication request is immediately allowed if no previous modules failed.
    - If Failed: the module is ignored. The rest of the chain is executed.
  - optional
    - module is executed but the result is ignored.

On my system, the /etc/pam.d/sshd file includes from system-remote-login, which includes from system-login.

These mechanisms and control indicators are used in conjunction to specify
which PAM modules are to be used for the different mechanisms and whether or
not the modules return value is considered for accepting or rejecting a request.
```

```
cat /etc/pam.d/system-login
#%PAM-1.0

auth       required   pam_shells.so
auth       requisite  pam_nologin.so
auth       include    system-auth

account    required   pam_access.so
account    required   pam_nologin.so
account    include    system-auth

password   include    system-auth

session    optional   pam_loginuid.so
session    optional   pam_keyinit.so       force revoke
session    include    system-auth
session    optional   pam_motd.so
session    optional   pam_mail.so          dir=/var/spool/mail standard quiet
session    optional   pam_umask.so
-session   optional   pam_systemd.so
session    required   pam_env.so

```

2. What are the common PAM modules?
  - review `/etc/pam.d/sshd` on a linux system
  - What is happening in that file relative to these functionalities?

```md
Common PAM modules:
  - pam_unix
    - uses traditional UNIX authentication (i.e. /etc/passwd)
  - pam_ldap
    - authenticates users via an LDAP directory
  - pam_wheel
    - restricts access to su to users in the wheel group
  - pam_cracklib
    - enforces password strength, like length and complexity
  - pam_console
    - grants special privileges to the first user logged in on the console
    (such as mounting devices)
  - pam_tally
    - tracks failed login attempts
  - pam_security
  - pam_nologin
    - denies login if /etc/nologin exists. useful during maintenance
  - pam_limits
    - enforces user limits from /etc/security/limits.conf
  - pam_time
    - limits login access based on time and day rules from /etc/security/time.conf
  - pam_access
    - restricts login access based on rules in /etc/security/access.conf


With regards to the /etc/pam.d/sshd file, which includes from the
system-remote-login file, which includes from the system-login file,
modules such as these are being applied to different mechanisms
with control indicators on whether they are necessary for authentication or not.
```
3. Look for a blog post or article about PAM that discusses real world application.
Post it here and give us a quick synopsis.

```md
I dont think this one has been taken yet, sorry if it has.
[https://www.miniorange.com/blog/pluggable-authentication-modules-pam/]

The article goes into a lot of detail regarding what is PAM and how it works.
There is one heading though that talks about real world application.

It mentions PAM enhances security and improves user management and it gave
3 examples.
1. Centralized Authentication with LDAP/Kerberos
  - great for when you have to manage multiple servers and users.
2. Enforcing Multi-Factor Authentication
  - MFA provides extra security, so that if a users password is compromised,
  an additional authentication step is required to gain access.
3. Preventing Brute-Force attacks with Failed Login Attempts
  - Preventing login after many failed attempts mitigates brute-force attacks.
```

RESOURCES

(Michael Pesa Prolug)[https://michaelpesa.com/posts/creating-custom-authselect-profiles/]
(scaleway PAm with ftp)[https://www.scaleway.com/en/blog/understand-pam/]

### 2 

Read about active directory (or LDAP) configuratoins of Linux via sshd here:
[https://docs.rockylinux.org/guides/security/authentication/active_directory_authentication]

1. Why do we not want to just use local authentication in Linux? Or really any system?

```
If we were to stick with local linux authentication, then every single machine
we build will need to be configured with all users, groups, and access privileges
one by one. This not only is just a big headache, even with automation tools,
but is very prone to error. Having a central place for all authentication
related concerns allows all systems to have the same authentication rules.
```

2. There are 4 SSSD STIGs.
  - What are they?
  - What do they seek to do with the system?

```
I found 6 STIGs, not 4.

V-258019
  - RHEL 9 must be able to initiate directly a session lock for all connection types 
  using smart card when the smart card is removed.
  - A smart card is a physical medium that contains someones certificate
  and private key.
  - The goal of this STIG is to make sure that when a user leaves their
  computer, the computer locks.
V-258122
  - RHEL 9 must enable certificate based smart card authentication.
  - The goal of this STIG is to require MFA to access the machine, specifically
  with the use of a smart card, such as the DOD Common Access Card (CAC)
V-258123
  - RHEL 9 must implement certificate status checking for multifactor authentication.
  - The goal of this STIG is to ensure the validity of certificates before
  granting access to a system.
V-258131
  - RHEL 9, for PKI-based authentication, must validate certificates by constructing 
  a certification path (which includes status information) to an accepted trust anchor.
  - The goal of this STIG is to ensure that certificates are valid and trusted by
  means of a top level secure source.
V-258132
  - RHEL 9 must map the authenticated identity to the user or group account for 
  PKI-based authentication.
  - The goal of this STIG is to ensure that when a user authenticates with
  a certificate, the system accurately gives proper access controls and auditing.
V-258133
  - RHEL 9 must prohibit the use of cached authenticators after one day.
  - The goal of this STIG is to limit how long credentials are cached. If cached
  credentials are out of date, then the validity of the authentication is in 
  question.
```


1.
```
If we were to stick with local linux authentication, then every single machine we build will need to be configured with all users, groups, and access privileges one by one. This not only is just a big headache, even with automation tools, but is very prone to error. Having a central place for all authentication related concerns allows all systems to have the same authentication rules.
```

2.
```
V-258019
  - RHEL 9 must be able to initiate directly a session lock for all connection types 
  using smart card when the smart card is removed.
  - A smart card is a physical medium that contains someones certificate
  and private key.
  - The goal of this STIG is to make sure that when a user leaves their
  computer, the computer locks.
V-258122
  - RHEL 9 must enable certificate based smart card authentication.
  - The goal of this STIG is to require MFA to access the machine, specifically with the use of a smart card, such as the DOD Common Access Card (CAC)
V-258123
  - RHEL 9 must implement certificate status checking for multifactor authentication.
  - The goal of this STIG is to ensure the validity of certificates before granting access to a system.
V-258131
  - RHEL 9, for PKI-based authentication, must validate certificates by constructing a certification path (which includes status information) to an accepted trust anchor.
  - The goal of this STIG is to ensure that certificates are valid and trusted by means of a top level secure source.
V-258132
  - RHEL 9 must map the authenticated identity to the user or group account for PKI-based authentication.
  - The goal of this STIG is to ensure that when a user authenticates with a certificate, the system accurately gives proper access controls and auditing.
V-258133
  - RHEL 9 must prohibit the use of cached authenticators after one day.
  - The goal of this STIG is to limit how long credentials are cached. If cached credentials are out of date, then the validity of the authentication is in question.
```




## Definitions

PAM:
  - PAM is a library suite that allows a Linux system administrator to configure
  methods to authenticate users. It provides a flexible and centralized way to
  switch authentication methods for secured applications using configuration files
  instead of changing application code.
  - (Rocky 9 PAM guide)[https://docs.rockylinux.org/guides/security/pam/] 

AD:
  - Microsoft's Active Directory
  - default authentication system for Windows systems and external, LDAP-connected servcies.
  - Allows configuration of users and groups, access control, permissions, auto-mounting,
  and more.
  - (Rocky 9 AD Authentication)[https://docs.rockylinux.org/guides/security/authentication/active_directory_authentication/?h=active+directory]

LDAP:
  - Open, vendor-neutral, industry standard application protocol for accessing and
  maintaining distributed directory information services over an IP network.
  - (RFC 4511)[https://datatracker.ietf.org/doc/html/rfc4511]

SSSD:
  - System Security Services Daemon is a system service to access remote dirctories
  and authentication mechanisms.
  - (RHEL SSSD)[https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_authentication_and_authorization_in_rhel/understanding-sssd-and-its-benefits_configuring-authentication-and-authorization-in-rhel#understanding-SSSD-and-its-benefits_configuring-authentication-and-authorization-in-rhel]

oddjob:
  - D-Bus service that performs simple tasks on behalf of client applications.
  A common use is `obbjob-mkhomedir` module, which auto creates home directories
  for users upon their first login.
  - (RHEL)[https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/system-level_authentication_guide/authconfig-homedirs]

krb5:
  - Kerberos 5 is a network authentication protocol designed to provide strong
  authentication for client/server applications using secret-key cryptography.
  - (Kerberos MIT doc)[https://web.mit.edu/kerberos/krb5-latest/doc/index.html]

realm/realmd:
  - system service that simplifies the process of joining a linux system to a domain,
  such as Active Directory or Identity Management (IdM). It automates the discovery
  and enrollment processes, configuring necessary services like SSSD and Kerberos.
  - (RHEL)[https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/windows_integration_guide/ch-configuring_authentication]

wheel (system group in RHEL):
  - special user group traditionally used to control access to administrative
  (superuser) privileges. Users who are part of the wheel group are typically
  allowed to use `sudo` or `su` to execute commands as the root user.
  - (RHEL)[https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/managing-sudo-access_configuring-basic-system-settings]


## Digger Deeper

1. How does `/etc/security/access.conf` come into play with pam_access? Read up
on it here: [https://man7.org/linux/man-pages/man8/pam_access.8.html]
  - Can you find any other good resources?
  - What is the structure of the access.conf file directives?
2. What other important user access or user management information do you learn
by reading this? [https://docs.rockylinux.org/books/admin_guide/06-users/]
  - What is the contents of the `/etc/login.defs` file? Why do you care?

## Reflection Questions

1. What questions do you still have about this week?

2. How are you going to use what you've learned in your current role?

