# Store DataWeave scripts in external files

## Exporting DataWeave code to a DWL file

In the Transform Message component, click the Edit current target button and set source code to file.
- Transform is saved in a DWL file
- Those files are stored in `src/main/resources`

![[Pasted image 20240919123206.png]]

### Reusing DataWeave code

A single script can be stored in an ***external DWL file*** and referenced:
- In the Transform Message component using the resource attribute
- In an element that has an expression property (like the Choice router) using the `${file::filename}` syntax

You can also create ***modules*** (libraries) of reusable DataWeave functions.

### Creating multiple transformations

You can also create multiple transformations with one Transform Message component

![[Pasted image 20240919123837.png]]


# Write DataWeave expressions for basic XML, JSON, and Java transformations

## Writing DataWeave transformation expressions
### The DataWeave expression is a data model for the output

It is not dependent upon the types of the input and output, just their structures. Its against this model that the transform executes.

The data model of the produced output can consist of three different types of data:
- Objects
- Arrays
- Simple literals

#### Example DataWeave transformation expression

![[Pasted image 20240919133811.png]]

#### The output directive

![[Pasted image 20240919134035.png]]

### Two types of DataWeave errors

***Scripting errors*** - a problem with the syntax.

***Formatting errors***
- A problem with how the transformation from one format to another is written
- For example, a script to output XML that does not specify a data structure with a single root node

If you get an error, transform the input to ***application/dw***, if the transformation is successful, then the error is likely a formatting error.

### Comments in DataWeave expressions

Java-like syntax is used for comments

`// My single-line comment here`

```
/** My multi-line 
comment here */
```

## Writing expressions for JSON or Java input and output

[[#The DataWeave expression is a data model for the output|Data model consists of..]]

![[Pasted image 20240919140159.png]]

## Writing expressions for XML output

XML can only have one top-level value and that value must be an object with one property.

![[Pasted image 20240919140541.png]]

### Specifying attributes for XML output

Use `@(attName: attValue)` to create an attribute

![[Pasted image 20240919140813.png]]

## Writing expressions for XML input

By default, only XML elements, not the attributes, are created as JSON fields or Java object properties.

Use `@` to reference attributes.

![[Pasted image 20240919141303.png]]




# Write DataWeave transformations for complex data structures with repeated elements

## Transforming complex data structures with arrays

Use the ***map*** function to apply a transformation to each element in an array.
- The input array can be JSON or Java
- Returns an array of elements

![[Pasted image 20240919143614.png]]

### The transformation function (or lambda)

Inside the transformation function
- `$$` refers to the index
- `$` refers to the value

![[Pasted image 20240919143917.png]]

#### Explicit arguments

![[Pasted image 20240919144140.png]]


## Transforming complex XML data structures

### Writing expressions for XML output

When mapping array element (JSON or Java) to XML, wrap the map function in `{(...)}`
- `{}`are defining the object
- `()` are transforming each element in the array as a key/value pair

![[Pasted image 20240919150319.png]]
![[Pasted image 20240919150359.png]]

### Writing expressions for XML input

Use `.*` selector to reference repeated elements

 ![[Pasted image 20240919150539.png]]


# Define and use global and local variables and functions

## Defining and using global variables

By using the ***var*** directive in the header you can define a variable, you can assign it a constant or a lambda expression.

DataWeave is a functional programming language where variables behave just like functions.

Global variables can be referenced anywhere in the body.

![[Pasted image 20240919160308.png]]

### Using variables in a syntax similar to traditional functions

An alternative syntax is available to access lambda expressions assigned to a variable as functions, may help the code being clearer or more readable.

![[Pasted image 20240919160629.png]]

## Defining and using local variables

To define a local variable you will have to use the ***do*** keyword with the syntax.

`do {<variable declaration header> --- body}`

Local variables can only be referenced from within the scope of the expression where they are initialised.

![[Pasted image 20240919161025.png]]

# Coerce and format strings, numbers and dates

## Using the `as` operator for type coercion

Defined types included:
- Any (the top-level type)
- Null, String, Number, Boolean
- Object, Array, Range
- Date, Time, LocalTime, DateTime, LocalDateTime, TimeZone, Period
- More...

Examples: 
- `price: payload.price as Number`
- `price: $.price as Number {class: "java.lang.Double"}`

### Using format patterns

Use the metadata ***format*** schema property to format numbers and dates

```
price as Number as String {format: "###.00"},
someDate as DateTime {format: "yyyyMMddHHmm"}
```

# Define and use custom data types

## Defining and using custom data types

Create your customer date type by using the type header directive
- Recommended to lead with an uppercase letter
- No special characters

![[Pasted image 20240919171614.png]]

## Transforming objects to POJOs

![[Pasted image 20240919171715.png]]



# Use DataWeave functions

## Review: Using DataWeave functions

![[Pasted image 20240919172916.png]]

## Calling functions that have a lambda expression as a parameter

The alternate notation can make calling functions that have a lambda expression as a parameter easier to read
```
sizeOf(filter(payload, (value) -> value.age > 30))
sizeOf(payload filter $.age > 30)
```

When using a series of functions, the first functions in the chain is excuted first:
- Filter then orderBy then groupBy in the below case

`flights filter ($.availableSeats > 30) orderBy $.price groupBy $.toAirport`

## Using DataWeave functions not in the Core module

![[Pasted image 20240919174014.png]]


# Use flow results in DataWeave expressions

## Calling flows from a DataWeave expression

Use the ***lookup*** function:
- First arguments is the name of the flow to be called
	- Cannot call subflows
- Second argument is the payload to send to the flow as a map
- Whatever payload the flow returns is what the expression returns

`{a: lookup("myFlow", {b: "Hello"})}