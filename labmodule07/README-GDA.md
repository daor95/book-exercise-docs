# Gateway Device Application (Connected Devices)

## Lab Module 07

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-GDA-07-000: Git "labmodule07" branch created.
  
- PIOT-GDA-07-001: The **MqttClientConnector** class has been implemented within the `programmingtheiot.cda.connection` package to provide MQTT communication capabilities for the application. This class implements both the `IPubSubClient` and `MqttCallbackExtended` interfaces and includes logic for initializing and configuring a MQTT client using values retrieved from a configuration file. The constructor sets up the broker address, port, keep-alive interval, and other connection options such as clean sessions and auto-reconnect. The core functionality for establishing and terminating connections with the MQTT broker has been implemented in the `connectClient()` and `disconnectClient()` methods, with appropriate logging and exception handling. Placeholder methods for publishing, subscribing, unsubscribing, and message handling have been added, with detailed implementation to follow in future exercises. Additionally, a method to set the `IDataMessageListener` has been included, and the necessary callback methods for MQTT events have been stubbed out. This setup ensures that the class is ready for integration testing and can successfully establish and close connections with an MQTT broker as verified by the `testConnectAndDisconnect()` test.

- PIOT-GDA-07-002: The **MqttClientConnector** class has been updated to implement the MQTT callback methods defined by the `MqttCallbackExtended` interface. These include `connectComplete()`, which now logs a confirmation message when the client connects or reconnects to the broker; `connectionLost()`, which logs a warning when the connection to the broker is lost; and `deliveryComplete()`, which logs the successful delivery of a published message. Additionally, the `messageArrived()` method has been implemented to log a message whenever a new MQTT message is received on a subscribed topic. These enhancements allow the connector to provide real-time feedback on MQTT communication events, improving visibility into connection status and message flow. With these additions, the `testConnectAndDisconnect()` integration test confirms that the client not only connects and disconnects successfully but also logs relevant MQTT events.

  

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule07


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 unit tests
- All part02 unit tests
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- All part02 integration tests
- MqttClientConnectorTest (testConnectAndDisconnect())

EOF.
