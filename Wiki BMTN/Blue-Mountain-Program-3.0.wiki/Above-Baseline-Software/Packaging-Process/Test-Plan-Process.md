Please direct any questions/ideas to ABS Test POC.  ABS Test POC handles all communication with Test Team POC.

ABS Test POC: Bennilyn Quek (Backup: Junaid Paracha)
Test Team POC: Bridgit Abel

---
[[_TOC_]]

---

# Difference between Test Plan, Test Suite, Test Case

## Test Plan
Test team uses a Test Plan for each customer.  A Test Plan may contain many Test Suites.  

This is where ADO Test Plans are located: 
https://dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%203.0/_testManagement/all?showFilters=true

## Test Suite

A Test Suite can contain one or more sub-Static Suite. And each Test Suite contain multiple Test Cases.  Each app should contain only ONE Test Suite no matter what environment the app goes in.  Because testing scope starts from once tester is in the environment where the app can be accessed from.

The purpose of a Test Suite is to validate we have a usable software.

## Test Cases
A Test Case contain Action and Expected result from that action. This is where a test case can pass or fail.

---

# Step 1: Create a Test Suite for your app

1. In ADO left menu, click on 'Test Plans'
1. Click on 'Test Plan Landing Zone' Test Plans
1. Check if an existing Test Suite exist for your app under 'Above Baseline Software' 
1. If not, right click on 'Above Baseline Software' Test Suites > 'New Suite' > 'Static suite'

# Step 2: Create Test Cases for your app's Test Suite

1. Click on the Test Suite you created from steps above.
1. Click on 'Define' tab
1. Click on 'New Test Case'. 
You can also use the 'Grid View' or 'Import test cases from CSV' if you know how.
1. Enter a 'Title' for the Test Cases, make sure to start with a verb
1. Enter 'Action', provide enough details for regular user to follow
1. Enter 'Expected result', think "how do I know what I did was correct?". This is the passing criteria. Anything that doesn't match Expected result is failing.
1. Repeat from 'New Test Case' step until all user testing is covered.
1. See next step to assign Configuration.

# Step 3: Assign Configuration to Test Cases
Configuration is the environment your app is going into. Please check all that applies to your app. This will automatically duplicate each Test Case for each environment, so we only need to maintain one Test Case.

1. Select all Test Cases by checking the box to the left of each Test Cases
1. Right click on any one of the test cases > 'Assign configuration' > Check all environments that apply to your app.
1. Click on 'Execute' tab, make sure all your Test Cases appear for each environment.
1. Once completed, please ping ABS Test POC for a review.
1. Copy your app Test Suite's URL and paste into ConOps.
1. Once ABS Test POC reviewed with no additional feedback, you are done.

---

# Test Plan Process Changes

## Q & A
Please reach out to ABS Test POC for any questions.

### Q: Do we still need to include Test Suite URL in ConOps?
A: ~~Yes, for now. This will change once we obtain stakeholders approval.~~
No, as of decision made on 8/11/2022 with stakeholders.

### Q: Do we need to export a CSV copy of our Test Suite and upload into our ABS repo?
A: No. Once it's handed off to Test Team in 'Test Plan Landing Zone', they own and manage all Test Plan artifacts.

### Q: I have the same app but different customer/environment. Do I need to create new Test Suite?
A: No. Please reuse the same Test Suite that was created for the app. Our goal is to have one Test Suite per app no matter the customer or environment for maintainability. 

If you have a Test Case that is specific to an environment, please mark it with the correct Configuration and make it explicit in the Test Case Title. Example: 'Login to remote app environment', 'Login to Fairfax environment'

### Q: Do I need to include a Test Case on how to access the environment where the app is located?
A: No, for now. This may change in the future.

### Q: Where do I list assumptions?
A: TBD. Please submit your ideas to ABS Test POC.

---
