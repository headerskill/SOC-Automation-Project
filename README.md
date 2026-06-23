# SOC-Automation-Project
Using wazuh , TheHive and Suffle
## Overview
This project demonstrates the design and implementation of a Security Operations Center (SOC) automation lab using Wazuh, TheHive, Shuffle, Sysmon, and VirusTotal within a virtualized environment.

The primary objective of this project was to build an automated incident detection and response pipeline that mirrors the workflow of a modern SOC. Rather than manually investigating every alert, the environment automatically detects suspicious activity, enriches alerts with threat intelligence, creates incidents, and notifies analysts for further investigation.

To validate the effectiveness of the solution, a credential-dumping attack using Mimikatz was simulated on a Windows endpoint. This allowed the complete alert lifecycle to be observed, from event generation and detection to enrichment, case creation, and analyst notification.

Through this project, I gained hands-on experience with SIEM, SOAR, threat intelligence integration, detection engineering, and incident response processes commonly used in enterprise security environments.

## Project Architecture

### Components and its purpose

* Wazuh	SIEM and XDR platform for log collection, analysis, and threat detection
* Sysmon	Advanced Windows event logging and telemetry generation
* Shuffle	Security Orchestration, Automation, and Response (SOAR) platform
* TheHive	Incident response and case management platform
* VirusTotal	Threat intelligence enrichment service
* Windows 10 VM	Endpoint used for attack simulation
* Ubuntu Server	Hosts Wazuh, TheHive, and Shuffle services

## Project Objectives
The project was designed to achieve the following goals:
* Centralize endpoint log collection and monitoring
* Improve endpoint visibility using Sysmon telemetry
* Detect malicious activity using Wazuh detection rules
* Automate alert enrichment using threat intelligence
* Reduce manual analyst workload through SOAR automation
* Automatically create and manage incidents in TheHive
* Simulate real-world attack techniques for validation
* Understand the complete alert-to-investigation lifecycle

## Lab Environment
### Infrastructure
#### Windows 10 Virtual Machine

The Windows endpoint was used to simulate attacker activity and generate security events. Sysmon and the Wazuh Agent were installed on this system to collect telemetry and forward it to the SIEM platform.

#### Ubuntu Server

The Ubuntu server hosted the core SOC infrastructure, including:
* Wazuh
* TheHive
* Shuffle
* Elasticsearch
* Cassandra

This architecture closely resembles how many organizations deploy centralized monitoring and response platforms.

## Desigining the Data flow Diagram
### Data Flow
1.	Sysmon generates detailed endpoint telemetry.
2.	Wazuh Agent collects Sysmon logs.
3.	Wazuh Manager receives and analyses events.
4.	Detection rules generate security alerts.
5.	Alerts are forwarded to Shuffle via Webhook.
6.	Shuffle extracts Indicators of Compromise (IOCs).
7.	VirusTotal enriches the alert with threat intelligence.
8.	TheHive automatically creates an incident case.
9.	Email notifications are sent to SOC analysts
<img width="506" height="452" alt="image" src="https://github.com/user-attachments/assets/b875b974-f0bc-4930-8f77-fc4ca6f00d76" />



## Installing Sysmon
Windows Event Logs provide useful information, but they often lack the level of detail required for effective threat hunting and forensic investigations.

Sysmon extends native Windows logging by providing:
* Process creation events
* Network connection events
* File creation events
* Registry modifications
* Cryptographic hashes

This additional telemetry improves visibility into attacker behavior and enables more accurate threat detection.

### Download Resources
*	Sysmon: [https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
*	Sysmon Configuration: [https://github.com/olafhartong/sysmon-modular](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)


Installation Steps
1.	Download and extract Sysmon.
2.	Copy the Sysmon configuration XML file into the Sysmon directory.
3.	Open PowerShell as Administrator.
4.	Navigate to the Sysmon installation directory.
5.	Install Sysmon using:
.\Sysmon64.exe -i .\sysmonconfig.xml
6.	Verify installation through:
Event Viewer
 └── Applications and Services Logs
      └── Microsoft
           └── Windows
                └── Sysmon



## Installing the wazhu server in the ubuntu 

Wazuh acts as the central SIEM platform within the SOC environment.

Its role is to:
* Collect logs from endpoints
* Analyze security events
* Detect suspicious activity
* Generate alerts
* Provide centralized visibility

Without a SIEM, analysts would need to manually review logs from multiple systems, significantly increasing investigation time.


### Documentation for installing the wazhu

* https://documentation.wazuh.com/current/quickstart.html
* https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html
* 
### Updating the Ubuntu
* get-apt update && get-upgrade -y

### Installing the Wauzh
* curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a --ignore-check
* After installation you will get the credentail that is needed to login to wazuh
* launch the wazuh using the web brower with your host ip : https:\\198.2.x.x , and use the advance option to proceed to the site.


## Installing the theHive
Generating alerts is only part of the incident response process.

Security teams require a structured platform to:
* Create investigations
* Assign ownership
* Record findings
* Track evidence
* Manage response activities

TheHive provides centralized incident management, ensuring that security investigations are documented and tracked effectively.

#### Required Dependencies

TheHive requires:
1. Java 11
2. Apache Cassandra
3. Elasticsearch

Install the dependencies and configure them according to the official documentation before installing TheHive.
#### Official Documentation:
https://docs.strangebee.com/thehive/installation/installation-guide-linux-standalone-server/

##### Installing Dependences
apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl  software-properties-common python3-pip lsb-release

### Install Java
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"

#### Install Apache Cassandra
wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra

#### Install ElasticSearch
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch

#### Install thehive
wget https://thehive.download.strangebee.com/5.7/deb/thehive_5.7.2-1_all.deb
sudo apt install ./thehive_5.7.2-1_all.deb

## Configuring the Wazuh  Agent 
The Wazuh Agent acts as the bridge between the monitored endpoint and the SIEM platform.

Its responsibilities include:

Collecting logs
Monitoring endpoint activity
Forwarding events to Wazuh Manager
Supporting Sysmon integration

Deploy the Wazuh Agent on the Windows endpoint and register it with the Wazuh server.
Start the service:
Configuring the wauzh agent 
<img width="1723" height="782" alt="image" src="https://github.com/user-attachments/assets/c27b300d-bdbf-412d-896c-14167e481603" />

After that open the powershell in administaration mode and past the command you get will configuting the agent on wauzh 
to start : net start wauzh on powershell

net start wazuh


## Sysmon Integration
Why Integrate Sysmon with Wazuh?

While Sysmon generates detailed telemetry, those logs must be centralized for monitoring and analysis.

By integrating Sysmon with Wazuh:

Endpoint events become searchable
Detection rules can be applied
Alerts can be generated automatically
Investigations become significantly easier

Modify the Wazuh Agent configuration to collect Sysmon events:

C:\Program Files (x86)\ossec-agent\ossec.conf

Restart the Wazuh Agent after making configuration changes.

Once configured successfully, Sysmon events should begin appearing in the Wazuh Dashboard.

## configuring the cassandra
Directory :  /etc/cassandra/cassandra.yml
 Change the following according to your host ip address
 clustername: <img width="133" height="36" alt="image" src="https://github.com/user-attachments/assets/45b80c12-a990-43b0-a149-5af59ea7c8c1" />
listen address: <img width="202" height="49" alt="image" src="https://github.com/user-attachments/assets/9536081f-28c8-4cd7-8df2-b903190ea049" />

rpc_address: <img width="154" height="27" alt="image" src="https://github.com/user-attachments/assets/a91c04e5-12fe-45bf-9541-263200879217" />

seed_provider: - seeds: <img width="194" height="43" alt="image" src="https://github.com/user-attachments/assets/d3107a99-3c7b-4d11-a624-3421ff1d8281" />

Always restart the service once the configuration is made.
<img width="226" height="61" alt="image" src="https://github.com/user-attachments/assets/e509f453-7e72-4ec7-a7aa-1b01ed5ae165" />

To check the status use: systemctl status cassabdra.service


Configuting the elastic 
Directory : /etec/elasticsearch/elasticsearch.yml
<img width="188" height="130" alt="image" src="https://github.com/user-attachments/assets/c0b1133e-ea2b-4a1e-a29c-7b4553559f31" />
<img width="160" height="21" alt="image" src="https://github.com/user-attachments/assets/e64f4755-9581-4031-9d9b-8b78cc917ddf" />
<img width="248" height="187" alt="image" src="https://github.com/user-attachments/assets/09187097-785b-495e-8382-8cc66e8a7dcc" />
<img width="346" height="111" alt="image" src="https://github.com/user-attachments/assets/49bef8f3-38f7-4ab0-8a42-23e61e10ca63" />

## Configuring TheHive 
Changing the permisson to the directory
<img width="298" height="21" alt="image" src="https://github.com/user-attachments/assets/8576e371-67cd-463e-ab55-f60278c40b41" />

<img width="242" height="56" alt="image" src="https://github.com/user-attachments/assets/ee34490e-9500-494c-9fce-b0d7257bd4d3" />

Directory : /etc/thehive/application.conf
<img width="241" height="211" alt="image" src="https://github.com/user-attachments/assets/ae7b3b4b-c84b-4248-a1a2-8cca37ba03ab" />

<img width="243" height="52" alt="image" src="https://github.com/user-attachments/assets/0c34ced2-fad9-45ca-8490-486f05e82f23" />
<img width="245" height="55" alt="image" src="https://github.com/user-attachments/assets/1d2ec92f-fa43-4ad3-ae39-9a04e609474d" />



Confifguring the ossce file on the machine where agent is installed.
<img width="552" height="291" alt="image" src="https://github.com/user-attachments/assets/69d1059b-9a4b-4d2c-9072-0bde8d16d55f" />

now you can see the sysmson log on your wazuh dashboard
<img width="946" height="763" alt="image" src="https://github.com/user-attachments/assets/b26ba582-cfe4-4f36-bd73-11d85c3bd237" />

To generate the mimikatz on the wauzuh you need to create the rule


configuring the osec file 
<img width="1284" height="648" alt="image" src="https://github.com/user-attachments/assets/de3d8fcb-0f2a-4e73-b580-37a2fdfaddf7" />

configuring the filebeat
<img width="973" height="857" alt="image" src="https://github.com/user-attachments/assets/d6ac9485-6d5f-4f98-b214-8b5e28961b2a" />

creating the index

<img width="1034" height="642" alt="image" src="https://github.com/user-attachments/assets/4ab28fe2-94a0-4447-8c10-b0fd9dab7d48" />
<img width="1234" height="363" alt="image" src="https://github.com/user-attachments/assets/cf6b3f7d-8489-421e-a8d6-799aa30d66a9" />
<img width="658" height="676" alt="image" src="https://github.com/user-attachments/assets/1fe9113d-fc3f-46a9-8215-5ce21672f293" />
<img width="935" height="278" alt="image" src="https://github.com/user-attachments/assets/2b373aba-35b6-40b1-b278-624897042f67" />
<img width="685" height="352" alt="image" src="https://github.com/user-attachments/assets/e54b6ca1-c57e-4a25-88ff-9f9217495162" />
<img width="989" height="539" alt="image" src="https://github.com/user-attachments/assets/e8328f12-714b-4c33-9cfd-a9d44118eb1c" />
<img width="988" height="724" alt="image" src="https://github.com/user-attachments/assets/1693d92e-dc35-4998-bc76-c6f068b46dce" />
<img width="1015" height="174" alt="image" src="https://github.com/user-attachments/assets/182723f2-a62a-4154-b4eb-64f9cd6c0862" />



Adding the rules
<img width="1582" height="807" alt="image" src="https://github.com/user-attachments/assets/285e7f0f-fd4e-4652-857f-210fafa93483" />
<img width="824" height="557" alt="image" src="https://github.com/user-attachments/assets/e255e617-90d4-469a-829a-ee55abc8f634" />
<img width="994" height="568" alt="image" src="https://github.com/user-attachments/assets/33829bfd-9b4c-40e9-856a-19d6c5f9f239" />
<img width="963" height="733" alt="image" src="https://github.com/user-attachments/assets/3021241e-8438-4e6f-821c-48f4e12dc53c" />

## Geneating the temeletry 





### SOAR 
step 1: use the webhook , can copy the webhookurl
<img width="1451" height="696" alt="image" src="https://github.com/user-attachments/assets/d2795441-99e7-4896-88db-d9cfd715a0b9" />


step 2: modify wauzh manger ossec config file to connect to the webhook
<img width="1012" height="705" alt="image" src="https://github.com/user-attachments/assets/6d0a4b0a-3386-4775-aad1-3f507dae3afb" />


step 3: chaning the change me to refex capture group to extract the sha256
<img width="646" height="561" alt="image" src="https://github.com/user-attachments/assets/83203005-f845-42d9-89cf-50527bcb630e" />


step4: connecting the regex to virus total and getting the report on the sha256 using the virus total api key.
<img width="454" height="506" alt="image" src="https://github.com/user-attachments/assets/1a2b37db-da55-4bcc-bcb0-d41fd903b83f" />
<img width="514" height="360" alt="image" src="https://github.com/user-attachments/assets/a0352ca3-b930-4e52-88ed-15435e6ae75d" />
<img width="512" height="1105" alt="image" src="https://github.com/user-attachments/assets/8cd236fd-9b78-4094-8b75-703202a107e0" />
<img width="852" height="670" alt="image" src="https://github.com/user-attachments/assets/7e502296-2010-491b-baf6-e37ef2c0da11" />


##  configuring the hive in shufffle
step 1 : login using the default credentials
step 2 : add the orginsation
<img width="1064" height="163" alt="image" src="https://github.com/user-attachments/assets/12b43a1d-737b-4ae4-9e16-78617bf0071a" />


step3: under the organistaion that we just created add the 2 user , one normal and one sercive
<img width="923" height="128" alt="image" src="https://github.com/user-attachments/assets/9727f960-0694-4501-8351-270554c2494c" />


strp 4: using the service user , create a API key so that we can connect it in the suffle
<img width="533" height="303" alt="image" src="https://github.com/user-attachments/assets/e5b98fa7-4a4c-4575-98c9-dcfade1d198d" />

<img width="720" height="248" alt="image" src="https://github.com/user-attachments/assets/f6efb30d-a2a1-494b-871e-adb1eb6bdbef" />

step 5: configure the hive  in suffle using the advanced,
<img width="334" height="163" alt="image" src="https://github.com/user-attachments/assets/b774ddae-4ea5-49d8-8e71-0e8ba6b23e3b" />


<img width="868" height="120" alt="image" src="https://github.com/user-attachments/assets/b470ac92-5393-43da-b376-5e35e93c03f6" />
 ## sending the email to soc analyst from suffle
<img width="190" height="268" alt="image" src="https://github.com/user-attachments/assets/64ee18d1-d5c3-4d14-a27d-4d3aaa055183" />

<img width="452" height="259" alt="image" src="https://github.com/user-attachments/assets/5900ea3c-4f44-4443-97fc-7ce0765fc892" />


<img width="428" height="159" alt="image" src="https://github.com/user-attachments/assets/13d58a01-8c66-4ece-8582-8a2ce527b050" />

![Uploading image.png…]()

## Detection Engineering
Why Use Mimikatz?

Mimikatz is one of the most widely known post-exploitation tools used by attackers to extract credentials from memory.

Because of its prevalence in real-world attacks, it serves as an excellent tool for validating detection capabilities.

## Attack Simulation

The following attack scenario was performed:

Execute Mimikatz on the Windows endpoint.
Sysmon records process activity.
Wazuh receives the generated events.
Detection rules identify suspicious behavior.
Alerts are generated and forwarded to Shuffle.

## Detection Challenge

A common attacker evasion technique is renaming malicious tools to bypass detections that rely solely on filenames.

To improve resilience against this technique, custom Wazuh detection rules were created to identify suspicious behavior and process characteristics rather than depending entirely on executable names.

This approach aligns with modern detection engineering practices and improves detection coverage.

## SOAR Automation with Shuffle
Why SOAR?

In traditional SOC environments, analysts manually enrich alerts, gather context, create tickets, and notify stakeholders.

This process is repetitive and time-consuming.

SOAR platforms automate these tasks, allowing analysts to focus on investigation and decision-making rather than administrative work.

Shuffle was implemented to automate alert enrichment, incident creation, and analyst notifications.

### Step 1: Receive Wazuh Alerts

Alerts generated by Wazuh are forwarded to Shuffle through a webhook.

This enables automated processing of security events immediately after detection.

#### Configuration
1. Create a webhook in Shuffle.
2. Copy the generated webhook URL.
3. Configure Wazuh Manager to forward alerts to the webhook.
4. Verify successful alert delivery.


### Step 2: Extract SHA256 Hashes

Indicators of Compromise often include file hashes.

Shuffle uses Regular Expressions (Regex) to automatically extract SHA256 hashes from incoming alerts.

These hashes are then passed to threat intelligence services for enrichment.

### Step 3: Threat Intelligence Enrichment
Why VirusTotal?

Security alerts often provide limited context.

VirusTotal enriches alerts by checking file hashes against multiple antivirus vendors and threat intelligence sources.

This helps analysts quickly determine whether a file has previously been identified as malicious.

#### Configuration
1. Obtain a VirusTotal API key.
2. Configure authentication within Shuffle.
3. Select the Get Hash Report action.
4. Pass the extracted SHA256 hash.
5. Validate successful enrichment.


### Step 4: Automatic Incident Creation
Why TheHive Integration?

Manually creating cases for every significant alert can become a bottleneck in SOC operations.

To reduce analyst workload, Shuffle automatically creates incidents in TheHive whenever a suspicious alert is detected.

This ensures that investigations begin immediately and that evidence is preserved from the start of the incident lifecycle.

#### Configuration
1. Create an organization in TheHive.
Create:
Analyst User
Service Account User
2. Generate an API key for the Service Account.
3. Configure TheHive integration in Shuffle.
4. Validate successful case creation.




### Step 5: Analyst Notifications
Why Email Notifications?

Analysts may not always be actively monitoring dashboards when a critical alert occurs.

Automatic notifications ensure that incidents receive immediate attention.

Shuffle sends emails containing:
* Alert details
* Host information
* Severity level
* VirusTotal enrichment results
* TheHive case reference

This improves response times and helps analysts prioritize investigations.

## Project Outcomes

This project successfully demonstrates:
* SIEM deployment and administration using Wazuh
* Endpoint monitoring with Sysmon
* Detection engineering and custom rule creation
* Threat intelligence integration using VirusTotal
* SOAR automation using Shuffle
* Incident management using TheHive
* End-to-end SOC automation workflows
* Security event investigation and response

Most importantly, this project demonstrates how automation can reduce manual effort, improve detection efficiency, and accelerate incident response within a modern Security Operations Center.

Key Skills Demonstrated
* Security Operations Center (SOC) Operations
* SIEM Engineering
* Detection Engineering
* Incident Response
* Threat Intelligence
* SOAR Automation
* Security Monitoring
* Endpoint Security
* Log Analysis
* Windows Security
* Linux Administration
* Threat Detection
* Security Investigation

## Future Improvements

Potential enhancements include:
* Active Directory integration
* Malware sandbox automation
* YARA rule deployment
* Sigma rule integration
* Automated endpoint isolation
* MITRE ATT&CK mapping
* Threat hunting dashboards
* Multi-endpoint monitoring
* Threat intelligence platform integration
* Automated IOC blocking





 <img width="224" height="116" alt="image" src="https://github.com/user-attachments/assets/aac769a3-48db-4089-8cd3-4664c55e7849" />
