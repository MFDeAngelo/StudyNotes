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




