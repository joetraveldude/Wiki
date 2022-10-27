## **Overview**
Following these instructions will allow new systems in the Data Plane, Desktop Plane, and Mission Plane to enroll in Vulnerability Management protection.

**Pre-Requisites**
- Locate the Agent Installation file for either Linux or Windows from the following file path:  \\\phc-msft.us\sysvol\phc-msft.us\packages\

**Agent Installation Steps**

1. Identify the file path for the appropriate Nessus Agent installation file.
1. Execute installation through either a Linux CLI or Windows Powershell as the administrator, or with Windows install the '.msi' file as administrator.

**Installing in Windows with Powershell, follow these steps:**
1. Download the file to the desktop by dragging and dropping the Nessus Agent file to your desktop.
1. In Powershell as the Administrator, run the following command 
`Install-Package <filename of the agent file>`
1. The file is installed.  Ensure the Nessus Agent is running by running the following commands: 
`net start Tenable Nessus Agent`
1. To properly link the Agent to the Nessus Manager, change the file directory so that you are here: `C:\Program Files\Tenable\Nessus Agent>`
1. After navigating to the listed file path, run the link command: 
`.\Nessuscli.exe agent link --key=e8eed0cad64a25b316ff5745297de8944270996c903bd176768d859f2c39afe8 --host=nessus.phc-msft.us --port=8834 --groups="All Deployed Agents"`
Note: ABS apps should add "Mission Plane" to the groups arg separated by a comma. "Tier1 Servers" or "Tier2 Servers" can be added if applicable.
1. From the same file path, verify the status of the Nessus Agent with: 
`.\nessuscli.exe agent status`
1. The Nessus Agent should be running and linked.
1. Once completed, notify the Nessus Security Administrator to verify that the newly deployed agent is being detected in the Nessus Manager and is running properly.  The Nessus Security Administrator should then group the Nessus Agent with the proper Nessus Agent Group in the Nessus Manager. Adding the "groups" arg to the link command above may make this step no longer needed.
1. Any issues should be documented and escalated through an IcM for tracking and remediation.

**Installing in Linux, follow these steps:**
1. Download the file with `# wget "<filename/path>"`  NOTE: If the Linux VM is not domain joined, the file will have to be picked up from a location it can access.
1. In the CLI as the Administrator, run the following command `# rpm -ivh <filename>`
1. The file is installed.  Ensure the Nessus Agent is running by running the following commands: `/sbin/service nessusagent start`
1. Now connect the Agent to the Nessus Manager by running: `# sudo /opt/nessus_agent/sbin/nessuscli agent link --key=e8eed0cad64a25b316ff5745297de8944270996c903bd176768d859f2c39afe8 --host=nessus.phc-msft.us --port=8834`
1. Verify the overall status of the installed agent by running: `/opt/nessus_agent/sbin/nessuscli agent status`
1. Once completed, notify the Nessus Security Administrator to verify that the newly deployed agent is being detected in the Nessus Manager and is running properly.  The Nessus Security Administrator should then group the Nessus Agent with the proper Nessus Agent Group in the Nessus Manager.
1. Any issues should be documented and escalated through an IcM for tracking and remediation.


**Installing with the Windows executable file, follow these steps:**

During installation, use the following options to link to this manager:
--host=<nessus> 
--port=<8834>
--key=e8eed0cad64a25b316ff5745297de8944270996c903bd176768d859f2c39afe8
- At the Welcome screen click Next.
- At the License screen select “I accept the terms…” and then click Next.
- At the Destination screen click Next. 
- At the Setup Type screen select Typical and then click Next.
- At the Configuration Option screen enter the following then click Next.
- [ ] 	Key = e8eed0cad64a25b316ff5745297de8944270996c903bd176768d859f2c39afe8
- [ ] 	Server = nessus:8834 
- [ ] 	Groups = All Deployed Agents
Note: ABS apps should add "Mission Plane" to the groups arg separated by a comma. "Tier1 Servers" or "Tier2 Servers" can be added if applicable.

**(These steps are embedded with instructions above)**
**Post Installation **
From PowerShell or Linux CLI, running as administrator, navigate to the file path that has the 'Nessus Agent' folder, for example “C:\Program Files\Tenable\Nessus Agent”

- Check the nessus agent status with the following powershell command:`.\nessuscli.exe agent status`
- The Nessus Agent status should be running, but not linked at this time.  The next steps will tell you how to link your Nessus Agent to the Nessus Scanner.

**Link Nessus Agent to Nessus Scanner**
To link your newly installed Nessus Agent to the Nessus Scanner, navigate to the 'Nessus Agent' file path like in the commands above, and execute the following command as administrator:

`.\Nessuscli.exe agent link --key=e8eed0cad64a25b316ff5745297de8944270996c903bd176768d859f2c39afe8 --host=nessus.phc-msft.us --port=8834 --groups="All Deployed Agents"`
Note: ABS apps should add "Mission Plane" to the groups arg separated by a comma. "Tier1 Servers" or "Tier2 Servers" can be added if applicable.

The Agent should be successfully linked, and a notification in the CLI should return a notification with this successful link.
