## App Services

### General
Get a list of supported runtimes:
az webapp list-runtimes --os-type linux
IIS logging is not available in linux cuz no IIS

If the plan is configured to run five VM instances, then all apps in the plan run on all five instances.
Get a new service plan if:
* The app is resource intensive
* You want to scale independently of other apps
* The app needs to be in a different geographical location

### Deployment 
Manual:
* Git repo
* az webapp up -g $resourceGroup -n $appName --html
* Send zip
* FTPS

### Security
Dedicated auth services for app service
federated sign in
You can manage your own login page or use azure's
show outbound traffic:
az webapp show \
    --resource-group <group_name> \
    --name <app_name> \ 
    --query outboundIpAddresses \
    --output tsv

You can import or buy certs. you can also store in key vault
private cert requirements:
* Exported as a password-protected PFX file, encrypted using triple DES.
* Contains private key at least 2048 bits long
* Contains all intermediate certificates in the certificate chain

The free certificate comes with the following limitations:
* Doesn't support wildcard certificates.
* Doesn't support usage as a client certificate by using certificate thumbprint, which is planned for deprecation and removal.
* Doesn't support private DNS.
* Isn't exportable.
* Isn't supported in an App Service Environment (ASE).
* Only supports alphanumeric characters, dashes (-), and periods (.).

### Settings
Can configure appsettings.json overrides
General settings include:
* Stack settings
* Platform settings
  * ARR affinity
  * pipeline version(classic or yaml)
  * Always on
* Enable debugging (48 hr)
* Require client certs

You can handle different extension request using path mappings

Types of logs:
* Application
* Web server (can be sent to blobs)
* detailed error (copies of 400 responses)
* Failed Request tracing (of IIS components)
* deployment
System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
az webapp log tail --name appname --resource-group myResourceGroup


### Autoscaling
More expensive than manual scaling

Based on:
* Metrics
* Schedule

Metrics:
* CPU Percentage
* Memory Percentage
* Disk Queue Length (outstanding I/O requests)
* Data In
* Data Out
* Metrics from other service

Any scale out rule is met => scale out
All scale in rules are met => scale in

Scale out by instances or until metric condition is met
Configure autoscale notifications:
* When a scale operation occurs
* results of scale operation
* metrics are unavailable, or re-available

### Deployment Slots
available to:
* Web apps
* mobile backends
* APIs

Swapped settings:
* Handler Mappings
* webjobs content
* hybrid connections
* azure content delivery network
* service endpoints
* path mappings

Not swapped settings:
* Publishing endpoints
* custom domain names
* non-public certificates
* scale settings
* webjobs schedulers
* Ip restrictions
* Always on
* diagnostic log settings
* CORS
* virtual network integration
* managed identities
* settings that end with _EXTENSION_VERSION

Automated warm up in web.config under applicationInitialization

### Routing
x-ms-routing-name is the header or query param to tell you which slot a request is routed to
x-ms-routing-name=self is production
<webappname>.azurewebsites.net/?x-ms-routing-name=staging routes to staging
