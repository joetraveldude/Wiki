## To elevate a cmd window to reset passwords unlock ids etc.
retrieve {userid} and {password} from the Azure Secrets (Should be saved as keyvault secrets "domain-account" and "domain-password")
1. Open a cmd prompt as administrator
1. Run command - `net localgroup Administrators {userid} /add`
1. Run command - `runas /user:PHC-MSFT\PhcAdmin cmd`
1. Enter {password} when prompted. A new elevated cmd prompt window will open.

## To unlock a {username}
1. Run command - `Net user {username} /DOMAIN /active:YES`
1. Example - `Net user Svc-sharepoint-run /DOMAIN /active:YES`

## To reset a {username} {password}
1. Run command - `net user {username} {password} /domain`
1. Example - `net user Svc-sharepoint-run -l0k9j8h_L)K(J*H /domain`

Note:  if there is a special character in either the {username} {password} you can put a ^ character before the special character and it will be treated as just a character. This also means the "^" character should be avoided in passwords, among others.

