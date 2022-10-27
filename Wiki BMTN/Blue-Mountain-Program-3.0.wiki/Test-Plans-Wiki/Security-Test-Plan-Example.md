

> The following is a sample of a security test plan in a table format

||Test Authentication||
|:----:|-------------------|----|
|Step|Operator/User Action|P/F|
|1.|From a VDI Session, navigate to https://portal.spice.tld and authenticate as a privileged user with access to the Management Bastion workload.||
|Expected Results| <ul> <li>Access to https://portal.spice.tld is successful from the VDI host.</li><li>The DoD Warning Banner is displayed<li>Authenticator feedback is obscured</li><li>Authentication with RSA token credential is successful</li><li>Previous successful and unsuccessful logins are displayed</li><li>The Management Bastion tile is available</li>||
|2.|Click on the Management Bastion tile and authenticate to the management bastion host with Tier 0 AD credentials.||
|Expected Resuls|<li>The Gates Portal establishes a Guacamole session to the management bastion in the Gates VDMS.</li><li>The DoD Warning Banner is displayed.</li><li>Authenticator feedback is obscured</li><li>Authentication with a Tier 0 privileged user account and password is successful.</li><li>Previous successful and unsuccessful logins are displayed.</li>||
|3.|Connect to the Gates VDSS CSR with the Tier 0 AD account used to connect to the management bastion.||
|Expected Results|<li>The DoD Warning Banner is displayed</li><li>Authenticator feedback is obscured</li><li>The authentication attempt fails</li>||
|4.|Press ctrl+c to disconnect the SSH session. Connect to the Gates VDSS CSR with a valid local account but an incorrect password.||
|Expected Results|<li>The DoD Warning Banner is displayed</li><li>Authenticator feedback is obscured</li><li>The authentication attempt fails</li>||
|5.|Press ctrl+c to disconnect the SSH session. Connect to the Gates VDSS CSR with a valid local account and correct password.||
|Expected Results|<li>The DoD Warning Banner is displayed</li><li>Authenticator feedback is obscured</li><li>The authentication attempt fails</li>Note: Previous successful/unsuccessful logins are NOT displayed.||
|6.|Show additional local user accounts and privilege level<br>_show run \| section username_ ||
|Expected Results|A list of local users is displayed<br><li>Named authorized network administrators<li>ACAS scan account<li>Emergency break-glass account<br><br>Each user has Level 15 privileges (full admin)<br>Each user’s password is hashed||
|Requirements: | JSIG: AC-2(1), AC-2(7), AC-6, AC-6(1), AC-6(5), AC-8, AC-9, AC-9(1), AC-11, IA-2, IA-4(4), IA-6|
|Test Results|
|Comments|
















