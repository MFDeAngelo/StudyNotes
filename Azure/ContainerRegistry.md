## Container Registry

### Tasks
Types:
* Quick: docker build & docker push in the cloud
* Automatically triggered:
  * On source code update
  * On base image update
  * On a schedule
* Multistep: yaml

az acr task create

az acr build builds and pushes.
az acr run --registry <myContainerRegistry> \
    --cmd '$Registry/sample/hello-world:v1' /dev/null

## Container Instances

### Benefits
* Fast startup (seconds)
* Container access with full domain name
* Issolate your application
* Custom sizes
* Mount shares directly for persistent storage

### Deployment
vial classic, or yaml

### Restart policies
* Always
* Never
* OnFailure

Stops the container when the script exits.
az container create ... --environment-variables 'hello'='world'
Good for secure values ^

Can define how to run a container using yaml:
az container create --resource-group myResourceGroup \
    --file secure-env.yaml \

apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups

### Storage
containers are stateless. All is lost if restarted.
Limits to mounting Azure File Share:
* Linux containers
* Container run as root
* CIFS Support required

az container create 
    --resource-group $ACI_PERS_RESOURCE_GROUP 
    --name hellofiles 
    --image mcr.microsoft.com/azuredocs/aci-hellofiles 
    --dns-name-label aci-demo  MUST BE UNIQUE
    --ports 80 
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME 
    --azure-file-volume-account-key $STORAGE_KEY 
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME 
    --azure-file-volume-mount-path /aci/logs/


Mount via Yaml

apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
