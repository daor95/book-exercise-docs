# Constrained Device Application (Connected Devices)

## Lab Module 05

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?


- PIOT-CDA-05-000: Git "labmodule05" branch created.
  
- PIOT-CDA-05-001: The **SystemPerformanceManager** class has been updated to collect and store CPU and memory utilization data within `SystemPerformanceData`. The `handleTelemetry()` method now instantiates `SystemPerformanceData`, assigns the utilization values, and triggers a callback if an `IDataMessageListener` is set. Additionally, the `setDataMessageListener()` method has been implemented to enable future callback support. 
Integration test **SystemPerformanceManagerTest** was executed successfully.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule05


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 unit tests
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- SystemPerformanceManagerTest
- 
- 

EOF.
