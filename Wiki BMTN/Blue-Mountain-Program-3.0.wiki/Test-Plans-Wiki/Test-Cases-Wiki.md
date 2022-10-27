# Introduction 

Welcome to Test Cases.
```plaintext
Last modified 25MAY2022 1532EST
```

# Getting Started


1.  [What is a Test Case](#what-is-a-test-case)
1.  [What is a Test Suite](#what-is-a-test-suite)
1.  [Importing Test Cases](#importing-test-cases)


# What is a Test Case

A test case is a list of steps used to test code or an app for deployment. You define test cases to ensure your code works correctly, has no errors, and meets business and customer requirements.<br>
[Test objects and terms - Azure Test Plans | Microsoft Docs](https://docs.microsoft.com/en-us/azure/devops/test/test-objects-overview?view=azure-devops#test-specific-work-item-types)

# What is a Test Suite

A test suite is a grouping of test cases in separate testing scenarios within a single test plan. Grouping test cases makes it easier to see which scenarios are complete. <br>
[Test objects and terms - Azure Test Plans | Microsoft Docs](https://docs.microsoft.com/en-us/azure/devops/test/test-objects-overview?view=azure-devops#test-specific-work-item-types)

# Importing Test Cases

1. Open Azure Devops
1. Browse to correct Organization, and Project.
1. Click [Test Plans](https://dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%202.0/_testManagement/all) on the left hand side of the screen
1. Choose "unassigned test Plan"
1. Choose relevant Test Suite (ex Above Baseline Software, Gates Desktop, Requirements driven testing)
1. Click ellipsys after Test Suite name (3 dots)
 ![image.png](/.attachments/image-23f0bd82-3894-46e2-accc-8007e557d0db.png)
1. Choose New Suite > Static Suite > Type Name of new Suite
1. In right panel of screen, choose Define
1. Choose Up Arrow to import Test Cases from CSV
1. Choose File, click ok
1. Click Import (a pop-up window may appear to confirm)
1. Validate test cases by opening them and noting if there are any special characters.


If you want to learn more about Azure DevOps Test Plans then refer the following [guidelines](https://docs.microsoft.com/en-us/azure/devops/test/overview).