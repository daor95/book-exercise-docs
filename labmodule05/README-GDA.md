# Gateway Device Application (Connected Devices)

## Lab Module 05

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?


Steps:

- PIOT-GDA-05-000: Git "labmodule05" branch created.

- PIOT-GDA-05-001: We implemented key Java modules for handling sensor and actuator data in the **GDA**, ensuring consistency with the **CDA**. The core data classes—`ActuatorData`, `SensorData`, `SystemPerformanceData`, and `SystemStateData`—were developed as extensions of `BaseIotData`, which provides common attributes like name, type ID, status code, and timestamps. These classes were designed to facilitate seamless serialization and deserialization for data exchange.  
The **`ActuatorData` class** was implemented with attributes for command execution, including `command`, `value`, `stateData`, and `isResponse`. **`SensorData`** was structured to store sensor readings, while **`SystemPerformanceData`** was created to track CPU, memory, and disk utilization. Additionally, **`SystemStateData`** was introduced as a higher-level container for aggregating multiple instances of sensor and performance data, along with system commands.
To maintain uniformity, all variable names and constants were aligned with predefined labels in the `ConfigConst` class, ensuring consistency between the Java and Python implementations.  
Finally, unit tests **(ActuatorDataTest, SensorDataTest, SystemPerformanceDataTest, SystemStateDataTest)** were executed to validate the correctness of each data container.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule05


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 unit tests
- ActuatorDataTest
- SensorDataTest
- SystemPerformanceDataTest
- SystemStateDataTest
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- 
- 

EOF.
