#### Security
Controls can be set using Entra ID and RBAC amoung other things

#### General 

Can host static websites (should be activated in options)
When doing so, Azure handles access security (your rules don't matter)
You cannot access an archive directly. You must switch it to an active tier or copy it first

Metadata is passed as a header: x-ms-meta-name:string-value
Container metadata retrieval: GET/HEAD https://myaccount.blob.core.windows.net/mycontainer?restype=container
Blob: GET/HEAD https://myaccount.blob.core.windows.net/mycontainer/myblob?comp=metadata

replace metadata:
PUT https://myaccount.blob.core.windows.net/mycontainer?comp=metadata&restype=container
PUT https://myaccount.blob.core.windows.net/mycontainer/myblob?comp=metadata

The standard HTTP headers supported on containers include:
ETag
Last-Modified

The standard HTTP headers supported on blobs include:
ETag
Last-Modified
Content-Length
Content-Type
Content-MD5
Content-Encoding
Content-Language
Cache-Control
Origin
Range

#### Life Cycle

Example rules:

{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

Change rule:
az storage account management-policy create --account-name <storage-account> --policy @policy.json --resource-group <resource-group>


#### Code
1. BlobServiceClient
2. BlobContainerClient
3. BlobClient
