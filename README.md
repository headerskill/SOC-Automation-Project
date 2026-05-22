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
