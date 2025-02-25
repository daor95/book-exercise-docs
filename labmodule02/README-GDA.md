# Gateway Device Application (Connected Devices)

## Lab Module 02

Be sure to implement all the PIOT-GDA-* issues.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-GDA-02-000: New branch "labmodule02" created.
- PIOT-GDA-02-001: Implemented the `GatewayDeviceApp` class with required methods for starting, stopping, and initializing the application. Added logging for key operations and error handling using `try/catch` blocks. Integrated `initConfig` and `parseArgs` methods as placeholders for future enhancements. Implemented a `main` method to execute the app with a 65-second run cycle. Successfully ran unit and integration tests, confirming correct functionality. The methods were already implemented, the only thing done was to review the code and execute the tests.
- PIOT-GDA-02-002: Developed the `SystemPerformanceManager` module, responsible for scheduling system performance data collection tasks. Added a static variable `pollRate` and a logging instance for tracking execution. Implemented the `startManager` and `stopManager` methods to log when the manager starts and stops. The constructor initializes `pollRate` by retrieving the value from `ConfigConst.POLL_CYCLES_KEY` in the `ConfigConst.GATEWAY_DEVICE` section using `ConfigUtil`; if undefined, it defaults to `ConfigConst.DEFAULT_POLL_CYCLES`. Successfully passed the `SystemPerformanceManagerTest` integration test, producing the expected output.
- PIOT-GDA-02-003: Integrated `SystemPerformanceManager` into `GatewayDeviceApp` to enable automatic start and stop alongside the main application. Added a class-scoped variable `sysPerfMgr` and initialized it in the `GatewayDeviceApp` constructor. Modified the `startApp()` method to call `sysPerfMgr.startManager()`, ensuring it starts successfully, and updated `stopApp(int code)` to call `sysPerfMgr.stopManager()` before exiting. Successfully ran `GatewayDeviceAppTest`, confirming expected logging output and correct functionality of system performance management within the application lifecycle.
- PIOT-GDA-02-004: Developed the `BaseSystemUtilTask` class in the system package to serve as the foundation for all system performance tasks. The class includes two core attributes: `name` (defaulting to a constant value) and `typeID` (defaulting to a defined default). A constructor initializes these variables, and getter methods provide access to them. An abstract method `getTelemetryValue()` is defined, requiring subclasses to implement the logic for retrieving telemetry data. This implementation establishes a common structure for future performance tasks.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule02


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ConfigUtilTest
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- GatewayDeviceAppTest
- SystemPerformanceManagerTest
- 

EOF.
