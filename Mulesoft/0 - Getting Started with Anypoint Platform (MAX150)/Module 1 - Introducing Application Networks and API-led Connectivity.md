# Problems faced by IT today

Demands on IT is increasing which is creating a **transformation delivery gap**.

![[Pasted image 20240909125908.png]]

## A way of working to close the delivery gap

By building re-useable assets, it helps overcome the delivery gap.

![[Pasted image 20240909130043.png]]

# New Operating model emphasizes consumption

By consuming the re-useable assets that are created, feedback and other information helps produces better re-useable assets that free up the developers.

The key though is to consume as much as you produce.

![[Pasted image 20240909130523.png]]

## Modern API - Core building block for new operating model

Must be:
- Discoverable and accessible through self-service
- Productized and designed for ease of consumption
- Easily managed for security, scalability and performance

# The API-led connectivity approach

- System APIs - where services are connected and available
- Process APIs - where data fetched from System APIs is processed
- Experience APIs - where the processed data is consumed 

![[Pasted image 20240909131641.png]]

## Center for Enablement (C4E)

C4E is a cross functional team which ensures that assets are:
- Productized and published
- Consumable
- Consumed broadly
- Fully leveraged

The success of C4E is measured on the asset consumption.

# An Application Network

An application network is created from the bottom-up via self-service, every time an API is added the network grows.

It provides visibility, security and governability at every API node. It provides speed and agility to a business to response to the market.


# What is an API?

An API is an Application Programming Interface.

It provides the information for **communicating with software components**, defining the:
- **Operations** - what to call
- **Inputs** - what to send with a call
- **Outputs** - what you get back from a call
- Underlying data types

It defines **functionalities independent of implementations**, meaning you can change the implementations without changing how it is called.

## What is a web service?

A **web service** is a method of communication that allows two software systems to exchange data over the internet.

Systems interact with the web service in a manner prescribed by some defined rules of communication, such as:
- How one system can request data from another
- What parameters are required
- Structure of returned data
- etc..

### Two Types: SOAP vs RESTful

SOAP:
- Traditional, more complex type
- Communication rules are defined in an XML-based WSDL(Web Service Description Language)

RESTful:
- Recent, simpler type
- Use the existing HTTP communication protocol

### RESTful web services

REST stands for Representational State Transfer:
- An architectural style where clients and servers exchange representations of resources using the standard HTTP protocol.
- Stateless - The server does not remember any client state from previous requests
- Clients can cache previous responses to avoid repeated network calls

#### Request Methods

- GET
- POST
- DELETE
- PUT
- PATCH

#### Making calls to RESTful APIs

To make a call to web services, you need to write code or use a tool to make the HTTP requests.

There are tools such as:
- An API portal with an API console
- Advance Rest Client (ARC)
- Postman
- A cURL command-line utility 

An API can be:
- Unsecured
- Secured
	- You need to authenticated by providing credentials or a token
	- Other authentication protocols:
		- OAuth
		- SAML
		- JWT

## Designing for API success

Developers must create simple, user-friendly APIs that others will want to use for the success of the API. Designing the API by focusing on what it needs to solve is crucial and by focusing on the performance and user experience is good for the success of the API.

By using an **API design-first approach**, you get the design correct before investing in building it.  


## API Development Cycle (Spec-driven development)

![[Pasted image 20240909152107.png]]