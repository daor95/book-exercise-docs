# Gateway Device Application (Connected Devices)

## Lab Module 08

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-CFG-08-001: The Californium CoAP tools were successfully installed and configured on Ubuntu to facilitate initial testing of a CoAP server. The process involved cloning the Californium Tools repository from GitHub and building the project using Maven. Upon successful compilation, the key components (`cf-client` and `cf-server`) were verified to be generated within their respective `target` directories.

![coap_help](https://github.com/user-attachments/assets/6df15647-8e13-4235-94dc-479e22f384e4)

The command-line client tool was validated for functionality by executing it with the `--help` flag, confirming the availability of supported parameters. A functional test was performed by starting the CoAP server (`cf-server`) and sending a test GET request using the client (`cf-client`). Console outputs confirmed that the request was successfully sent and received, demonstrating correct setup and basic operation of the CoAP tools. The server was then properly terminated using standard interrupt commands.

Server:

![coap_server](https://github.com/user-attachments/assets/b7f9d357-6364-4dc2-9829-715fc6ecd91c)

Client:

![coap_client](https://github.com/user-attachments/assets/8f223da1-f296-46e3-9c31-efe3f1c09bb8)

```text
==[ CoAP Request ]=============================================
MID    : 28601
Token  : 345EE8D5F0473E6D
Type   : CON
Method : 0.01 - GET
Options: {"Uri-Host":"localhost"}
Payload: 0 Bytes
===============================================================

>>> UDP(localhost/127.0.0.1:5683)

Time elapsed (ms): 31
==[ CoAP Response ]============================================
MID    : 28601
Token  : 345EE8D5F0473E6D
Type   : ACK
Status : 2.05 - CONTENT
Options: {"Content-Format":"text/plain"}
RTT    : 31 ms
Payload: 471 Bytes
---------------------------------------------------------------
****************************************************************
CoAP RFC 7252                                  Cf 4.0.0-SNAPSHOT
****************************************************************
```

- PIOT-CFG-08-002: "aiocoap" has been installed.

- PIOT-GDA-08-000: Git "labmodule08" branch created.

- PIOT-GDA-08-001: The **CoapServerGateway** class was implemented within the `programmingtheiot.gda.connection` package to enable CoAP server functionality. The implementation utilized the Eclipse Californium CoAP library and was developed following Option 1, creating a constructor that accepts an `IDataMessageListener` instance and instantiates the CoAP server without default resource handlers.
The necessary import statements were included, along with static initializers (`CoapConfig.register()` and `UdpConfig.register()`) to ensure compatibility with Californium versions greater than 3.8.0. Core class-scoped variables (`coapServer` and `dataMsgListener`) were declared, and the constructor was defined to assign the listener and invoke the `initServer()` method. Although the `initServer()` method currently contains no logic, it establishes the structure for adding resource handlers in future modules.
A convenience method, `setDataMessageListener`, was added to allow reconfiguration of the listener reference if needed. Additionally, methods to start and stop the CoAP server were implemented, including logic to trace messages via the `MessageTracer` interceptor and to handle exceptions.
Integration with the **DeviceDataManager** class was completed by introducing a configuration flag (`enableCoapServer`) that enables or disables CoAP server support based on the properties defined in `PiotConfig.props`. Conditional logic was added to the `initManager()`, `startManager()`, and `stopManager()` methods to initialize and control the lifecycle of the CoAP server accordingly. A class-scoped instance of `CoapServerGateway` was instantiated and managed within `DeviceDataManager` when CoAP functionality is enabled.

- PIOT-GDA-08-002: Two new CoAP resource handler classes, **UpdateSystemPerformanceResourceHandler** and **UpdateTelemetryResourceHandler**, were created and implemented within the `programmingtheiot.gda.connection.handlers` package. These classes were modeled after the provided `GenericCoapResourceHandler` and extend the `CoapResource` class from the Eclipse Californium CoAP library. Each class includes a one-argument constructor that accepts a resource name, which is passed to the superclass, and a class-scoped variable to store a reference to an `IDataMessageListener`. A `setDataMessageListener` method was implemented in both classes to allow message callbacks to the `DeviceDataManager`.
All four CoAP methods—`handleGET`, `handlePUT`, `handlePOST`, and `handleDELETE`—were overridden in both handler classes. For initial testing, each method logs a message and sends an appropriate response to the client using the `CoapExchange` object. The primary logic was focused on the `handlePUT` method, which receives and logs the payload, accepts the request, and attempts to parse the incoming JSON string into either a `SystemPerformanceData` or `SensorData` object.
Upon successful parsing, the data is forwarded to the `DeviceDataManager` via the `dataMsgListener`, and a `CHANGED` response is returned. In the event of a failure, appropriate fallback response codes (`BAD_REQUEST`, `CONTINUE`, etc.) are used, and exceptions are logged for debugging.

- PIOT-GDA-08-003: A new CoAP resource handler class named **GetActuatorCommandResourceHandler** was created and implemented in the `programmingtheiot.gda.connection.handlers` package. Modeled after `GenericCoapResourceHandler`, this class enables the GDA to support CoAP **OBSERVE** functionality, allowing the CDA to receive asynchronous actuation command updates after initiating a GET request.
The class extends `CoapResource` and implements the `IActuatorDataListener` interface, requiring the implementation of the `onActuatorDataUpdate(ActuatorData data)` method. This method updates the stored actuator data and notifies all observing clients using the `changed()` method, ensuring that the CDA receives the latest actuation commands in real time.
A constructor accepting a single resource name was implemented. Within it, the resource was marked observable using `setObservable(true)`. A class-scoped `ActuatorData` variable was defined to maintain the latest actuation state.
The `handleGET()` method was overridden to accept incoming GET requests, convert the current `ActuatorData` instance to JSON using `DataUtil`, and respond with this data using an appropriate CoAP `ResponseCode.CONTENT` response. The implementation includes logging for debugging and proper handling of the request lifecycle via `CoapExchange`.

- PIOT-GDA-08-004: The `CoapServerGateway` and `DeviceDataManager` components were significantly extended to enable full support for dynamic and static CoAP resource handler registration and to integrate actuator command notification using the CoAP OBSERVE pattern.
In the **`DeviceDataManager`**, support for actuator data listeners was added by implementing the `setActuatorDataListener(String name, IActuatorDataListener listener)` method. A class-scoped variable was used to hold a single listener instance, preparing the system for future actuator command delivery from the GDA to the CDA. Additionally, the private method `handleIncomingDataAnalysis` was optionally updated to forward incoming actuator data to the registered listener, ensuring the system is ready for Lab Module 10’s functionality.
In the **`CoapServerGateway`**, the `initServer()` method was fully implemented to initialize the `CoapServer` instance and register default CoAP resources by internally creating and organizing them in a hierarchical structure based on `ResourceNameEnum` values. A helper method, `initDefaultResources()`, was introduced to create and configure instances of the resource handlers: `GetActuatorCommandResourceHandler`, `UpdateTelemetryResourceHandler` and `UpdateSystemPerformanceResourceHandler`.
Each handler was properly initialized and, where applicable, associated with the `IDataMessageListener`. The `addResource()` method and a supporting `createAndAddResourceChain()` helper were implemented to break down the resource names into a hierarchical chain (e.g., `PIOT/ConstrainedDevice/SystemPerfMsg`) and register them into the server.
Both internal (default) and external resource registration strategies were supported, providing the flexibility to add resource handlers at runtime or during server initialization.
Finally, an integration test named **`CoapServerGatewayTest`** was created under the `programmingtheiot.part03.integration.connection` package. This test launched the CoAP server, performed a resource discovery using a `CoapClient`, and logged the discovered resource URIs and their attributes. After a wait period, the server was stopped. The test confirmed correct resource registration and server behavior.
Additional CLI tests were conducted using the Californium tools to send GET and POST requests to specific resources, verifying that the server handled requests as expected. These resources included: `/PIOT/ConstrainedDevice/SensorMsg` and `/PIOT/ConstrainedDevice/SystemPerfMsg`.
The results are:

```text
java -jar cf-client-4.0.0-SNAPSHOT.jar --method=GET coap://localhost:5683/PIOT/ConstrainedDevice/SystemPerfMsg
04:28:57.608 WARN [Configuration]: Add missing module TCP.
==[ CoAP Request ]=============================================
MID    : 32638
Token  : 588AEA48FCAC53DF
Type   : CON
Method : 0.01 - GET
Options: {"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}
Payload: 0 Bytes
===============================================================

>>> UDP(localhost/127.0.0.1:5683)

Time elapsed (ms): 11
==[ CoAP Response ]============================================
MID    : 18283
Token  : 588AEA48FCAC53DF
Type   : CON
Status : 2.04 - CHANGED
Options: {"Content-Format":"text/plain"}
RTT    : 11 ms
Payload: 54 Bytes
---------------------------------------------------------------
Update system perf data request handled: SystemPerfMsg
===============================================================
```

```text
java -jar cf-client-4.0.0-SNAPSHOT.jar --method=GET coap://localhost:5683/PIOT/ConstrainedDevice/SensorMsg
05:19:59.084 WARN [Configuration]: Add missing module TCP.
==[ CoAP Request ]=============================================
MID    : 10740
Token  : A4368B174EBDA421
Type   : CON
Method : 0.01 - GET
Options: {"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}
Payload: 0 Bytes
===============================================================

>>> UDP(localhost/127.0.0.1:5683)

Time elapsed (ms): 35
==[ CoAP Response ]============================================
MID    : 54961
Token  : A4368B174EBDA421
Type   : CON
Status : 2.03 - VALID
Options: {"Content-Format":"text/plain"}
RTT    : 35 ms
Payload: 47 Bytes
---------------------------------------------------------------
Generic handler. No GET action taken: SensorMsg
===============================================================
```





### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule08


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 and part02 unit tests
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 and part02 integration tests
- CoapClientToServerConnectorTest
- CoapServerGatewayTest

EOF.
