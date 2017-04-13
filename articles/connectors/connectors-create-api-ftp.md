<properties
pageTitle="Lær at bruge FTP-forbindelsen i logik apps | Microsoft Azure"
description="Opret logik apps med Azure App service. Oprette forbindelse til FTP-server til at administrere dine filer. Du kan udføre forskellige handlinger som upload, opdatere, få og slette filer i FTP-serveren."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-ftp-connector"></a>Komme i gang med FTP-forbindelse

Bruge FTP-forbindelsen til at overvåge, administrere og oprette filer på et FTP-server. 

Hvis du vil bruge [en forbindelse](./apis-list.md), skal du først oprette en logik app. Du kan komme i gang ved at [oprette en logik app nu](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Oprette forbindelse til FTP

Før din logik app kan få adgang til alle tjenester, skal du først oprette en *forbindelse* til tjenesten. En [forbindelse](./connectors-overview.md) indeholder forbindelsen mellem en logik app og en anden tjeneste.  

### <a name="create-a-connection-to-ftp"></a>Oprette en forbindelse til FTP

>[AZURE.INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]

## <a name="use-a-ftp-trigger"></a>Bruge en FTP-udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen defineret i en logik app. [Få mere at vide om udløsere](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.IMPORTANT]FTP-forbindelsen kræver en FTP-server, der er tilgængelige fra internettet og er konfigureret til at fungere med passiv tilstand. Desuden er FTP-forbindelsen **ikke kompatibel med implicit FTPS (FTP over SSL)**. FTP-forbindelsen understøtter kun eksplicitte FTPS (FTP over SSL).  

I dette eksempel jeg viser dig, hvordan du bruger **FTP - når en fil er tilføjet eller ændret** udløser til at starte en arbejdsproces for logik app, når en fil er blevet tilføjet til, eller ændret på et FTP-server. Du kan bruge denne udløser til at overvåge en FTP-mappe for nye filer, der repræsenterer ordrer fra kunder i en virksomhed eksempel.  Du kan derefter bruge en FTP-forbindelse handling som **få filindhold** til at vise indholdet af rækkefølgen, til videre behandling og lager i databasen ordrer.

1. Angive *ftp* i søgefeltet i logik apps designer, og vælg **FTP - når en fil er tilføjet eller ændret** udløseren derefter   
![Billede af udløser FTP-1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
**Når en fil er tilføjet eller ændret** kontrolelementet åbnes  
![Billede af FTP-udløser 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
- Vælg **...** placeret på højre side af kontrolelementet. Dette åbner kontrolelementet mappe Vælg  
![Billede af udløser FTP-3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
- Vælg den **>** (højre pil) og gennemse for at finde den mappe, du vil overvåge for nye eller ændrede filer. Vælg mappen, og Bemærk mappen vises nu i kontrolelementet **mappe** .  
![Billede af udløser FTP-4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   


På dette tidspunkt er din logik app konfigureret med en udløser, der starter en kørsel af de andre udløsere og handlinger i arbejdsprocessen, når en fil er enten ændret eller oprettet i den ønskede FTP-mappe. 

>[AZURE.NOTE]Til en logik app fungerer, skal den indeholde mindst én udløser og en handling. Følg trinnene i næste afsnit for at føje en handling.  



## <a name="use-a-ftp-action"></a>Brug et FTP-handling

En handling er en handling, der er foretaget af den arbejdsproces, der er defineret i en logik app. [Lær mere om handlinger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

Nu hvor du har tilføjet en udløser, skal du følge disse trin for at føje en handling, der får indholdet af den nye eller ændrede fil, der er fundet af udløseren.    

1. Vælg **+ nyt trin** at føje den handlingen for at få indholdet af filen på FTP-serveren  
- Vælg linket **Tilføj en handling** .  
![Billede af FTP-handling 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
- Angiv *FTP* til at søge efter alle handlinger, der er relateret til FTP.
- Vælg **FTP - indhold i filen** som handlingen skal udføre, når en ny eller ændret fil findes i mappen FTP.      
![FTP-handling billede 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
Kontrolelementet **indhold i filen** åbnes. **Bemærk**: bliver du bedt om at godkende din logik app til at få adgang til din FTP-server-konto, hvis du ikke har gjort det tidligere.  
![Billede af FTP-handling 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
- Vælg kontrolelementet **fil** (det tomme område, der er placeret under **fil***). Her kan du bruge en af de forskellige egenskaber fra den nye eller ændrede fil, der er fundet på FTP-serveren.  
- Vælg indstillingen **filens indhold** .  
![Billede af FTP-handling 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
-  Kontrolelementet opdateres, der angiver, at handlingen **FTP - indhold i filen** får den *arkivere indhold* af filen nye eller ændrede på FTP-serveren.      
![Billede af FTP-handling 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
- Gemme dit arbejde og derefter føje en fil til FTP-mappe til at teste din arbejdsproces.    

På dette tidspunkt er logik app konfigureret med en udløser til at overvåge en mappe på et FTP-serveren og starte arbejdsprocessen, når der er angivet en ny fil eller en fil på FTP-serveren. 

Appen logik er også blevet konfigureret med en handling til at få indholdet af den nye eller ændrede fil.

Du kan nu føje endnu en handling som handlingen [SQL Server - Indsæt række](./connectors-create-api-sqlazure.md#insert-row) til at indsætte indholdet af den nye eller ændrede fil i en SQL-databasetabeller.  

## <a name="technical-details"></a>Tekniske detaljer

Her er nogle oplysninger om udløsere, handlinger og svar, der understøtter denne forbindelse:

## <a name="ftp-triggers"></a>FTP-udløsere

FTP har følgende trigger(s):  

|Udløser | Beskrivelse|
|--- | ---|
|[Når en fil er tilføjet eller ændret](connectors-create-api-ftp.md#when-a-file-is-added-or-modified)|Denne handling udløser et flow, når en fil er tilføjet eller ændret i en mappe.|


## <a name="ftp-actions"></a>FTP-handlinger

FTP har følgende handlinger:


|Handling|Beskrivelse|
|--- | ---|
|[Få filens metadata](connectors-create-api-ftp.md#get-file-metadata)|Denne handling får metadataene for en fil.|
|[Opdatere fil](connectors-create-api-ftp.md#update-file)|Denne handling opdaterer en fil.|
|[Slette filer](connectors-create-api-ftp.md#delete-file)|Denne handling sletter en fil.|
|[Få filmetadata ved hjælp af sti](connectors-create-api-ftp.md#get-file-metadata-using-path)|Denne handling får metadataene i en fil med sti.|
|[Få filindhold ved hjælp af sti](connectors-create-api-ftp.md#get-file-content-using-path)|Denne handling bliver indholdet af en fil med sti.|
|[Få filindhold](connectors-create-api-ftp.md#get-file-content)|Denne handling bliver indholdet af en fil.|
|[Opret fil](connectors-create-api-ftp.md#create-file)|Denne handling opretter en fil.|
|[Kopiere fil](connectors-create-api-ftp.md#copy-file)|Denne handling kopierer en fil til en FTP-server.|
|[Liste over filer i mappe](connectors-create-api-ftp.md#list-files-in-folder)|Denne handling får liste over filer og undermapper i en mappe.|
|[Liste over filer i rodmappen](connectors-create-api-ftp.md#list-files-in-root-folder)|Denne handling får liste over filer og undermapper i rodmappen.|
|[Udtrække mappe](connectors-create-api-ftp.md#extract-folder)|Denne handling henter en arkivfilen til en anden mappe (eksempel: .zip).|
### <a name="action-details"></a>Handlingsdetaljer

Her er detaljerne for handlingerne og udløsere for denne forbindelse, sammen med deres svar:



### <a name="get-file-metadata"></a>Få filens metadata
Denne handling får metadataene for en fil. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|id *|Fil|Vælg en fil|

En * angiver, at en egenskab er påkrævet

#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="update-file"></a>Opdatere fil
Denne handling opdaterer en fil. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|id *|Fil|Vælg en fil|
|brødteksten *|Filens indhold|Indholdet af filen|

En * angiver, at en egenskab er påkrævet

#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="delete-file"></a>Slette filer
Denne handling sletter en fil. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|id *|Fil|Vælg en fil|

En * angiver, at en egenskab er påkrævet




### <a name="get-file-metadata-using-path"></a>Få filmetadata ved hjælp af sti
Denne handling får metadataene i en fil med sti. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|stien *|Filstien|Vælg en fil|

En * angiver, at en egenskab er påkrævet

#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="get-file-content-using-path"></a>Få filindhold ved hjælp af sti
Denne handling bliver indholdet af en fil med sti. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|stien *|Filstien|Vælg en fil|

En * angiver, at en egenskab er påkrævet




### <a name="get-file-content"></a>Få filindhold
Denne handling bliver indholdet af en fil. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|id *|Fil|Vælg en fil|

En * angiver, at en egenskab er påkrævet




### <a name="create-file"></a>Opret fil
Denne handling opretter en fil. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|Mappesti *|Mappesti|Vælg en mappe|
|navn *|Filnavn|Navnet på fil|
|brødteksten *|Filens indhold|Indholdet af filen|

En * angiver, at en egenskab er påkrævet

#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="copy-file"></a>Kopiere fil
Denne handling kopierer en fil til en FTP-server. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|kilde *|Kildens URL-adresse|URL-adressen til kildefilen|
|destination *|Destination filsti|Destination filsti, herunder target filnavn|
|overskrive|Vil du overskrive?|Overskriver destinationsfilen, hvis er angivet til "true"|

En * angiver, at en egenskab er påkrævet

#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="when-a-file-is-added-or-modified"></a>Når en fil er tilføjet eller ændret
Denne handling udløser et flow, når en fil er tilføjet eller ændret i en mappe. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|folderId *|Mappe|Vælg en mappe|

En * angiver, at en egenskab er påkrævet




### <a name="list-files-in-folder"></a>Liste over filer i mappe
Denne handling får liste over filer og undermapper i en mappe. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|id *|Mappe|Vælg en mappe|

En * angiver, at en egenskab er påkrævet



#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="list-files-in-root-folder"></a>Liste over filer i rodmappen
Denne handling får liste over filer og undermapper i rodmappen. 


Der er ingen parametre til dette opkald

#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|




### <a name="extract-folder"></a>Udtrække mappe
Denne handling henter en arkivfilen til en anden mappe (eksempel: .zip). 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|kilde *|Kilde arkiv filsti|Stien til arkivfilen|
|destination *|Stien destinationsmappen|Stien til destinationsmappen|
|overskrive|Vil du overskrive?|Overskriver at destinationsfilen, hvis er angivet til "true"|

En * angiver, at en egenskab er påkrævet



#### <a name="output-details"></a>Output detaljer

BlobMetadata


| Egenskabsnavn | Datatype |
|---|---|---|
|Id|streng|
|Navn|streng|
|Vist navn|streng|
|Sti|streng|
|LastModified|streng|
|Størrelse|heltal|
|Medietype|streng|
|IsFolder|Boolesk værdi|
|ETag|streng|
|FileLocator|streng|



## <a name="http-responses"></a>HTTP-svar

De handlinger og udløsere ovenfor kan returnere en eller flere af følgende HTTP statuskoder: 

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod ukendt fejl.|
|standard|Mislykkedes.|







## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)