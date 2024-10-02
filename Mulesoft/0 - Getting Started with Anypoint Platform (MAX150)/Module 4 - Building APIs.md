In this module, you will:
- Use Anypoint Studio to build, run and test Mule apps
- Use a connector to connect to databases
- Use the graphical DataWeave editor to transform data
- Create RESTful interfaces for apps from API specifications
- Connect API interfaces to API implementations
- Synchronise changes to API specs between Anypoint Studio and Platform

![[Pasted image 20240910113818.png]]


# What are Mule 4 apps and flows?

- A **mule application** is a sequence of mule event processors (a flow), which ultimately is a group of XML files. The app can have:
	- A single flow
	- Multiple flows
	- Multiple flows connected together
- A **flow** is a processor that groups together mule event processors

A flow is the only thing that is executable in Mule 4. A flow has three main areas:
- Source - controls how a flow is triggered
- Process Area - determines what the flow does
- Error Handler

A Mule event source initiates the execution of the flow and can be triggered by events such as:
- A consumer request from a mobile device
- A change to data in a database

The Mule event processors then transform, filter, enrich and process the event and its message.

![[Pasted image 20240910114750.png]]
# What happens when a Mule 4 app is triggered?

A new data structure is created and passed through the flows of the app called **Mule event**.

The Mule event has two main elements:
- A Mule message
	- Attributes - metadata that describes details of the payload
	- Payload - actual data that is processed
- Variables - metadata for the Mule event

# How to build Mule apps with Anypoint Studio?

The Anypoint Studio is used to create Mule applications, it is based on the Eclipse IDE. Apps can be created by using either:
- A graphical view to generate XML
- XML editor

![[Pasted image 20240910120703.png]]

## Anatomy

The user interface is based off the concept called **perspectives**. Each perspective contains a collection of **panels** that each contain **views**.

Three perspectives are included:
- API Development
- Debugging
- Design

Design Perspective by default has:
- Package Explorer - displays packages, folders, files
- Outline - Displays flows of the selected mule configuration file
- Canvas - displays message flows
- Mule Pallete - Displays modules added to project
	- Each module display connectors and processors used to build the flows

By default every project includes the core module which contains:
- Primary components - used to create flows
- Modules - used to interact with HTTP, TCP and UDP protocols

## How to run applications?

Anypoint Studio comes with its own embedded **mule runtime**. It is used to run and test the apps and view the console for messages and outputs.

## How to test applications?

Automated testing is done using Mule app testing framework called **MUnit**, which is fully integrated with Anypoint Studio 
# How to get data from a database?

You can get data from any JDBC relational database by using the **Database connector**.

To use the connector, you must:
- Add the Database module to your project
- Add a database operation to a flow
- Configure the connection to the database

# Global configurations

For most operations, a lot of the configuration is encapsulated in a separate **global element**. It is a reusable config that can be used by many operations and defines a connection to a network resource.

# How is data transformed?

Transforming data in a mule flow can be accomplished using the transform message processor to execute the expressions that are written in DataWeave. 
## What is DataWeave 2.0?

DataWeave is a JSON like expression language designed for accessing, querying and transforming event data.

## What is DataWeave Playground?

It is an interactive browser environment for mocking data transformation based off a input payload. 
# Creating RESTful interfaces

An interface for an application will have listeners for each resource/method pairing defined by the API spec.
- GET: /flights
- POST: /flights
- GET: /flights/{ID}
- DELETE: /flights/{ID}
- PUT: /flights/{ID}

You can manually create the interface or have generated from the API definition.

## Automatic creation using APIkit

APIkit is an open-source toolkit that includes an Anypoint Studio plugin. It generates an interface automatically using the RAML API definition.

It generates:
- A main routing flow that is used for the main entry point of the mule application
- Also generates the resource flows defined in the API definition.

You can then add processors to the resource flows to hook up your backend logic.
# Connecting Interfaces to implementations

Large flows can be broken down into multiple smaller flows, this helps with:
- Making the graphical view more intuitive and the XML code easier to read
- Promotes code reusability
- Easier to test with MUnit

Flows are identified by name and can be called via **Flow Reference** components in other flows, the mule event is passed to the child flow and once completed returns back to parent flow.
# Synchronising API specifications

The **API Sync** feature of Anypoint Studio enables you to:
- Pull specifications from Design Center into Studio
	- You can also initiate the creation of API specs from scratch in Studio
- Edit the specs offline in Studio
- Push the updates back to Design Center
- Publish the new API asset version to Exchange

This helps follow API lifecycle development practises from within Studio while developing the app.

## Handling change conflicts

The API Design perspective includes a Git Staging view which helps view changes. Under the hood, Git VCS uses commands to correctly implement your changes.

A conflict is triggered in Git, if both the local and uploaded versions are being edited.