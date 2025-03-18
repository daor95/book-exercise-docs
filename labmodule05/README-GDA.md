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

- PIOT-GDA-05-002: The **SystemPerformanceManager** class has been updated to store system performance data collected by the `handleTelemetry()` method within instances of **SystemPerformanceData**. Additionally, callback functionality has been integrated using the `IDataMessageListener` interface to allow real-time handling of performance data.  
To achieve this, two new class-scoped variables have been introduced within **SystemPerformanceManager**: `locationID` to store the device’s location, retrieved from the configuration file, and `dataMsgListener` to manage callbacks. The `handleTelemetry()` method was modified to capture **CPU and memory utilization** values and store them within a new `SystemPerformanceData` instance, which is then passed to the `handleSystemPerformanceMessage()` method if a listener is registered. `setDataMessageListener()` method was also implemented to enable future callback support.
Integration test **SystemPerformanceManagerTest** was successful executed.

- PIOT-GDA-05-003:  In this module, the **DataUtil** class was implemented and refined to handle JSON serialization and deserialization for key data objects in the GDA. Using the **Gson** library, we developed conversion methods to transform `ActuatorData`, `SensorData`, `SystemPerformanceData` and `SystemStateData` instances into JSON format and vice versa.  
The **DataUtil** class was structured with eight core methods: four for converting objects to JSON strings and four for reconstructing objects from JSON. These methods ensure that system data can be efficiently stored, transmitted, and reloaded while maintaining consistency across the GDA and the CDA.  
Unit test **DataUtilTest** and Integration test **DataIntegrationTest** were executed successfully, being the integration test dependant of the execution of equivalent test in the CDA.


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
- DataUtilTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- SystemPerformanceManagerTest
- DataIntegrationTest

EOF.
