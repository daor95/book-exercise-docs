# Gateway Device Application (Connected Devices)

## Lab Module 02

Be sure to implement all the PIOT-GDA-* issues.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

**What does your implementation do?**  
The implementation creates a self-monitoring gateway application (GDA) that not only runs its core functionalities but also continuously tracks system performance. It integrates modules to collect telemetry data, specifically CPU and memory utilization metrics, and logs these values periodically. In addition, it ensures that performance monitoring is managed in tandem with the application's lifecycle, with proper startup, periodic execution, and shutdown.

**How does your implementation work?**  
The GatewayDeviceApp class initializes the SystemPerformanceManager, which in turn instantiates two specialized tasks—SystemCpuUtilTask and SystemMemUtilTask—that extend a common BaseSystemUtilTask. These tasks use Java’s ManagementFactory API to retrieve CPU load and JVM memory usage. The SystemPerformanceManager employs a ScheduledExecutorService to periodically execute a runnable that calls the getTelemetryValue() methods of both tasks, logging the collected performance metrics. This scheduled monitoring is started when the application begins and is cleanly stopped during shutdown. All changes are integrated using standard Git merge procedures.

Steps:

- PIOT-GDA-02-000: New branch "labmodule02" created.
- PIOT-GDA-02-001: Implemented the `GatewayDeviceApp` class with required methods for starting, stopping, and initializing the application. Added logging for key operations and error handling using `try/catch` blocks. Integrated `initConfig` and `parseArgs` methods as placeholders for future enhancements. Implemented a `main` method to execute the app with a 65-second run cycle. Successfully ran unit and integration tests, confirming correct functionality. The methods were already implemented, the only thing done was to review the code and execute the tests.
- PIOT-GDA-02-002: Developed the `SystemPerformanceManager` module, responsible for scheduling system performance data collection tasks. Added a static variable `pollRate` and a logging instance for tracking execution. Implemented the `startManager` and `stopManager` methods to log when the manager starts and stops. The constructor initializes `pollRate` by retrieving the value from `ConfigConst.POLL_CYCLES_KEY` in the `ConfigConst.GATEWAY_DEVICE` section using `ConfigUtil`; if undefined, it defaults to `ConfigConst.DEFAULT_POLL_CYCLES`. Successfully passed the `SystemPerformanceManagerTest` integration test, producing the expected output.
- PIOT-GDA-02-003: Integrated `SystemPerformanceManager` into `GatewayDeviceApp` to enable automatic start and stop alongside the main application. Added a class-scoped variable `sysPerfMgr` and initialized it in the `GatewayDeviceApp` constructor. Modified the `startApp()` method to call `sysPerfMgr.startManager()`, ensuring it starts successfully, and updated `stopApp(int code)` to call `sysPerfMgr.stopManager()` before exiting. Successfully ran `GatewayDeviceAppTest`, confirming expected logging output and correct functionality of system performance management within the application lifecycle.
- PIOT-GDA-02-004: Developed the `BaseSystemUtilTask` class in the system package to serve as the foundation for all system performance tasks. The class includes two core attributes: `name` (defaulting to a constant value) and `typeID` (defaulting to a defined default). A constructor initializes these variables, and getter methods provide access to them. An abstract method `getTelemetryValue()` is defined, requiring subclasses to implement the logic for retrieving telemetry data. This implementation establishes a common structure for future performance tasks.
- PIOT-GDA-02-005: Developed the `SystemCpuUtilTask` module by extending `BaseSystemUtilTask` to measure CPU utilization. The class imports necessary libraries and uses the `OperatingSystemMXBean` from `ManagementFactory` to retrieve the system load average, which is then returned as a float. This method effectively captures CPU performance metrics for systems that support this functionality. The implementation meets the requirements and passes the corresponding unit tests, validating correct behavior on supported operating systems.
- PIOT-GDA-02-006: Implemented the `SystemMemUtilTask` class by extending `BaseSystemUtilTask` to monitor JVM memory usage. The task retrieves heap memory statistics using the `ManagementFactory.getMemoryMXBean()`, calculates the percentage of memory used relative to the maximum available, and logs the computed value. The resulting memory utilization is returned as a float, ensuring compatibility with the expected telemetry format. This implementation successfully meets the functional requirements and passes all associated unit tests.
- PIOT-GDA-02-007: Integrated the performance monitoring tasks into the SystemPerformanceManager. In this update, both the SystemCpuUtilTask and SystemMemUtilTask are instantiated within the manager's constructor, and a ScheduledExecutorService is set up using a configurable poll rate. A dedicated Runnable (taskRunner) periodically calls the handleTelemetry() method, which retrieves and logs the CPU and memory utilization metrics. The startManager() method schedules this recurring task, while the stopManager() method properly shuts down the scheduler. Integration tests confirm that telemetry data is collected and logged as expected.
- PIOT-GDA-02-100: Reviewed and verified the code changes in the Lab Module 02 branch and ensured that all unit and integration tests passed successfully. Performed a git merge from the Lab Module 02 branch into the primary branch and pushed the merged changes to the remote repository. Confirmed the merge history using `git log --oneline default` to ensure that the integration was successful.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule02


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ConfigUtilTest
- SystemCpuUtilTaskTest
- SystemMemUtilTaskTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- GatewayDeviceAppTest
- SystemPerformanceManagerTest
- 

EOF.
