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
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- HumiditySensorSimTaskTest
- PressureSensorSimTaskTest
- TemperatureSensorSimTaskTest
- SensorAdapterManagerTest

EOF.
