# Unit 1 Worksheet


## Important Links

[STIG document library](https://public.cyber.mil/stigs/downloads/)
[OpenSCAP](https://www.open-scap.org/)
[Security Policy Templates](https://www.sans.org/information-security-policy/)
[SANS Cheat Sheets](https://www.sans.org/blog/the-ultimate-list-of-sans-cheat-sheets/)


## Discussion Posts

### 1

The first question of this course is, “What is Security?”

1. Describe the CIA Triad.
```
CIA stands for Confidentiality, Integrity, and Availability, the cornerstone of information security.
  - Confidentiality: making sure that only authorized parties can access and modify data
  - Integrity: how reliable is the data. Unauthorized parties cannot access and modify
  - Availability: is the data available to the parties that need it
```
2. What is the relationship between Authority, Will, and Force as they relate to security?
```
Authority: the right or legitimacy to make and enforce decisions and rules. This would
be a manager, company, or similar person/organization.

Will: a personals internal inention to act in a certain way. A person exercises their
will to follow or not follow the rules put in place by authority.

Force: a type of control in place to prevent or ensure certain practices. Force
overides a persons will. An example would be needing a badge to enter a building. 
If you don't have a badge, you don't get access.

```
3. What are the types of controls and how do they relate to the above question?
```
There are several security controls. They are all based on who has the authority to
enact these controls, the will of different actors to follow or break rules and polices,
and the force to stop unauthorized behavior.

Administrative Controls
  - These are the rules, polices, and guidelines that are put in place.
  - Examples:
    - Security polices and procedures such as for password and data handling
    - Personnel mecurity measures such as training and separation of duties
Corrective Controls
  - Actions taken after a security incident to minimise damage and restore normalcy
  - Examples:
    - Incident response procedures, like a defined response plan
    - Backup and recovery systems, like regular data backups or even offsite backups
Detective Controls
  - Controls that are designed to monitor and look for security incidents
  - Examples:
    - Security monitoring systems, like real-time log analysis and correlation
    - Intrusion detection systems, like network intrustion detection which monitors network traffic
Preventive Controls
  - Controls put in place to prevent security incidents
  - Examples:
    - Access control systems, such as role-base access control
    - Authentication mechanisms, such as multi-factor authentication
Physical Controls
  - Controls put in place to protect physical assets
  - Examples:
    - Physical access systems, like key card access systems
    - Environmental controls, like fire suppresion systems
Technical Controls
  - Controls that use technology to enfore security
  - Examples:
    - Firewalls and network segmentation, things like next generation firewalls or virtual private networks
    - Encryption technologies, like full disk encryption
```

### 2

Find a STIG or compliance requirement that you do not agree is necessary for a server or service build.
```
Group ID: V-221571
Rule ID: SV-221571r987620
STIG ID: DTBC-0020
Title: Google Data Synchronization must be disabled.
```

1. What is the STIG or compliance requirement trying to do?
```
The STIG is trying to prevent data being stored on external servers.
```

2. What category and type of control is it?
```
Technical and Preventative
```

3. Defend why you think it is not necessary.
```
This is a situational one as it depends on your use case as a company.
Google provides services for company accounts and storage. At my previous job
we used google accounts and apps for all our internal work. Because of that, 
implementing a STIG such as this would simply now allow us to access any internal
data.
If you do not use google accounts for your company, then yes I do agree with 
the STIG
```


## Definitions

CIA Triad: Stands for Confidentiality, Integrity, and Availability.
These are the heart of all information security.
  - Confidentiality is the property that information is not made available
  or disclosed to unauthorized individuals, entities, or proecesses. 
  - Integrity means maintaining and assuring the accuracy and completeness of data
  over its entire lifecycle. This means that data cannot be modified in an 
  unauthorized or undetected manner.
  - Availability means that information is made available when it is needed.
  This means the computing systems used to store and process the information,
  the security controls used to protect it, and the communication channels used to
  access it must be functioning correctly.

Regulatory Compliance: Compliance means conforming to a rule such as a specification,
policy, standard, or law. Regulatory complicance is a goal that organizations
aspire to achieve in their efforts to ensure that they are aware of and take steps
to comply with relevant laws, policies, and regulations.

HIPAA: Health Insurance Portability and Accountability Act of 1996
It referse to the protection of patient medical information and the secure handling
of healthcare data.

Industry Standards: A technical standard use in a given industry. It's an
established norm or requirement for a repeatable technical task which is applied
to a common and repeated use of rules, conditions, guidelines or characteristics
for products or related processes and production  methods, and related management
system practices.

PCI/DSS: Payment Card Industry Data Security Standard
its a set of security standards designed to protect credit card information
and reduce fraud.

Security Frameworks: a structured set of policies, best practices, and standards
designed to protect IT systems, data, and networks from threats.
Some examples are NIST Cybersecurity Framework, ISO/IEC 27001,
CIS Controls (Center for internet Security), PCI/DSS, and MITRE ATT&CK Framework

CIS: Center for Internet Security is a nonprofit org that provides best practices
for cybersecurity. CIS Critical Security Controls (simply CIS Controls) is a framework
they made. It is a set of 18 prioritized security measures.

STIG: Security Technical Implementation Guide is a set of cybersecurity
configuration standards developed by the U.S. Department of Defense. Provides
detailed hardening guidelines for various technologies.


## Digging Deeper

1. Research a risk management framework. https://csrc.nist.gov/projects/risk-management/about-rmf
  - What are the areas of concern for risk management?

the NIST RMF is a process that integrates security, privacy, and cyber supply chain
risk management activities into the system development life cycle.
The System Development Life Cycle is a structured process for planning, creating,
testing, and deploying an information system.

7 Stages of SDLC
Planning
  - Purpose: define goals, feasibility, and project scope
  - Key Activities: Identify requirements, estimate costs, and asses risks
Analysis
  - Purpose: Understand user needs and system functionality
  - Key Activities: Gather requirements, anazlyes workflows, and document system specs
Design
  - Purpose: Create architecture, UI, and technical specifications
  - Key Activities: Define data models, choose technology stack, and design system components
Development
  - Purpose: Write the actual code and build the system
  - Key Activities: Implement features, integrate components, and follow coding standards
Testing
  - Purpose: Ensure the system works correctly and securely
  - Key Activities: Perform unit, integration, performance, and security testing
Deployment
  - Purpose: Release the system for real world use
  - Key Activities: Deploy to production, train users, and monitor performance
Maintenance
  - Purpose: Keep the system running smoothly with updates
  - Key Activities: Fix bugs, optimize performance, and apply security patches

2. Research the difference between quantitative and qualitative risks.
  - Why might you use one or the other?

Quantitative risks:
  - involve measuring and assigning numerical values to risks.
  - focus on measurable factors
  - provide an objective assessment based on data statistics, or financial metrics

Qualitative risks:
  - Subjective analysis based on expert judgement, experience, and intuition
  - categorizes risks in terms of severity and probability using descriptive scales

You'd choose one or the other depending on the stage of the project, the available data,
and the level of detail required.
Quantitative:
  - when you have data and need precision
  - when the project is high-risk and requires detailed calculations
  - compliance standards require exact risk assessments
Qualitative:
  - Data is lacking or hard to measure
  - you need a quick, high-level assessment
  - the risks are subjective or involve non-quantifiable factors

3. Research ALE, SLE, and ARO.
  - What are these terms in relation to?
  - How do these help in the risk discussion?

These are terms used in risk management and risk assesment to quantify
and manage potential threats. Specifically they work to quantify and manage 
financial risk.

SLE - Single Lose Expectancy
  - the expected financial loss from a single occurence of a risk event
  - SLE = AssetValue x ExposureFactor
    - AssetValue is the total value of the asset being protected
    - ExposureFactor is the percentage of the asset value that could be lost or damaged (0 to 1)
ARO - Annual Rate of Occurrence
  - frequency with which a specific risk event or threat is expected to occur within a year
  - 0 to 1 number. e.g. ARO=0.5 means one every two years
ALE - Annual Loss Expectancy
  - total expect loss per year due to a specific risk event or threat
  - product of SLE and ARO, providing a yearly financial estimate of the rick's impact
  - ALE = SLE x ARO

## Reflection Questions

1. What question do you still have about this week?

I'm so lost I don't know what to ask really right now.

2. How are you going to use what you've learned in your current role?

I have no current role so this is prepping for the next role.
