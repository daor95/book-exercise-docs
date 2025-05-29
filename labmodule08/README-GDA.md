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

- **PIOT-STU-08-002**: Deferred tests have been executed. Some modifications have been added to **CoapClientConnector** class to enable the tests to properly run.

  **CoAP Discovery (PIOT-GDA-08-002)**:  
  The log shows a successful integration test of the CoAP server and client. **Key results**:

  - The CoAP server started and listened on a dynamically assigned UDP port.
  - The client sent a discovery request to the server.
  - The server responded with a list of available resources.

  **Resources discovered**:
  - `/PIOT`
  - `/PIOT/ConstrainedDevice`
  - `/PIOT/ConstrainedDevice/ActuatorCmd`
  - `/PIOT/ConstrainedDevice/SensorMsg`
  - `/PIOT/ConstrainedDevice/SystemPerfMsg`

  **Outcome**:  
  Communication between client and server was successful, with proper request/response handling and no errors reported.

  <details>
    <pre><code>
    05:28:09.900 [main] DEBUG org.eclipse.californium.elements.util.NetworkInterfacesUtil -- Found broadcast address /10.0.2.255 - enp0s3.
    05:28:09.901 [main] INFO org.eclipse.californium.core.network.RandomTokenGenerator -- using tokens of 8 bytes in length
    05:28:09.901 [main] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap using TokenProvider org.eclipse.californium.core.network.RandomTokenGenerator
    05:28:09.901 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap CoapEndpoint uses udp context
    05:28:09.902 [main] INFO org.eclipse.californium.core.network.stack.BlockwiseLayer -- coap BlockwiseLayer uses MAX_MESSAGE_SIZE=1024, PREFERRED_BLOCK_SIZE=512, BLOCKWISE_STATUS_LIFETIME=300000, MAX_RESOURCE_BODY_SIZE=8192, BLOCKWISE_STRICT_BLOCK2_OPTION=false
    05:28:09.902 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap Endpoint [coap://0.0.0.0:0] requires an executor to start, using default single-threaded daemon executor
    05:28:09.903 [main] DEBUG org.eclipse.californium.core.network.CoapEndpoint -- coap Starting endpoint at coap://0.0.0.0:0
    05:28:09.903 [main] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap no MessageIdProvider set, using default org.eclipse.californium.core.network.InMemoryMessageIdProvider
    05:28:09.904 [main] INFO org.eclipse.californium.elements.UDPConnector -- UDPConnector starts up 2 sender threads and 2 receiver threads
    05:28:09.904 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]]
    05:28:09.905 [main] INFO org.eclipse.californium.elements.UDPConnector -- UDPConnector listening on /[0:0:0:0:0:0:0:0]:56941, recv buf = 106496, send buf = 106496, recv packet size = 2048
    05:28:09.905 [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]]
    05:28:09.905 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Sender-0.0.0.0/0.0.0.0:0[1]]
    05:28:09.905 [UDP-Sender-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Sender-0.0.0.0/0.0.0.0:0[0]]
    05:28:09.905 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap Started endpoint at coap://[0:0:0:0:0:0:0:0]:56941
    05:28:09.905 [main] INFO org.eclipse.californium.core.network.EndpointManager -- created implicit endpoint coap://[0:0:0:0:0:0:0:0]:56941 for coap
    05:28:09.909 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L1, localhost:5683] send request
    05:28:09.909 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L1, localhost:5683] prepare retransmission for CON-GET    MID=   -1, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":[".well-known","core"]}, <empty data>
    05:28:09.911 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L1, localhost:5683] added with generated mid KeyMID[localhost/127.0.0.1:5683-28056], CON-GET    MID=28056, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":[".well-known","core"]}, <empty data>
    05:28:09.912 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L1, localhost:5683] added with generated token KeyToken[localhost/127.0.0.1:5683-84FCC8893110ADCB], CON-GET    MID=28056, Token=84FCC8893110ADCB, OptionSet={"Uri-Host":"localhost", "Uri-Path":[".well-known","core"]}, <empty data>
    05:28:09.912 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open request [KeyMID[localhost/127.0.0.1:5683-28056], KeyToken[localhost/127.0.0.1:5683-84FCC8893110ADCB]]
    05:28:09.915 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#38,UDP-Sender-0.0.0.0/0.0.0.0:0[1],5,Californium/Elements]) sent 39 bytes to localhost/127.0.0.1:5683
    05:28:09.915 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 39 bytes from 127.0.0.1:56941
    05:28:09.917 [CoapServer(main)#4] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> req CON-GET    MID=28056, Token=84FCC8893110ADCB, OptionSet={"Uri-Host":"localhost", "Uri-Path":[".well-known","core"]}, <empty data>
    05:28:09.917 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:56941-28056]
    05:28:09.923 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R2, 127.0.0.1:56941] send response null-2.05   MID=   -1, Token=null, OptionSet={"Content-Format":"application/link-format"}, "</PIOT>,</PIOT/ConstrainedDevice".. 151 bytes
    05:28:09.925 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.Exchange -- Exchange[R2, 127.0.0.1:56941, complete]!
    05:28:09.925 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.Exchange -- Remote Exchange[R2, 127.0.0.1:56941, complete] completed ACK-2.05   MID=28056, Token=84FCC8893110ADCB, OptionSet={"Content-Format":"application/link-format"}, "</PIOT>,</PIOT/ConstrainedDevice".. 151 bytes!
    05:28:09.925 [CoapServer(main)#5] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== res ACK-2.05   MID=28056, Token=84FCC8893110ADCB, OptionSet={"Content-Format":"application/link-format"}, "</PIOT>,</PIOT/ConstrainedDevice".. 151 bytes
    05:28:09.926 [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#32,UDP-Sender-0.0.0.0/0.0.0.0:5683[0],5,Californium/Elements]) sent 166 bytes to 127.0.0.1:56941
    05:28:09.926 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 166 bytes from 127.0.0.1:5683
    05:28:09.927 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- Exchange[L1, localhost:5683, complete]!
    05:28:09.927 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L1, localhost:5683, complete] for token KeyToken[localhost/127.0.0.1:5683-84FCC8893110ADCB]
    05:28:09.927 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L1, localhost:5683, complete] for MID KeyMID[localhost/127.0.0.1:5683-28056]
    05:28:09.927 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- local Exchange[L1, localhost:5683, complete] completed CON-GET    MID=28056, Token=84FCC8893110ADCB, OptionSet={"Uri-Host":"localhost", "Uri-Path":[".well-known","core"]}, acked <empty data>!
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendDiscoveryRequest
    INFO: Discovered resource: /PIOT
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendDiscoveryRequest
    INFO: Discovered resource: /PIOT/ConstrainedDevice
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendDiscoveryRequest
    INFO: Discovered resource: /PIOT/ConstrainedDevice/ActuatorCmd
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendDiscoveryRequest
    INFO: Discovered resource: /PIOT/ConstrainedDevice/SensorMsg
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendDiscoveryRequest
    INFO: Discovered resource: /PIOT/ConstrainedDevice/SystemPerfMsg
    </code></pre>
  </details>

  ---

  **SystemPerformancePutMessage (PIOT-GDA-08-002)**:
  
  CoAP PUT request for system performance message has been executed.
  The log shows a successful PUT request from the CoAP client to the system performance resource on the server. **Key results**:

    - The CoAP server initialized correctly and started listening on UDP port `35298`.
    - A PUT request was sent to `/PIOT/ConstrainedDevice/SystemPerfMsg` with a JSON payload containing system metrics (`cpuUtil`, `diskUtil`, `memUtil`).
    - The server received, acknowledged, and processed the PUT request.
    - A response with code `2.04 Changed` was returned indicating successful handling.

  **Request details**:
    - **Method**: PUT (Confirmable)
    - **Payload Format**: `application/json`
    - **Payload Content**:
      ```json
      {
        "cpuUtil": 0.0,
        "diskUtil": 0.0,
        "memUtil": 0.0,
        "name": "SysPerfData",
        "typeID": 0,
        "statusCode": 0,
        "hasError": false,
        "locationID": "gatewaydevice001",
        "latitude": 0.0,
        "longitude": 0.0,
        "elevation": 0.0
      }
      ```
  - **Response Message**: `"Update system perf data request handled: SystemPerfMsg"`

  **Outcome**:  
  The PUT request was handled successfully. The server parsed and acknowledged the data with a `2.04 Changed` response, confirming that system performance information was updated on the resource endpoint.

  <details>
    <pre><code>
    /home/pic/.jdks/openjdk-23.0.2/bin/java -javaagent:/snap/intellij-idea-community/609/plugins/java/lib/rt/debugger-agent.jar=file:///tmp/capture10431406645990037625.props -ea -Didea.test.cyclic.buffer.size=1048576 -javaagent:/snap/intellij-idea-community/609/lib/idea_rt.jar=38317 -Dkotlinx.coroutines.debug.enable.creation.stack.trace=false -Ddebugger.agent.enable.coroutines=true -Dkotlinx.coroutines.debug.enable.flows.stack.trace=true -Dkotlinx.coroutines.debug.enable.mutable.state.flows.stack.trace=true -Dfile.encoding=UTF-8 -Dsun.stdout.encoding=UTF-8 -Dsun.stderr.encoding=UTF-8 -classpath /snap/intellij-idea-community/609/lib/idea_rt.jar:/snap/intellij-idea-community/609/plugins/junit/lib/junit5-rt.jar:/snap/intellij-idea-community/609/plugins/junit/lib/junit-rt.jar:/home/pic/Documents/PIC/java-components/target/test-classes:/home/pic/Documents/PIC/java-components/target/classes:/home/pic/.m2/repository/com/google/code/gson/gson/2.11.0/gson-2.11.0.jar:/home/pic/.m2/repository/com/google/errorprone/error_prone_annotations/2.27.0/error_prone_annotations-2.27.0.jar:/home/pic/.m2/repository/junit/junit/4.13.2/junit-4.13.2.jar:/home/pic/.m2/repository/org/hamcrest/hamcrest-core/1.3/hamcrest-core-1.3.jar:/home/pic/.m2/repository/ch/qos/logback/logback-classic/1.5.18/logback-classic-1.5.18.jar:/home/pic/.m2/repository/ch/qos/logback/logback-core/1.5.18/logback-core-1.5.18.jar:/home/pic/.m2/repository/org/slf4j/slf4j-api/2.0.17/slf4j-api-2.0.17.jar:/home/pic/.m2/repository/org/apache/commons/commons-configuration2/2.11.0/commons-configuration2-2.11.0.jar:/home/pic/.m2/repository/org/apache/commons/commons-lang3/3.14.0/commons-lang3-3.14.0.jar:/home/pic/.m2/repository/org/apache/commons/commons-text/1.12.0/commons-text-1.12.0.jar:/home/pic/.m2/repository/commons-logging/commons-logging/1.3.2/commons-logging-1.3.2.jar:/home/pic/.m2/repository/commons-cli/commons-cli/1.9.0/commons-cli-1.9.0.jar:/home/pic/.m2/repository/org/eclipse/paho/org.eclipse.paho.mqttv5.client/1.2.5/org.eclipse.paho.mqttv5.client-1.2.5.jar:/home/pic/.m2/repository/org/eclipse/californium/californium-core/3.12.1/californium-core-3.12.1.jar:/home/pic/.m2/repository/org/eclipse/californium/californium-legal/3.12.1/californium-legal-3.12.1.jar:/home/pic/.m2/repository/org/eclipse/californium/element-connector/3.12.1/element-connector-3.12.1.jar:/home/pic/.m2/repository/org/eclipse/californium/scandium/3.12.1/scandium-3.12.1.jar:/home/pic/.m2/repository/redis/clients/jedis/5.2.0/jedis-5.2.0.jar:/home/pic/.m2/repository/org/apache/commons/commons-pool2/2.12.0/commons-pool2-2.12.0.jar:/home/pic/.m2/repository/org/json/json/20240303/json-20240303.jar:/home/pic/.m2/repository/com/influxdb/influxdb-client-java/7.2.0/influxdb-client-java-7.2.0.jar:/home/pic/.m2/repository/com/influxdb/influxdb-client-core/7.2.0/influxdb-client-core-7.2.0.jar:/home/pic/.m2/repository/com/influxdb/influxdb-client-utils/7.2.0/influxdb-client-utils-7.2.0.jar:/home/pic/.m2/repository/com/squareup/okio/okio/3.9.0/okio-3.9.0.jar:/home/pic/.m2/repository/com/squareup/okio/okio-jvm/3.9.0/okio-jvm-3.9.0.jar:/home/pic/.m2/repository/org/jetbrains/kotlin/kotlin-stdlib/1.9.21/kotlin-stdlib-1.9.21.jar:/home/pic/.m2/repository/org/jetbrains/annotations/13.0/annotations-13.0.jar:/home/pic/.m2/repository/com/squareup/okhttp3/okhttp/4.12.0/okhttp-4.12.0.jar:/home/pic/.m2/repository/org/jetbrains/kotlin/kotlin-stdlib-jdk8/1.8.21/kotlin-stdlib-jdk8-1.8.21.jar:/home/pic/.m2/repository/org/jetbrains/kotlin/kotlin-stdlib-jdk7/1.8.21/kotlin-stdlib-jdk7-1.8.21.jar:/home/pic/.m2/repository/com/squareup/retrofit2/retrofit/2.11.0/retrofit-2.11.0.jar:/home/pic/.m2/repository/com/squareup/okhttp3/logging-interceptor/4.12.0/logging-interceptor-4.12.0.jar:/home/pic/.m2/repository/org/apache/commons/commons-csv/1.11.0/commons-csv-1.11.0.jar:/home/pic/.m2/repository/commons-io/commons-io/2.16.1/commons-io-2.16.1.jar:/home/pic/.m2/repository/io/reactivex/rxjava3/rxjava/3.1.8/rxjava-3.1.8.jar:/home/pic/.m2/repository/org/reactivestreams/reactive-streams/1.0.4/reactive-streams-1.0.4.jar:/home/pic/.m2/repository/com/squareup/retrofit2/adapter-rxjava3/2.11.0/adapter-rxjava3-2.11.0.jar:/home/pic/.m2/repository/com/squareup/retrofit2/converter-scalars/2.11.0/converter-scalars-2.11.0.jar:/home/pic/.m2/repository/com/squareup/retrofit2/converter-gson/2.11.0/converter-gson-2.11.0.jar:/home/pic/.m2/repository/com/google/code/findbugs/jsr305/3.0.2/jsr305-3.0.2.jar:/home/pic/.m2/repository/jakarta/mail/jakarta.mail-api/2.1.3/jakarta.mail-api-2.1.3.jar:/home/pic/.m2/repository/jakarta/activation/jakarta.activation-api/2.1.3/jakarta.activation-api-2.1.3.jar:/home/pic/.m2/repository/com/amazonaws/aws-iot-device-sdk-java/1.3.13/aws-iot-device-sdk-java-1.3.13.jar:/home/pic/.m2/repository/com/fasterxml/jackson/core/jackson-core/2.13.4/jackson-core-2.13.4.jar:/home/pic/.m2/repository/com/fasterxml/jackson/core/jackson-databind/2.13.4.2/jackson-databind-2.13.4.2.jar:/home/pic/.m2/repository/com/fasterxml/jackson/core/jackson-annotations/2.13.4/jackson-annotations-2.13.4.jar:/home/pic/.m2/repository/org/eclipse/paho/org.eclipse.paho.client.mqttv3/1.2.4/org.eclipse.paho.client.mqttv3-1.2.4.jar:/home/pic/.m2/repository/com/amazonaws/aws-java-sdk-secretsmanager/1.12.315/aws-java-sdk-secretsmanager-1.12.315.jar:/home/pic/.m2/repository/com/amazonaws/aws-java-sdk-core/1.12.315/aws-java-sdk-core-1.12.315.jar:/home/pic/.m2/repository/org/apache/httpcomponents/httpclient/4.5.13/httpclient-4.5.13.jar:/home/pic/.m2/repository/org/apache/httpcomponents/httpcore/4.4.13/httpcore-4.4.13.jar:/home/pic/.m2/repository/software/amazon/ion/ion-java/1.0.2/ion-java-1.0.2.jar:/home/pic/.m2/repository/com/fasterxml/jackson/dataformat/jackson-dataformat-cbor/2.12.6/jackson-dataformat-cbor-2.12.6.jar:/home/pic/.m2/repository/joda-time/joda-time/2.8.1/joda-time-2.8.1.jar:/home/pic/.m2/repository/com/amazonaws/jmespath-java/1.12.315/jmespath-java-1.12.315.jar:/home/pic/.m2/repository/com/amazonaws/aws-iot-device-sdk-java-samples/1.3.13/aws-iot-device-sdk-java-samples-1.3.13.jar:/home/pic/.m2/repository/commons-codec/commons-codec/1.12/commons-codec-1.12.jar:/home/pic/.m2/repository/com/microsoft/azure/sdk/iot/iot-device-client/2.5.0/iot-device-client-2.5.0.jar:/home/pic/.m2/repository/org/apache/qpid/proton-j/0.34.1/proton-j-0.34.1.jar:/home/pic/.m2/repository/com/microsoft/azure/qpid-proton-j-extensions/1.2.4/qpid-proton-j-extensions-1.2.4.jar:/home/pic/.m2/repository/com/microsoft/azure/sdk/iot/provisioning/security/security-provider/2.0.1/security-provider-2.0.1.jar com.intellij.rt.junit.JUnitStarter -ideVersion5 -junit4 programmingtheiot.part03.integration.connection.CoapClientToServerConnectorTest,testSystemPerformancePutMessage
    05:56:12.194 [main] INFO org.eclipse.californium.elements.config.Configuration -- defaults added COAP.
    05:56:12.207 [main] INFO org.eclipse.californium.elements.config.Configuration -- defaults added SYS.
    05:56:12.208 [main] INFO org.eclipse.californium.elements.config.Configuration -- defaults added UDP.
    05:56:12.220 [main] INFO org.eclipse.californium.elements.config.Configuration -- loading properties from file /home/pic/Documents/PIC/java-components/Californium3.properties
    May 29, 2025 5:56:12 AM programmingtheiot.gda.connection.CoapServerGateway initServer
    INFO: No resources provided for server initialization.
    May 29, 2025 5:56:12 AM programmingtheiot.gda.connection.CoapServerGateway createAndAddResourceChain
    INFO: Adding server resource handler chain: PIOT/ConstrainedDevice/ActuatorCmd
    May 29, 2025 5:56:12 AM programmingtheiot.gda.connection.CoapServerGateway createAndAddResourceChain
    INFO: Adding server resource handler chain: PIOT/ConstrainedDevice/SensorMsg
    May 29, 2025 5:56:12 AM programmingtheiot.gda.connection.CoapServerGateway createAndAddResourceChain
    INFO: Adding server resource handler chain: PIOT/ConstrainedDevice/SystemPerfMsg
    05:56:12.264 [main] INFO org.eclipse.californium.core.CoapServer -- Starting server
    05:56:12.271 [main] DEBUG org.eclipse.californium.elements.util.ExecutorsUtil -- remove on cancel: true, split: true, log-diff: 10000
    05:56:12.279 [main] INFO org.eclipse.californium.core.CoapServer -- no endpoints have been defined for server, setting up server endpoint on default port 5683
    05:56:12.287 [main] INFO org.eclipse.californium.core.network.RandomTokenGenerator -- using tokens of 8 bytes in length
    05:56:12.298 [main] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap using TokenProvider org.eclipse.californium.core.network.RandomTokenGenerator
    05:56:12.312 [main] INFO org.eclipse.californium.ban -- Started.
    05:56:12.313 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap CoapEndpoint uses udp context
    05:56:12.324 [main] INFO org.eclipse.californium.core.network.stack.BlockwiseLayer -- coap BlockwiseLayer uses MAX_MESSAGE_SIZE=1024, PREFERRED_BLOCK_SIZE=512, BLOCKWISE_STATUS_LIFETIME=300000, MAX_RESOURCE_BODY_SIZE=8192, BLOCKWISE_STRICT_BLOCK2_OPTION=false
    05:56:12.332 [main] DEBUG org.eclipse.californium.core.network.CoapEndpoint -- coap Starting endpoint at coap://0.0.0.0:5683
    05:56:12.334 [main] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap no MessageIdProvider set, using default org.eclipse.californium.core.network.InMemoryMessageIdProvider
    05:56:12.345 [main] INFO org.eclipse.californium.elements.UDPConnector -- UDPConnector starts up 2 sender threads and 2 receiver threads
    05:56:12.349 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]]
    05:56:12.349 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Receiver-0.0.0.0/0.0.0.0:5683[1]]
    05:56:12.352 [main] INFO org.eclipse.californium.elements.UDPConnector -- UDPConnector listening on /[0:0:0:0:0:0:0:0]:5683, recv buf = 106496, send buf = 106496, recv packet size = 2048
    05:56:12.352 [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]]
    05:56:12.352 [UDP-Sender-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Sender-0.0.0.0/0.0.0.0:5683[1]]
    05:56:12.352 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap Started endpoint at coap://[0:0:0:0:0:0:0:0]:5683
    05:56:12.970 [main] DEBUG org.eclipse.californium.elements.util.NetworkInterfacesUtil -- Found broadcast address /10.0.2.255 - enp0s3.
    05:56:12.971 [main] INFO org.eclipse.californium.core.network.RandomTokenGenerator -- using tokens of 8 bytes in length
    05:56:12.971 [main] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap using TokenProvider org.eclipse.californium.core.network.RandomTokenGenerator
    05:56:12.971 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap CoapEndpoint uses udp context
    05:56:12.971 [main] INFO org.eclipse.californium.core.network.stack.BlockwiseLayer -- coap BlockwiseLayer uses MAX_MESSAGE_SIZE=1024, PREFERRED_BLOCK_SIZE=512, BLOCKWISE_STATUS_LIFETIME=300000, MAX_RESOURCE_BODY_SIZE=8192, BLOCKWISE_STRICT_BLOCK2_OPTION=false
    05:56:12.971 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap Endpoint [coap://0.0.0.0:0] requires an executor to start, using default single-threaded daemon executor
    05:56:12.973 [main] DEBUG org.eclipse.californium.core.network.CoapEndpoint -- coap Starting endpoint at coap://0.0.0.0:0
    05:56:12.973 [main] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap no MessageIdProvider set, using default org.eclipse.californium.core.network.InMemoryMessageIdProvider
    05:56:12.973 [main] INFO org.eclipse.californium.elements.UDPConnector -- UDPConnector starts up 2 sender threads and 2 receiver threads
    05:56:12.974 [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]]
    05:56:12.974 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]]
    05:56:12.974 [UDP-Sender-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Sender-0.0.0.0/0.0.0.0:0[0]]
    05:56:12.974 [main] INFO org.eclipse.californium.elements.UDPConnector -- UDPConnector listening on /[0:0:0:0:0:0:0:0]:35298, recv buf = 106496, send buf = 106496, recv packet size = 2048
    05:56:12.974 [main] INFO org.eclipse.californium.core.network.CoapEndpoint -- coap Started endpoint at coap://[0:0:0:0:0:0:0:0]:35298
    05:56:12.974 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- Starting network stage thread [UDP-Sender-0.0.0.0/0.0.0.0:0[1]]
    05:56:12.974 [main] INFO org.eclipse.californium.core.network.EndpointManager -- created implicit endpoint coap://[0:0:0:0:0:0:0:0]:35298 for coap
    05:56:12.978 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L1, localhost:5683] send request
    05:56:12.978 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L1, localhost:5683] prepare retransmission for CON-PUT    MID=   -1, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"], "Content-Format":"application/json"}, "{"cpuUtil":0.0,"diskUtil":0.0,"m".. 247 bytes
    05:56:12.981 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L1, localhost:5683] added with generated mid KeyMID[localhost/127.0.0.1:5683-53051], CON-PUT    MID=53051, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"], "Content-Format":"application/json"}, "{"cpuUtil":0.0,"diskUtil":0.0,"m".. 247 bytes
    05:56:12.982 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L1, localhost:5683] added with generated token KeyToken[localhost/127.0.0.1:5683-18B593F853F614E2], CON-PUT    MID=53051, Token=18B593F853F614E2, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"], "Content-Format":"application/json"}, "{"cpuUtil":0.0,"diskUtil":0.0,"m".. 247 bytes
    05:56:12.982 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open request [KeyMID[localhost/127.0.0.1:5683-53051], KeyToken[localhost/127.0.0.1:5683-18B593F853F614E2]]
    05:56:12.986 [UDP-Sender-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#37,UDP-Sender-0.0.0.0/0.0.0.0:0[0],5,Californium/Elements]) sent 311 bytes to localhost/127.0.0.1:5683
    05:56:12.986 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 311 bytes from 127.0.0.1:35298
    05:56:12.988 [CoapServer(main)#4] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:35298) ==> req CON-PUT    MID=53051, Token=18B593F853F614E2, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"], "Content-Format":"application/json"}, "{"cpuUtil":0.0,"diskUtil":0.0,"m".. 247 bytes
    05:56:12.988 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:35298-53051]
    05:56:12.991 [CoapServer(main)#5] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:35298) <== emp ACK        MID=53051
    05:56:12.993 [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#32,UDP-Sender-0.0.0.0/0.0.0.0:5683[0],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:35298
    05:56:12.993 [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:35298) received 4 bytes from 127.0.0.1:5683
    05:56:12.994 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L1, localhost:5683] for MID KeyMID[127.0.0.1:5683-53051]
    05:56:12.994 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:5683-53051]
    05:56:12.995 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L1, localhost:5683] acknowledge ACK        MID=53051 for request CON-PUT    MID=53051, Token=18B593F853F614E2, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"], "Content-Format":"application/json"}, "{"cpuUtil":0.0,"diskUtil":0.0,"m".. 247 bytes (2 msg observer)
    May 29, 2025 5:56:12 AM programmingtheiot.common.DefaultDataMessageListener handleSystemPerformanceMessage
    INFO: Topic: PIOT/ConstrainedDevice/SystemPerfMsg, Message: name=SysPerfData,typeID=0,timeStamp=2025-05-29T03:56:12.920122297Z,statusCode=0,hasError=false,locationID=gatewaydevice001,latitude=0.0,longitude=0.0,elevation=0.0,cpuUtil=0.0,diskUtil=0.0,memUtil=0.0
    05:56:12.999 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R2, 127.0.0.1:35298] send response null-2.04   MID=   -1, Token=null, OptionSet={"Content-Format":"text/plain"}, "Update system perf data request ".. 54 bytes
    05:56:12.999 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R2, 127.0.0.1:35298] prepare retransmission for CON-2.04   MID=   -1, Token=null, OptionSet={"Content-Format":"text/plain"}, "Update system perf data request ".. 54 bytes
    05:56:12.999 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[R2, 127.0.0.1:35298] added with generated mid KeyMID[127.0.0.1:35298-10569], CON-2.04   MID=10569, Token=18B593F853F614E2, OptionSet={"Content-Format":"text/plain"}, "Update system perf data request ".. 54 bytes
    05:56:12.999 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open response [KeyMID[127.0.0.1:35298-10569]]
    05:56:12.999 [CoapServer(main)#5] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:35298) <== res CON-2.04   MID=10569, Token=18B593F853F614E2, OptionSet={"Content-Format":"text/plain"}, "Update system perf data request ".. 54 bytes
    05:56:13.000 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:35298) received 68 bytes from 127.0.0.1:5683
    05:56:13.001 [UDP-Sender-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#33,UDP-Sender-0.0.0.0/0.0.0.0:5683[1],5,Californium/Elements]) sent 68 bytes to 127.0.0.1:35298
    05:56:13.001 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:5683-10569]
    05:56:13.001 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L1, localhost:5683] acknowledging CON response
    05:56:13.001 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- Exchange[L1, localhost:5683, complete]!
    05:56:13.001 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L1, localhost:5683, complete] for token KeyToken[localhost/127.0.0.1:5683-18B593F853F614E2]
    05:56:13.001 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#38,UDP-Sender-0.0.0.0/0.0.0.0:0[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:5683
    05:56:13.001 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- local Exchange[L1, localhost:5683, complete] completed CON-PUT    MID=53051, Token=18B593F853F614E2, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"], "Content-Format":"application/json"}, acked "{"cpuUtil":0.0,"diskUtil":0.0,"m".. 247 bytes!
    05:56:13.002 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 4 bytes from 127.0.0.1:35298
    05:56:13.002 [CoapServer(main)#1] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:35298) ==> emp ACK        MID=10569
    05:56:13.002 [CoapServer(main)#2] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[R2, 127.0.0.1:35298] for MID KeyMID[127.0.0.1:35298-10569]
    05:56:13.002 [CoapServer(main)#2] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:35298-10569]
    05:56:13.002 [CoapServer(main)#2] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R2, 127.0.0.1:35298] acknowledge ACK        MID=10569 for response CON-2.04   MID=10569, Token=18B593F853F614E2, OptionSet={"Content-Format":"text/plain"}, "Update system perf data request ".. 54 bytes (2 msg observer)
    May 29, 2025 5:56:13 AM programmingtheiot.gda.connection.CoapClientConnector sendPutRequest
    INFO: PUT response for coap://localhost:5683/PIOT/ConstrainedDevice/SystemPerfMsg: Update system perf data request handled: SystemPerfMsg
    May 29, 2025 5:56:13 AM programmingtheiot.part03.integration.connection.CoapClientToServerConnectorTest testSystemPerformancePutMessage
    INFO: PUT request result: true
    05:56:13.007 [main] INFO org.eclipse.californium.core.CoapServer -- Stopping server ...
    05:56:13.007 [main] DEBUG org.eclipse.californium.core.network.CoapEndpoint -- coap Stopping endpoint at coap://[0:0:0:0:0:0:0:0]:5683
    05:56:13.007 [main] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector on [/[0:0:0:0:0:0:0:0]:5683] stopping ...
    05:56:13.008 [main] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector on [/[0:0:0:0:0:0:0:0]:5683] has stopped.
    05:56:13.008 [main] DEBUG org.eclipse.californium.core.network.CoapEndpoint -- coap Stopped endpoint at coap://[0:0:0:0:0:0:0:0]:5683
    05:56:13.008 [main] INFO org.eclipse.californium.core.CoapServer -- Stopped server.
    
    Process finished with exit code 0
    </code></pre>
  </details>

  ---

  **CoAP GET (PIOT-CDA-08-003)**:
  
  CoAP GET requests to individual resources have been executed. 
  The log shows a successful test of GET requests from the CoAP client to multiple resource endpoints on the server. **Key results**:

  - The client sent confirmable GET requests to three resources.
  - The server responded with appropriate CoAP response codes.
  - Message exchanges were tracked and acknowledged correctly.

  **Resources tested**:

  - `/PIOT/ConstrainedDevice/ActuatorCmd`  
    - **Response Code:** `2.05 Content`  
    - **Response Data:** *(empty)*  
    - **Outcome:** Successfully handled. Server acknowledged and completed exchange.

  - `/PIOT/ConstrainedDevice/SensorMsg`  
    - **Response Code:** `2.03 Valid`  
    - **Response Data:** `"Generic handler. No GET action taken: SensorMsg"`  
    - **Outcome:** Successfully handled. No specific GET logic implemented for this resource.

  - `/PIOT/ConstrainedDevice/SystemPerfMsg`  
    - **Response Code:** `2.03 Valid`  
    - **Response Data:** `"No system performance data available: SystemPerfMsg"`  
    - **Outcome:** Successfully handled. Server reports no performance data available.

  **Overall Outcome**:  
  All requests and responses were properly exchanged with no errors. The CoAP server correctly processed each request, matched tokens and message IDs, and responded reliably over UDP.

  <details>
    <pre><code>
    05:28:09.939 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L3, localhost:5683] send request
    05:28:09.939 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L3, localhost:5683] prepare retransmission for CON-GET    MID=   -1, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","ActuatorCmd"]}, <empty data>
    05:28:09.940 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L3, localhost:5683] added with generated mid KeyMID[localhost/127.0.0.1:5683-28057], CON-GET    MID=28057, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","ActuatorCmd"]}, <empty data>
    05:28:09.940 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L3, localhost:5683] added with generated token KeyToken[localhost/127.0.0.1:5683-B4797DCA4923A2C6], CON-GET    MID=28057, Token=B4797DCA4923A2C6, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","ActuatorCmd"]}, <empty data>
    05:28:09.940 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open request [KeyMID[localhost/127.0.0.1:5683-28057], KeyToken[localhost/127.0.0.1:5683-B4797DCA4923A2C6]]
    05:28:09.941 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 58 bytes from 127.0.0.1:56941
    05:28:09.941 [UDP-Sender-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#37,UDP-Sender-0.0.0.0/0.0.0.0:0[0],5,Californium/Elements]) sent 58 bytes to localhost/127.0.0.1:5683
    05:28:09.941 [CoapServer(main)#6] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> req CON-GET    MID=28057, Token=B4797DCA4923A2C6, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","ActuatorCmd"]}, <empty data>
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.handlers.GetActuatorCommandResourceHandler handleGET
    INFO: GET request received for resource: /PIOT/ConstrainedDevice/ActuatorCmd with query: 
    05:28:09.942 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:56941-28057]
    05:28:09.944 [CoapServer(main)#6] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== emp ACK        MID=28057
    05:28:09.946 [UDP-Sender-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#33,UDP-Sender-0.0.0.0/0.0.0.0:5683[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:56941
    05:28:09.946 [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 4 bytes from 127.0.0.1:5683
    05:28:09.946 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R4, 127.0.0.1:56941] send response null-2.05   MID=   -1, Token=null, OptionSet={"Content-Format":"application/json"}, <empty data>
    05:28:09.946 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R4, 127.0.0.1:56941] prepare retransmission for CON-2.05   MID=   -1, Token=null, OptionSet={"Content-Format":"application/json"}, <empty data>
    05:28:09.947 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[R4, 127.0.0.1:56941] added with generated mid KeyMID[127.0.0.1:56941-16847], CON-2.05   MID=16847, Token=B4797DCA4923A2C6, OptionSet={"Content-Format":"application/json"}, <empty data>
    05:28:09.948 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open response [KeyMID[127.0.0.1:56941-16847]]
    05:28:09.948 [CoapServer(main)#6] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== res CON-2.05   MID=16847, Token=B4797DCA4923A2C6, OptionSet={"Content-Format":"application/json"}, <empty data>
    05:28:09.949 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 14 bytes from 127.0.0.1:5683
    05:28:09.949 [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#32,UDP-Sender-0.0.0.0/0.0.0.0:5683[0],5,Californium/Elements]) sent 14 bytes to 127.0.0.1:56941
    05:28:09.949 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L3, localhost:5683] for MID KeyMID[127.0.0.1:5683-28057]
    05:28:09.949 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:5683-28057]
    05:28:09.949 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L3, localhost:5683] acknowledge ACK        MID=28057 for request CON-GET    MID=28057, Token=B4797DCA4923A2C6, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","ActuatorCmd"]}, <empty data> (2 msg observer)
    05:28:09.949 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:5683-16847]
    05:28:09.950 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L3, localhost:5683] acknowledging CON response
    05:28:09.950 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- Exchange[L3, localhost:5683, complete]!
    05:28:09.950 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L3, localhost:5683, complete] for token KeyToken[localhost/127.0.0.1:5683-B4797DCA4923A2C6]
    05:28:09.950 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- local Exchange[L3, localhost:5683, complete] completed CON-GET    MID=28057, Token=B4797DCA4923A2C6, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","ActuatorCmd"]}, acked <empty data>!
    05:28:09.950 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#38,UDP-Sender-0.0.0.0/0.0.0.0:0[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:5683
    05:28:09.950 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 4 bytes from 127.0.0.1:56941
    05:28:09.950 [CoapServer(main)#4] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> emp ACK        MID=16847
    05:28:09.950 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[R4, 127.0.0.1:56941] for MID KeyMID[127.0.0.1:56941-16847]
    05:28:09.950 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:56941-16847]
    05:28:09.950 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R4, 127.0.0.1:56941] acknowledge ACK        MID=16847 for response CON-2.05   MID=16847, Token=B4797DCA4923A2C6, OptionSet={"Content-Format":"application/json"}, <empty data> (2 msg observer)
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendGetRequest
    INFO: GET response for coap://localhost:5683/PIOT/ConstrainedDevice/ActuatorCmd: 
    May 29, 2025 5:28:09 AM programmingtheiot.part03.integration.connection.CoapClientToServerConnectorTest testGetEachResource
    INFO: GET request for PIOT/ConstrainedDevice/ActuatorCmd was successful
    05:28:09.956 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L5, localhost:5683] send request
    05:28:09.956 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L5, localhost:5683] prepare retransmission for CON-GET    MID=   -1, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}, <empty data>
    05:28:09.956 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L5, localhost:5683] added with generated mid KeyMID[localhost/127.0.0.1:5683-28058], CON-GET    MID=28058, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}, <empty data>
    05:28:09.957 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L5, localhost:5683] added with generated token KeyToken[localhost/127.0.0.1:5683-0CBD6487BC67346F], CON-GET    MID=28058, Token=0CBD6487BC67346F, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}, <empty data>
    05:28:09.957 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open request [KeyMID[localhost/127.0.0.1:5683-28058], KeyToken[localhost/127.0.0.1:5683-0CBD6487BC67346F]]
    05:28:09.958 [UDP-Sender-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#37,UDP-Sender-0.0.0.0/0.0.0.0:0[0],5,Californium/Elements]) sent 56 bytes to localhost/127.0.0.1:5683
    05:28:09.958 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 56 bytes from 127.0.0.1:56941
    05:28:09.958 [CoapServer(main)#1] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> req CON-GET    MID=28058, Token=0CBD6487BC67346F, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}, <empty data>
    05:28:09.958 [CoapServer(main)#1] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:56941-28058]
    05:28:09.958 [CoapServer(main)#6] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== emp ACK        MID=28058
    05:28:09.959 [UDP-Sender-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#33,UDP-Sender-0.0.0.0/0.0.0.0:5683[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:56941
    05:28:09.959 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 4 bytes from 127.0.0.1:5683
    05:28:09.959 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R6, 127.0.0.1:56941] send response null-2.03   MID=   -1, Token=null, OptionSet={"Content-Format":"text/plain"}, "Generic handler. No GET action t".. 47 bytes
    05:28:09.959 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R6, 127.0.0.1:56941] prepare retransmission for CON-2.03   MID=   -1, Token=null, OptionSet={"Content-Format":"text/plain"}, "Generic handler. No GET action t".. 47 bytes
    05:28:09.959 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[R6, 127.0.0.1:56941] added with generated mid KeyMID[127.0.0.1:56941-16848], CON-2.03   MID=16848, Token=0CBD6487BC67346F, OptionSet={"Content-Format":"text/plain"}, "Generic handler. No GET action t".. 47 bytes
    05:28:09.959 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L5, localhost:5683] for MID KeyMID[127.0.0.1:5683-28058]
    05:28:09.959 [CoapServer(main)#6] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open response [KeyMID[127.0.0.1:56941-16848]]
    05:28:09.959 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:5683-28058]
    05:28:09.959 [CoapServer(main)#6] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== res CON-2.03   MID=16848, Token=0CBD6487BC67346F, OptionSet={"Content-Format":"text/plain"}, "Generic handler. No GET action t".. 47 bytes
    05:28:09.960 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L5, localhost:5683] acknowledge ACK        MID=28058 for request CON-GET    MID=28058, Token=0CBD6487BC67346F, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}, <empty data> (2 msg observer)
    05:28:09.960 [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#32,UDP-Sender-0.0.0.0/0.0.0.0:5683[0],5,Californium/Elements]) sent 61 bytes to 127.0.0.1:56941
    05:28:09.960 [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 61 bytes from 127.0.0.1:5683
    05:28:09.960 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:5683-16848]
    05:28:09.960 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L5, localhost:5683] acknowledging CON response
    05:28:09.960 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- Exchange[L5, localhost:5683, complete]!
    05:28:09.960 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L5, localhost:5683, complete] for token KeyToken[localhost/127.0.0.1:5683-0CBD6487BC67346F]
    05:28:09.961 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- local Exchange[L5, localhost:5683, complete] completed CON-GET    MID=28058, Token=0CBD6487BC67346F, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SensorMsg"]}, acked <empty data>!
    05:28:09.961 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#38,UDP-Sender-0.0.0.0/0.0.0.0:0[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:5683
    05:28:09.961 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 4 bytes from 127.0.0.1:56941
    05:28:09.961 [CoapServer(main)#5] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> emp ACK        MID=16848
    05:28:09.961 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[R6, 127.0.0.1:56941] for MID KeyMID[127.0.0.1:56941-16848]
    05:28:09.961 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:56941-16848]
    05:28:09.961 [CoapServer(main)#5] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R6, 127.0.0.1:56941] acknowledge ACK        MID=16848 for response CON-2.03   MID=16848, Token=0CBD6487BC67346F, OptionSet={"Content-Format":"text/plain"}, "Generic handler. No GET action t".. 47 bytes (2 msg observer)
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendGetRequest
    INFO: GET response for coap://localhost:5683/PIOT/ConstrainedDevice/SensorMsg: Generic handler. No GET action taken: SensorMsg
    May 29, 2025 5:28:09 AM programmingtheiot.part03.integration.connection.CoapClientToServerConnectorTest testGetEachResource
    INFO: GET request for PIOT/ConstrainedDevice/SensorMsg was successful
    05:28:09.962 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L7, localhost:5683] send request
    05:28:09.962 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L7, localhost:5683] prepare retransmission for CON-GET    MID=   -1, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}, <empty data>
    05:28:09.963 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L7, localhost:5683] added with generated mid KeyMID[localhost/127.0.0.1:5683-28059], CON-GET    MID=28059, Token=null, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}, <empty data>
    05:28:09.963 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[L7, localhost:5683] added with generated token KeyToken[localhost/127.0.0.1:5683-5CA8655D098AEA18], CON-GET    MID=28059, Token=5CA8655D098AEA18, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}, <empty data>
    05:28:09.963 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open request [KeyMID[localhost/127.0.0.1:5683-28059], KeyToken[localhost/127.0.0.1:5683-5CA8655D098AEA18]]
    05:28:09.964 [UDP-Sender-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#37,UDP-Sender-0.0.0.0/0.0.0.0:0[0],5,Californium/Elements]) sent 61 bytes to localhost/127.0.0.1:5683
    05:28:09.964 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 61 bytes from 127.0.0.1:56941
    05:28:09.965 [CoapServer(main)#1] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> req CON-GET    MID=28059, Token=5CA8655D098AEA18, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}, <empty data>
    05:28:09.965 [CoapServer(main)#1] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:56941-28059]
    05:28:09.965 [CoapServer(main)#1] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== emp ACK        MID=28059
    05:28:09.966 [UDP-Sender-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#33,UDP-Sender-0.0.0.0/0.0.0.0:5683[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:56941
    05:28:09.966 [UDP-Receiver-0.0.0.0/0.0.0.0:0[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 4 bytes from 127.0.0.1:5683
    05:28:09.966 [CoapServer(main)#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R8, 127.0.0.1:56941] send response null-2.03   MID=   -1, Token=null, OptionSet={"Content-Format":"text/plain"}, "No system performance data avail".. 51 bytes
    05:28:09.966 [CoapServer(main)#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R8, 127.0.0.1:56941] prepare retransmission for CON-2.03   MID=   -1, Token=null, OptionSet={"Content-Format":"text/plain"}, "No system performance data avail".. 51 bytes
    05:28:09.966 [CoapServer(main)#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap Exchange[R8, 127.0.0.1:56941] added with generated mid KeyMID[127.0.0.1:56941-16849], CON-2.03   MID=16849, Token=5CA8655D098AEA18, OptionSet={"Content-Format":"text/plain"}, "No system performance data avail".. 51 bytes
    05:28:09.966 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L7, localhost:5683] for MID KeyMID[127.0.0.1:5683-28059]
    05:28:09.966 [CoapServer(main)#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- tracking open response [KeyMID[127.0.0.1:56941-16849]]
    05:28:09.966 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:5683-28059]
    05:28:09.966 [CoapServer(main)#1] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) <== res CON-2.03   MID=16849, Token=5CA8655D098AEA18, OptionSet={"Content-Format":"text/plain"}, "No system performance data avail".. 51 bytes
    05:28:09.966 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L7, localhost:5683] acknowledge ACK        MID=28059 for request CON-GET    MID=28059, Token=5CA8655D098AEA18, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}, <empty data> (2 msg observer)
    05:28:09.967 [UDP-Sender-0.0.0.0/0.0.0.0:5683[0]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#32,UDP-Sender-0.0.0.0/0.0.0.0:5683[0],5,Californium/Elements]) sent 65 bytes to 127.0.0.1:56941
    05:28:09.967 [UDP-Receiver-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:56941) received 65 bytes from 127.0.0.1:5683
    05:28:09.967 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.deduplication.SweepDeduplicator -- add exchange for KeyMID[127.0.0.1:5683-16849]
    05:28:09.967 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[L7, localhost:5683] acknowledging CON response
    05:28:09.967 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- Exchange[L7, localhost:5683, complete]!
    05:28:09.967 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[L7, localhost:5683, complete] for token KeyToken[localhost/127.0.0.1:5683-5CA8655D098AEA18]
    05:28:09.967 [UDP-Sender-0.0.0.0/0.0.0.0:0[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector (Thread[#38,UDP-Sender-0.0.0.0/0.0.0.0:0[1],5,Californium/Elements]) sent 4 bytes to 127.0.0.1:5683
    05:28:09.967 [:CoapEndpoint-UDP-0.0.0.0:0#1] DEBUG org.eclipse.californium.core.network.Exchange -- local Exchange[L7, localhost:5683, complete] completed CON-GET    MID=28059, Token=5CA8655D098AEA18, OptionSet={"Uri-Host":"localhost", "Uri-Path":["PIOT","ConstrainedDevice","SystemPerfMsg"]}, acked <empty data>!
    05:28:09.967 [UDP-Receiver-0.0.0.0/0.0.0.0:5683[1]] DEBUG org.eclipse.californium.elements.UDPConnector -- UDPConnector ([0:0:0:0:0:0:0:0]:5683) received 4 bytes from 127.0.0.1:56941
    May 29, 2025 5:28:09 AM programmingtheiot.gda.connection.CoapClientConnector sendGetRequest
    INFO: GET response for coap://localhost:5683/PIOT/ConstrainedDevice/SystemPerfMsg: No system performance data available: SystemPerfMsg
    05:28:09.968 [CoapServer(main)#4] INFO org.eclipse.californium.core.network.interceptors.MessageTracer -- UDP(127.0.0.1:56941) ==> emp ACK        MID=16849
    05:28:09.968 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.InMemoryMessageExchangeStore -- coap removing Exchange[R8, 127.0.0.1:56941] for MID KeyMID[127.0.0.1:56941-16849]
    05:28:09.968 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.UdpMatcher -- received expected ACK reply for KeyMID[127.0.0.1:56941-16849]
    05:28:09.968 [CoapServer(main)#4] DEBUG org.eclipse.californium.core.network.stack.ReliabilityLayer -- Exchange[R8, 127.0.0.1:56941] acknowledge ACK        MID=16849 for response CON-2.03   MID=16849, Token=5CA8655D098AEA18, OptionSet={"Content-Format":"text/plain"}, "No system performance data avail".. 51 bytes (2 msg observer)
    May 29, 2025 5:28:09 AM programmingtheiot.part03.integration.connection.CoapClientToServerConnectorTest testGetEachResource
    INFO: GET request for PIOT/ConstrainedDevice/SystemPerfMsg was successful

    </code></pre>
  </details>

  ---



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
