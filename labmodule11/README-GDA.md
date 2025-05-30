# Gateway Device Application (Connected Devices)

## Lab Module 11

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-CFG-11-001: A cloud-based IoT connectivity solution was successfully configured using the Ubidots STEM platform, which supports MQTT with TLS encryption and token-based authorization. An account was created on Ubidots STEM specifically for educational purposes, and a secure API token was generated to enable client authentication. This token was saved locally in a separate configuration file.
In addition, the necessary root certificate required for establishing a TLS connection with the Ubidots MQTT broker was downloaded and stored securely on the local filesystem. The `PiotConfig.props` configuration file was then updated under the `Cloud.GatewayService` section to include the path to the certificate file, as well as connection settings such as host, ports, QoS level, and base topic structure. 


- PIOT-GDA-11-000: Git "labmodule11" branch created.

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
