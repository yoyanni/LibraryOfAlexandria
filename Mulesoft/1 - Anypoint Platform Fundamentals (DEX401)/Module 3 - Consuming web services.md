
# Consume web services that have an API (and connector) in Anypoint Exchange

## Connectors in Anypoint Exchange

Many connectors in Exchange, package an easy way to make calls to APIs

## Connectors and Modules

***Modules*** are extensions to the Mule runtime that you can use when building a Mule app
- HTTP, Database, Salesforce
- SAP, Slack, Validation
- Java and more

Connectors are modules that connect to an external server
- HTTP, Database, Salesforce
- SAP, Slack

## Connectors types specify creator and support level

Type of selector is specified in its tags on Exchange, the levels are:
- Premium - not included with subscription for the platform
- Select - included with subscription
- MuleSoft Certified - developed by partners and verified by MuleSoft
- Community - Open sourced

## Connectors are automatically generated for APIs on Exchange

***REST CONNECT*** is the tool that generates connectors for RESTful APIs
- Works for both RAML 1.0 and OAS API specifications
- Both Mule 3 and 4 connectors are generated

The connectors can be used in Anypoint Studio or Flow Designer.

## Adding connectors from Exchange

![[Pasted image 20240917184956.png]]

# Consume RESTful web services

First check and see if there is an existing Anypoint Connector in Studio/Exchange to connect to the service provider. 

If there isnt, use the **HTTP** connector and its **Request** operation
- Configure the operation and/or global element configuration
- Specify any headers, query parameters, or URI parameters to pass to the call
# Consume SOAP web services

First check and see if there is an existing Anypoint Connector in Studio/Exchange to connect to the service provider. 

If there isnt, use the ***Web Service Consumer*** connector
- Add the Web Service Consumer module to the project
- Configure a global element configuration, which includes the location of the WSDL
- Use the Consume operation
- Select the SOAP operation to invoke

# Pass parameters to SOAP web services using the Transform Message component

Use the ***Transform Message*** component to pass arguments to a SOAP web service, which could also be used to set the required input payload to the SOAP operation.

When you add it before the Consume operation, DataSense is used to create metadata for the input that includes the arguments.

# Transform data from multiple services to a canonical format

## Combining data from multiple services

Data from different services is pretty much always going to be in different formats.

To combine the data sets, you need to transform each of them to a canonical, or standard format.


