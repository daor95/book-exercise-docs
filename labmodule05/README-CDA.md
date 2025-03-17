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

- PIOT-CDA-05-002: The **DataUtil** class in Python has been implemented to handle JSON serialization and deserialization for `ActuatorData`, `SensorData`, and `SystemPerformanceData`. This allows data conversion between object representations and JSON strings, enabling easy data exchange within the system.  
A `JsonDataEncoder` class has been created with a `default` method to convert objects into dictionaries for serialization. The `DataUtil` class includes methods to convert objects to JSON (`actuatorDataToJson`, `sensorDataToJson`, and `systemPerformanceDataToJson`) and back from JSON to objects (`jsonToActuatorData`, `jsonToSensorData`, and `jsonToSystemPerformanceData`). These methods rely on a private helper function to format the JSON data, load it into a dictionary, and update the respective IoT data object.  
Unit test **DataUtilTest** for `DataUtil` has been successfully executed, confirming that JSON conversion works correctly for all supported data types.
# TODO Deferred: The integration test **DataIntegrationTest** has also been run to validate the interoperability between the **CDA and GDA**


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule05


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 unit tests
- DataUtilTest
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- SystemPerformanceManagerTest
- DataIntegrationTest
- 

EOF.
