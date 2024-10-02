In this module, we will:

- Create the API specification using a standardised API description language (RAML - Restful API Modelling Language)
- Then learn to test it with users without writing code
	- Using Most APIs
- Make APIs discoverable by adding them to the private Anypoint Exchange
- Create public API portals for external developers.

![[Pasted image 20240909184903.png]]
# What is Spec Driven Development?

A development process where your app is built in two distinct phases:
- The creation of a spec (the design phase)
- Development of code to match the spec (the dev phase)
## What options are there for defining APIs?

- Hand coding
- Apiary - API Blueprint
	- Document-like look
	- Markdown language
	- Structure is lost
- Swagger - OpenAPI Spec (OAS)
	- JSON
	- Descriptive
	- Generated OAS from code 
	- Great for documentation
- AsyncAPI Spec - Event-driven API Spec
	- Open Source
	- Technology agnostic
- RAML
	- Open source
	- API design in a succinct human-like language
	- Support structured files and inheritance

###  What is RAML?

A simple, structured and succinct way of describing RESTful APIs. It is a non-proprietary, vendor-neutral open specification which was developed to help out the current API ecosystem, it:
- Encourages reuse
- Enables discovery and pattern-sharing
- Aims for merit-based emergence of best-practises

RAML files can be used to auto-generate:
- Documentation
- Mocked endpoints
- Interfaces for API implementations
- etc..

#### RAML Syntax

RAML is based on YAML and JSON which uses a human-readable data serialisation format where data structure hierarchy is specified by indentation.

![[Pasted image 20240909190644.png]]
##### How to define resources and methods?

RAML syntax include:
- **nodes** - are resources that begin with a slash 
- **facets**  - are special configurations that can be applied to nodes. 
 
Any methods and parameters nested under a resource belong to and act upon that resource. Nested resources are used for a subset of a resource:
- URI parameter are enclosed in `{}`

![[Pasted image 20240909191151.png]]