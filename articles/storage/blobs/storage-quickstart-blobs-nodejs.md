---
title: "Quickstart: Azure Blob storage library v12 - JavaScript"
description: In this quickstart, you learn how to use the Azure Blob storage client library version 12 for JavaScript to create a container and a blob in Blob (object) storage. Next, you learn how to download the blob to your local computer, and how to list all of the blobs in a container.
author: mhopkins-msft

ms.author: mhopkins
ms.date: 11/19/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
---

# Quickstart: Azure Blob storage client library v12 for JavaScript

Get started with the Azure Blob storage client library v12 for JavaScript. Azure Blob storage is Microsoft's object storage solution for the cloud. Follow steps to install the package and try out example code for basic tasks. Blob storage is optimized for storing massive amounts of unstructured data.

> [!NOTE]
> To get started with the previous SDK version, see [Quickstart: Azure Blob storage client library for JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Use the Azure Blob storage client library v12 for JavaScript to:

* Create a container
* Upload a blob to Azure Storage
* List all of the blobs in a container
* Download the blob to your local computer
* Delete a container

[API reference documentation](/javascript/api/@azure/storage-blob) | [Library source code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Package (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## Prerequisites

* Azure subscription - [create one for free](https://azure.microsoft.com/free/)
* Azure storage account - [create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Current [Node.js](https://nodejs.org/en/download/) for your operating system.

## Setting up

This section walks you through preparing a project to work with the Azure Blob storage client library v12 for JavaScript.

### Create the project

Create a JavaScript application named *blob-quickstart-v12*.

1. In a console window (such as cmd, PowerShell, or Bash), create a new directory for the project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Switch to the newly created *blob-quickstart-v12* directory.

    ```console
    cd blob-quickstart-v12
    ```

1. Create a new text file called *package.json*. This file defines the Node.js project. Save this file in the *blob-quickstart-v12* directory. Here is the contents of the file:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    You can put your own name in for the `author` field, if you'd like.
   
### Install the package

While still in the *blob-quickstart-v12* directory, install the Azure Blob storage client library for JavaScript package by using the `npm install` command. This command reads the *package.json* file and installs the Azure Blob storage client library v12 for JavaScript package and all the libraries on which it depends.

```console
npm install
```

### Set up the app framework

From the project directory:

1. Open another new text file in your code editor
1. Add `require` calls to load Azure and Node.js modules
1. Create the structure for the program, including very basic exception handling

    Here's the code:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Save the new file as *blob-quickstart-v12.js* in the *blob-quickstart-v12* directory.

### Copy your credentials from the Azure portal

When the sample application makes a request to Azure Storage, it must be authorized. To authorize a request, add your storage account credentials to the application as a connection string. View your storage account credentials by following these steps:

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Locate your storage account.
3. In the **Settings** section of the storage account overview, select **Access keys**. Here, you can view your account access keys and the complete connection string for each key.
4. Find the **Connection string** value under **key1**, and select the **Copy** button to copy the connection string. You will add the connection string value to an environment variable in the next step.

    ![Screenshot showing how to copy a connection string from the Azure portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### Configure your storage connection string

After you have copied your connection string, write it to a new environment variable on the local machine running the application. To set the environment variable, open a console window, and follow the instructions for your operating system. Replace `<yourconnectionstring>` with your actual connection string.

#### Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

After you add the environment variable in Windows, you must start a new instance of the command window.

#### Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### Restart programs

After you add the environment variable, restart any running programs that will need to read the environment variable. For example, restart your development environment or editor before continuing.

## Object model

Azure Blob storage is optimized for storing massive amounts of unstructured data. Unstructured data is data that does not adhere to a particular data model or definition, such as text or binary data. Blob storage offers three types of resources:

* The storage account
* A container in the storage account
* A blob in the container

The following diagram shows the relationship between these resources.

![Diagram of Blob storage architecture](./media/storage-blob-introduction/blob1.png)

Use the following JavaScript classes to interact with these resources:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): The `BlobServiceClient` class allows you to manipulate Azure Storage resources and blob containers.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): The `ContainerClient` class allows you to manipulate Azure Storage containers and their blobs.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): The `BlobClient` class allows you to manipulate Azure Storage blobs.

## Code examples

These example code snippets show you how to perform the following with the Azure Blob storage client library for JavaScript:

* [Get the connection string](#get-the-connection-string)
* [Create a container](#create-a-container)
* [Upload blobs to a container](#upload-blobs-to-a-container)
* [List the blobs in a container](#list-the-blobs-in-a-container)
* [Download blobs](#download-blobs)
* [Delete a container](#delete-a-container)

### Get the connection string

The code below retrieves the connection string for the storage account from the environment variable created in the [Configure your storage connection string](#configure-your-storage-connection-string) section.

Add this code inside the `main` function:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### Create a container

Decide on a name for the new container. The code below appends a UUID value to the container name to ensure that it is unique.

> [!IMPORTANT]
> Container names must be lowercase. For more information about naming containers and blobs, see [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Create an instance of the [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) class by calling the [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) method. Then, call the [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) method to get a reference to a container. Finally, call [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) to actually create the container in your storage account.

Add this code to the end of the `main` function:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### Upload blobs to a container

The following code snippet:

1. Creates a text string to upload to a blob.
1. Gets a reference to a [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) object by calling the [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) method on the [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) from the [Create a container](#create-a-container) section.
1. Uploads the text string data to the blob by calling the [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) method.

Add this code to the end of the `main` function:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### List the blobs in a container

List the blobs in the container by calling the [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) method. In this case, only one blob has been added to the container, so the listing operation returns just that one blob.

Add this code to the end of the `main` function:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### Download blobs

Download the previously created blob by calling the [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) method. The example code includes a helper function called `streamToString` which is used to read a Node.js readable stream into a string.

Add this code to the end of the `main` function:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Add this helper function *after* the `main` function:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### Delete a container

The following code cleans up the resources the app created by removing the entire container using the [​delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) method. You can also delete the local files, if you like.

Add this code to the end of the `main` function:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## Run the code

This app creates a text string and uploads it to Blob storage. The example then lists the blob(s) in the container, downloads the blob, and displays the downloaded data.

From a console prompt, navigate to the directory containing the *blob-quickstart-v12.py* file, then execute the following `node` command to run the app.

```console
node blob-quickstart-v12.js
```

The output of the app is similar to the following example:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Step through the code in your debugger and check your Azure portal throughout the process. Check to see that the container is being created. You can open the blob inside the container and view the contents.

## Next steps

In this quickstart, you learned how to upload, download, and list blobs using JavaScript.

To see Blob storage sample apps, continue to:

> [!div class="nextstepaction"]
> [Azure Blob storage SDK v12 JavaScript samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* To learn more, see the [Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* For tutorials, samples, quick starts and other documentation, visit [Azure SDK for JavaScript documentation](/azure/javascript/).
