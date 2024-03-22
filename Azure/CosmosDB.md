## CosmosDB

* Account
  * Database
    * Container
      * Items
      * stored procs
      * triggers
      ...



Throughput modes
* Dedicated: Container specific, SLA backed
* Shared: Shared amoung all non-dedicated containers

* Provisioned: Set max.
* Serverless: All calls are handled
* Autoscale: scales specifics. Set maximum and minimum

%memory + %cpu + %IOPS of 1KB fetch = 1RU

API types:
* documents
* rows
* edges

TTL option available

### Consistency
* Strong
* Bounded staleness: Can define maximum lag in time or operations
* Session: Everything is in order for a user's actions
* Consistent prefix: only garenteed to be in order
* Eventual

### API support

Supports
* NoSQL
  * Prefered for starting new db
  * SQL syntax
* MongoDB
  * BSON
* PostgreSQL
* Apache Cassandra
  * Highly scalable. column oriented schema
  * NoSQL
* Table
  * Key/Value
  * lots of limitations in performance
* Apache Gremlin
  * Graph


### Code

#### Clients
Cosmos is build off of the idea of having an ID for everything. Create an account, get an id. create a container, get an id.

CosmosClient
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(databaseId, 10000);
DatabaseResponse readResponse = await database.ReadAsync();
await database.DeleteAsync();

// Set throughput to the minimum value of 400 RU/s
ContainerResponse simpleContainer = await database.CreateContainerIfNotExistsAsync(
    id: containerId,
    partitionKeyPath: partitionKey,
    throughput: 400);

Container container = database.GetContainer(containerId);
ContainerProperties containerProperties = await container.ReadContainerAsync();

ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder, new PartitionKey(salesOrder.AccountNumber));

string id = "[id]";
string accountNumber = "[partition-key]";
ItemResponse<SalesOrder> response = await container.ReadItemAsync(id, new PartitionKey(accountNumber));

#### Stored Procedures
javascript
Uses context like httpcontext

Seems to act like an HTTP API
(can send responses)

Creating document involves an onFinished callback for both success and failer. (err == null)
Only accepts arrays. (use JSON)

All collection functions return bools. Stored proc can timeout
Supports transactions

#### Triggers
pretrigger and posttrigger
pretriggers can validate
No inputs. Calls getContext().getRequest().getBody()
posttriggers are just call backs. Exceptions, roll the change back

#### Change feed
tracks all changes on a document (except deletes)
Push or pull models

prefer push unless:
* Reading changes from a particular key
* You need to control the pace of processing
* Doing a one time read (like data migration)

Change feed processor is the following 4 items:
* Monitored container (cosmosdb)
* Lease container (tracks event data)
* The instance handling events
* The code handling events

