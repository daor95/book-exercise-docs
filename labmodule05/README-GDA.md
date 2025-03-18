# Gateway Device Application (Connected Devices)

## Lab Module 05

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

My implementation provides a structured and extensible framework within the GDA to efficiently manage IoT device data, particularly sensor readings, actuator commands, and system performance metrics. By creating specialized data container classes (ActuatorData, SensorData, SystemPerformanceData, and SystemStateData) that inherit from the common parent class BaseIotData, my approach allows for uniform data handling and serialization. The DataUtil class leverages the Gson library to serialize these data objects into JSON, facilitating consistent communication between the Java-based GDA and the Python-based CDA. Additionally, the integration of DeviceDataManager into GatewayDeviceApp centralizes the data handling logic, allowing modular management of system performance metrics through the SystemPerformanceManager.


How does your implementation work?

At runtime, the GatewayDeviceApp initializes and starts the DeviceDataManager, which subsequently initiates the SystemPerformanceManager to periodically collect CPU, memory, and disk utilization data. This collected telemetry is stored within instances of SystemPerformanceData and dispatched using a callback mechanism provided by the IDataMessageListener interface. By adopting a modular design and utilizing centralized configuration (ConfigUtil), this implementation maintains clear separation of responsibilities and enables easy expansion of communication protocols such as MQTT, CoAP, and cloud integration in the future.


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

- PIOT-GDA-05-004: The **DeviceDataManager** class, the core processing unit of the GDA, was edited. This class is responsible for handling incoming data, processing system performance metrics, and managing communication between different system components.  
To set up the **DeviceDataManager**, I first created class-scoped variables to manage MQTT, CoAP, cloud, and persistence clients, along with a `SystemPerformanceManager` instance. The constructor was updated to retrieve configuration values from `PiotConfig.props` using `ConfigUtil`, determining which communication modules should be enabled. A private method, `initManager()`, was implemented to instantiate and configure these components.  
The `startManager()` and `stopManager()` methods were introduced to manage lifecycle operations, ensuring proper initialization and cleanup of system components (not yet implemented at this Lab Exercise). Additionally, the `IDataMessageListener` interface was implemented, this will provide callback methods for handling actuator commands, sensor messages, and system performance data when fully implemented.
Finally, placeholder methods such as `handleUpstreamTransmission()` and `handleIncomingDataAnalysis()` were added to support future enhancements.
Integration test **DeviceDataManagerNoCommsTest** was successfully conducted.

- PIOT-GDA-05-005: The **DeviceDataManager** has been integrated into the **GatewayDeviceApp**, ensuring it serves as the central processing unit of the GDA. This allows for initialization, management, and termination of system components.  
**DeviceDataManager** has been instantiated as a class-scoped variable within **GatewayDeviceApp** and updated the `startApp()` and `stopApp()` methods to invoke the respective `startManager()` and `stopManager()` methods of **DeviceDataManager**. As part of this integration, all references to **SystemPerformanceManager** within **GatewayDeviceApp** were removed, consolidating its management within **DeviceDataManager**.  
Additionally, I ensured that **SystemPerformanceManager** was properly instantiated within **DeviceDataManager** (according to PIOT-GDA-05-004), and its start/stop lifecycle methods were invoked accordingly. This guarantees that system performance data is continuously collected and processed.  
**GatewayDeviceAppTest** integration test was executed, confirming that the **GDA** initializes and shuts down correctly, logging relevant system performance data.

- PIOT-GDA-05-006: Skipped optional requirement.

- PIOT-GDA-05-007: Skipped optional requirement.

- PIOT-GDA-05-008: Skipped optional requirement.

- PIOT-GDA-05-100: The code updates within the labmodule05 branch were reviewed and verified. All part01 and part02 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule05 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.


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
- All part02 unit tests

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- SystemPerformanceManagerTest
- DataIntegrationTest
- DeviceDataManagerNoCommsTest
- GatewayDeviceAppTest
- All part02 integration tests

EOF.
