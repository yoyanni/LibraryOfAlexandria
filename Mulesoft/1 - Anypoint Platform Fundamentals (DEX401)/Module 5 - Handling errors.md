You can handle errors at the:
- Application level
- Flow level
- Processor level

# Default and Basic error handling

## Handling messaging errors

When an event is being processed through a Mule flow that throws an error, the normal form execution stops and the event is passed to the first processor (in an error scope, eg. **On Error**) in an error handler.

![[Pasted image 20240918135916.png]]

## Default error handler behaviour

If there is no error handler defined, a **Mule default error handler** is used which:
- Implicitly and globally handles all messaging errors thrown in Mule apps
- Stops execution of the flow and logs information about the error
- Cannot be configured

### Information about the error

When an error is thrown, an error object is added to the Mule event. It contains many properties but the important ones are:
- `error.description` - a string
- `error.errorType` - an object

Error types are identified by a namespace and an identifier, examples such as:
- `HTTP:UNAUTHORISED`
- `HTTP:CONNECTIVITY`
- `VALIDATION:INVALID_BOOLEAN`

### Error types follow a hierarchy

Each error type has a parent:
- `HTTP:UNAUTHORISED` has `MULE:CLIENT_SECURITY` as the parent, which has `MULE:SECURITY` as the parent
- `VALIDATION:INVALID_BOOLEAN` has `VALIDATION:VALIDATION` as the parent, which has `MULE: VALIDATION` as the parent

The error type `ANY` is the most general parent.

![[Pasted image 20240918141346.png]]

#### Error type hierarchy reference

Errors can be of two types:
- General - which can be handled
- Critical - which cannot be handled

![[Pasted image 20240920081956.png]]

# Use different error scopes to either handle an error and continue execution of the parent flow or propagate an error to the parent flow

## Creating error handlers

Error handlers can be added to
- An application (outside of any flows)
- A flow
- A selection of one or more event processors

### Adding error handler scopes

When creating an error, the first thing you do is add a scope. Each error handler can contain one or more error handler scopes.
- On Error Continue
- On Error Propagate

Each scope can contain any number of event processors.

![[Pasted image 20240918145813.png]]

#### Two types of error handling scopes

***On Error Propagate***:
- All processors in the error handling scope are executed
- At the end of the scope:
	- The rest of the flow that threw the error is not executed
	- It returns the **initial** error object with the event
		- The error is rethrown up to the next level and handled there
- An HTTP Listener which returns an ***error*** response to the client

***On Error Continue***:
- All processors in the error handling scope are executed
- At the end of the scope:
	- The rest of the flow that threw the error is not executed
	- It ***DOES NOT*** return the **initial** error object with the event
		- The event is passed up to the next level as if the flow execution had completed successfully
- An HTTP Listener returns a ***successful*** response

## Adding multiple error handler scopes

Each handler can contain multiple error handler scopes whether they be "On Error Continue" and/or "On Error Propagate".

Each handler scope specifies when it should be executed, the first handler whose conditions is met will be the handler.

### Specifying scope execution for specific error types

Set the ***type*** to ANY (the default) or one or more types of errors

### Specifying scope execution upon a specific condition

Set the **when** condition to a Boolean DataWeave expression:
- `HTTP:UNAUTHORISED`
- `error.errorType.namespace == 'HTTP'`
- `error.errorType.identifier == 'UNAUTHORISED'`
- `error.cause.message contains 'request unauthorised'`
- `error.cause.class contains 'http'`

# Handle messaging errors at the application, flow, and processor level

## Defining a default error handler for an application

A default error handler for the application ***only*** executes when there are not any flow or processor level error handlers.
 
Add an error handler outside a flow by putting it in the global (or any) config file. A config object is then used to set the default error handler of the app.

![[Pasted image 20240918152121.png]]

## Defining error handlers in flows

All flows (except subflows) can have their own error handlers and any number of scopes regarding that.

Errors that occur in the subflow will be handled by the calling flow.
### Which error scope handles an error?

If the flow ***has*** an error handler:
- Error is handled by the first error scope that has their condition met
- ***If no condition is met,*** the error is handled by the ***Mule default error handler*** NOT the scopes for the application's error handlers
	- Mule default error handler ***propagates*** the error up the execution chain

If a flow ***does not*** have an error handler:
- The error is handled by a scope in an application's default error handler
- Otherwise, the Mule default error handler.

## Handling errors at the processor level

For more fine grain error handling of elements within a flow, use the ***Try scope***, any number of processors can be added to it.

The Try scope has its own error handling section to which one or more error scopes can be added.

![[Pasted image 20240918185824.png]]

### Error handling behaviour in the Try scope

***On Error Propagate***:
- All processors in the error handling scope are executed
- At the end of the scope:
	- The rest of the Try scope is not executed
	- If a transaction is being handled, it is ***rolled back***
		- The error is rethrown up the execution chain to the parent flow, which handles the error
- A HTTP Listener returns an error response

***On Error Continue***:
- All processors in the error handling scope are executed
- At the end of the scope:
	- The rest of the Try scope is not executed
	- If a transaction is being handled, it is ***committed***
		- The error is rethrown up the execution chain to the parent flow, which continues execution
- A HTTP Listener returns a successful response

# Handle different types of errors, including custom errors

## Mapping errors for more granular error handling

If you have two HTTP Request operations that call different REST services, a connectivity failure on either produces the same error which makes it difficult to identify the source of the error.

You can map each error to different custom error types to differentiate between the two.

### Mapping to custom error types

For each module operation in a flow, each possible error type can be mapped to a custom error type.

You assign a custom namespace and identifier to distinguish them from other existing types within an application:
- Define namespaces related to the particular Mule application name or context
- Do not use existing module namespaces
- Identifiers and namespaces must consist only of capital letters and underscores



# Set the success and error response settings for an HTTP Listener

### Information returned for HTTP Listeners

By default, what is returned for a ***success response*** is:
- The payload
- A status code of 200

By default, what is returned for a ***error response*** is:
- The error description
- A status code of 500

These can be overridden for the HTTP Listener.

# Set reconnection strategies for system errors

## Applications can have two types of errors

***Messaging errors***:
- Thrown within a flow whenever a Mule event is involved

***System errors***:
- Thrown at the system-level when no Mule event is involved
- Errors that occur
	- During application start-up
	- When a connection to an external system fails
- Handled by a system error handling strategy
	- It is non-configurable
	- Logs the error and, for connection failures, executes the reconnection strategy

### Reconnection Strategies

Set for a connector (in Global Elements Properties) or for a specific connector operation (in Properties view).

![[Pasted image 20240918205424.png]]


## Error handling generated by APIkit

Interfaces created with APIkit have error handlers with multiple on Error Propagate scopes that handle APIkit errors
- The scopes set HTTP status codes and response messages.

The main routing flow has six error scopes:
- APIKIT:BAD_REQUEST > 400
- APIKIT:NOT_FOUND > 404
- APIKIT:METHOD_NOT_ALLOWED > 405
- APIKIT:NOT_ACCEPTABLE > 406
- APIKIT:UNSUPPORTED_MEDIA_TYPE > 415
- APIKIT:NOT_IMPLEMENTED > 501

### Integrating with APIkit error handling

You can modify the APIkit error scopes and add additional scopes, you also need to make sure the error handling in the application works as expected with the new interface router.
- ***On Error Continue*** - event in implementation is not passed back to main router flow
- ***On Error Propagate*** - error in implementation is propagated to main router flow
	- Payload and variables are propagated as well