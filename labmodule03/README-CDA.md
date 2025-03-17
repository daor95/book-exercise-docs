# Constrained Device Application (Connected Devices)

## Lab Module 03

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

My implementation provides a structured framework for managing IoT-related data, simulating sensors, controlling actuators, and coordinating system performance within a constrained device environment. At the core are specialized data classes (e.g., SensorData, ActuatorData, SystemPerformanceData) that encapsulate sensor readings, actuation commands, and performance metrics. Building on these data classes, I have created simulator tasks for sensors (humidity, pressure, temperature) and actuators (humidifier, HVAC), enabling the system to generate realistic telemetry data or respond to commands as if real-world hardware were present. Finally, manager classes (e.g., SensorAdapterManager, ActuatorAdapterManager, and DeviceDataManager) orchestrate data flow between these tasks, ensuring telemetry is collected at a defined interval, actuator commands are processed, and system performance updates are tracked. By integrating everything into the main ConstrainedDeviceApp, the implementation allows a smooth startup, operation, and shutdown sequence.


How does your implementation work?

First, there is a common parent class called `BaseIotData` that stores basic information for all IoT data, such as an ID, a type, a name, and a status. Other classes like `SensorData`, `ActuatorData`, and `SystemPerformanceData` build on this parent class by adding extra fields or methods. For example, `SensorData` tracks sensor values, while `ActuatorData` tracks the last command or state of an actuator.

Next, there are “simulator” classes for sensors and actuators. A class called `BaseSensorSimTask` creates or updates sensor readings. Then, specialized sensor classes (for humidity, pressure, or temperature) inherit from it to produce realistic readings. On the actuator side, `BaseActuatorSimTask` simulates commands and activations. Classes like `HVAC` or `Humidifier` extend this to model specific devices.

Managers tie these tasks together. The `SensorAdapterManager` schedules and collects readings from all sensors, while the `ActuatorAdapterManager` sends commands to the actuators. The `DeviceDataManager` oversees everything, including system performance checks. Lastly, `ConstrainedDeviceApp` starts the entire process, runs it, and then stops everything when finished. Once all tests pass, the code is merged into the main branch.


Steps:

- PIOT-CDA-03-000: Git "labmodule03" branch created.
- PIOT-CDA-03-001: Refined several Python modules that serve as data containers for an IoT system. A base class has been implemented, BaseIotData, which provides common properties like name, type ID, status code, and a timestamp, and then derived three specialized classes from it:
-- **SensorData:** This class manages sensor values (as floats), includes getter and setter methods for the value, and updates the timestamp whenever the value changes.
-- **ActuatorData:** This class handles actuator commands with support for both numeric (float) values and string-based state data. It includes methods to get and set the value, command, and state data, and features an update mechanism to refresh its data based on another instance.
-- **SystemPerformanceData:** This class collects system performance metrics (CPU and memory utilization) with appropriate accessors that update the timestamp upon modification.
I also ensured that the default constants from ConfigConst are properly used for initializing values, and that the variable names and types remain consistent for future integration with the GDA code base. Finally, the implementation has been structured so that all relevant unit tests pass, confirming the correct functionality.
- PIOT-CDA-03-002: The BaseSensorSimTask module has been updated. In the constructor, key attributes such as dataSet, name, typeID, dataSetIndex, and the useRandomizer flag have been initialized. The getName and getTypeID methods were implemented to return the sensor’s name and type identifier, respectively. Additionally, the generateTelemetry method was developed to create a SensorData instance (either by generating a random value within specified bounds or by retrieving the next entry from a provided dataset) while the getTelemetryValue method returns the current sensor value. No tests have been executed for this module.
- PIOT-CDA-03-003: The sensor simulator task modules (HumiditySensorSimTAsk, PressureSensorSimTAsk y TemperatureSensorSimTAsk) have been created, each derived from **BaseSensorSimTask**. These modules implement specific sensor simulations with minimal functionality. The necessary class shells were provided in the **python-components** repository. Each sensor simulator inherits the foundational methods from **BaseSensorSimTask**, ensuring consistency in telemetry generation. This implementation allows for easy extension and customization of sensor behavior within the IoT system. Unit tests have been tested and passed.
- PIOT-CDA-03-004: The **BaseActuatorSimTask** module has been updated. In the constructor, key attributes such as `name`, `typeID`, `simpleName`, `lastKnownCommand`, and `lastKnownValue` have been initialized. Additionally, the `_activateActuator` and `_deactivateActuator` methods have been implemented to simulate actuator activation and deactivation, logging relevant information. 
The **updateActuator** method has also been developed to process incoming `ActuatorData` commands, execute the appropriate actuator action, and return a response. This method ensures that repeated commands are ignored unless a new value is provided and updates the last known command and value accordingly. The class is designed to be extended by specific actuator simulator tasks, which may override the activation and deactivation methods as needed. 
No unit tests have been executed for this base class.
- PIOT-CDA-03-005: The **HumidifierActuatorSimTask** and **HvacActuatorSimTask** modules have been edited, both derived from **BaseActuatorSimTask**. These modules serve as actuator simulators for a humidifier and an HVAC system, respectively, and have been implemented with minimal functionality. 
In both cases, the constructor initializes the actuator’s **name**, **typeID**, and a **simpleName** for logging purposes, following the same structure as the base class. No modifications were made to the `_activateActuator()` or `_deactivateActuator()` methods, as the default behavior provided by **BaseActuatorSimTask** is sufficient.
Unit tests for **HumidifierActuatorSimTaskTest** and **HvacActuatorSimTaskTest** have passed successfully. ON/OFF and the reported values are displayed properly.
- PIOT-CDA-03-006: The **SensorAdapterManager** module has been updated to manage sensor simulators. The class now includes a constructor that initializes key parameters using ConfigUtil, such as useEmulator, pollRate, and locationID. It also instantiates a scheduler from the apscheduler library to handle periodic telemetry updates. The `_initEnvironmentalSensorTasks()` method was implemented to configure sensor adapters for humidity, pressure, and temperature, ensuring they generate simulated data within defined thresholds.
Additionally, the `setDataMessageListener()`, `startManager()`, and `stopManager()` methods were implemented to allow external components to interact with the sensor manager. The `handleTelemetry()` method was developed to generate sensor data, set location identifiers, log generated values, and pass data to the message listener.
All unit tests passed except for `DataUtilTest.py` (as expected). Integration test **SensorAdapterManagerTest** completed successfully.
- PIOT-CDA-03-007: The **ActuatorAdapterManager** module has been implemented to manage actuator simulators efficiently. The class now includes a constructor that initializes key parameters using ConfigUtil, such as useEmulator, deviceID, and locationID. It also sets up actuator instances for a humidifier and an HVAC system using `_initEnvironmentalActuationTasks()`.
Additionally, the `setDataMessageListener()` method allows external components to register a listener for actuator messages. The `sendActuatorCommand()` method validates actuator commands before forwarding them to the appropriate actuator simulator. The implementation ensures that actuation commands are correctly processed and logged within the IoT system.
All unit tests passed except for `DataUtilTest.py` (as expected). Integration test **ActuatorAdapterManagerTest** completed successfully.
-PIOT-CDA-03-008: The **DeviceDataManager** module has been implemented to act as the core orchestrator for data handling within the CDA. This module is responsible for managing system performance tracking, sensor data acquisition, and actuator control. It integrates key managers such as `SystemPerformanceManager`, `SensorAdapterManager`, and `ActuatorAdapterManager` which instances have been created. Additionally, it implements message-handling methods to process incoming sensor readings, actuator commands, and system performance reports: `startManager()` to start the managers, `stopManager()` to stop the managers, `handleActuatorCommandMessage`, `handleActuatorCommandResponse`, `handleIncomingMessage`, `handleSensorMessage` and `handleSystemPerformanceMessage` have been implemented (together with their helper methods), with logic for local sensor analysis and actuation triggers based on environmental thresholds.
Integration test **DeviceDataManagerNoCommsTest** executed successfully.
- PIOT-CDA-03-009: The **ConstrainedDeviceApp** has been updated to include an instance of `DeviceDataManager`. The `startApp()` and `stopApp()` methods were modified to invoke `startManager()` and `stopManager()`, respectively. All references to `SystemPerformanceManager` within `ConstrainedDeviceApp` have been removed. Finally, the **ConstrainedDeviceAppTest** integration test was successfully executed, validating the correct initialization and shutdown of the system.
- PIOT-CDA-03-100: The code updates within the labmodule03 branch were reviewed and verified. All part01 and part02 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule03 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.



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
- HumidifierActuatorSimTaskTest
- HvacActuatorSimTaskTest
- BaseIotDataTest
- HumiditySensorSimTaskTest
- PressureSensorSimTaskTest
- TemperatureSensorSimTaskTest


### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- SensorAdapterManagerTest
- ActuatorAdapterManagerTest
- DeviceDataManagerNoCommsTest
- ConstrainedDeviceAppTest

EOF.
