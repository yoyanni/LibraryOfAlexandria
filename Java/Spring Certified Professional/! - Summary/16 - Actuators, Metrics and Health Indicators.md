# What are Actuators?

They are a set of built-in features that help monitor and manage a Spring Boot application. 

# What value do they provide?

They provide endpoints to expose operational information about the running application, such as its health, metrics, environment properties, configuration details, and more.

- Monitoring without having to implement yourself
- Framework to easily gather and return metrics and health indicators
- Integration with 3rd party monitoring system for aggregation and visualisation

## What do we do with the Actuator data?

Actuator alone does not provide anything except REST endpoints. 

To add value, this data needs to be gathered, persisted, aggregated and visualised for easy consumption.

### What are some External monitoring systems that can be integrated?

[[3 - Health Indicators#External monitoring systems that can be integrated with Actuator|Answer]]
# How does it work?

By adding the Actuator library it adds many monitoring features which are accessible via HTTP endpoints:
- `/actuator/info`
- `/actuator/health`
- `/actuator/metrics`

## What does `/info` provide?

- General data
- Custom data
- Build information
- Details about latest commit

[[0 - Actuators, Metrics and Health Indicators#`/actuator/info`|Example]]

## What does `/health` provide?

- Application health status

[[0 - Actuators, Metrics and Health Indicators#`/actuator/health`|Example]]


## ## What does `/metrics` provide?

- List of generic and custom metrics which are measured by the application.

[[0 - Actuators, Metrics and Health Indicators#`/actuator/metrics`|Example]]

[[0 - Actuators, Metrics and Health Indicators#Example|Example of accessing one of the specific endpoints]]


# Starter Dependency

`spring-boot-starter-actuator` must be added in the dependencies

# What are some of the available actuator endpoints?

[[1 - Setting up Actuators#Some of the Available Actuator Endpoints - 1|Answer]]
[[1 - Setting up Actuators#Some of the Available Actuator Endpoints - 2|Answer continued]]

## What is the difference between Enabled and Exposed?

Enabled means, the given endpoint is created and its bean exists in the app context.
- All endpoints are enabled except `shutdown` by **default**

Exposed means, The given endpoint is accessible via JMX or HTTP.
- HTTP Default - only `health` exposed
- JMX Default - all enabled endpoints are exposed

[[1 - Setting up Actuators#Actuator Endpoints Enabled vs Exposed|NOTE..]]

## Can you tell me more about the HTTP endpoints?

They are mapped to `/actuator/xxx` by default which is [[1 - Setting up Actuators#HTTP Actuator Endpoints|customisable]].

For security reasons, only one endpoint is exposed by default.
- `/actuator/health`
- Secure actuator URLs using Spring Security

## Can you tell me more about exposing endpoints?

If endpoints are exposed explicitly, default are overridden.

[[1 - Setting up Actuators#Exposing HTTP Endpoints|Examples]]

## Example

[[1 - Setting up Actuators#Secure Endpoints - Aligned with Spring Security|Secure Endpoints aligned with Spring Security]]



# Metrics
## How do you collect metrics?

Spring Boot 2.0 uses the Micrometer library which include multi-dimensional metrics. It is designed to add little to no overhead to your metrics collection activity.

It instruments your JVM-based application code without vendor locking it, specifically **SLF4J** is used for metrics.

## What are Custom Metrics?

Custom metrics can be measured using Micrometer classes such as:
- Counter
- Gauge
- Timer
- DistributionSummary

- Classes are created or registered with a **MeterRegistry** bean
- Custom metric names are listed on the `/actuator/metrics` endpoint
- Custom metric data can be fetched at `/actuator/metrics/[custom-metric-name]`

### Example of MeterRegistry - Timer

[[2 - Metrics#MeterRegistry - Timer|Example]]

### Example of DistributionSummary

[[2 - Metrics#Recording to a `DistributionSummary`|Example]]
## What are Hierarchical Metrics?

- Naming convention: 
	- key/value attribute pairs that are separated by periods
- Characteristics:
	- Consistent naming convention is hard to achieve
	- Adding new attributes could break existing queries

[[2 - Metrics#Hierarchical Metrics|Details]]

## What are Dimensional Metrics?

- Metrics are tagged (a.k.a dimensional)
- Characteristics:
	- Flexible naming convention
	- Adding a new attribute to a query is easy

# Health Indicators

## Default behaviour

Health endpoint shows only [[3 - Health Indicators#Default Behaviour|basic health information]].

## How can I display more Health details?

[[3 - Health Indicators#More Health Details Possible|Answer]]

### What if I don't want to see that many?

With the help of **Groups**, you can group together multiple health indicators for simplicity.\

[[3 - Health Indicators#Group Health Indicators|Group examples and how to set up]]

[[3 - Health Indicators#Configure Health Indicator Group Individually|Show more details for grouped indicators]]


## List of Auto-configured Indicators

Many health indicators are setup automatically, such as:
- Disk Space
- DataSource
- Cassandra
- JMS
- MongoDB
- many more..

## How to setup custom health indicator?

- Create a class which implements the **HealthIndicator** interface
	- Then override the `health()` to return the status
- OR
- Extend **AbstractHealthIndicator**
	- Override the `doHealthCheck()` method

[[3 - Health Indicators#Custom Health Indicators|More Details]]

[[3 - Health Indicators#Implementing a custom Health Indicator|Example of custom health indicator]]

## How to add details to custom Indicator?

[[3 - Health Indicators#Adding Detailed Health Indicator Information|Answer]]

## What are the Health Indicator Statuses?

Built in status values are:
- DOWN
- OUT_OF_SERVICE
- UNKNOWN
- UP

[[3 - Health Indicators#Health Indicator statuses|How to override severity order of status?]]
## 