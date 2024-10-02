In this module, you will:
- Describe the options for deploying Mule applications
- Deploy Mule applications to CloudHub
- Use API Manager to create and deploy API proxies
- Use API Manager to restrict access to API proxies

![[Pasted image 20240910142212.png]]

# Deployment options

During development:
- Embedded Mule runtime in Anypoint Studio

Anything else (testing, Q&A, and production):
- CloudHub and CloudHub 2.0
	- PaaS component of Anypoint Platform
	- MuleSoft-hosted Mule runtimes on AWS
	- Cloud platform for integrations and APIs
- Customer-hosted Mule runtimes
- Anypoint Runtime Fabric
	- Customer-hosted container service of the runtime

Main benefit of CloudHub is the simplicity. 

![[Pasted image 20240910151604.png]]
## How to view deployed applications with Visualizer?

Visualizer provides a real-time view into your application architecture in a context that best suits your role.

- Organises APIs and apps into relational diagrams
	- Promotes best practises and layer-based architectural consistency
	- Pinpoints issues rapidly through root cause analysis
- Diagram data is secure automatically and dynamically updated 

## How to monitor the state of your application?

By using **Anypoint Monitoring**, it provides you visibility into integrations across your app network. The tools it provides are designed to reduce the time to identify and resolve issues.

# Deploying applications to CloudHub

Using the **Runtime Manager**, you can deploy from the Studio or the Platform. You must specify the number of workers and worker size.

## What are CloudHub workers?

A worker is a dedicated VM that runs a single Mule app in CloudHub. Each worker:
- Runs in a separate VM
- Deployed and Monitored independently
- Runs in a specific worker cloud in a region of the world

Workers can be scaled horizontally or vertically by changing the number of workers or the size of the worker. 
# Creating API proxies to manage access

An **API proxy** is an application that controls access to a web service, restricting access and usage through the use of an **API gateway**.

The **API Gateway** is a runtime designed and optimised to host an API or to open a connection to an API deployed to another runtime.
- Separates orchestration from implementation concerns.

The API Gateway is the point of control, it can:
- Determine which traffic is authorised to reach the backend services
- Meters the traffic flowing through
- Logs all transactions, collecting and tracking analytic data
- Applies runtime policies to enforce governance
	- Rate Limiting
	- Throttling
	- Caching

![[Pasted image 20240910162607.png]]
## Using API Manager to manage access to APIs

![[Pasted image 20240910163126.png]]

# Restricting access to APIs

You can use the **API Manager** to manage access to API proxies by:
- Defining SLA tiers
- Appling runtime policies

The API Gateway enforces these policies.

The **API Autodiscovery** is a mechanism that enables a deployed Mule application to:
- Download policies from API Manager
- Act as its own proxy %% Not sure about this %%

## Applying policies to restrict access

There are out-of-the-box policies for many common use cases, such as:
- Rate limiting
- Spike control
- Security

![[Pasted image 20240910165755.png]]

- You can define **custom** policies (using XML and YAML)
- You can apply multiple policies and set the order
- You can define automated policies to comply with common requirements

## Using SLA tiers to restrict access by client ID

A **Service Level Agreement** tier defines the number of requests that can be made per time frame to an API. 
## Enforcing access to APIs using SLA tiers

To enforce access, apply an SLA based rate limiting policy. SLA based policies require all applications that consume the API to:
- Register for access to a specific tier
	- From API Portal or Exchange (private vs public)
- Pass their client credentials in calls made to the API

### Requesting Access

![[Pasted image 20240910170924.png]]

## Adding client ID enforcement to API specs

You need to add client ID enforcement to the API spec for the:
- REST connector that is created for the API to enforce the authentication
- Required headers to automatically show up in the API console so you dont have to manually add them for every call