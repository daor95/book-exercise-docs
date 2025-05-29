# Constrained Device Application (Connected Devices)

## Lab Module 09

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-CDA-09-000: Git "labmodule09" branch created.

- PIOT-CDA-09-001: I implemented a CoAP client connector in Python by selecting the `aiocoap` library as the preferred CoAP protocol stack. The implementation involved creating a new module, `CoapClientConnector`, which adheres to the `IRequestResponseClient` interface as required. Within this class, the constructor was developed to load the necessary CoAP configuration using `ConfigUtil`, retrieve host and port settings from `PiotConfig.props`, and initialize a CoAP client using `aiocoap`'s asynchronous context. The `_initClient()` method manages the setup by running an asynchronous routine to create a client context using `asyncio`, ensuring compatibility with `aiocoap`'s event-driven architecture. The class also includes method stubs for standard CoAP operations like GET, POST, PUT, DELETE, discovery, and observation, which will be implemented in future tasks.
To complete the integration, the `CoapClientConnector` was linked with the `DeviceDataManager` class. A configuration flag named `enableCoapClient` was added to determine whether the CoAP client should be active. If enabled, an instance of `CoapClientConnector` is initialized and injected with a reference to the data message listener (`DeviceDataManager` itself). Additionally, utility methods such as `_createResourcePath()` were implemented to dynamically build CoAP URIs based on the requested resource.





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
- 
- 

EOF.
