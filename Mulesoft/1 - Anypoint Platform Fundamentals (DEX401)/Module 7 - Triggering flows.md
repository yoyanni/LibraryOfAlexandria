
# Read and write files

There are four connectors for working with files and folders
- File (for locally mounted file system)
- FTP
- FTPS
- SFTP

All have the same set of operations and they behave almost identically, supporting:
- File matching functionality
- File locking
- Overwriting, appending and generating new files

## Using the File connector

Add the File module to the project and create a global element configuration (creating the config isn't required but is a best practise). Set the working directory that will be the root for every path used with the connector. Use one of the connector operations and specify its properties.

On **CloudHub**, the connector can only be used with the `/tmp folder`. 

On **Customer-hosted** Mule runtimes, the account running Mule must have read and/or write permissions on the specified directories.

Be careful not to permanently delete or overwrite files, move or rename them after processing.

## Trigger a flow when a new file is created or updated

Use the ***On New or Updated File*** listener
- Polls a directory for files that have been created or updated
- One message is generated for each file that is found

Multiple ways to ensure a file is new
- Delete each file after it has been processed so all files in the next poll will be new
- Move each file to a different directory after it has been processed
- Rename a file after it has been processed and filter the files to be processed
- Save and compare the file creation or modification times


# Trigger flows when files are added or updated

## Synchronising data from one system to another

The general process is:
- First time, all data needs to be synced
- After that, new data needs to be synced
- This requires a field with ordered values to identify processed items

How do you determine what is new and needs to be synced?
- First sync must store the highest field value for any item in the data set
- Subsequent syncs, retrieve the value and compare the value of each item and see if it is larger

The field with ordered values is often a:
- Record ID
- Creation or modification timestamp

## Introducing watermarks

The timestamp or ID that is stored each sync and then retrieved and compared against in the next sync is called a watermark.

### Types of watermarking in Mule

***Automatic***
- The saving, retrieving, and comparing is automatically handled for you
- Available for several connector listeners
	- On New or Updated File
	- On Table Row
- Restricted in how you can specify what items/records are retrieved

***Manual***
- You handle saving, retrieving, and comparing the watermark
- More flexible in that you specify exactly what records you want retrieved

## Using automatic watermarking with files

There is a watermarking option for the ***On New and Updated Files*** operation for the family of file connectors. There are two watermarking modes:
- CREATED_TIMESTAMP
- MODIFIED_TIMESTAMP

# Trigger flows when new records are added to a database table

## Triggering a flow for each row in a database table

The Database connector has an ***On Table Row*** operation that is triggered for every row in a table.

The operation can handle:
- Generating the query
- Watermarking
- Idempotency across concurrent requests

You can specify one, both, or neither of:
- Watermark column
- ID column

### Using automatic watermarking with database tables

When a watermark column is specified, this query is automatically generated and used

`SELECT * FROM table WHERE table_column > :watermark`

On each poll, the component will go through all the retrieved rows and store the maximum value obtained.

## Handling idempotency across concurrent requests

A new poll can be exited before the watermark is updated if:
- The poll interval is small
- The amount of rows is big
- Processing one single row takes too much time

To avoid a record being processed more than once:
- Specify an ID column
	- A unique identifier for the row
- The listener will make sure the row is not processed again if
	- It has already been retrieved and 
	- Processing of it hasn't finished yet



# Schedule flows to run at a certain time or frequency

## Handling watermarking manually

The general process:
- Schedule when a flow should be executed
- Give the watermark a default value
- On the first sync
	- Determine a new watermark value
	- Store the watermark value so it is available in the future to other flow executions
- On later syncs
	- Retrieve the watermark from storage
	- Check if each item in the data set should be retrieved based on the watermark value

## Triggering flows at a certain time or frequency

Some connector event sources use a scheduling strategy to trigger a flow
- Like "On New or Update File" and "On Table Row"

To trigger any flow at any time, use the ***Scheduler*** event source

![[Pasted image 20240919200249.png]]

## Two types of scheduling strategies

**Fixed frequency** - the default is to poll every 1000 milliseconds

**Cron**
- A standard for describing time and date information
- Can specify either
	- An event to occur just once at a certain time
	- A recurring event on some frequency

![[Pasted image 20240919200638.png]]



# Persist and share data in flows using the Object Store

## Persisting data across executions of flows

![[Pasted image 20240919200749.png]]

## Using the Object Store connector for watermarking

![[Pasted image 20240919201018.png]]

# Publish and consume JMS messages

## Java Messaging Service

![[Pasted image 20240919201902.png]]

## Using the JMS connector

![[Pasted image 20240919201927.png]]