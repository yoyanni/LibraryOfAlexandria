
# Multicast events

## Routers

Routers send events to one or more groups of event processors (routes)

The different types of routers are:
- **Choice** - one route executed based on condition
- **First Successful** - routes executed sequentially until one is executed
- **Round Robin** - one route executed, the route is selected by iterating through a list maintained across executions
- **Scatter-Gather** - All routes executed concurrently

### The Scatter-Gather router

The router sends the event to each route concurrently and returns a collection of all results

A collection is an object of objects, each object contains attributes and payload from each Mule event returned from a flow.
# Route events based on conditions

## The Choice routers

Sends the event to one route based on conditions, the conditions are written with DataWeave

![[Pasted image 20240918122408.png]]
# Validate events
## Validators

Validators provide a way to test some conditions are met and throw an error if the validation fails.

To use the validator:
- Add the validation module to a project
- Select a validation operation

![[Pasted image 20240918131644.png]]