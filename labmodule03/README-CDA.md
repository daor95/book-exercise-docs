# Constrained Device Application (Connected Devices)

## Lab Module 03

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?


Steps:

- PIOT-CDA-03-000: Git "labmodule03" branch created.
- PIOT-CDA-03-001: Refined several Python modules that serve as data containers for an IoT system. A base class has been implemented, BaseIotData, which provides common properties like name, type ID, status code, and a timestamp, and then derived three specialized classes from it:
-- **SensorData:** This class manages sensor values (as floats), includes getter and setter methods for the value, and updates the timestamp whenever the value changes.
-- **ActuatorData:** This class handles actuator commands with support for both numeric (float) values and string-based state data. It includes methods to get and set the value, command, and state data, and features an update mechanism to refresh its data based on another instance.
-- **SystemPerformanceData:** This class collects system performance metrics (CPU and memory utilization) with appropriate accessors that update the timestamp upon modification.
I also ensured that the default constants from ConfigConst are properly used for initializing values, and that the variable names and types remain consistent for future integration with the GDA code base. Finally, the implementation has been structured so that all relevant unit tests pass, confirming the correct functionality.

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule03

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ActuatorDataTest
- SensorDataTest
- SystemPerformanceDataTest
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- 
- 
- 

EOF.
