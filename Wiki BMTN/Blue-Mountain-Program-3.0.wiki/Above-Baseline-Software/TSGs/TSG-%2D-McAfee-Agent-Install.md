## **Overview**
Following these instructions will allow new systems in the Data Plane, Desktop Plane, and Mission Plane to enroll in centrally managed Anti-Virus protection.

**Pre-Requisites**
- Ensure the system that is getting the agents is applied to the ‘staging’ OU in Active Directory.  Once the agent has been installed and is communicating with the Enterprise Policy Orchestrator (ePO), then place the system in its target OU.

- Locate the Agent Installation file for either Linux or Windows from the following file path:  \\\phc-msft.us\sysvol\phc-msft.us\packages\

- There should be two files available in the McAfee Agents folder.  One for Linux and one for Windows.  Use the appropriate file for the target system.

**McAfee Agent Install Steps**
There are two ways that an agent install can occur.  Either a security admin can install the McAfee Agent on your behalf, **create an IcM ticket**.

OR

Manually install by running the McAfee Installation file as administrator.  This can be accomplished by:
- Run the file McAfeeAgent_FF_DP.exe as administrator
- Click Ok when “McAfee Agent Setup completed successfully”
- After the agent has successfully installed, notify the Virus Protection Admin that manages McAfee ePO that the agent is installed on your VM.  You will have to specify the name of the system.  The Virus Protection Admin will then have to push McAfee Agent products/content to the newly deployed agent.


**Post Agent Installation**
- From the McAfee ePO, the new system should be online and updated with a status of ‘managed’
- The Virus Protection Administrator should then drop the system into the appropriate group in the System Tree to apply the relevant policies
- To ensure proper installation, the newly installed agent should conduct an eicar test.  See the following for instructions:  How to use the EICAR test file with our products (mcafee.com)

Or follow these steps:
- Use a text editor to create the file:
-  Open a text editor such as Notepad.
- Copy the following string into the new file:

`X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*`

NOTE: The third character is the capital letter 'O,' and not the digit zero.
 
- Save the file as eicar.com.
- Once the file is setup, conduct a system scan from the McAfee agent console.  This will be an on-demand scan.  Or you can scan the file for viruses and the McAfee Agent should quarantine the file.

