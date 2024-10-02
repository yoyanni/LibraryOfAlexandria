In this module, you will:
- Log event data
- Debug Mule applications
- Read and write event properties
- Write expressions with the DataWeave expression language
- Create variables

# Log event data

Review the structure of the Mule event [[Module 4 - Building APIs#What happens when a Mule 4 app is triggered?|here]]. 

## Viewing event data at the design time

You can view event data at design time in Anypoint Studio using DataSense:
- In the Transform Message component
- In the DataSense Explorer
- When writing expressions using auto-completion

***DataSense*** is the ability to proactively discover metadata from internal and external resources
- Avoids manual discovery of data info
- Facilitates transformations by providing DataWeave expected input or output

### DataSense Notification and Indicator

- If DataSense is timing out, a notification will appear
- A red dot DataSense indicator will also appear in the lower-left corner
	- Clicking it allows inspection of the DW process and restarting if required

![[Pasted image 20240916115221.png]]

## Viewing event data at run time

At run time:
- In the client when making a request
- For deployed applications, in the log files
- In the Anypoint Studio console by using a Logger
- In Anypoint Studio using the Visual Debugger
	- Most comprehensive way


# Debug Mule applications
## View Event data by logging it

Add a ***Logger*** component to a flow and view it output, its output is displayed:
- In the **Console view**, from Anypoint Studio
- In the **log files**, from deployed applications

## Debugging applications with the Mule Debugger

You can add breakpoints to processors and step through the app, it enables you to:
- View event properties and values
- View and evaluate DataWeave expressions
- Manage breakpoints from list

By default, Debugger listens for incoming TCP connections one localhost port 6666 (can be changed).

![[Pasted image 20240916151702.png]]

# Read and write event properties

## Changes to the event object

Previously we saw change to an event as it moved through a flow and we set the payload.

What happens to the event object when calls are made to an **external resource** from a flow?
- for example, when you call a web service and get return data?

![[Pasted image 20240916162010.png]]

## Setting the event payload

To set the payload, use the **Set Payload** transformer

### Setting data returned from HTTP Listeners

You can do that by going to the properties view > Response tab in the HTTP Listener.

### Setting data sent to HTTP requests

You do that by going to properties view > General in HTTP Request.



# Write expressions with the DataWeave expression language

## The DataWeave expression language

DataWeave is a case-sensitive, Mule-specific expression and transformation language, it can be used to do the following:
- Access and evaluate data in the:
	- Payload
	- Attributes
	- Variables 

It is accessible and usable from all event processors and global elements
- Used to modify the way the processors act upon the event (eg. Routing)

## Types of DataWeave expressions

- Standalone scripts
	- Can be generated using the Transform Message graphical editor
- Inline expressions
	- Dynamically set the value of properties in an event processor or global configuration element
	- Are enclosed in `#[]`

## Referencing Mule objects in DataWeave expressions

Allows you to accesses the following Mule objects:
- Server
- Mule instance
- Mule application
- Mule flow
- Mule event
	- Variables - `#[vars.foo]`
	- Mule message - `#[message.payload]`
		- Attribute - `#[attribute.queryParams.param1]`
		- Payload - `#[payload]`

## Accessing event attributes

![[Pasted image 20240916181253.png]]

### Using selectors in DataWeave expressions

![[Pasted image 20240916181447.png]]

### Using operators in DataWeave expressions

![[Pasted image 20240916181521.png]]
### Using conditional logic statements in DataWeave 

![[Pasted image 20240916181637.png]]

### Using DataWeave functions

![[Pasted image 20240916181856.png]]

# Create variables

You can create variables to store metadata for the Mule event by using the **Set Variable** transformer. 

You can reference the variable using the keyword **vars** in expressions. (`#[vars.foo]`)

