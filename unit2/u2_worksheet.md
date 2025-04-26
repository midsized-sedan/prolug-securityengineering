# Unit 2 Worksheet - Securing the Network Connection

## Resources / Important Links

(SANS Security Policy Templates)[https://www.sans.org/information-security-policy/]
(SANS List of Cheat Sheets)[https://www.sans.org/blog/the-ultimate-list-of-sans-cheat-sheets/]
(Rocky View Current Kernel Configuration)[https://docs.rockylinux.org/gemstones/core/view_kernel_conf/]
(CIQ Blog - Troubleshooting Name Resolution in Rock Linux)[https://ciq.com/blog/demystifying-and-troubleshooting-name-resolution-in-rocky-linux/]
(Diamond Model of Intrusion Analysis)[https://www.activeresponse.org/wp-content/uploads/2013/07/diamond.pdf]


## Discussion Posts

### 1

There are 401 stigs for RHEL 9. If you filter in your STIG viewer for sysctl there 
are 33 (mostly network focused), ssh - 39, and network - 58. 
Now there are some overlaps between those, but review them and answer these questions.

1. As systems engineers why are we focused on protecting the network portion of our server builds?

```
A server's network interfaces are how it communicates with anything and everything outside of itself.
Just having networking enabled greatly increases the risk of unauthrorized access or 
communication with the server. Without protecting the network portion of a server, actors 
can get access to server information, and conduct attacks, such as denial of services 
or amplification attacks.
```

2. Why is it important to understand all the possible ingress points to our servers that exist?

```
Why is it important to know all entry points to your home? If and how each window and door can
be opened or locked? If you miss any possible entry point, its a way for someone to
break in. The same goes for all ways our servers can be accessed. If we miss even one 
possible ingress point it can be exploited.
```

3. Why is it so important to understand the behaviors of processes that are connecting 
on those ingress points?

```
In order to fully protect ingress points, you need to understand how they will be used.
That knowledge will allow you to anticipate the different ways that people will try to
utilize various means of connection to the server. If an attack should occur, 
it also will help to better react and mitigate.
```

### 2

Read this: (link)[https://ciq.com/blog/demystifying-and-troubleshooting-name-resolution-in-rocky-linux/] or
similar blogs on DNS and host file configurations.

1. What is the significance of the nsswitch.conf file?

```
The man page reads "The  Name  Service  Switch  (NSS) configuration file, /etc/nsswitch.conf,
is used by the GNU C Library and certain other applications to determine the sources 
from which to obtain  name-service  information  in  a range of categories, and in what order."
This is important, as it controls how hostnames resolve on your system, which is 
vital for networking and other system services. It's also important for security considerations.
```

2. What are security problems associated with DNS and common exploits? 

```
DNS servers are vulnerable to many types of attacks, such as amplification, 
Denial of Service, or the interception of private information.
Its a result of the fact DNS was not designed with security in mind, and modern
technology makes it easier than ever to exploit. 
(clouflare blog)[https://www.cloudflare.com/learning/dns/dns-security/]

Some more examples of attacks are:
- DNS Spoofing/cache poisoning: An attack were forged DNS data is introduced into a
  DNS resolver's cache, resulting in  an incorrect IP address for a domain.
- DNS tunneling: other protocols are used to tunnel through DNS queries and responses.
- DNS hijacking: an attacker redirects queries to a different domain name server.
```


## Definitions

sysctl:
  - Tool for examining and modifying kernel parameters at runtime
  - `man 8 sysctl`

nsswitch.conf:
  - Configuration file that specifies the sources from which to obtain name-service
  information in a range of categories
  - `man 5 nsswitch.conf`

DNS:
  - A Hierarchical system for naming resources on the internet, translating 
  human-friendly domain names to IP addresses.
  - (Domain Names RFC 1034)[https://datatracker.ietf.org/doc/html/rfc1034]

Openscap:
  - An open-source framework that provides a set of tools for compliance monitoring and
  vunerability management.
  - (OpenSCAP website)[https://www.open-scap.org/]

CIS Benchmarks:
  - Security configuration guides developed by the Center for Internet Security to
  establish best practices for securing systems and applications.
  - (CIS website)[https://www.cisecurity.org/cis-benchmarks]

ss/netstat:
  - Command line tools used to display network connections, routing tables, interface
  statistics, masquerade connections, and multicast memberships
  - `man 8 ss`, `man 8 netstat`

tcpdump:
  - A command line packet analyzer, allows the user to display TCP, UDP, and other
  packets being transmitted or received over a network
  - `man 8 tcpdump`

ngrep:
  - A network packet analyzer that uses regular expresions to match against 
  data payloads of packets
  - `man 8 ngrep`

## Digging Deeper

1. See if you can find any DNS exploits that have been used and written up in the diamond model
of intrusion analysis format. If you can , what are the primary actors and actions that made up the attack?

## Reflection Questions

1. What questions do you still have about this week?

Honestly, I'm just overall confused and not sure what to ask.

2. How are you going to use what you've learned in your current role?

Nothing yet.
