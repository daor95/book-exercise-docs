# Constrained Device Application (Connected Devices)

## Lab Module 12 - Semester Project - CDA Components

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

**What does your implementation do?**
The `labmodule12` branch of the `python-components` repository focuses on the emulation and testing of a new IoT actuator component, specifically the Fire Extinguisher Actuator. It implements a sensor to detect the size of smoke particles ando to activate a remote fire extinguishing system if the size is above a predefite threshold.

**How does your implementation work?**
A new sensor and actuator have been successfully implemented. The new components include a **SmokeDetector** sensor, which estimates the level of smoke particles in the air, and a **FireExtinguisher** actuator, designed to simulate a fire suppression system that activates in response to an alarm.
To support this implementation, several new modules were created along with their corresponding test cases. The `PiotConfig.props` file was updated to include the simulated minimum and maximum values for the SmokeDetector sensor (0 and 35 µg/m³ PM2.5 smoke particles). Additional configuration entries, such as the sensor and actuator names and types, were also added following the established format used for previous components. Updates were made to the `SensorAdapterManager`, `ActuatorAdapterManager`, and `SensorDataGenerator` modules to incorporate the necessary logic for the new devices. Emulation and simulation modules (`SmokeDetectorSensorEmulatorTask`, `FireExtinguisherActuatorEmulatorTask`, `SmokeDetectorSensorSimTask`, and `FireExtinguisherActuatorSimTask`) were developed to replicate the behavior of the new components. Finally, a set of basic tests was implemented to ensure the new functionality works as expected.


Steps:
 
- PIOT-CDA-12-000: Git "labmodule12" branch created.

- PIOT-CDA-12-001: I have tried to add a new sensor. Due to the complexity and the deadline of the project, this impementation is not complete.
  Most of the required points were already implemented in previous PIOTs.
  
  - **Smoke Detector Sensor Support**:  
    - Added `SmokeDetectorSensorEmulatorTask` to generate random smoke sensor values.
    - Updated `SmokeDetectorSensorSimTask` to use correct smoke particle size limits.
    - Added smoke detection capabilities to `SensorAdapterManager`.
    - Updated `ConfigConst` with new sensor names, types, and simulation keys for smoke and fire detection.
    - Updated configuration (`PiotConfig.props`) to include smoke simulation limits.
 
  - **Fire Extinguisher Actuator Support**:  
    - Added `FireExtinguisherActuatorSimTask` for actuator simulation.
    - Added `FireExtinguisherActuatorEmulatorTask` and corresponding unit test class.
    - Added support for the fire extinguisher actuator in `ActuatorAdapterManager`.
  
  - **Testing**:  
    - Added integration tests for `FireExtinguisherActuatorEmulatorTaskTest` and `SmokeDetectorSensorEmulatorTask`.
    - Added unit tests for `FireExtinguisherActuatorSimTaskTest` and `SmokeDetectorSensorSimTaskTest`.

  The generated data of the smoke detector is being sent to the cloud through the GDA as illustrated:
![smoke](https://github.com/user-attachments/assets/ac6ee21d-b428-44f8-9e3a-1f34fee43e1c)

  
  
 - PIOT-CDA-12-100: The code updates within the labmodule12 branch were reviewed and verified. All part01, part02, part03 and part04 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule12 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.




### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule12


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01, part02 and part03 unit tests
- FireExtinguisherActuatorSimTaskTest
- SmokeDetectorSensorSimTaskTest


### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01, part02, part03 and part04 integration tests
- SmokeDetectorEmulatorTaskTest
- FireExtinguisherActuatorEmulatorTaskTest

EOF.
