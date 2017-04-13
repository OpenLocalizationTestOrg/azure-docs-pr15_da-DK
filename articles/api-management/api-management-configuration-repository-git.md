<properties 
    pageTitle="Sådan gemmes og konfigurere konfigurationen API Management-tjenesten ved hjælp af ciffer" 
    description="Lær, hvordan du gemmer og konfigurere konfigurationen API Management-tjenesten ved hjælp af ciffer." 
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


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Sådan gemmes og konfigurere konfigurationen API Management-tjenesten ved hjælp af ciffer

>[AZURE.IMPORTANT] Ciffer konfiguration for administration af API er i øjeblikket i Vis udskrift. Det er funktionelt fuldført, men er i Vis udskrift, fordi vi søger aktivt feedback på denne funktion. Det er muligt, kan vi foretager en ændring i svar på kundefeedback, så det anbefales ikke afhængigt af funktionen til brug i fremstilling miljøer seneste. Hvis du har en hvilken som helst feedback eller spørgsmål, skal du fortælle os, på `apimgmt@microsoft.com`.

Hver forekomst af API Management service fører en konfigurationsdatabase, der indeholder oplysninger om konfiguration og metadataene for forekomsten af. Kan ændres til forekomsten af ved at ændre en indstilling i publisher-portalen, ved hjælp af en PowerShell-cmdlet eller foretager et REST-API-opkald. Ud over disse metoder, kan du også administrere forekomst konfigurationen af tjenesten ved hjælp af ciffer, aktivere service management scenarier f.eks.:

-   Konfiguration versionsstyring - hente og gemme forskellige versioner af konfigurationen af tjenesten
-   Flere ændringer i konfigurationen - foretage ændringer i flere dele af konfigurationen af tjenesten i din lokale lager og integrere ændres tilbage til serveren med en enkelt handling
-   Velkendte ciffer toolchain og arbejdsproces - bruge ciffer værktøj og arbejdsprocesser, du allerede kender

I det følgende diagram viser en oversigt over de forskellige måder at konfigurere din API Management service forekomst.

![Konfigurere ciffer][api-management-git-configure]

Når du foretager ændringer til tjenesten ved hjælp af portalen publisher, PowerShell-cmdlet'er eller REST-API, du administrerer din tjeneste konfiguration database ved hjælp af den `https://{name}.management.azure-api.net` slutpunkt, som vist i højre side af diagrammet. I venstre side af diagrammet illustrerer, hvordan du kan administrere konfigurationen af tjenesten ved hjælp af ciffer og ciffer lager til din tjeneste, der er placeret i `https://{name}.scm.azure-api.net`.

Følgende trin indeholder en oversigt over administration af din API Management service-forekomst, der bruger ciffer.

1.  Aktivere ciffer adgang i din tjeneste
2.  Gemme din konfigurationsdatabase til din ciffer lager
3.  Klone ciffer repo til din lokale computer
4.  Hente den nyeste repo ned til din lokale computer, og Anvend og opslagsnål ændringerne tilbage til din repo
5.  Implementere ændringer fra din repo til databasen service konfiguration

I denne artikel beskrives det, hvordan du aktiverer og bruger ciffer til at administrere konfigurationen af tjenesten og indeholder en reference til de filer og mapper i ciffer lager.

## <a name="to-enable-git-access"></a>Sådan aktiveres ciffer adgang

Du kan hurtigt få vist status for din ciffer konfiguration ved at få vist ikonet ciffer i øverste højre hjørne af publisher-portalen. I dette eksempel er ciffer access endnu ikke blevet aktiveret.

![Ciffer status][api-management-git-icon-enable]

For at få vist og konfigurere dine ciffer konfigurationsindstillinger, kan du enten klikke på ikonet ciffer, eller klik på menuen **sikkerhed** og gå til fanen **konfiguration lager** .

![Aktivere CIFFER][api-management-enable-git]

Markér afkrydsningsfeltet **Aktivér ciffer adgang** for at aktivere ciffer adgang.

Når et øjeblik ændringen er gemt, og der vises en bekræftelsesmeddelelse. Bemærk, at ciffer ikon er blevet ændret til farve, der angiver, at ciffer adgang er aktiveret og Statusmeddelelsen angiver nu, der er ændringer til lageret. Dette skyldes, at API Management service konfigurationsdatabase endnu ikke er gemt til lageret.

![Ciffer aktiveret][api-management-git-enabled]

>[AZURE.IMPORTANT] En hvilken som helst hemmeligheder, som ikke er defineret som egenskaber skal gemmes i lageret og forbliver i dets oversigten, indtil du deaktivere og genaktivere ciffer adgang. Egenskaber giver et sikkert sted for at administrere konstant strengværdier, herunder hemmeligheder, på tværs af alle API konfiguration og politikker, så du ikke behøver at gemme dem direkte i din politik sætninger. Se, [hvordan du bruger egenskaber i politikker for administration af Azure API](api-management-howto-properties.md)kan finde flere oplysninger.

Finde oplysninger om at aktivere eller deaktivere ciffer access ved hjælp af REST-API, under [aktivere eller deaktivere ciffer adgang ved hjælp af REST-API](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Gemme konfigurationen af tjenesten til ciffer lager

Det første trin før klone lageret er at gemme den aktuelle udgave af konfigurationen af tjenesten til lageret. Klik på **Gem konfiguration til lager**.

![Gemme konfiguration][api-management-save-configuration]

Foretag de ønskede ændringer på skærmen for bekræftelse, og klik på **Ok** at gemme.

![Gemme konfiguration][api-management-save-configuration-confirm]

Efter et øjeblik at konfigurationen er gemt, og der vises konfiguration status for lageret, herunder dato og klokkeslæt for den seneste konfigurationsændring af og den seneste synkronisering mellem konfigurationen af tjenesten og lageret.

![Konfiguration af status][api-management-configuration-status]

Når konfigurationen er gemt på lageret, kan den klones.

Oplysninger om at udføre denne handling ved hjælp af REST-API finder du [acceptere konfiguration snapshot ved hjælp af REST-API](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>At klone lager til din lokale computer

Hvis du vil klone et lager, skal du URL-adressen til dit lager, et brugernavn og en adgangskode. Det brugernavn og en URL-adressen vises i øverst del af fanen **konfiguration lager** .

![Ciffer Klon][api-management-configuration-git-clone]

Adgangskoden er oprettet i bunden af fanen **konfiguration lager** .

![Oprette adgangskode][api-management-generate-password]

Oprette en adgangskode, først sikre dig, **udløbet** er indstillet til den ønskede udløbsdato og det klokkeslæt, og klik derefter på **Opret Token**.

![Adgangskode][api-management-password]

>[AZURE.IMPORTANT] Noter denne adgangskode. Når du forlader denne side vises adgangskoden ikke igen.

I følgende eksempler bruges værktøjet ciffer fest fra [Ciffer til Windows](http://www.git-scm.com/downloads) , men du kan bruge en hvilken som helst ciffer værktøj, du allerede kender.

Åbne dit ciffer værktøj i den ønskede mappe og køre følgende kommando for at klone ciffer lager til din lokale computer, ved hjælp af kommandoen fra portalen publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Angive brugernavn og adgangskode, når du bliver bedt om.

Hvis du modtager en fejl, kan du prøve at ændre din `git clone` kommando for at medtage det brugernavn og adgangskode, som vist i følgende eksempel.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Hvis dette giver en fejl, kan du prøve URL-adressen kodning adgangskode del af kommandoen. Der er en hurtig måde at gøre dette til at åbne Visual Studio og udstede følgende kommando i **Vinduet Immediate**. Åbne en hvilken som helst løsning eller et projekt i Visual Studio for at åbne **Vinduet Immediate**(eller oprette et nyt tomt console program), og vælg **Windows**, **Immediate** fra menuen **fejlfinding** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Brug den kodede adgangskode sammen med dit navn og lager brugerplacering til at oprette kommandoen ciffer.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Når lageret er klonet, kan du få vist og arbejde med dem i dit lokale filsystem. Du kan finde yderligere oplysninger finder [fil- og struktur referencen til lokale ciffer lager](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Opdatere din lokale lager med den nyeste forekomstkonfiguration af tjenesten

Hvis du foretager ændringer i din API Management service forekomst i portalen publisher eller ved hjælp af REST-API, skal du gemme disse ændringer til lageret, før du kan opdatere din lokale lager med de seneste ændringer. Gør dette, klik på **Gem konfiguration til lager** under fanen **konfiguration lager** i publisher-portalen, og derefter udstede følgende kommando i din lokale lager.

    git pull

Før du kører `git pull` sikre, at du er i mappen til din lokale lager. Hvis du lige har fuldført den `git clone` kommando, og derefter skal du ændre mappen til din repo ved at køre en kommando, som følgende.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Til at overføre ændringer fra din lokale repo til server repo

Hvis du vil overføre ændringer fra din lokale lager til server-lager, skal du tilføje dine ændringer og distribuere dem til server-lager. For at bekræfte dine ændringer, åbne værktøjet din ciffer, skifte til mappen på din lokale lager og udstede følgende kommandoer.

    git add --all
    git commit -m "Description of your changes"

For at overføre alle anvendelser på serveren, skal du køre følgende kommando.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Installere en hvilken som helst tjenestekonfiguration ændres til forekomsten af API administration

Når dine lokale ændringer er anvendt og overføres til server-lager, kan du installere dem til din API Management service forekomst.

![Installere][api-management-configuration-deploy]

Du kan finde oplysninger om at udføre denne handling ved hjælp af REST-API [installere ciffer ændringer til konfigurationsdatabase ved hjælp af REST-API](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fil- og strukturere referencen til lokale ciffer lager

De filer og mapper i den lokale ciffer lager indeholder konfigurationsoplysninger om forekomsten af.

| Element                       | Beskrivelse                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| api-management rodmappen | Indeholder på øverste niveau konfiguration for forekomsten af                                  |
| API'er mappe                | Indeholder konfigurationen til API'er i forekomsten af                            |
| grupper mappe              | Indeholder konfigurationen for grupperne i forekomsten af tjenesten                          |
| politikker mappe            | Indeholder politikker i forekomsten af                                              |
| portalStyles mappe        | Indeholder konfiguration for udvikler portalen tilpasninger i forekomsten af |
| mappen produkter            | Indeholder konfigurationen for produkterne i forekomsten af                        |
| skabelonmappen           | Indeholder konfiguration for e-mail-skabeloner i forekomsten af tjenesten                 |

Hver mappe kan indeholde et eller flere filer, og i nogle tilfælde en eller flere mapper, for eksempel en mappe for hver API, produkt eller gruppe. Filerne i hver enkelt mappe er specifikke for den enhedstype, der er beskrevet i mappenavnet.

| Filtype | Formål                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Af konfigurationsoplysninger om den pågældende enhed                  |
| HTML      | Beskrivelser af den enhed, ofte vises i portalen udvikler |
| XML       | Politik sætninger                                                      |
| CSS       | Typografiark for udvikler portalen tilpasning                        |

Disse filer kan være oprettet, slettes, redigeres og administreres på dit lokale filsystem, og de ændringer, der er installeret tilbage til den din API Management service forekomst.

>[AZURE.NOTE] De følgende enheder findes ikke i ciffer lager og kan ikke konfigureres ved hjælp af ciffer.
>
>-    Brugere
>-    Abonnementer
>-    Egenskaber
>-    Udvikler portalen enheder end typografier

### <a name="root-api-management-folder"></a>Api-management rodmappen

I roden `api-management` mappen indeholder en `configuration.json` fil, der indeholder oplysninger om forekomsten af i følgende format på øverste niveau.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

De første fire indstillinger (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, og `UserRegistrationTermsConsentRequired`) kort til følgende indstillinger under fanen **identiteter** i afsnittet **sikkerhed** .

| Identitet indstilling                     | Kort til                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | **Omdirigere anonyme brugere til logonsiden** afkrydsningsfelt |
| UserRegistrationTerms                | **Vilkår for anvendelse på bruger tilmelding** tekstfelt               |
| UserRegistrationTermsEnabled         | Afkrydsningsfeltet **Vis vilkår for anvendelse på siden for tilmelding**         |
| UserRegistrationTermsConsentRequired | **Kræver samtykke** afkrydsningsfelt                          |

![Indstillinger for identitet][api-management-identity-settings]

De næste fire indstillinger (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, og `DelegationValidationKey`) kort til følgende indstillinger under fanen **delegering** i afsnittet **sikkerhed** .

| Delegering           | Kort til                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Afkrydsningsfeltet **stedfortræderen logon og tilmelding**    |
| DelegationUrl                | **Delegering slutpunkt URL-adressen** tekstfelt        |
| DelegatedSubscriptionEnabled | Afkrydsningsfeltet **stedfortræderen produkt abonnement** |
| DelegationValidationKey      | **Stedfortræder valideringsnøgle** tekstfelt        |

![Indstillinger for delegering][api-management-delegation-settings]

Indstillingen endelige `$ref-policy`, som er tilknyttet filen global politik sætninger for forekomsten af.

### <a name="apis-folder"></a>API'er mappe

Den `apis` mappen indeholder en mappe til hver API i forekomsten af som indeholder følgende elementer.

-   `apis\<api name>\configuration.json`-Dette er konfigurationen for API og indeholder oplysninger om back end-URL-adressen, og handlingerne. Dette er de samme oplysninger, der skal returneres, hvis du skulle ringe [få en bestemt API](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) med `export=true` i `application/json` format.
-   `apis\<api name>\api.description.html`-Dette er en beskrivelse af API og svarer til den `description` egenskab for [API enhed](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-mappen indeholder `<operation name>.description.html` filer, der er tilknyttet handlingerne i API. Hver fil indeholder en beskrivelse af en enkelt handling i API, som er tilknyttet den `description` egenskab på [handlingen enhed](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) i REST-API.

### <a name="groups-folder"></a>grupper mappe

Den `groups` mappen indeholder en mappe for hver gruppe, der er defineret i forekomsten af tjenesten.

-   `groups\<group name>\configuration.json`-Dette er konfigurationen for gruppen. Dette er de samme oplysninger, der skal returneres, hvis du skulle ringe handlingen [få en bestemt gruppe](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
-   `groups\<group name>\description.html`-Dette er en beskrivelse af gruppen og svarer til den `description` egenskab for den [gruppe enhed](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>politikker mappe

Den `policies` mappen indeholder sætningerne politik for din tjenesteforekomst.

-   `policies\global.xml`-indeholder politikker, der er defineret på globalt for din tjenesteforekomst.
-   `policies\apis\<api name>\`-Hvis du har en hvilken som helst politikker, der er defineret på API omfang, de er indeholdt i denne mappe.
-   `policies\apis\<api name>\<operation name>\`mappe - Hvis du har en hvilken som helst politikker, der er defineret på handlingen omfang, de er indeholdt i denne mappe i `<operation name>.xml` filer, der er tilknyttet sætningerne politik for hver handling.
-   `policies\products\`-Hvis du har en hvilken som helst politikker, der er defineret på produktomfang, de er indeholdt i denne mappe, som indeholder `<product name>.xml` filer, der er tilknyttet politik-sætningerne for hvert produkt.

### <a name="portalstyles-folder"></a>portalStyles mappe

Den `portalStyles` mappe indeholder konfiguration og typografi ark for udvikler portalen tilpasninger for forekomsten af.

-   `portalStyles\configuration.json`-indeholder navnene på de typografiark, der bruges af portalen udvikler
-   `portalStyles\<style name>.css`-hver `<style name>.css` filen indeholder typografier til portalen udvikler (`Preview.css` og `Production.css` som standard).

### <a name="products-folder"></a>mappen produkter

Den `products` mappen indeholder en mappe for hvert produkt, der er defineret i forekomsten af tjenesten.

-   `products\<product name>\configuration.json`-Dette er konfigurationen for produktet. Dette er de samme oplysninger, der skal returneres, hvis du skulle ringe handlingen [få et bestemt produkt](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-Dette er en beskrivelse af produktet og svarer til den `description` egenskab på [produkt enhed](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) i REST-API.

### <a name="templates"></a>skabeloner

Den `templates` mappe indeholder konfigurationen for [e-mail-skabeloner](api-management-howto-configure-notifications.md) for forekomsten af.

-   `<template name>\configuration.json`-Dette er konfigurationen af e-mail-skabelonen.
-   `<template name>\body.html`-Dette er brødteksten i e-mail-skabelonen.

## <a name="next-steps"></a>Næste trin

Finde oplysninger om andre måder at administrere din forekomst af tjenesten, i:

-   Administrere din forekomst af tjenesten ved hjælp af de følgende PowerShell-cmdletter
    -   [Tjenesten installation PowerShell-cmdlet reference](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Styring af PowerShell-cmdlet reference](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Administrere din tjenesteforekomst i publisher-portalen
    -   [Administrere din første API](api-management-get-started.md)
-   Administrere din forekomst af tjenesten ved hjælp af REST-API
    -   [API Management REST-API reference](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Se en video oversigt

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




