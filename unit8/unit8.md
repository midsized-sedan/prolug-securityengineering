# Unit 8 - Configuration Drift and Remediation

## Lecture Notes

### Overview

What is the lifecycle of a system?

What is configuration drift?
How do we deal with configuration drift?
	- Operational practices
		- change management activities
	- Technical practices
		- what can we do to control drift in a system
	- System built in tools
		- aide does a lot for us
	- Custom automation
		- checking drift
		- fixing drift
			- always potentially disruptive


Servers change daily
	- storage, resource use
	- services running
	- etc


Build book: how to bring up a system
	- automated?
	- manual

### Theory of configuration management

Key Concepts:
	- Configuration Item (CI): Any hardware, software, documentation, or other component
	that needs to be managed.
	- Baseline: A set of CIs that have been formally agreed auupon and serve as a basis
	for future
	- Change Control: The process of managing and approving changes to CIs
	- Configuration status accounting: Documenting and tracking information about
	CIs and their changes.
	- Configuration Audit: Evalating configurations to ensure they meet requirements.
	
### Goals of configuration management

Consistency: Ensure that the system is built and operates as intended.
Traceability: track changes to CIs and their impact on the system.
Accountability: define who is reponseible.

### Configuration Management Tools

Many tools are available to automate and support CM activities. These tools
can help with:
	- Version control
	- Infrastructure as code
	- automation and orchestration
	- change tracking
	- compliance and auditing
Examples of Configuration Management Tools:
	- Git: for version control
	- Puppet, Chef, Ansible: For infrastructure automation
	- Docker, Kubernetes: For container orchestration
	- Terraform: For infrastructure as code






