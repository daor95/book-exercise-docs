# Constrained Device Application (Connected Devices)

## Lab Module 09

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-CDA-09-000: Git "labmodule09" branch created.

- PIOT-CDA-09-001: I implemented a CoAP client connector in Python by selecting the `aiocoap` library as the preferred CoAP protocol stack. The implementation involved creating a new module, **CoapClientConnector**, which adheres to the `IRequestResponseClient` interface as required. Within this class, the constructor was developed to load the necessary CoAP configuration using `ConfigUtil`, retrieve host and port settings from `PiotConfig.props`, and initialize a CoAP client using `aiocoap`'s asynchronous context. The `_initClient()` method manages the setup by running an asynchronous routine to create a client context using `asyncio`, ensuring compatibility with `aiocoap`'s event-driven architecture. The class also includes method stubs for standard CoAP operations like GET, POST, PUT, DELETE, discovery, and observation, which will be implemented in future tasks.
To complete the integration, the `CoapClientConnector` was linked with the **DeviceDataManager** class. A configuration flag named `enableCoapClient` was added to determine whether the CoAP client should be active. If enabled, an instance of `CoapClientConnector` is initialized and injected with a reference to the data message listener (`DeviceDataManager` itself). Additionally, utility methods such as `_createResourcePath()` were implemented to dynamically build CoAP URIs based on the requested resource.

- PIOT-CDA-09-002: The existing **CoapClientConnector** module has been extended by implementing support for GET requests using the `aiocoap` library. The implementation included handling both Confirmable (CON) and Non-confirmable (NON) message types. The method `sendGetRequest()` was developed to initiate GET requests based on a dynamic resource path built from `ResourceNameEnum` values and optional suffixes. This method invokes an asynchronous handler, `_handleGetRequest()`, which constructs and sends the GET request via the `aiocoap` context, then processes the response using the `_onGetResponse()` callback.
In the `_onGetResponse()` method, logic has been implemented to decode the payload and route actuator commands (if present) to the appropriate `IDataMessageListener` instance. Additionally, the `sendDiscoveryRequest()` method was completed using **Option 2**, which involves issuing a GET request to the CoAP resource path `/.well-known/core` to perform resource discovery in compliance with RFC7252 and RFC6690. The payload returned from this discovery request contains the list of available CoAP resources on the server.
To verify functionality, the integration test `CoapClientConnectorTest` has been used, including methods for testing both CON and NON GET requests to retrieve `ActuatorData`. Although most of them being left disabled for now, these tests were correctly implemented. In Wireshark we can see the CoAP messages:


![COAP WIRESHARK](https://github.com/user-attachments/assets/17275b84-0a21-4581-a498-96173de0f2cb)

With the Discovery output:

![Coap Extended Discovery](https://github.com/user-attachments/assets/54e357c6-bc21-4d7f-8201-cbd34c89b768)


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule09



### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 and part02 unit tests
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 and part02 integration tests
- CoapClientConnectorTest
- 

EOF.
