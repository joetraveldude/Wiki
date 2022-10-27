[[_TOC_]]

Important: See ABS repo for latest images, these are examples

# Category 1 - Client app with only RDP external connection
Summary: Client app or no additional VM connections
![abs_cat1.jpg](/.attachments/abs_cat1-c6ac7462-f99e-4e43-a76b-d06c179d7a16.jpg)
# Category 2 - Client --> Server App
Summary: Client/Server app or an app that requires a connection external to the app VM (SQL Server, separate license server VM, etc).
![abs_cat2.jpg](/.attachments/abs_cat2-401d5bc2-603f-4418-bdfa-be667bd04344.jpg)

# Category 3 - Web Apps
Summary: A web app that integrates with AD (via LDAP) to authenticate users.
## Category 3a - Web App with built-in AD Authentication (e.g. LDAP)
![abs_cat3a.jpg](/.attachments/abs_cat3a-f376fd5e-c18b-4c2e-a31b-46ff1f374b04.jpg)

## Category 3b - Web App without AD Authentication
Summary: A web app with no auth or inadequate auth so a browser will be published as a remoteApp to access the web app.
![abs_cat3b.jpg](/.attachments/abs_cat3b-73c11763-0c39-4564-b147-70845efc8a13.jpg)
# Category 4 - External Hardware (deprecated)
Summary: Azure Stack Hub with added GPUs
{on hold}

# Category 5 - Container App Dev Desktop
Summary: SW Developer workstation. Multiple SW Dev apps deployed on one VM/session host.
![abs_cat5.jpg](/.attachments/abs_cat5-5d8f2b47-62a3-4fe3-af00-8921bc3945ed.jpg)