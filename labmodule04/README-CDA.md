# Constrained Device Application (Connected Devices)

## Lab Module 04

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?



- PIOT-CFG-04-001: The Sense-Emu Sense HAT emulator and its supporting libraries were successfully installed and configured on Linux. The installation process began by installing the necessary `GTK` and `PyGObject` dependencies. Followed by setting up the `Sense-Emu emulator`. After completing the installation, the emulator’s graphical interface was tested using `sense_emu_gui`, confirming that it launched correctly.
Next, the `pisense` library was installed, but an issue arose due to compatibility problems with Python 3.12 and the Pillow library. Specifically, a deprecated function in `pisense/anim.py` caused an error when running the emulator tests. To resolve this, the faulty line in `anim.py` was modified, replacing the deprecated `textlength()` function with `textbbox()`, ensuring compatibility with the updated Python and Pillow versions.
With the Pisense issue resolved, integration tests were conducted using `SenseHatEmulatorQuickTest`. The test was executed successfully, displaying the expected scrolling messages in the emulator’s GUI, including “hello world,” “welcome,” and “temperature is.” The command-line output confirmed that all tests passed without errors, verifying that the emulator was fully operational.
- PIOT-CDA-04-000: Git "labmodule04" branch created.
- PIOT-CDA-04-001: Implementation of the **sensor emulator tasks** using the **Sense-Emu** emulator. The goal was to create Python modules that simulate the behavior of real sensors, all derived from `BaseSensorSimTask`. These modules include **HumiditySensorEmulatorTask, PressureSensorEmulatorTask, and TemperatureSensorEmulatorTask**. Each module was configured to use the Pisense library and retrieve sensor data from the emulator.
The `SenseHAT` instance was set up in emulation mode by loading the `enableEmulator` flag from the configuration file. Each sensor task was implemented following a similar structure: retrieving the respective sensor value (`humidity`, `pressure`, or `temperature`), storing the data in a `SensorData` object, and returning it for further processing.
Once the implementation was completed, integration tests were executed to verify correct functionality. The Sense-Emu GUI (`sense_emu_gui`) was launched to ensure the emulator was running. The tests were executed using `unittest`, validating each sensor task independently:
- **HumiditySensorEmulatorTask** was tested using `HumidityEmulatorTaskTest.py`, confirming correct retrieval and logging of humidity values.
- **PressureSensorEmulatorTask** was tested using `PressureEmulatorTaskTest.py`, successfully retrieving pressure data.
- **TemperatureSensorEmulatorTask** was tested using `TemperatureEmulatorTaskTest.py`, validating the proper simulation of temperature readings.
All tests passed successfully.




### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule04


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
- HumidityEmulatorTaskTest
- PressureEmulatorTaskTest
- TemperatureEmulatorTaskTest
- 

EOF.
