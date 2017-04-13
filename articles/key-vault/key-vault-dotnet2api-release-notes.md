<properties
   pageTitle="Primære samling .NET 2.x API produktbemærkninger | Microsoft Azure"
   description=".NET udviklere skal bruge denne API til kode til Azure-tasten samling"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure vigtige samling .NET 2.0 - produktbemærkninger og Overførselsvejledningen

Følgende noter og vejledning, der er for udviklere, som arbejder med Azure-tasten samling .NET / C#-bibliotek. I ændringen fra den 1,0 version 2.0-versionen, er et antal opdateringer blevet foretaget, der kræver overførsel arbejde i din kode i rækkefølge for dig at drage fordel af de funktionelle forbedringer og tilføjelser som nøgle samling certifikater understøttelse af funktioner.

Tasten samling certifikater support indeholder til administration af din x509 certifikater og følgende funktionsmåder:  

-   Gør det muligt for ejer af et certifikat til at oprette et certifikat via en nøgle samling oprettelsesprocessen eller import af et eksisterende certifikat. Dette omfatter både selvsigneret og nøglecenter oprettet certifikater.

- Gør det muligt for en nøgle samling certifikat ejer at implementere sikkert sted og administration af X509 certifikater uden interaktion med privat nøgle materiale.  

-   Gør det muligt for ejer af et certifikat til at oprette en politik, der omdirigerer nøgle samling af legitimationsoplysninger til at administrere livscyklussen for et certifikat.  

-   Gør det muligt for ejere af certifikat til at levere kontaktoplysninger for meddelelse om livscyklus begivenheder af udløb og fornyelse af certifikat.  

-   Understøtter automatisk fornyelse med valgte udstedere - nøgle samling partner X509 udbydere af certifikat / myndigheder af certifikat.
    - Bemærk - ikke gået sammen udbydere/myndigheder, også er tilladt, men ikke understøtter funktionen automatisk fornyelse.


## <a name="net-support"></a>Understøttelse af .NET
- **.NET 4.0** understøttes ikke af 2.0-versionen af Azure-tasten samling .NET / C#-bibliotek
- **.NET Core** understøttes af 2.0-versionen af Azure-tasten samling .NET / C#-bibliotek

## <a name="namespaces"></a>Navneområder
- Navneområdet for **modeller** er ændret fra **Microsoft.Azure.KeyVault** til **Microsoft.Azure.KeyVault.Models**.
- Navneområdet **Microsoft.Azure.KeyVault.Internal** afbrydes.
- Navneområdet Azure SDK afhængigheder, ændres fra **Hyak.Common** og **Hyak.Common.Internals** til **Microsoft.Rest** og **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Type ændringer
- *Hemmeligt* ændres til *SecretBundle*
- *Ordbog* ændres til *IDictionary*
- *Listen<T>, en streng, []* ændres til *IList<T> *
- *NextList* ændres til *NextPageLink*


## <a name="return-types"></a>Returnere typer
- **KeyList** og **SecretList** returnerer *IPage<T> * i stedet for *ListKeysResponseMessage*
- Den oprettede **BackupKeyAsync** returnerer *BackupKeyResult* , som indeholder *værdien* (sikkerhedskopiering blob). Før metoden blev ombrudt og returnerer kun værdien.

## <a name="exceptions"></a>Undtagelser
- *KeyVaultClientException* ændres til *KeyVaultErrorException*
- Fejl i tjenesten er ændret fra *undtagelse. Fejl* til *undtagelse. Body.Error.Message*.
- Fjernet yderligere oplysninger fra en fejlmeddelelse om for **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktører
- I stedet for at acceptere en *HttpClient* som et konstruktørargument, accepterer konstruktøren kun *HttpClientHandler* eller *DelegatingHandler []*.



## <a name="downloaded-packages"></a>Hentede pakker  
Når en klient, der behandler en afhængighed på tasten samling er blevet følgende hentet
#### <a name="previous-package-list"></a>Forrige pakke liste
- pakke id="Hyak.Common" version = "1.0.2" targetFramework = "net45"
- pakke id="Microsoft.Azure.Common" version = "2.0.4" targetFramework = "net45"
- pakke id="Microsoft.Azure.Common.Dependencies" version = "1.0.0" targetFramework = "net45"
- pakke id="Microsoft.Azure.KeyVault" version = "1.0.0" targetFramework = "net45"
- pakke id="Microsoft.Bcl" version = "1.1.9" targetFramework = "net45"
- pakke id="Microsoft.Bcl.Async" version = "1.0.168" targetFramework = "net45"
- pakke id="Microsoft.Bcl.Build" version = "1.0.14" targetFramework = "net45"
- pakke id="Microsoft.Net.Http" version = "2.2.22" targetFramework = "net45"

#### <a name="current-package-list"></a>Aktuelle pakke liste
- pakke id="Microsoft.Azure.KeyVault" version = "2.0.0-preview" targetFramework = "net45"
- pakke id="Microsoft.Rest.ClientRuntime" version = "2.2.0" targetFramework = "net45"
- pakke id="Microsoft.Rest.ClientRuntime.Azure" version = "3.2.0" targetFramework = "net45"


## <a name="class-changes"></a>Klasse ændringer

- **UnixEpoch** klasse er blevet fjernet
- **Base64UrlConverter** klasse er omdøbt til **Base64UrlJsonConverter**

## <a name="other-changes"></a>Andre ændringer

- Understøttelse af konfigurationen af KV handlingen forsøg politik på midlertidige mislykkede forsøg er blevet føjet til denne version af API.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- For de handlinger, der returneres en *samling af legitimationsoplysninger*, blev returtypen en klasse, der indeholdt en samling af legitimationsoplysninger egenskab. Returtypen er nu *samling af legitimationsoplysninger*.
- *PermissionsToKeys* og *PermissionsToSecrets* er nu *Permissions.Keys* og *Permissions.Secrets*
- Nogle af de returnerede typer ændringer gælder for kontrolelement-planet samt.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- Pakken deles op til **Microsoft.Azure.KeyVault.Extensions** og **Microsoft.Azure.KeyVault.Cryptography** for krypteringshandlinger.
