<properties 
   pageTitle="Introduktion til Azure Data sø butikker med Azure SDK til Node.js | Microsoft Azure"
   description="Lær, hvordan du bruger Node.js til at arbejde med sø datalager konti og filsystemet." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Introduktion til Azure Data sø Store med Azure SDK til Node.js

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)


Lær at bruge Azure SDK for Node.js til at oprette en Azure datalager sø konto og udføre grundlæggende handlinger, sådan som oprette mapper, upload og download-datafiler, skal du slette din konto, osv. Du kan finde flere oplysninger om sø datalager, [Oversigt over sø datalager](data-lake-store-overview.md). På nuværende tidspunkt understøtter SDK

  *  **Node.js version: 0.10.0 eller nyere**
  *  **REST-API version for konto: 2015-10-01-preview**
  *  **REST-API version til filsystemet: 2015-10-01-preview**

## <a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Oprette et Azure Active Directory-program**. Du kan bruge Azure AD-programmet til at godkende programmet sø datalager med Azure AD. Der er forskellige måder til at godkende med Azure AD, som er **Slutbrugerlicensaftale godkendelse** eller **tjeneste til godkendelse**. Instruktioner og flere oplysninger om, hvordan du godkender, under [Godkend med sø datalager brug af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Sådan installeres

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Godkende ved hjælp af Azure Active Directory

De nedenstående kodestykker viser to separate måder for at godkende med sø datalager ved hjælp af Azure AD. En detaljeret beskrivelse på forskellige metoder til at bruge til godkendelse med sø datalager, se [Godkend med sø datalager ved hjælp af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

Den nedenstående kodestykke kræver også input som Azure AD-domænenavn, klient-ID for en Azure AD app osv. Alle disse oplysninger kan hentes fra et Azure AD-program, skal du oprettede, detaljer der også er inkluderet i ovenstående link.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Oprette sø datalager-klienter

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Oprette en sø datalager-konto

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>Oprette en fil med indhold
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Få en liste over filer og mapper

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Se også

- [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK for Node.js - sø Analytics datastyring](https://www.npmjs.com/package/azure-arm-datalake-analytics)
