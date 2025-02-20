# Constrained Device Application (Connected Devices)

## Lab Module 02

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

- PIOT-CDA-02-000: Branch "labmodule02" created.
- PIOT-CDA-02-001: he new ConstrainedDeviceApp module was created by maintaining the architecture provided in the source code. Both the unit test "ConfigUtilTest" and the integration test "ConstrainedDeviceAppTest" have passed successfully.
- PIOT-CDA-02-002: To create the new SystemPerformanceManager module, the provided architecture was used. The SystemPerformanceManager class was implemented with a constructor that initializes the poll rate and location ID using ConfigUtil and declares the dataMsgListener variable. Additionally, the startManager and stopManager methods were implemented to log when the manager starts and stops. The integration test 'SystemPerformanceManagerTest' passes successfully.

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule02

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
- SystemPerformanceManagerTest
- 

EOF.
