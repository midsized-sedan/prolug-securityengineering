# Unit 4 Worksheet - Bastions and Jailing Users

## Resources

https://www.sans.org/information-security-policy/
https://www.sans.org/blog/the-ultimate-list-of-sans-cheat-sheets/
https://aws.amazon.com/search/?searchQuery=air%20gapped#facet_type=blogs&limit=25&page=1&sortResults=custom_20160114%20desc
https://aws.amazon.com/blogs/security/tag/bastion-host/

## Discussion Posts

### 1

Review some of the blogs here:

https://aws.amazon.com/search/?searchQuery=air%20gapped#facet_type=blogs&limit=25&page=1&sortResults=custom_20160114%20desc
https://aws.amazon.com/blogs/security/tag/bastion-host/

Or find some on your own about air-gapped systems

1. What seems to be the theme of air-gapped systems?

```

```

2. What seems to be their purpose?

```

```

3. If you use google, or an AI, what are some of the common themes that come up
when asked about air-gapped or bastion systems?

```

```

### 2

Do a Google or AI search of topics around jailing a user or processes in Linux

1. Can you enumerate the methods of jailing users?

```

```

2. Can you think of when you've been jailed as a Linux user?
If not, can you think of the useful ways to use a jail?

```

```


## Definitions

Air-gapped
	- An air-gapped system is physically isolated from unsecured networks, such
	as the public internet. This isolation ensures that the system cannot be accessed
	remotedly, enhancing its security. Air-gapped systems are often used in sensitive
	environments like military networks or critical infrastructure.
	- [https://en.wikipedia.org/wiki/Air_gap_%28networking%29]
	- [https://www.techtarget.com/whatis/definition/air-gapping]

Bastion
	- A bastion host is a special-purpose computer on a network designed to 
	withstand attacks. It typically hosts a single application, such as a proxy
	server, and is placed on the network's perimeter to provide access to a private
	network from an external network, like the internet.
	- https://en.wikipedia.org/wiki/Bastion_host

Jailed process
	- A jailed process refers to a process that operates within a restricted environment,
	limiting its access to the broader system. This concept is implemented in various
	operating systems to enhance security by isolating processes.
	- https://en.wikipedia.org/wiki/FreeBSD_jail

Isolation
	- In cybersecurity, isolation refers to the practice of separating systems
	or processes to prevent the spread of threats. This can involve techniques like
	sandboxing, where applications run in a controlled environment to mitigate
	potential risks.
	- https://csrc.nist.gov/glossary/term/isolation
	- https://cyberpedia.reasonlabs.com/EN/isolation.html
	
Ingress
	- Ingress refers to trffic that enterse a network from an external source.
	Managing ingress traffic is crucial for protecting networks from unauthorized
	access and potential threats.
	- https://aviatrix.com/learn-center/cloud-network-security/egress-and-ingress/

Egress
	- Egress is the opposite of ingress; it pertains to traffic that exits a network
	to an external destination. Monitoring egress traffic helps in detecting data
	exfiltration and other unauthroized data transfers.
	- https://www.fortinet.com/resources/cyberglossary/data-egress

Exfiltration
	- Data exfiltration involves the unauthroized transfer of data from a system
	to an external entity. It's a significatn security concern, often associated with 
	cyberattacks aiming to steal sensitive information.
	- https://www.fortinet.com/resources/cyberglossary/data-exfiltration
	- https://www.digitalguardian.com/blog/what-data-exfiltration

Cgroups
	- Control groups (cgroups) are a Linux kernel feature that allows the allocation
	of system resources - like CPU time, memory, and network bandwith - among
	user-defined groups of processes. This enables fine-grained control over
	resource usage.
	- https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/6/html/resource_management_guide/ch01

Namespaces
	- Name spaces in Linux provide isolation of system resources for processes. They
	are fundamental to containerization, allowing multiple containers to run on 
	the same host without interfering with each other.
	- Mount (mnt): Isolates the set of filesystem mount points, so processes in
	different namespaces can have different views of the filesystem.
	- PID: Isolates the process ID number space, allowing processes in different
	namespaces to have the same PID.
	- IPC: Isolated interprocess communication resources, such as message queues
	and semaphores.
	- UTS: Isolates hostname and domain name, allowing each namespace to have its
	own hostname and domain name.
	- https://www.redhat.com/en/blog/7-linux-namespaces
	- https://securitylabs.datadoghq.com/articles/container-security-fundamentals-part-2/
	
## Digging Deeper

1. While this isn't, strictly speaking, an automation course there is some value in 
looking at automation of the bastion deployements. Check out this ansible code:
https://github.com/het-tanis/stream_setup/blob/master/roles/bastion_deploy/tasks/main.yml
	- Does the setup make sense to you with our deployment?
	- What can improve and make this better?

2. Find a blog or github where someone else deploys a bastion. Compare it to our process.

3. Knowing what you now know about bastions, jails, and air-gapped systems, reflect
on the first 3 weeks, all the STIGs you've reviewed and touched. Do any of them seem
moot, or less necessary if applied in an air-gapped environment?
	- Does you answer change if you read about Zero Trust and know how much of a
	hot topic that is in the security world now?
		- Why or why not?
		

4. Think of a Linux system where you would like to deploy a bastion (if you cannot
think of one, use ProLUG lab). Draw out how you think the system works in excalidraw.


## Reflection Questions

1. Does it matter if the user knows that they are jailed? Why or why not?

2. What questions do you still have about this week?

3. How are you going to use what you've learned in your current role?

