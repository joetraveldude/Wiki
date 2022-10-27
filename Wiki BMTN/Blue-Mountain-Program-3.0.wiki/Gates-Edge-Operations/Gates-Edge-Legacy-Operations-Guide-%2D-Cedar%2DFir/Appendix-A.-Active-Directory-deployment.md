This section provides coverage of steps required to deploy Active Directory forest in mission enclave configured for one-way trust with Gates Core AD domain.

**Deploying Forest with Server Manager**

Leverage [Deploying a Forest with Server Manager](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-#BKMK_SMForest) guidance to deploy AD forest in mission enclave, specifically:
1.	Follow **Server Pool and Add Roles** section to add relevant roles in Server Manager:
a.	Use **Role-based or Feature-based Installation**.
b.	Select the VM name as the target server in **Server Selection**
c.	Choose **Active Directory Domain Services** role, check include management tools.
d.	In **Features** tab, choose **Group Policy Management**.
e.	Click **Install**.
f.	At the end of the AD DS role installation, click **Promote this server to a domain controller** 
2.	Follow **Create an AD DS Forest Root Domain with Server Manager** section to run **Active Directory Domain Services Configuration Wizard** using Server Manager:
a.	Select Add a new forest, supply the preferred mission enclave AD root forest domain name: <enclave>.<missionenclave>.com
b.	Select **Windows Server 2012** forest functional level, **DNS Server** option and choose **Directory Services Restore Mode Password** in Domain Controller Options
c.	Leave all other settings to default values and click **Install** at the end of the wizard

References
-	Install a New Windows Server 2012 Active Directory Forest: https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200- 

-	https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/rodc/install-a-windows-server-2012-active-directory-read-only-domain-controller--rodc---level-200 
