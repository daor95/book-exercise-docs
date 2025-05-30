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

- PIOT-GDA-11-001: The implementation focused on enhancing the **MqttClientConnector** class to support greater flexibility and modularity when interacting with MQTT brokers. The class was updated to load configuration parameters either from the default MQTT section or from a separate cloud gateway section in the `PiotConfig.props` file. To support this, two new constructors were added: one accepting a boolean flag and another accepting a string section name. The configuration loading logic was extracted into a new private method `initClientParameters()`, aligning with the design implemented in Lab Module 10.
In addition, the class was extended with the ability to notify an external `IConnectionListener` of MQTT connection events, using a new `setConnectionListener()` method. To enable subclass and package-scoped access, new `protected` versions of `publishMessage`, `subscribeToTopic`, and `unsubscribeFromTopic` methods were introduced, each accepting raw topic strings and optionally a message listener. These were integrated into the existing public methods, which now delegate to the new implementations. The `connectComplete()` callback was updated to optionally subscribe to either local or cloud topics based on the `useCloudGatewayConfig` flag, supporting two subscription strategies. Integration test (`MqttClientConnectorTest`) was rerun using the local broker to validate backward compatibility and the correctness of the new behavior.

- PIOT-GDA-11-002: The Java interface named **ICloudClient** was created (it already existed) to define the contract for cloud-based pub/sub clients. The interface includes methods for connecting and disconnecting from the cloud service, publishing sensor and system performance data, subscribing and unsubscribing to cloud events, and setting a data message listener. This interface will serve as a foundational component for implementations such as `CloudClientConnector`, enabling standardized communication between edge devices and cloud platforms.



### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: 


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01, part02 and part03 unit tests
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01, part02 and part03 integration tests
- 
- 

EOF.
