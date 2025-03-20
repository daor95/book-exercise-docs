# Constrained Device Application (Connected Devices)

## Lab Module 01

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

The CDA (Constrained Device Application) development environment has been set up using the python-components repository which was created using a template from the provided original GitHub repository.
PyCharm has been configured to use for this project.
A `.env` virtual environment has been created and all the required dependencies have been installed.
Also, PYTHONPATH has been configured as indicated by the teacher to avoid it having to be set on the terminal for each execution.
Tests have passed and app has been executed.


How does your implementation work?

**ConfigConst.py** updated: The `DEFAULT_CONFIG_FILE_NAME` property has been set to an absolute path for `PiotConfig.props` to ensure both the application and test classes can locate the configuration file.
**PYTHONPATH** correctly set: The necessary environment variable paths have been configured for both main and test directories.
A new branch `labmodule01` was created (`git checkout -b labmodule01`). Code updates have been reviewed and verified. A merge between `labmodule01` and the `default` branch was performed. The merged work was pushed to the remote repository.
After successfully executing the unit and integration tests, the CDA app has been run with no problems.


### Code Repository and Branch

NOTE: Be sure to include the branch 

URL: https://github.com/daor95/python-components/tree/labmodule01

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ConfigUtilTest
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- ConstrainedDeviceAppTest
- 
- 

EOF.
