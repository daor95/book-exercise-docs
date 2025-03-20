# Gateway Device Application (Connected Devices)

## Lab Module 01

Be sure to implement all the PIOT-GDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

- The GDA (Gateway Device Application) development environment was set up using the java-components repository, which was created using a template from the provided original GitHub repository.
- IntelliJ has been configured to use for this project.
- POM.xml file was edited to set JDK to version 21 for compatibility.
- Tests have passed and app has been executed.


How does your implementation work?

- A new branch labmodule01 was created (git checkout -b labmodule01).
- The GDA application was successfully built using Maven with `mvn install -DskipTests`. This command was used to generate an executable JAR file while skipping tests to avoid early failures.
- GDA application was run from the command line with `java -jar target/gateway-device-app-0.0.1-jar-with-dependencies.jar`. The application successfully initialized, started, and exited with a proper status.
- Unit and Integration Tests were successfully executed.
- Code updates have been reviewed and verified. A merge between `labmodule01` and the `default` branch was performed. The merged work was pushed to the remote repository.

  

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule01


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
- 
- 

EOF.
