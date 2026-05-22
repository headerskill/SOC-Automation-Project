# SOC-Automation-Project
Using wazuh , Hive and Suffle


# Scope of the Project
The scope of this project is to design and implement a small-scale SOC automation environment using Wazuh, Shuffle, and TheHive within a virtualized lab setup. The project focuses on collecting and monitoring endpoint logs from Windows systems, detecting suspicious activities, automating alert processing, and managing incident response cases.

The project includes:

deployment and configuration of Wazuh for centralized log collection and threat detection,
installation of Sysmon for advanced Windows event logging,
integration of Shuffle to automate security workflows and alert handling,
integration of TheHive for incident and case management,
simulation of cyber attack scenarios such as brute force attacks and suspicious PowerShell activity,
and analysis of alerts generated within the SOC environment.


## Data flow Diagram
<img width="506" height="452" alt="image" src="https://github.com/user-attachments/assets/b875b974-f0bc-4930-8f77-fc4ca6f00d76" />


## Requirments
* Virtual machihne, Windows 10 , ubuntu , wazuh , TheHive, Suffle , Sysmon

## Insatalling the sysmon on the Windows 10 vm 
* To download the sysmon: https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
* To download the config file : https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml
** Step1 : Extract the symon and copy the sysmonfig.xml file to the sysmon directory.
Step 2: Open the powershell in the administrator mode then change the directory where sysmon is install 
Step 3: Use commnad .\Sysmon64.exe -i .\sysmonconfig.xml to install the configure file on the powershell

# Installing the wazhu server in the ubuntu 
* get-apt update && get-upgrade -y
* curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a --ignore-check
* After installation you will get the credentail that is needed to login to wazuh
* launch the wazuh using the web brower with your host ip : https:\\198.2.x.x , and use the advance option to proceed to the site.
*

# Installing the theHive
Dependences
apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl  software-properties-common python3-pip lsb-release

### Install Java
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"

#### Install Cassandra
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

#### 
wget https://thehive.download.strangebee.com/5.7/deb/thehive_5.7.2-1_all.deb
sudo apt install ./thehive_5.7.2-1_all.deb

