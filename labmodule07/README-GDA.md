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

- PIOT-GDA-07-003: The **MqttClientConnector** class has been enhanced to support MQTT publish and subscribe capabilities. The `publishMessage()` method was implemented to validate the topic and QoS level, convert the message to a byte payload, and publish it to the specified topic, with error handling and logging. Similarly, the `subscribeToTopic()` and `unsubscribeFromTopic()` methods were implemented to manage topic subscriptions and ensure valid parameters, while also logging the outcomes. A class-level `DEFAULT_QOS` constant was introduced to handle invalid QoS values. Additionally, the `isConnected()` method was completed to accurately reflect the client’s current connection status. These updates enable the class to participate fully in MQTT communication, including receiving and handling callbacks for successful delivery (`deliveryComplete`) and incoming messages (`messageArrived`). With these changes, the integration test `testPublishAndSubscribe()` now executes successfully, demonstrating that the class can reliably connect to a broker, manage topic subscriptions, publish messages, and receive them through proper callback handling.

- PIOT-GDA-07-004: The **MqttClientConnector** has been integrated into the **DeviceDataManager** to enable end-to-end MQTT-based communication within the Gateway Device Application (GDA). A class-scoped boolean flag `enableMqttClient` now controls whether MQTT functionality should be used, allowing dynamic configuration through `ConfigUtil`. When enabled, the `MqttClientConnector` is instantiated and initialized during the `initManager()` method, with the `DeviceDataManager` also registered as the `IDataMessageListener`. The `startManager()` method connects to the MQTT broker and subscribes to essential topics using default QoS, while `stopManager()` ensures clean disconnection by unsubscribing from the topics and terminating the connection. To validate the implementation, a dedicated integration test class `MqttClientControlPacketTest` was created, successfully exercising all 14 MQTT 3.1.1 control packet types through a sequence of connection, publishing with QoS levels 0, 1, and 2, subscription management, and ping handling based on the configured keep-alive interval.

  

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
- MqttClientConnectorTest (testPublishAndSubscribe())
- MqttClientControlPacketTest (custom test)

EOF.
