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





### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: 


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- 
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
