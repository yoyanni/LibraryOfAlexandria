# What is Anypoint Platform?

Anypoint Platform is a unified platform that has all of the components needed for the entire lifecycle. It is used for designing, developing and managing APIs and integrations. 

- Uniquely built as a single product
- Deploy anywhere
- Wide range of use cases

It includes tools such as:
- Design Centre - Productizes and designed for ease of consumption
- Exchange - Discoverable and accessible through self-service
- Management Centre - Easily managed for Security, Scalability and Performance

The platform is supported by the ***Mule run-time*** and is the application server for Mulesoft applications. 

Regardless of where this is hosted, it has the same connectivity options such as:
- Databases
- FTP, files
- Web Services
- Social apps
- etc..

# Unique benefits for each layer

- Speed of Delivery - Prebuilt APIs, connectors and templates, automated unit and integrations testing for CI/CD
- Actionable visibility - helps by tracing calls and pin-pointing problems quickly
- Secure by design - Carefully curated assets for specific users or groups 
- Future-proof architecture:
	- Extensible - using custom connector
	- Re-composable - using API's to loosely couple architecture to isolate impact of change allowing bending, not braking of the system
- Intentional Self Service - Embedded capabilities to discover and re-use APIs

![[Pasted image 20240909155411.png]]

# What is MuleSoft Catalyst Approach?

## What are the three aspects?

- Business outcomes - define outcomes with clear KPIs and align stakeholders
- Technology delivery - quick start with Anypoint Platform, building APIs and integrations
- Organisation enablement - ensuring the organisation is ready to use and adopt the Anypoint Platform
## What are the 6 Playbooks for best practises?

- Business Outcomes
- Technology delivery
	- Anypoint Platform
	- Projects
- Organisation enablement
	- Center for Enablement
	- Internal support
	- Training

### What are the 4 stages of a blueprint?

- Plan for success
- Establish the foundation
- Build to scale
- Measure impact



# What Tools are there for each [[Module 1 - Introducing Application Networks and API-led Connectivity#API Development Cycle|Development phase]]?

- API Specifications phase - produces an API spec (RAML):
	- Design - API designer
	- Simulate - API designer (Mocking Service, API console)
	- Feedback - Exchange, API portal
	- Validate - API notebook
- API Implementation phase - produces a web service with API:
	- Build - Anypoint Studio, Composer
	- Test - MUnit
- API Management phase - maintains the API:
	- Deploy Service - Runtime Manager
	- Create & Deploy proxy - API Manager
	- Secure - API Manager
	- Monitor - Anypoint Monitoring
	- Analyse - API Analytics
	- Troubleshoot - Runtime Manager, API   and Visualizer
	- Scale - Runtime Manager, API Manager and Visualizer
	- Respond - Repeat all the phases for updating the API

# The API lifecycle: Discovery and Consumption

![[Pasted image 20240909172729.png]]

## Heart of the Discovery and Consumption cycle: Anypoint Exchange

It is a library of assets which is the central repository that is critical for the success of building an application network.

It provides a place where assets can be published for discovery and reuse is heavily helped by strong search capabilities.

### What does (and should) Exchange contain?

- Public assets available for everyone
- Private content only available to your organisation
- Anything that's required for integration projects
	- APIs
	- connectors
	- diagrams
	- videos
	- links
	- etc..
### What happens with REST APIs in Exchange?

When REST APIs are added to Exchange, an API Portal is automatically created for it.

An API Portal has:
- Auto-generated API documentation
- An API console for consuming and testing APIs
- An automatically generated API endpoint that uses a mocking service to allow the API to be tested without having to implement it

The portal can be shared with both internal and external users.

### Automatic connector creation in Exchange

When an asset is published to Exchange, a connector is created automatically which is called a REST Connect.

### Example: Using Exchange - Success of C4E in action

Central IT publishes APIs and Templates to Exchange which then allows Line of Business (LoB) employees and other teams to access these and use them for their own projects.

![[Pasted image 20240909175750.png]]
# Design Center

## What are the two applications?

- API Designer - Web app for designing, documenting and mocking APIs
	- It allows to create API specifications or API fragments using RAML or OAS specification languages
- Anypoint Studio - Desktop IDE for implementing APIs and building integration applications
	- Mule applications can be created, which are essentially Java apps using Spring framework
	- Creates XML code visually by adding components to flows
	- Uses connectors and other re-useable assets from Exchange

# Mule Runtime

Mule is the runtime engine of Anypoint Platform, it is a:
- Lightweight integration and automation platform
	- Allows apps to connect together, enabling exchange of data
- Decouples point-to-point integrations
	- Non-mule apps talk to a Mule runtime instead of directly to each other
- Enforces policies for API governance
- Can be deployed anywhere

## What are Mule applications?

They are applications designed to transfer and transform data from system(s) to system(s). They are created by integration developers to tie together various subsystems.

The apps run on the Mule Runtime which enables:
- Consumption of inbound data in a predefined Mule message format
- Mule messages are transformed and routed to paths called **flows** and to stages called **components** or **processors**.
- Mule finally delivers the processed data to the recipient or destination

Mule apps can be deployed to anywhere a Mule Runtime is hosted.
