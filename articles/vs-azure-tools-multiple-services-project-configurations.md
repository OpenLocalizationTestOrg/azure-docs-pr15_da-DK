<properties
   pageTitle="Konfigurere din Azure projekt ved hjælp af flere service konfigurationer | Microsoft Azure"
   description="Lær, hvordan du konfigurerer et Azure skyen service projekt ved at ændre ServiceDefinition.csdef og ServiceConfiguration.cscfg filer."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Konfigurere din Azure projekt ved hjælp af flere Service konfigurationer

En Azure skyen tjenesten project omfatter to konfigurationsfiler: ServiceDefinition.csdef og ServiceConfiguration.cscfg. Disse filer er pakket med din Azure cloud-tjenesteprogram og brug af Azure.

- Filen **ServiceDefinition.csdef** indeholder de metadata, der kræves af Azure miljø til tjenesteprogrammet skyen, herunder hvilke roller, den indeholder krav. Denne fil indeholder også konfigurationsindstillinger, der gælder for alle forekomster. Disse indstillinger for søgekonfiguration kan læses på kørselstidspunktet, ved hjælp af Azure Service vært Runtime API. Denne fil kan ikke opdateres, mens din tjeneste kører i Azure.

- Filen **ServiceConfiguration.cscfg** indstiller værdier for de konfigurationsindstillinger, der er defineret i definitionsfil tjenesten og angiver antallet forekomster, der skal køres for hver rolle. Denne fil kan opdateres, mens din skytjeneste kører i Azure.

Azure-værktøjer til Microsoft Visual Studio giver egenskabssider, du kan bruge til at angive konfigurationsindstillinger, der er gemt i disse filer. Dobbeltklikke på den rolle reference under Azure skyen service projektet i Solution Explorer skal for at få adgang til siden med egenskaber, eller Højreklik på rolle referencen, og vælg **Egenskaber**, som vist i følgende figur.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Du kan finde oplysninger om de underliggende skemaer til tjenestedefinitionen og tjenesten konfigurationsfiler [Skemareference](https://msdn.microsoft.com/library/azure/dd179398.aspx). Du kan finde flere oplysninger om konfiguration af tjenesten, se, [hvordan du konfigurerer Cloud Services](./cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Konfigurere rolleegenskaber for

Siden med egenskaber for en web rolle og en kollega rolle er de samme, selvom der er nogle forskelle, fremhævet i de følgende afsnit.

Du kan konfigurere Azure cachelagring tjenester fra siden **cachelagring** .

### <a name="configuration-page"></a>Konfigurationssiden

Du kan angive disse egenskaber på siden **konfiguration** :

**Forekomster**

Angiv egenskaben **forekomst** count til antallet af forekomster tjenesten skal køre for denne rolle.

Angive egenskaben **VM størrelse** til **Ekstra lille**, **lille**, **Medium**, **stor**eller **Ekstra stor**.  Du kan finde yderligere oplysninger finder [størrelser for Cloud Services](./cloud-services/cloud-services-sizes-specs.md).

**Starthandling** (Kun web rolle)

Angive denne egenskab til at angive, at Visual Studio skal Start en webbrowser for slutpunkterne HTTP eller HTTPS slutpunkterne, eller begge, når du starter fejlfinding.

Indstillingen HTTPS slutpunkt er kun tilgængelig, hvis du allerede har defineret et HTTPS slutpunkt for din rolle. Du kan angive et HTTPS slutpunkt på siden **slutpunkter** egenskab.

Hvis du allerede har tilføjet et HTTPS-slutpunkt, indstillingen HTTPS slutpunkt er aktiveret som standard, og Visual Studio Start en browser for dette slutpunkt, når du starter fejlfinding, ud over en browser for din HTTP slutpunkt. Dette forudsætter, at begge startindstillinger er aktiveret.

**Diagnosticering**

Diagnosticering er som standard aktiveret for rollen Web. Azure skyen project og lager kontoen for tjenesten er indstillet til at bruge lokale lager emulatoren. Når du er klar til at installere på Azure, kan du vælge knappen generator (**...**) for at opdatere lagerplads konto for at bruge Azure lagerplads i skyen. Du kan overføre diagnosticering dataene til kontoen lagerplads efter behov eller med automatisk planlagte intervaller. Se [Aktivere diagnosticering i Azure Cloud Services og virtuelle computere](./cloud-services/cloud-services-dotnet-diagnostics.md)kan finde flere oplysninger om Azure diagnosticering.

## <a name="settings-page"></a>Indstillingssiden for

Du kan føje konfigurationsindstillinger på siden **Indstillinger** for din tjeneste. Indstillinger for søgekonfiguration er navn / værdi-par. Kode, der kører i rollen kan læse værdierne i dine konfigurationsindstillinger på kørselstidspunktet, ved hjælp af klasser, der leveres af [Azure administrerede bibliotek](http://go.microsoft.com/fwlink?LinkID=171026). Nærmere betegnet kan returnerer metoden [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) værdien af en navngivet indstilling på kørselstidspunktet.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Konfigurere en forbindelsesstreng til en lagerplads konto

En forbindelsesstreng er en indstilling, der giver oplysninger om forbindelsen og godkendelse for lagerplads emulatoren eller for en Azure-lager-konto. Når din kode skal have adgang Azure-lager services data – det vil sige, blob, kø eller tabeldata – fra kode, der kører i en rolle, er du nødt til at definere en forbindelsesstreng for den pågældende lagerplads konto.

En forbindelsesstreng, der peger på en Azure lagerplads konto skal bruge et defineret format. Du kan finde oplysninger om, hvordan du opretter forbindelsesstrenge [Konfigurere Azure lagerplads forbindelsesstrenge](./storage/storage-configure-connection-string.md).

Når du er klar til at teste din tjeneste mod Azure lagerplads tjenesterne, eller når du er klar til at anvende din skytjeneste til Azure, kan du ændre værdien af en hvilken som helst forbindelsesstrenge til at pege på kontoen Azure-lager. Vælg (**...**) skal du vælge **Enter lagerplads kontolegitimationsoplysninger**. Angiv dine kontooplysninger, der indeholder dit kontonavn og kontonøgle. I dialogboksen **Lagerplads konto forbindelsesstreng** , kan du også angive, om du vil bruge standard HTTPS slutpunkter (standardindstillingen), standard HTTP slutpunkter eller brugerdefineret slutpunkter. Du kan vælge at bruge brugerdefinerede slutpunkter, hvis du har registreret et brugerdefineret domænenavn til din tjeneste, som beskrevet i [konfigurere et brugerdefineret domænenavn til blob-data i en Azure-lager-konto](./storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] Du skal ændre din forbindelsesstrenge til at pege på en Azure-lager-konto, før du installerer din tjeneste. Hvis ikke dette, kan det medføre, at din rolle ikke at starte eller for at gå gennem initialiseringen af, optaget og standse tilstand.

## <a name="endpoints-page"></a>Slutpunkter side

En kollega rolle kan have en hvilken som helst antal HTTP, HTTPS eller TCP slutpunkter. Slutpunkter kan være input slutpunkter, der er tilgængelige for eksterne kunder, eller interne slutpunkter, der er tilgængelige for andre roller, der kører i tjenesten.

- Ændre typen slutpunkt at skrive for at frigøre HTTP-ark til eksterne kunder og en webbrowser, og Angiv et navn og et offentligt portnummer.

- Ændre slutpunkt til **input**for at frigøre en HTTPS-ark til eksterne kunder og en webbrowser, og Angiv et navn, et offentligt portnummer og et navn til administration af certifikat.

    Vær opmærksom på, før du kan angive et management-certifikat, skal du definere certifikatet på siden **certifikaterne** egenskab.

- Ændre slutpunkt til interne for at frigøre et slutpunkt for interne adgang af andre roller i skytjenesten, og Angiv et navn og mulige privat porte for dette slutpunkt.

## <a name="local-storage-page"></a>Lokale lager side

Du kan bruge siden **Lokale lager** egenskab til at reservere ressourcer i en eller flere lokale lager til en rolle. Et lokalt lager ressource er en reserveret mappe i filsystemet på den Azure virtuelle maskine, der kører en forekomst af en rolle.

## <a name="certificates-page"></a>Certifikater side

På siden **certifikaterne** , kan du knytte certifikater til din rolle. De certifikater, som du har tilføjet kan bruges til at konfigurere HTTPS slutpunkterne på siden **slutpunkter** for egenskaben.

Siden **certifikaterne** egenskab føjer oplysninger om dine certifikater til konfigurationen af tjenesten. Bemærk, at dine certifikater ikke er pakket med din tjeneste Du skal overføre dine egne certifikater separat til Azure via [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

For at knytte et certifikat til din rolle, skal du angive et navn til certifikatet. Du kan bruge dette navn til at referere til certifikatet, når du konfigurerer et HTTPS slutpunkt på siden **slutpunkter** egenskab. Næste, Angiv, om lageret er **Lokale computer** eller **Aktuelle bruger** og navnet på store. Til sidst skal du angive det certifikat miniature. Hvis certifikatet, der er i den aktuelle User\Personal (mit) lager, kan du angive det certifikat miniature ved at vælge certifikatet fra en på forhånd liste. Hvis den findes i enhver anden placering, angive miniatureværdien manuelt.

Når du tilføjer et certifikat fra certifikat store, føjes automatisk en mellemliggende certifikater til konfigurationsindstillinger for dig. Certifikaterne mellemliggende skal også overføres til Azure for at konfigurere din tjeneste til SSL korrekt.

En hvilken som helst management certifikater, der tilknyttes din tjeneste gælder for din tjeneste, kun, når den kører i skyen. Når din tjeneste kører i den lokale udviklingsmiljø, bruger et standard-certifikat, der administreres af Beregn emulatoren.

## <a name="configuring-the-azure-cloud-service-project"></a>Konfiguration af Azure skyen service projektet

For at konfigurere indstillinger, der vedrører et projekt i hele Azure skyen tjeneste skal du først åbne genvejsmenuen for projektnoden, og vælg derefter Egenskaber for at åbne dets egenskabssider. I følgende tabel vises siderne egenskab.

|Egenskabsside|Beskrivelse|
|---|---|
|Program|Du kan få vist oplysninger om versionen af Azure-værktøjer, der bruges i denne skyen tjenesten project, og du kan opgradere til den aktuelle version af værktøjerne fra denne side.|
|Opbygge begivenheder|Du kan angive før og efter build begivenheder fra denne side.|
|Udvikling|Du kan angive build konfigurationsvejledning og de betingelser, under som en hvilken som helst efter build hændelser der køres fra denne side.|
|Web|Du kan konfigurere indstillinger, der er relateret til webserveren fra denne side.|
