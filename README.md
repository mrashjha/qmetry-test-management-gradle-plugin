# qmetry-test-management-gradle-plugin
QMetry Test Management plugin for Gradle has been designed to seamlessly integrate your CI/CD pipeline with QMetry.

QTMGradlePlugin uploads result file(s) generated in a Gradle project to QTM Enterprise server. The plugin, if used in a gradle project, provides an additional gradle task 'qtmUploadResults'

## How to use the plugin?

### Step 1: Prerequisite

To get qmetry configuration from QMetry Test Management :-

1) Login to QMetry Test Management application.
2) Choose an existing project or create new.
3) Goto Integration > Automation API > Copy Automation API Key
OR directly visit https://testmanagement.qmetry.com/#/integration/automation-api

### Step 2:Add following information:
Use the plugin from anywhere in your gradle project, by including the following code in 'build.gradle' file...
```
plugins
{
    id 'com.qmetry.QTMGradlePlugin' version '1.6'
}

qtmConfig
{
	qtmUrl='https://testmanagement.qmetdry.com/'
	qtmAutomationApiKey='zEzs7iy77D8ARWX8xMFzJRZTzb66W0LCyaK6xdec'
	automationFramework='JUNIT'
	testResultFilePath='/test-results/test/TEST-ispl.sample.AppSecondTest.xml'
	automationHierarchy='1'
	testSuiteId='STR-TS-01'
	testSuiteName='Demo Testsuite'
	tsFolderPath='Suite Tracker/Track1'
	tcFolderPath='Test Tracker/Track1'
	project='Demo Project'
	platform='Chrome'
	release='Default Release'
	cycle='Default Cycle'
	build='Demo Build'
	testcaseFields = '{"description":"Automated Test case","testCaseType":"Security","testCaseState":"Rejected","component":["x"],"priority":"Blocker","testcaseOwner":"Jack","estimatedTime":"143","userDefinedFields":{"Integrate":"Custom Field Testsuite"}}'
	testsuiteFields = '{"description":"Automated Test suite","testsuiteOwner":"Jack","testSuiteState":"New","userDefinedFields":{"Integrate":"Custom Field Testsuite"}}'
	skipWarning = '1'
	isMatchingRequired = 'true'
}
```

The task publishResultsToQTM always looks for qtmConfig in build.gradle file of your project. Provide following details :-

* **qtmUrl** - Url to QMetry Test Management instance
* **qtmAutomationApiKey** - Automation Key
* **automationFramework** - JUNIT/TESTNG/CUCUMBER/QAS/HPUFT/ROBOT/JSON
* **testResultFilePath** - Path to result file (or directory for multiple files) relative to build directory
* **automationHierarchy (optional)** - Hierarchy which will be used to parse test result files on QTM for JUnit and TestNG (In case of other frameworks automationHierarchy will be skipped if provided)
  * JUnit 
    * 1 - Use current Hierarchy in which JUnit Testcase is treated as TestStep and Testsuite is treated as Testcase
    * 2 - Use Junit Testcase as Testcase and link all those (from all testsuites and all files of Junit) to one Testsuite
    * 3 - Create Multiple Testsuites and then link their respective testcases in corresponding Testsuites
  * TestNG
    * 1 - Use 'class' tag as Testcase and 'test-method' tag as TestStep
    * 2 - Use 'test-method' tag as Testcase
    * 3 - Use 'test' tag as Testcase and 'test-method' tag as TestStep
* **testSuiteId (optional)** - Id or Entity key of the test suite (This will upload results to existing test suite)
* **testSuiteName (Optional)** - Test suite name (This will create a new test suite with given name)
* **tsFolderPath (optional)** - Test suite Folder Path (This will upload results to the mentioned Test Suite Folder)
* **tcFolderPath (optional)** - Test case Folder Path (This will create Test case if not created in the mentioned Test Case Folder)
* **project** - Project Id or Project Key or Project name
* **platform (Optional)** - Platform Id or Platform Name
* **release (Optional)** - Release Id or Release name
* **cycle (Optional)** - Cycle Id or Cycle Name
* **build (Optional)** - Build Id or Build name
* **testcaseFields (Optional)** - Mention system defined fields and user defined fields for test case as shown in Test Case JSON format above. All the mandatory fields other than Summary should be mentioned in this parameter
* **testsuiteFields (Optional)** - Mention system defined fields and user defined fields for test case as shown in Test Suite JSON format above. All the mandatory fields other than Summary should be mentioned in this parameter. This parameter will be ignored if existing Test suite Id is used to upload results
* **skipWarning (Optional)** - To skip test case summary length warning or not skipped
    * 0 - (Default) Test Case Import will be failed if the result file contains test case summary with more than 255 characters
    * 1 - Test Cases can be imported by ignoring the warning about summary length. If the test case summary is longer, it will be truncated to 255 characters
* **isMatchingRequired (Optional)** - To create a new test case version or reuse the lastest version
    * true - (Default) Create a new version of Test Case if the latest version does not match with the one being uploaded
    * false - Reuse already linked Test Case version on Test Suite OR auto link the existing latest version of Test Case, if the Test Case entity key OR Test Case summary is found matching

#### Important Points
* Automation Hierarchy which will be used to parse test result files on QTM is only supported for TestNG and JUnit framework.
* cycle refers to the Cycle Name in QMetry Test Management, and must be included in Release specified by release field of your project.
* testsuite should include Test Suite Key or Id from your QMetry Test Management project. Ignore the field if you want to create a new Test Suite for the results.
* platform (if specified), must be included in your QMetry Test Management project, before the task is executed.
* In case of QAS enter path to the folder in which testresult folder will be created. Plugin will automatically fetch testresult folder and upload results.
* Enter directory path to upload zip file plugin will create zip file and upload it to QMetry Test Management.
* testsuiteFields parameter will be ignored if existing Test suite Id is used to upload results.
* testsuiteFields and testcaseFields use "Field Name" as key in JSON.
* tsFolderPath parameter will be ignored if the QMetry instance type is Server.
* tcFolderPath parameter will be ignored if the QMetry instance type is Server.

### Step 4:Generate Test Result(s) for your project.

### Step 5:Upload Test Result(s) to QMetry Test Management by executing following goal:

Use the following command from your project.
```
gradle test qtmUploadResults
```

...or alternatively, use the executable jar file (generated by command 'gradle clean build') directly as a dependency in your Gradle project 'build.gradle' file.