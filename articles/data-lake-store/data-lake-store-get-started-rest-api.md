<properties 
   pageTitle="Komme i gang med datalager sø ved hjælp af REST-API | Microsoft Azure" 
   description="Bruge WebHDFS REST API'er til at udføre handlinger på sø datalager" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Introduktion til Azure Data sø Store ved hjælp af REST API'er

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

I denne artikel kan lære du, hvordan du bruger WebHDFS REST API'er og Data sø Store REST API'er til at udføre kontoadministration samt filsystemet handlinger på Azure sø datalager. Azure sø datalager Fremviser egne REST API'er for konto management handlinger. Da sø datalager er kompatible med HDFS og Hadoop økosystem, understøtter det, ved hjælp af WebHDFS REST API'er til filsystemet handlinger.

>[AZURE.NOTE] Finde detaljerede oplysninger om REST-API support til sø datalager, skal du se [Azure Data sø Store REST-API Reference](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Forudsætninger

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Oprette et Azure Active Directory-program**. Du kan bruge Azure AD-programmet til at godkende programmet sø datalager med Azure AD. Der er forskellige måder til at godkende med Azure AD, som er **Slutbrugerlicensaftale godkendelse** eller **tjeneste til godkendelse**. Instruktioner og flere oplysninger om, hvordan du godkender, under [Godkend med sø datalager brug af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [cURL](http://curl.haxx.se/). I denne artikel bruges krøllet til at vise, hvordan du kan foretage REST-API-opkald på en sø datalager-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hvordan kan jeg godkende ved hjælp af Azure Active Directory?

Du kan bruge to metoder til at godkende ved hjælp af Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Slutbrugere godkendelse (interaktive)

I dette scenarie skal programmet beder brugeren om at logge på og alle handlinger udføres i konteksten for brugeren. Udfør følgende trin til interaktive godkendelse.

1. Omdirigere brugeren til følgende URL-adressen til dit program:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<OMDIRIGER URI > skal kodes til brug i en URL-adresse. Brug så https://localhost, `https%3A%2F%2Flocalhost`)

    Du kan erstatte pladsholder værdierne i URL-adressen ovenfor og sæt den ind i en webbrowser adresselinjen til dette selvstudium. Du bliver brugeren omdirigeret for at godkende ved hjælp af dit Azure logon. Når du er logget korrekt, vises svaret i browserens adresselinje. Svaret bliver i følgende format:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Registrere godkendelseskoden fra svaret. Dette selvstudium skal du kopiere godkendelseskoden til fra adresselinjen i webbrowseren og sende dem i POST-anmodningen til det token slutpunkt, som vist nedenfor:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] I dette tilfælde den \<OMDIRIGER URI > skal ikke kodes.

3. Svaret er et JSON-objekt, der indeholder et adgangstoken (f.eks., `"access_token": "<ACCESS_TOKEN>"`) og et Opdater token (f.eks., `"refresh_token": "<REFRESH_TOKEN>"`). Dit program bruger adgangstoken ved adgang til Azure sø datalager og Opdater tokenet til at få en anden adgangstoken, når et adgangstoken udløber.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Når adgangstoken udløber, kan du anmode om en ny adgangstoken ved hjælp af token opdatering, som vist nedenfor:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Du kan finde flere oplysninger om interaktive brugergodkendelse, [godkendelseskode give flow](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Tjenesten service-godkendelse (ikke-interaktiv)

I dette scenarie skal den programmet indeholder sin egen legitimationsoplysninger for at udføre handlinger. I dette, skal du udstede en POST-anmodning som vist nedenfor. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Output fra mødeindkaldelsen indeholder et godkendelse token (markeret med `access-token` i outputtet nedenfor), du efterfølgende der går med dine REST-API-opkald. Gemme dette godkendelse symbol i en tekstfil Du skal bruge dette senere i denne artikel.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I denne artikel bruges på **ikke-interaktiv** metode. Du kan finde flere oplysninger om ikke-interaktiv (service-tjenesten opkald), [tjenesten til tjenesten opkald ved hjælp af legitimationsoplysninger](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Oprette en sø datalager-konto

Denne handling er baseret på REST-API opkald, der er defineret [her](https://msdn.microsoft.com/library/mt694078.aspx).

Brug følgende kommando for krøllet. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Erstat teksten i ovenstående kommando \< `REDACTED` \> med godkendelse tokenet du har hentet tidligere. Anmodning om data for denne kommando findes i filen **input.json** , som er knyttet til den `-d` parameter ovenfor. Indholdet af filen input.json minde om følgende:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Oprette mapper i en sø datalager-konto

Denne handling er baseret på WebHDFS REST-API opkald, der er defineret [her](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Brug følgende kommando for krøllet. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Erstat teksten i ovenstående kommando \< `REDACTED` \> med godkendelse tokenet du har hentet tidligere. Denne kommando opretter en mappe med navnet **mytempdir** under rodmappen på kontoen sø datalager.

Du burde se et svar således, hvis handlingen er fuldført:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Listen mapper i en sø datalager-konto

Denne handling er baseret på WebHDFS REST-API opkald, der er defineret [her](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Brug følgende kommando for krøllet. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Erstat teksten i ovenstående kommando \< `REDACTED` \> med godkendelse tokenet du har hentet tidligere.

Du burde se et svar således, hvis handlingen er fuldført:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Overføre data til en sø datalager-konto

Denne handling er baseret på WebHDFS REST-API opkald, der er defineret [her](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Overførsel af data ved hjælp af WebHDFS REST-API er en proces, som beskrevet nedenfor.

1. Sende en HTTP PLACERE anmodning uden at sende filer dataene, der skal overføres. Erstat teksten i den følgende kommando ** \<yourstorename >** med navnet på din sø datalager.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    Output til denne kommando skal være indeholde en midlertidig Omdiriger URL-adresse, vil den, der er vist nedenfor.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Nu skal du sende en anden HTTP PLACERE anmodning mod URL-adressen er angivet for egenskaben **placering** i svaret. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    Output bliver ligner følgende:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Læse data fra en sø datalager-konto

Denne handling er baseret på WebHDFS REST-API opkald, der er defineret [her](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Læse data fra et datalager sø er konto en proces.

* Du først sende en GET-anmodning mod slutpunktet `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Dette vil returnere en placering for at sende den næste GET-anmodning til.
* Du kan derefter sende GET-anmodning mod slutpunktet `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Derved vises indholdet af filerne.

Men fordi der er ingen forskel i de inputparametre mellem først og det andet trin, du kan bruge den `-L` parameter til at sende den første anmodning. `-L`indstillingen kombinerer to anmodninger på en grundlæggende og får krøllet annullere Fortryd anmodningen på den nye placering. Til sidst skal output fra alle anmodning opkald vises, ønsker vist nedenfor. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Du burde se output ligner følgende:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Omdøbe en fil i en sø datalager-konto

Denne handling er baseret på WebHDFS REST-API opkald, der er defineret [her](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Brug følgende krøllet kommando til at omdøbe en fil. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Du burde se output ligner følgende:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Slette en fil fra en sø datalager-konto

Denne handling er baseret på WebHDFS REST-API opkald, der er defineret [her](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Brug følgende krøllet kommando til at slette en fil. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Du burde se output ud som følger:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Slette en sø datalager-konto

Denne handling er baseret på REST-API opkald, der er defineret [her](https://msdn.microsoft.com/library/mt694075.aspx).

Brug følgende krøllet kommando til at slette en sø datalager-konto. Erstatte ** \<yourstorename >** med navnet på din sø datalager.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Du burde se output ud som følger:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Se også

- [Åbne stor kildedata-programmer, der er kompatible med Azure sø datalager](data-lake-store-compatible-oss-other-applications.md)
 
