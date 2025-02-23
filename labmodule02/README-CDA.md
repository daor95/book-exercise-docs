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
- PIOT-CDA-02-003: Within the CDA, an instance of SystemPerformanceManager was implemented to connect both modules within the architecture. The startApp and stopApp methods were edited to include the initialization and termination of the SystemPerformanceManager. The integration test 'ConstrainedDeviceAppTest' passes successfully, generating the expected output.
- PIOT-CDA-02-004: Two parameters were added to the constructor of the BaseSystemUtilTask class, along with two getter methods for each parameter (name and typeID). Additionally, the getTelemetryValue method was left as provided in the source code since it will be implemented in later phases. No tests were required to be executed for this class.
- PIOT-CDA-02-005: The SystemCpuUtilTask class was implemented to collect CPU utilization metrics from the local system. The getTelemetryValue method, previously left unimplemented, was now defined to return the system's CPU usage using the psutil library. The unit test SystemCpuUtilTaskTest was executed successfully, confirming the correct functionality by returning the percentage of CPU utilized.
- PIOT-CDA-02-006: Similar to PIOT-CDA-02-005. The SystemMemUtilTask class was implemented to collect memory utilization metrics from the local system. The getTelemetryValue method was defined to return the percentage of virtual memory usage. The unit test SystemMemUtilTaskTest was executed successfully, confirming the correct functionality.

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule02

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ConfigUtilTest
- SystemCpuUtilTaskTest
- SystemMemUtilTaskTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- ConstrainedDeviceAppTest
- SystemPerformanceManagerTest
- 

EOF.
