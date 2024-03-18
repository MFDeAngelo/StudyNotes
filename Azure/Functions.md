## Functions

### General
event hub triggers shouldn't use data lake storage
Don't leave background tasks unresolved when finishing function.
functions within a function app share total memory available
config in function.json
function apps have the posibility of processing events more than once.
Settings can be altered using (requires restart):
az functionapp config appsettings set [--ids]
                                      [--name]
                                      [--resource-group]
                                      [--settings]
                                      [--slot]
                                      [--slot-settings]
                                      [--subscription]
Triggers that support dynamic concurrency
* Queues: send batch updates
* Blobs: Send batch updates
* Service Bus: 



### Storage Account
Requires a storage account for triggers, bindings, and logging.
Storage account must support blob queue and tables
Avoid sharing storage account between function apps


### Triggers and Bindings
1 trigger. Any number of bindings.
[suported Bindings](https://learn.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?tabs=isolated-process%2Cpython-v2&pivots=programming-language-csharp#supported-bindingsa)
[Examples](https://learn.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?tabs=isolated-process%2Cpython-v2&pivots=programming-language-csharp#bindings-code-examples)
Can create custom bindings, only in .NET.


### Hosting
Can deploy as serverless, part of an App Service, or into custom containers
Plans:
* Consumption
  * Timeout default 5 min, max at 10 min
  * Has cold starts
  * For volitile number of requests
* Premium
  * Timeout at 30 min, unlimited max
  * At least one instance ready at all times. Warming others as limits are approching
  * Work on VLAN
* Dedicated (App Service) plan
  * Timeout at 30 min, unlimited max
  * Run functions on an existing vm

One function app can handle 600 concurrent running functions (consumption)

Max Burst is maximum number of instances allowed
Updated instances available via CLI (only premium):
az functionapp update -g <RESOURCE_GROUP> -n <FUNCTION_APP_NAME> --set siteConfig.minimumElasticInstanceCount=<YOUR_ALWAYS_READY_COUNT>
Scale ups happen once every 30 seconds

### Durable Functions
Are stateful
Application Patterns:
* Function Chaining
* Fan-out/fan-in
* Async Http apis
* monitoring
* Human interaction
* Aggregator

### Deployment
If you reference functions definitions externally, then you must manually sync the triggers (A restart works.)

### Security
Two kinds of keys:
function: single function call
host: all functions on host
Master: admin access to REST APIs
System: Used by internal systems to authorize access to function





#### Azure Service Bus
Should be used to pass messages larger than 64 KB between functions

#### Event Hub
If an event hub event function fails, then a retry policy can pick it up. otherwise, the event is skipped.

#### Event Grid
Sends an HTTP request when an event happens in a publisher.
Functions can handle event grid events
Prefer Event Grid trigger over HTTP trigger when handling http requests sent by event grid

#### Queues
Lock a file until all the services have tried processing. if all fail, then send to poison queue
