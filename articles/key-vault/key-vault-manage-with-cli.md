<properties
    pageTitle="Administrere nøgle samling ved hjælp af CLI | Microsoft Azure"
    description="Brug dette selvstudium til at automatisere almindelige opgaver i nøgle samling ved hjælp af CLI"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Administrere nøgle samling ved hjælp af CLI #
Azure-tasten samling findes i de fleste områder. Få mere at vide ved at se [tasten samling priser side](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduktion  
Brug dette selvstudium til at hjælpe dig med at komme i gang med Azure-tasten samling til at oprette en hærdet objektbeholder (en samling) i Azure, gemme og administrere cryptographic nøgler og hemmeligheder i Azure. Det vejleder dig gennem processen med at bruge Azure på tværs af platforme kommandolinjen til at oprette en samling af legitimationsoplysninger, der indeholder en nøgle eller adgangskode, kan du bruge med et Azure-program. Den derefter viser dig hvordan et program derefter kan bruge denne tast eller adgangskode.

**Anslået tid at gennemføre:** 20 minutter

>[AZURE.NOTE]  Dette selvstudium omfatter ikke oplysninger om, hvordan du kan skrive Azure-program, et af trinnene omfatter, som viser, hvordan du tillade, at et program du bruger en nøgle eller hemmeligt i den vigtige samling af legitimationsoplysninger.
>
>I øjeblikket, kan du konfigurere Azure-tasten samling i portalen Azure. I stedet bruge vejledningen på tværs af platforme kommandolinjen. Eller se [selvstudiet svarer](key-vault-get-started.md)til Azure PowerShell.

Oversigt oversigtsoplysninger om Azure-tasten samling, du [Hvad er Azure-tasten samling?](key-vault-whatis.md)

## <a name="prerequisites"></a>Forudsætninger
For at fuldføre dette selvstudium, skal du have følgende:

- Et abonnement på Microsoft Azure. Hvis du ikke har en, kan du tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial).
- Kommandolinjen version 0.9.1 eller nyere. For at installere den nyeste version og oprette forbindelse til dit Azure abonnement skal du se [installere og konfigurere kommandolinjen Azure på tværs af platforme](../xplat-cli-install.md).
- Et program, der skal konfigureres for at bruge tasten eller den adgangskode, du opretter i dette selvstudium. Et eksempelprogram er tilgængeligt fra [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Du kan finde instruktioner, ledsagende Vigtigt-filen.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjælp med Azure på tværs af platforme kommandolinjen

Dette selvstudium antages det, at du allerede kender, kommandolinjen (fest, Terminal, kommandoprompt)

Den – hjælp eller -h parameter kan bruges til at få vist hjælp til bestemte kommandoer. Alternativt azure Hjælp [kommando] [parametre] formater kan også bruges til at returnere de samme oplysninger. For eksempel returnerer følgende kommandoer alle de samme oplysninger:

    azure account set --help

    azure account set -h

    azure help account set

Når du er i tvivl om de parametre, bruges til en kommando refererer for at bruge – Hjælp, -h eller azure help [kommando].

Du kan også læse følgende selvstudier til at blive fortrolig med Azure ressourcestyring i Azure på tværs af platforme kommandolinjen:

- [Sådan installerer og konfigurerer Azure på tværs af platforme linje Kommandogrænseflade](../xplat-cli-install.md)
- [Brug af Azure på tværs af platforme kommandolinjen med Azure ressourcestyring](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Oprette forbindelse til dine abonnementer

For at logge på ved hjælp af en organisationskonto skal du bruge følgende kommando:

    azure login -u username -p password

eller hvis du vil logge på ved at skrive interaktivt

    azure login

>[AZURE.NOTE]  Login metode fungerer kun med organisationskonto. En organisationskonto er en bruger, der administreres af din organisation, og defineret i din organisation Azure Active Directory-lejer.


Hvis du ikke aktuelt har en organisationskonto og bruger en Microsoft-konto til at logge på dit Azure-abonnement, kan du nemt oprette én at benytte følgende fremgangsmåde.

1.  Log på siden Login to [Azure Management-portalen](https://manage.windowsazure.com/), og klik på Active Directory.
2.  Hvis der findes ingen directory, Vælg Opret mappen, og Angiv de nødvendige oplysninger.
3.  Vælg mappen, og tilføje en ny bruger. Den nye bruger er en organisationskonto. Under oprettelse af brugeren, skal du leveres med både en e-mail-adresse til brugeren og en midlertidig adgangskode. Gemme disse oplysninger, som det er anvendt i et andet trin.
4.  Vælg indstillinger fra portalen, og vælg derefter administratorer. Vælg Tilføj, og tilføje den nye bruger som administrator af en samtidig. Dette giver mulighed for organisationskonto til at administrere din Azure abonnement.
5.  Til sidst skal logger af portalen Azure og derefter logge på igen, med den nye organisationskonto. Hvis dette er den første gang at logge på med denne konto, bliver du bedt om at ændre adgangskoden.

Finde flere oplysninger om at bruge en organisationskonto med Microsoft Azure, [tilmelde sig Microsoft Azure som en organisation](../active-directory/sign-up-organization.md).

Hvis du har flere abonnementer, og du vil angive en bestemt én skal bruges til Azure-tasten samling, Skriv følgende for at se abonnementer til din konto:

    azure account list

Hvis du vil angive abonnementet, du bruger, Skriv:

    azure account set <subscription name>

Du kan finde flere oplysninger om konfiguration af Azure på tværs af platforme kommandolinjen, se, [hvordan du installere og konfigurere Azure på tværs af platforme-kommandolinjen](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Skifte til at bruge Azure ressourcestyring

Tasten samling kræver Azure Ressourcestyring, så Skriv følgende for at skifte til Azure ressourcestyring tilstand:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Oprette en ny ressourcegruppe

Når du bruger Azure Ressourcestyring, oprettes alle relaterede ressourcer i en ressourcegruppe. Vi opretter en ny ressourcegruppe 'ContosoResourceGroup' for dette selvstudium.

    azure group create 'ContosoResourceGroup' 'East Asia'

Den første parameter er ressource gruppenavn og den anden parameter er placeringen. Brug kommandoen for placering, `azure location list` til at identificere at angive en alternativ placering til den i dette eksempel. Hvis du vil have flere oplysninger, Skriv:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registrere provideren nøgle samling ressource
Sørg for, at den nøgle samling ressource udbyder er registreret i dit abonnement:

`azure provider register Microsoft.KeyVault`

Dette skal kun udføres én gang hver abonnement.


## <a name="create-a-key-vault"></a>Oprette en vigtige samling

Brug den `azure keyvault create` kommandoen til at oprette en vigtige samling af legitimationsoplysninger. Dette script har tre obligatoriske parametre: en ressource gruppenavn, et vigtige samling navn og geografiske placering.

Hvis du bruger samling navnet på ContosoKeyVault, gruppen ressourcenavnet af ContosoResourceGroup og placeringen af Østasien, Skriv:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

Output fra denne kommando viser egenskaberne for den vigtige samling af legitimationsoplysninger, som du lige har oprettet. De to vigtigste egenskaber er:

- **Navn**: I eksemplet er dette ContosoKeyVault. Du skal bruge dette navn til andre nøgle samling cmdletter.
- **vaultUri**: I eksemplet er dette https://contosokeyvault.vault.azure.net. Programmer, der bruger din samling via dens REST-API skal bruge denne URI.

Azure-konto er nu tilladelse til at udføre handlinger på denne vigtige samling. Ingen andre er endnu.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Tilføje en nøgle eller hemmeligt til den vigtige samling af legitimationsoplysninger

Hvis du vil Azure-tasten samling til at oprette en software-beskyttede nøgle for dig, kan du bruge den `azure key create` kommando, og skrive følgende:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Men hvis du har en eksisterende nøgle i en .pem-fil, der er gemt som lokale filer i en fil kaldet softkey.pem, du vil overføre til Azure-tasten samling, Skriv følgende for at importere tasten fra den. PEM-fil, der beskytter tasten af software i tjenesten nøgle samling af:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Du kan nu henvise til nøglen, som du har oprettet eller overføres til Azure-tasten samling ved hjælp af dens URI. Brug **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** til altid for at hente den aktuelle version, og følg **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** for at finde denne bestemte version.

Hvis du vil føje et hemmeligt til samling, som er en adgangskode, med navnet SQLPassword og, hvor værdien af Pa$ $w0rd til Azure-tasten samling, skrive følgende:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Du kan nu henvise til denne adgangskode, du har føjet til Azure-tasten samling ved hjælp af dens URI. Brug **https://ContosoVault.vault.azure.net/secrets/SQLPassword** til altid for at hente den aktuelle version, og følg **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** for at finde denne bestemte version.

Lad os se på tasten eller hemmeligt, som du lige har oprettet:

- For at få vist din nøgle, Skriv:`azure keyvault key list --vault-name 'ContosoKeyVault'`
- For at få vist din hemmeligt, Skriv:`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Registrere et program med Azure Active Directory

Dette trin vil normalt udføres af en udvikler, på en separat computer. Det er ikke specifik for Azure-tasten samling, men her, er medtaget for fuldstændighed.


>[AZURE.IMPORTANT] For at fuldføre selvstudiet, din konto, samling af legitimationsoplysninger og det program, du vil registrere i dette trin skal alle være i den samme Azure mappe.

Programmer, der bruger en vigtige samling af legitimationsoplysninger skal godkende ved hjælp af et token fra Azure Active Directory. For at gøre dette, skal ejeren af programmet først registrere programmet i deres Azure Active Directory. I slutningen af registrering bliver ejeren af programmet følgende værdier:


- Et **Program-ID** (også kaldet en klient-ID) og **godkendelsesnøgle** (også kaldet delte hemmeligt). Programmet skal præsentere begge af disse værdier til Azure Active Directory, at få et token. Hvordan programmet er konfigureret til at gøre dette afhænger af programmet. Til programmet nøgle samling eksempel angiver ejeren af programmet disse værdier i app.config-filen.



Til at registrere programmet i Azure Active Directory:

1. Log på portalen Azure.
2. Klik på **Active Directory**til venstre, og vælg derefter den mappe, hvor du vil registrere dit program. <br> <br> Bemærk: Skal du vælge den samme mappe, der indeholder det Azure abonnement, som du har oprettet din nøgle samling. Hvis du ikke ved, hvilken mappe dette er, klik på **Indstillinger**, identificere det abonnement, som du har oprettet din nøgle samling, og notér navnet på den mappe, der vises i den sidste kolonne.

3. Klik på **programmer**. Hvis ingen apps er blevet føjet til dit bibliotek, kan denne side kan se linket **Tilføj en App** . Klik på linket, eller du kan alternativt kan du klikke på **TILFØJET** på kommandolinjen.
4.  I guiden **Tilføj programmer** på den **Hvad vil du gøre?** på siden skal du klikke på **Tilføj et program, udvikling af min organisation**.
5.  Angiv et navn for dit program på siden **Fortæl os om dit program,** og vælg **WEB APPLICATION og/eller WEB API** (standard). Klik på ikonet næste.
6.  Angiv **SIGN-ON URL-adresse** og **APP ID URI** -webprogrammet på siden **App egenskaber** . Hvis dit program ikke har disse værdier, kan du gøre dem til dette trin (for eksempel du kan angive http://test1.contoso.com for begge felter). Det betyder ikke noget, hvis der findes følgende websteder; Hvad er vigtige er, app ID URI for hvert program er forskellige for hvert program i mappen. Mappen bruger denne streng til at identificere din app.
7.  Klik på ikonet udført for at gemme ændringerne i guiden.
8.  Klik på **KONFIGURER**på siden Hurtig Start.
9.  Rul ned til sektionen **taster** , vælg varigheden, og klik derefter på **Gem**. Siden opdaterer og viser nu en nøgle-værdi. Du skal konfigurere dit program med denne nøgleværdi og værdien **Klient-ID** . (Instruktioner til denne konfiguration bliver program-specifikke).
10. Kopiér værdien klient-ID fra denne side, som du skal bruge i næste trin for at angive tilladelser til din samling af legitimationsoplysninger.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Tillade, at du skal bruge tasten eller hemmeligt

Hvis du vil godkende program tilladelse til at få adgang til den tast eller hemmeligt i samling af legitimationsoplysninger, skal du bruge den `azure keyvault set-policy` kommandoen.

Eksempelvis hvis navnet på din samling af legitimationsoplysninger er ContosoKeyVault og det program, du vil give tilladelse har en klient-ID'ET for 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, og du vil tillade, at du kan dekryptere og tilmelde taster i din samling, derefter køre følgende:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Hvis du kører på Windows-kommandoprompt, skal du erstatte enkelt anførselstegn med dobbelte anførselstegn, og du kan også escape-interne dobbelte anførselstegn. For eksempel: "[\"dekryptere\",\"log\"]".

Hvis du vil tillade, at den samme program til at læse hemmeligheder i din samling, skal du køre følgende:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Hvis du vil bruge et hardware sikkerhed-modul (HSM) ##

Du kan importere eller oprette nøgler i hardware sikkerhed moduler (HSMs), som Efterlad aldrig i HSM rammen for større sikkerhed. HSMs er FIPS 140-2 niveau 2 valideres. Hvis dette krav ikke gælder for dig, springe dette afsnit, og gå til at [slette den vigtige samling af legitimationsoplysninger og tilhørende nøgler og hemmeligheder](#delete-the-key-vault-and-associated-keys-and-secrets).

Hvis du vil oprette disse HSM beskyttet taster, skal du have en samling af legitimationsoplysninger abonnement, der understøtter HSM beskyttet taster.

Når du opretter keyvault, kan du tilføje parameteren 'sku':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Du kan føje software-beskyttede nøgler (som vist tidligere) og HSM beskyttet nøgler til denne samling af legitimationsoplysninger. Angive parameteren Destination til 'HSM' for at oprette en HSM beskyttet nøgle:

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Du kan bruge følgende kommando til at importere en nøgle fra en .pem fil på computeren. Denne kommando importerer nøglen til HSMs i tjenesten nøgle samling:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

Den næste kommando importerer en "få din egen nøgle" (BYOK) pakke. Dette kan du oprette din nøgle i din lokale HSM og overføre den til HSMs i tjenesten nøgle samling uden tasten at forlade HSM rammen:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Finde mere detaljerede oplysninger om, hvordan du opretter denne BYOK pakke, se, [hvordan du bruger HSM-Protected taster med Azure nøgle samling](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Slette vigtige samling og tilknyttede nøgler og hemmeligheder

Hvis du ikke længere har brug for den vigtige samling af legitimationsoplysninger og nøgle eller hemmeligt, den indeholder, kan du slette den vigtige samling af legitimationsoplysninger ved hjælp af kommandoen azure keyvault Slet:

    azure keyvault delete --vault-name 'ContosoKeyVault'

Eller du kan slette en hel Azure ressourcegruppe, som indeholder den vigtige samling af legitimationsoplysninger og andre ressourcer, som du har brugt i den pågældende gruppe:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Andre Azure på tværs af platforme kommandolinjen kommandoer

Andre kommandoer, du kan nyttigt til håndtering af Azure-tasten samling.

Denne kommando viser en tabelformat visning af alle nøgler og de valgte egenskaber:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Denne kommando viser en komplet liste over egenskaberne for den angivne nøgle:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Denne kommando viser en tabelformat visning af alle hemmeligt navne og valgte egenskaber:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Her er et eksempel på, hvordan du fjerner en bestemt nøgle:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Her er et eksempel på, hvordan du fjerner en bestemt hemmeligt:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Næste trin

Se [i Azure nøgle samling developer guide](key-vault-developers-guide.md)til programmering referencer.
