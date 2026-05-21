# SOC-Automation-Project
Using wazuh , Hive and Suffle


# Scope of the Project
* Designing, setupping, configuring , telementry , SOAR function


## Creating a Diagram
<img width="506" height="452" alt="image" src="https://github.com/user-attachments/assets/b875b974-f0bc-4930-8f77-fc4ca6f00d76" />


## vm Required Windows 10 ,

## Insatalling the sysmon on the Windows 10 vm 
* To download the sysmon: https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
* To download the config file : https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml
** Step1 : Extract the symon and copy the sysmonfig.xml file to the sysmon directory.
Step 2: Open the powershell in the administrator mode then change the directory where sysmon is install 
Step 3: Use commnad .\Sysmon64.exe -i .\sysmonconfig.xml to install the configure file on the powershell
