<properties 
    pageTitle="At implementere nedbrud ved hjælp af tjenesten sikkerhedskopiering og gendannelse i Azure API Management | Microsoft Azure" 
    description="Lær at bruge Sikkerhedskopiering og gendannelse for at udføre nedbrud i Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>At implementere nedbrud ved hjælp af tjenesten sikkerhedskopiering og gendannelse i Azure API Management

Hvis du vælger at udgive og administrere din API'er via Azure API Management du at drage fordel af mange fejltolerance og infrastruktur egenskaber, som du ellers ville have til at designe, implementere og administrere. Azure-platformen afhjælper brøk stort antal potentielle mislykkede forsøg på en brøkdel.

Hvis du vil gendanne fra tilgængelighed skal, der påvirker det område, hvor din API administration af tjenesten er hostet du være klar til at således rekonstruere din tjeneste i et andet område til enhver tid. Afhængigt af din tilgængelighed mål og gendannelse tid mål du måske og reserver en ekstra tjeneste i en eller flere områder og prøve at vedligeholde deres konfiguration og indhold synkroniseret med den aktive tjeneste. Tjenesten Sikkerhedskopiér og Gendan leverer den nødvendige dokumentkomponent til implementering af din strategi for genoprettelse efter genoprettelse.

Denne vejledning viser, hvordan du kan godkende Azure ressourcestyring anmodninger, og hvordan du sikkerhedskopiere og gendanne dine API Management service forekomster.

>[AZURE.NOTE] Fremgangsmåde til at sikkerhedskopiere og gendanne en API Management service forekomst for nedbrud kan også bruges til replikering forekomster af API Management-tjenesten for scenarier som midlertidige.
>
>Bemærk, at hver enkelt sikkerhedskopi udløber efter 7 dage. Hvis du forsøger at gendanne en sikkerhedskopi, når udløbsperiode 7 dage er udløbet, gendannelsen mislykkes med en `Cannot restore: backup expired` meddelelse.

## <a name="authenticating-azure-resource-manager-requests"></a>Godkende Azure ressourcestyring anmoder om

>[AZURE.IMPORTANT] REST-API til sikkerhedskopiering og gendannelse bruger Azure ressourcestyring og har en anden godkendelsesmetode end RESTEN API'er for at administrere dine API Management-enheder. Trinnene i dette afsnit beskriver, hvordan til at godkende Azure ressourcestyring anmodninger. Få vist [anmodninger om godkendelse af Azure ressourcestyring](http://msdn.microsoft.com/library/azure/dn790557.aspx)kan finde flere oplysninger.

Alle de opgaver, som du gør på ressourcer ved hjælp af Ressourcestyring Azure skal godkendes med Azure Active Directory at benytte følgende fremgangsmåde.

-   Tilføje et program til Azure Active Directory-lejer.
-   Angive tilladelser for det program, du har tilføjet.
-   Få tokenet til godkendelse af anmodninger om til Azure ressourcestyring.

Det første trin er at oprette et Azure Active Directory-program. Log på [Azure klassisk Portal](http://manage.windowsazure.com/) ved hjælp af det abonnement, der indeholder din API Management service-forekomst, og gå til fanen **programmer** til din standard Azure Active Directory.

>[AZURE.NOTE] Hvis standardmappen Azure Active Directory ikke er synligt for din konto, kan du kontakte administratoren af Azure-abonnement for at give de nødvendige tilladelser til din konto. Finde oplysninger om at finde din standardmappen, under [finde dine standardmappen](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Oprette Azure Active Directory-program][api-management-add-aad-application]

Klik på **Tilføj**, **Tilføj et program, udvikling af min organisation**, og vælg **oprindelige-klientprogrammet**. Angiv et beskrivende navn, og klik på pilen Næste. Angiv en pladsholder URL-adresse f.eks `http://resources` for den **Omdirigere URI**, som det er et obligatorisk felt, men værdien, der bruges ikke senere. Klik på afkrydsningsfeltet for at gemme programmet.

Når programmet er gemt, klik på **Konfigurer**, Rul ned til sektionen **tilladelser til andre programmer** og klik på **Tilføj programmet på computeren**.

![Føj tilladelser][api-management-aad-permissions-add]

Vælg **Windows** **Azure Service Management API** , og klik på afkrydsningsfeltet for at tilføje programmet.

![Føj tilladelser][api-management-aad-permissions]

Klik på **Delegerede tilladelser** ud for det **Windows** **Azure Service Management API** -program, der er tilføjet for nylig, Markér afkrydsningsfeltet for **Access Azure Service Management (preview)**og klik på **Gem**.

![Føj tilladelser][api-management-aad-delegated-permissions]

Det er nødvendigt at få et token før aktivering de API'er, oprette sikkerhedskopien og gendanne den. I følgende eksempel bruges funktionen [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) nuget pakke til at hente tokenet.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Erstatte `{tentand id}`, `{application id}`, og `{redirect uri}` ved hjælp af de følgende instruktioner.

Erstatte `{tenant id}` med lejer-id'et af Azure Active Directory-programmet, du lige har oprettet. Du kan få adgang til id ved at klikke på **Vis slutpunkter**.

![Slutpunkter][api-management-aad-default-directory]

![Slutpunkter][api-management-endpoint]

Erstatte `{application id}` og `{redirect uri}` ved hjælp af **Klient-Id** og URL-adressen i afsnittet **Omdirigere URI'er** fra Azure Active Directory-programmets **Konfigurer** fane. 

![Ressourcer][api-management-aad-resources]

Når værdierne, der er angivet, skal kodeeksemplet returnere et token svarende til følgende eksempel.

![Token][api-management-arm-token]

Før du kalder handlingerne sikkerhedskopiering og gendannelse er beskrevet i følgende afsnit, ved at angive godkendelse anmodning om sidehovedet for opkaldet RESTEN.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Sikkerhedskopiér en API Management-tjenesten
Til at sikkerhedskopiere en API Management serviceanmodning problem følgende HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

hvor:

* `subscriptionId`-id'et for det abonnement, der indeholder den API Management-tjenesten, du forsøger at sikkerhedskopiere
* `resourceGroupName`-en streng i form af 'Api - standard-{service-området}' hvor `service-region` identificerer det Azure område, hvor API Management tjeneste du prøver at sikkerhedskopieringen er hostet, f.eks.`North-Central-US`
* `serviceName`-navnet på tjenesten API Management du laver en sikkerhedskopi af angivet på tidspunktet for oprettelsen
* `api-version`-Erstat med`2014-02-14`

Angiv mål kontonavn til Azure lager, hurtigtast, blob objektbeholder navn og navn på sikkerhedskopi i brødteksten i anmodningen:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Angive værdien af den `Content-Type` sidehoved for anmodning om at `application/json`.

Sikkerhedskopi er en længerevarende handling, der kan tage flere minutter at gennemføre.  Hvis anmodningen lykkedes og sikkerhedskopieringen er startet du får vist en `202 Accepted` svar statuskode med en `Location` sidehoved.  Gøre 'GET' anmodninger om til URL-adresse i den `Location` sidehoved for at finde ud af status for handlingen. Mens sikkerhedskopien er i gang skal du fortsat modtage en '202 accepterede' statuskode. Et svarkoden for `200 OK` indikerer sikkerhedskopieringen er fuldført.

**Bemærk**!

- **Objektbeholder** angivet i den anmodning om brødteksten **skal findes**.
* Mens sikkerhedskopi er i gang, du **bør ikke forsøge service management handlinger** som SKU opgradere eller nedgradere domain name skifter osv. 
* Gendannelse af en **sikkerhedskopi sikres kun for 7 dage** siden tidspunktet for oprettelsen. 
* **Anvendelsesdata** , der bruges til at oprette analytics-rapporter **er ikke medtaget** i sikkerhedskopien. Bruge [Azure API Management REST-API][] til at hente analytics-rapporter for opbevaring med jævne mellemrum.
* Den hyppighed, som du udfører service sikkerhedskopier påvirker din gendannelse punkt målsætning. For at minimere det anbefales det, at implementere regelmæssige sikkerhedskopier samt udføre efter behov sikkerhedskopier efter vigtige ændringer til din API Management-tjenesten.
* **Ændringer** foretaget tjenestekonfiguration (fx API'er, politikker, udvikler portalen udseende) under sikkerhedskopieringen er i processen **muligvis ikke medtages i sikkerhedskopien, og derfor går tabt**.

## <a name="step2"> </a>Gendanne en API Management-tjenesten
Hvis du vil gendanne en API Management service fra en tidligere oprettet sikkerhedskopi, skal du sørge følgende HTTP-anmodningen:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

hvor:

* `subscriptionId`-id'et for det abonnement, der indeholder den API Management-tjenesten, du gendanner en sikkerhedskopi i
* `resourceGroupName`-en streng i form af 'Api - standard-{service-området}' hvor `service-region` identificerer det Azure område, hvor tjenesten API Management du gendanner en sikkerhedskopi i er hostet, f.eks.`North-Central-US`
* `serviceName`-navnet på den API Management service til at blive gendannet, der er angivet på tidspunktet for oprettelsen
* `api-version`-Erstat med`2014-02-14`

I brødteksten i anmodningen, angive sikkerhedskopifil placeringen, det vil sige kontonavn Azure lager, hurtigtast, blob objektbeholder navn og navn på sikkerhedskopi:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Angive værdien af den `Content-Type` sidehoved for anmodning om at `application/json`.

Gendan er en længerevarende handling, der kan gå op til 30 eller flere minutter at gennemføre.  Hvis anmodningen lykkedes og gendannelsen er startet du får vist en `202 Accepted` svar statuskode med en `Location` sidehoved.  Gøre 'GET' anmodninger om til URL-adresse i den `Location` sidehoved for at finde ud af status for handlingen. Mens gendannelsen er i gang du vil fortsat modtage '202 accepterede' statuskode. Et svarkoden for `200 OK` indikerer vellykket gennemførelse af gendannelsen.

>[AZURE.IMPORTANT] **Feltet SKU** tjenesten er gendannet i **skal stemme overens med** SKU til sikkerhedskopierede tjenesten gendannes.
>
>**Ændringer** foretaget tjenestekonfiguration (fx API'er, politikker, udvikler portalen udseende) under gendannelsen er i gang **kan blive overskrevet**.

## <a name="next-steps"></a>Næste trin
Se på følgende Microsoft-blogge for to forskellige gennemgange af processen sikkerhedskopiering/gendannelse.

-   [Gentage Azure API Management konti](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Tak til Gisela for hendes bidrag til denne artikel.
-   [Azure API administration: Sikkerhedskopiere og gendanne konfiguration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   Den detaljerede ved Stuart tilgang svarer ikke til de officielle retningslinjer, men det er meget interessant.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST-API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
