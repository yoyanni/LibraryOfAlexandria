# Create applications composed of multiple flows and subflows

## Break up flows into separate flows and subflows

- The graphical view is more intuitive when having multiple flows and subflows
- XML code is easier to read
- Enables reusability
- Provides separation of concerns between an interface and implementation
- Easier to test

### Flows vs Subflows

A ***Flow is a scope*** that has sections for:
- Source (Optional)
- Processors
- Error handling (Optional)

A flow that has no source is called a **private flow** because of the accessibility which is enabled only from within the application.

A ***Subflow is a scope*** that has a section only for processors that run as if they were originally apart of the parent flow.

### Creating flows and subflows

How to create:
- Add a new scope:
	- Flow
	- Subflow
- Drag any event processor to the canvas which creates a flow
- Right-click processors in canvas and select ***Extract to***

You can pass the event to flows and subflows by using the ***Flow reference*** component, the metadata is automatically propagated and can be customised.

Variables persist through all flows unless the event crosses a ***transport boundary***, an example of this would be requesting via HTTP.

# Trigger flows to run synchronously and asynchronously

## Passing events between flows using asynchronous queues

When using Flow Reference, events are passed synchronously between flows.

Asynchronous passing of event objects between flows may be wanted to:
- Achieve higher levels of parallelism in specific stages of processing
- Allow for more-specific tuning of areas with a flows architecture
- Distribute work across a cluster
- Communicate with another application running in the same Mule domain
- Implement simple queueing that does not justify a JMS broker

This can be accomplished using the ***VM connector***.

### Using the VM connector

The connector is used for ***intra and inter application communication*** through asynchronous queues.

The way you use the connector is:
- Add the VM connector to the project
- Configure a global element configurations
	- Specify a queue name and type
		- Default in-memory queues are used
		- Queues can be:
			- **Transient** - queues are faster but are lost in a system crash
			- **Persistent** - queues are slower but reliable 
- Use operations to publish and/or consume events

# Encapsulate global elements in separate config files

## Separating apps into multiple configuration files

Just as we separated flows into multiple flows, we also want to separate config files into multiple config files.

Monolithic files are difficult to read and maintain, separating an application into multiple config files makes code easier to:
- Read
- Work with
- Test
- Maintain

### Encapsulating global elements in a config file

If global elements are referenced from a file and they are defined in other various and unrelated files then it can become confusing and hard to find them.

A good solution is to put most global elements into one config file, that way all the other files reference the one config file. If a global element is specific to and only used in one file, it can be left there.

## Creating multiple applications

Your flows will also be separated in different apps/projects. By separating functionalities into multiple apps, it allows managing and monitoring them as separate entities.

You can run more than one app at a time by creating a run configuration.

### Sharing global elements between apps

![[Pasted image 20240917144404.png]]

# Specify application properties in a separate properties file and use them in the application

## Application properties

- Provide an easier way to manage connector properties, credentials and other config
- Replace static values
- Defined in either:
	- YAML file
	- Properties file
- Implemented using property placeholders
- Can be encrypted
- Can be overridden by system properties when deployed to different environments

## Defining application properties

![[Pasted image 20240917162123.png]]

### Syntax

In global element configs and event processors:
- `${db.port}`

In DataWeave expressions:
- `${port: Mule::p('db.port')}`

## Overriding property values in different environments

System properties can override property values when deploying an application to a different environment.

Setting system properties (JVM parameters) from:
- Anypoint Studio - Run > Run Configurations > Arguments
- Command line - `mule -M-Ddb.database=training2 -M-Ddb.password=mule2`


# Describe the purpose of each file and folder in a Mule project

## Mule applications are Maven projects

***Maven*** is a tool for building and managing any Java-based project that provides:
- A standard way to build projects
- A clear definition of what a project consists
- An easy way to publish project information
- A way to share JARs across several projects

Maven manages a project's build, reporting and documentation from a central piece of information - the ***project object model (POM)***

A Maven build produces one or more ***artifacts***, like a compiled JAR
- Each artifact has a(n):
	- Group ID (usually a reserved domain name, like com.example.foo)
	- Artifact ID (just a name)
	- Version string

## The POM (Project Object Model)

The ***POM file*** is an XML file that contains info about the project and configuration details used by Maven to build the project including:
- Info like:
	- Version
	- Description
	- Developers
	- etc
- Project Dependencies
- Plugins or goals that can be executed

# Define and manage application metadata

## Defining metadata

Its often beneficial to define metadata for an application:
- For the output structures required for transformations
- For the output of operations that can connect to data sources of different structures
- For the output of connectors that are not DataSense enabled
	- And do not automatically provide metadata about the expected input and output

## Ways to access the Metadata Editor

![[Pasted image 20240917174843.png]]

## Where is the metadata stored?

In the ***application-types.xml*** in src/main/resources

![[Pasted image 20240917175055.png]]

