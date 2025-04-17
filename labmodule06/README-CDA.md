# Constrained Device Application (Connected Devices)

## Lab Module 06

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?


Steps:

- PIOT-CDA-06-000: Git "labmodule05" branch created.
  
- PIOT-CDA-06-001: The **MqttClientConnector** class was implemented within the `programmingtheiot.cda.connection` package, following the `IPubSubClient` interface. The constructor was created to initialize the MQTT client properties by retrieving configuration values such as the host, port, keep-alive interval, and client ID from the `PiotConfig.props` file using the `ConfigUtil` class. The `connectClient()` method was implemented to establish a connection to the MQTT broker and start the MQTT network loop, while the `disconnectClient()` method was added to properly disconnect from the broker and stop the loop. The remaining interface methods—`publishMessage()`, `subscribeToTopic()`, and `setDataMessageListener()`—were stubbed with basic logging statements for future development. Finally, the `testConnectAndDisconnect()` integration test was run to verify that the MQTT client could successfully connect to and disconnect from the broker.

- PIOT-CDA-06-002: Callback methods were added to the **MqttClientConnector** class to handle key MQTT client events. These included `onConnect()`, `onDisconnect()`, `onMessage()`, `onPublish()`, and `onSubscribe()`, each responsible for logging relevant information during their respective events. The `onConnect()` method logs a message when a connection to the broker is established, while `onDisconnect()` confirms when the client disconnects. The `onMessage()` method logs incoming messages and decodes their payload, preparing for future data handling. The `onPublish()` and `onSubscribe()` methods provide confirmation logs when messages are published or when topic subscriptions are acknowledged. These callbacks were registered in the `connectClient()` method before establishing the MQTT connection. Once completed, the changes were verified using the `testConnectAndDisconnect()` integration test, confirming successful connection and disconnection along with appropriate event logging.

- PIOT-CDA-06-003: Full publish and subscribe capabilities were implemented in the **MqttClientConnector** class. The `publishMessage()` method was added to validate the topic and message content before publishing to the MQTT broker, ensuring the QoS level is within valid bounds or defaulting to a configured value if necessary. Similarly, the `subscribeToTopic()` method was implemented to allow topic subscriptions with appropriate validation and logging, while the `unsubscribeFromTopic()` method enables clean unsubscription from a topic. These methods ensure communication handling and integration with the MQTT broker. With these additions, the `testConnectAndCDAManagementStatusPubSub()` integration test was enabled and successfully executed, confirming that the publish, subscribe, and callback mechanisms—such as `onConnect()`, `onSubscribe()`, `onPublish()`, `onMessage()`, and `onDisconnect()`—are functioning as expected and producing the appropriate log output during test execution.



### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: 


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
- MqttClientConnectorTest (testConnectAndCDAManagementStatusPubSub())

EOF.
