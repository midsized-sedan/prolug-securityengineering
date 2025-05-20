# Unit 6 - Monitoring and Parsing Logs

## Lecture Notes

What is a log?
	- immutable record of an action that occurred (or tried to occur)
		- read only, but we can then take that and parse for our use cases

How do we use logs?
What are common log elements?
	- What is the structure of a log?
Where do we store logs?
	- Centralized locations?
		- Rotation for space and compression?
	- Get them off servers in real time?
How do we parse and look at specific information from logs?
	- Parsing logs with command line tools
	- Querying logs with specific tools



### Log Usage

Troubleshooting: When things go wrong, logs provide valuable clues about what
happened, when, and why.

Security Auditing: Logs can track user activity, login attempts, and system
changes, helping to identify potential security breaches.
	- pseudo attempts

Check pointing
	- Performance Monitoring: Logs can sometimes provide insights into system
	performance and resource utilization.
	- Debugging Applications: Developers rely on application-specific logs to 
	understand the behavior of their software and identify bugs.


### Log elements

Logs often follow a RFC format
	- RFC 3164 BSD Syslog
	- RFC 5424 IRTF Syslog
	- JournalCTL
		- typically stored in binary so you need a tool to read them
Logs must be timestamped
	- Commonly ISO 8601
Logs must be categorized by severity
	- allows proper routing
	- Common in linux systems: debug, info, notice, warning, err, crit, alert, emerg
	- auth typically goes to a separate directory


### Log Storage and Rotation

Log rotation is an essentail automated process in Linux that prevents log files
from growing uncontrollably.

The logrotate utility, configured through /etc/logrotate.conf and files in 
/etc/logrotate.d/, handles this by periodically rotating, compressing, and deleting
old log files based on defined rules.

Understanding and properly configuring log rotation is crucial for maintaining system
stability and manageability.

### Sending Logs off the server

Common Architectures
1. Rsyslog forwarding and collection
2. Log Aggregation with Agents and a Centralized Platformm (e.g. ELK Stack,
Splunk, Graylog, Loki)
3. Using Message Queues (e.g. Kafka RabbitMQ)
	- Event Busses
4. Cloud-Native Logging Services

Choosing the right architecture

Scale: The number of servers and volume of logs generated
Reliability Requirements: How critical is it that all logs are delivered?
Real-time Analysis needs: Do you need to analyze logs in real-time for alerting
or dashboards?
Budget: The cose of software licenses, hardware, and operational overhead
Expertise: the skills ofyour team in managing different logging technologies
Compliance requirements: any speicific regulations regarding log retention and security
existing infrastructure: whether your are in a cloud environment or on-premises


### Using the logs

### Parsing logs with text tools

Tips

Uderstand the Log Formate

Start Simple

Use Regular Expressions with grep and sed

Iterate a


### Parsing Logs with Special Tools



Tools designed to parse logs and look for trends and exceptions. Often these are
known as Security Information and Event Management (SIEM) solutions.
	- Splunk
	- LogQL - https://grafana.com/docs/loki/latest/query/analyzer/
	- ELK (Elasticsearch, Logstash, Kibana)
	- Graylog
Libraries in programming languages:
	- Python Libraries (e.g. re for regex, json, csv, pyparsing): Pythong offers
	excellent libraries for handling various data formats and regular expressions,
	allowing developers to write custome log parers.
	- Go Libraries (e.g. regexp, encoding/json, encoding/csv): Similar to Python,
	Go provides built-in packages for text processing and data format handling
	Specialized Parsing Tools (e.g. grok-cli).

### When to use special tools

Large Log Volumes: When dealing with gigabytes or terabytes of logs per day.
Complext Log Formats: When logs are unstructured or have inconsistent formats.
Need for Real-Time Analysis and Alerting: For proactive monitoring and incident response.
Requirement for Visualization and Dashboards: To gain insights and communicate
trends effectively.
Security Analysis: For identifying and investigating security threats.
Compliance Requirements: For long-term log retention and auditing.


