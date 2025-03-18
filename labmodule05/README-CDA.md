# Constrained Device Application (Connected Devices)

## Lab Module 05

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

My implementation enhances the CDA system by introducing JSON serialization and deserialization capabilities within the Python-based DataUtil class. Specifically, it supports converting ActuatorData, SensorData, and SystemPerformanceData objects into JSON-formatted strings and vice versa. Additionally, I updated the SystemPerformanceManager to systematically collect and encapsulate CPU and memory utilization statistics within the SystemPerformanceData object. It will also trigger notifications via a callback mechanism (IDataMessageListener) whenever new system performance data becomes available.


How does your implementation work?

The approach works by defining a utility class named DataUtil that leverages Python's built-in json module for serialization. A custom encoder class, JsonDataEncoder, transforms IoT data objects into dictionaries for easy JSON formatting. Within DataUtil, dedicated methods handle both serialization (object to JSON) and deserialization (JSON to object). The serialization methods use the json.dumps() function along with JsonDataEncoder to produce JSON strings, while deserialization methods first sanitize the JSON string, load it into a dictionary using json.loads(), and then populate IoT data objects by mapping keys to object attributes dynamically. Meanwhile, the SystemPerformanceManager implementation periodically retrieves telemetry metrics through task objects, encapsulates them within a newly created SystemPerformanceData instance, and will notify registered listeners via the callback interface (data exchange between system components).


Steps: 

- PIOT-CDA-05-000: Git "labmodule05" branch created.
  
- PIOT-CDA-05-001: The **SystemPerformanceManager** class has been updated to collect and store CPU and memory utilization data within `SystemPerformanceData`. The `handleTelemetry()` method now instantiates `SystemPerformanceData`, assigns the utilization values, and triggers a callback if an `IDataMessageListener` is set. Additionally, the `setDataMessageListener()` method has been implemented to enable future callback support. 
Integration test **SystemPerformanceManagerTest** was executed successfully.

- PIOT-CDA-05-002: The **DataUtil** class in Python has been implemented to handle JSON serialization and deserialization for `ActuatorData`, `SensorData`, and `SystemPerformanceData`. This allows data conversion between object representations and JSON strings, enabling easy data exchange within the system.  
A `JsonDataEncoder` class has been created with a `default` method to convert objects into dictionaries for serialization. The `DataUtil` class includes methods to convert objects to JSON (`actuatorDataToJson`, `sensorDataToJson`, and `systemPerformanceDataToJson`) and back from JSON to objects (`jsonToActuatorData`, `jsonToSensorData`, and `jsonToSystemPerformanceData`). These methods rely on a private helper function to format the JSON data, load it into a dictionary, and update the respective IoT data object.  
Unit test **DataUtilTest** for `DataUtil` has been successfully executed, confirming that JSON conversion works correctly for all supported data types.

- PIOT-CDA-05-003: Skipped optional requirement.
- PIOT-CDA-05-004: Skipped optional requirement.

- PIOT-CDA-05-002 DEFERRED: The integration test **DataIntegrationTest** has also been run successfully to validate the interoperability between the **CDA and GDA**.

- PIOT-CDA-05-100: The code updates within the labmodule05 branch were reviewed and verified. All part01 and part02 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule05 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.

  

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule05


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 unit tests
- DataUtilTest
- All part02 unit tests

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- SystemPerformanceManagerTest
- DataIntegrationTest
- All part02 integration tests

EOF.
