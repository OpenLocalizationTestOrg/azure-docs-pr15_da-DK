<properties
pageTitle="Hvordan du opdaterer en skybaseret tjeneste | Microsoft Azure"
description="Lær at opdatere skytjenester i Azure. Få mere at vide, hvordan en opdatering på en skybaseret tjeneste fortsætter med at sikre, at tilgængelige."
services="cloud-services"
documentationCenter=""
authors="Thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>Hvordan du opdaterer en skybaseret tjeneste

## <a name="overview"></a>Oversigt
I 10.000 fod er ved at opdatere en skybaseret tjeneste, herunder både roller og Gæst OS, en proces i tre trin. Binære filer og af konfigurationsfiler til den nye skytjeneste eller OS-version skal først skal overføres. Azure forbeholder sig derefter Beregn og netværk ressourcer til skytjenesten baseret på den nye sky service version krav. Til sidst skal udfører Azure en rullende opgradering for at opdatere trinvist lejeren til den nye version eller gæst OS, mens din tilgængelighed. I denne artikel beskrives detaljerne for det sidste trin – rullende opgraderingen.

## <a name="update-an-azure-service"></a>Opdatere en Azure Service

Azure organiserer din rolle forekomster i logiske grupper kaldet opgradering domæner (UD). Opgradering domæner (UD) er logiske sæt af rolle forekomster, der opdateres som en gruppe.  Azure opdaterer en skybaseret tjeneste én UD på et tidspunkt, som giver mulighed for forekomster i andre UDs fortsat fungerer trafik.

Standardantallet af opgradering domæner er 5. Du kan angive et andet antal opgradering domæner ved at medtage attributten upgradeDomainCount i tjenestens definitionsfil (.csdef). Du kan finde flere oplysninger om attributten upgradeDomainCount, se [WebRole skema](https://msdn.microsoft.com/library/azure/gg557553.aspx) eller [WorkerRole skemaet](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Når du udfører en lokal opdatering af en eller flere roller i din tjeneste, opdaterer Azure sæt af rolle forekomster efter opgradering domænet, de tilhører. Azure opdateringer alle forekomster i en given opgradering domæne – stoppe dem, ved at opdatere dem, at hente dem igen online – går videre til næste domænet. Ved at stoppe kun de forekomster, der kører i det aktuelle opgradering domæne, sikrer Azure, at en opdatering sker med den mindst mulige virkning til tjenesten kører. Du kan finde flere oplysninger, se [hvordan opdateringen skrider frem](#howanupgradeproceeds) senere i denne artikel.

> [AZURE.NOTE] Mens de betingelser, **opdatere** og **opgradere** har en smule anderledes betydning i konteksten Azure, kan de bruges i flæng for processer og beskrivelser af funktionerne i dette dokument.

Din tjeneste skal definere mindst to forekomster af en rolle til rollen, der skal opdateres lokalt uden nedetid. Hvis tjenesten består af kun én forekomst af en rolle, bliver din tjeneste ikke tilgængelig, indtil den lokal opdatering er færdig.

I dette emne beskrives følgende oplysninger om Azure opdateringer:

-   [Tilladt tjenesteændringer under en opdatering](#AllowedChanges)
-   [Hvordan opgraderes forløber](#howanupgradeproceeds)
-   [Gendanne en opdatering](#RollbackofanUpdate)
-   [Start af flere mutating handlinger på en igangværende installation](#multiplemutatingoperations)
-   [Fordelingen af roller på tværs af opgradering domæner](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Tilladt tjenesteændringer under en opdatering
I følgende tabel vises de tilladte ændringer til en tjeneste under en opdatering:

|Ændringer tilladelse til at vært, tjenester og roller|Lokal opdatering|Faseinddelt (VIP Udskift)|Slet og installere igen|
|---|---|---|---|
|Operativsystemversion|Ja|Ja|Ja
|.NET sikkerhed og rettighedsadministration niveau|Ja|Ja|Ja|
|Virtuelt størrelse<sup>1</sup>|Ja<sup>2</sup>|Ja|Ja|
|Indstillinger for lokale lager|Øge kun<sup>2</sup>|Ja|Ja|
|Tilføje eller fjerne roller i en tjeneste|Ja|Ja|Ja|
|Antallet af forekomster af en bestemt rolle|Ja|Ja|Ja|
|Typen af slutpunkter til en tjeneste tal eller|Ja<sup>2</sup>|Nej|Ja|
|Navne og værdier for indstillinger for søgekonfiguration|Ja|Ja|Ja|
|Værdier (men ikke brugernavne) af indstillinger for søgekonfiguration|Ja|Ja|Ja|
|Tilføje nye certifikater|Ja|Ja|Ja|
|Ændre eksisterende certifikater|Ja|Ja|Ja|
|Installere ny kode|Ja|Ja|Ja|
<sup>1</sup> Ændring af størrelse er begrænset til undersættet af størrelser, der er tilgængelige for skytjenesten.

<sup>2</sup> Kræver Azure SDK 1,5 eller nyere versioner.

> [AZURE.WARNING] Ændring af størrelsen af virtuelt sletter lokale data.


Følgende elementer understøttes ikke under en opdatering:

-   Ændring af navnet på en rolle. Fjerne og derefter tilføje rollen med det nye navn.
-   Ændring af antallet opgradere domæne.
-   Reducere størrelsen på de lokale ressourcer.

Hvis du laver andre opdateringer til din tjeneste definition, såsom formindske størrelsen på lokal ressource, skal du udføre en VIP Byt om opdatering i stedet. Du kan finde yderligere oplysninger finder [Ombyt installation](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>Hvordan opgraderes forløber
Du kan beslutte, om du vil opdatere alle rollerne i din tjeneste eller en enkelt rolle i tjenesten. I begge tilfælde, er alle forekomster af hver rolle, som er ved at blive opgraderet og hører til det første opgradering domæne ikke længere, opgraderet, og hentet ind til online igen. Når de er online igen, er forekomster i det andet opgradering domæne ikke længere opgraderet og hentet ind til online igen. En skybaseret tjeneste kan have mindst én opgradering aktive ad gangen. Opgraderingen udføres altid mod den seneste version af skytjenesten.

I det følgende diagram vises, hvordan opgraderingen forløber, hvis du opgraderer alle roller i tjenesten:

![Opgradere service] (media/cloud-services-update-azure-service/IC345879.png "Opgradere service")

Denne næste diagram viser, hvordan opdateringen skrider frem, hvis du opgraderer en enkelt rolle:

![Opgradere rolle] (media/cloud-services-update-azure-service/IC345880.png "Opgradere rolle")  

> [AZURE.NOTE] Når du opgraderer en tjeneste fra en enkelt forekomst til flere forekomster anlægges din tjeneste, mens opgraderingen er udført på grund af de måde, hvorpå Azure opgraderinger tjenesterne. Tjenesten serviceniveauaftale sikrer tjenesten kører gælder kun for tjenester, der er installeret med mere end én forekomst. Følgende liste beskrives, hvordan dataene på hvert drev påvirkes af hver Azure service opgradering scenarie:
>
>VM genstart:
>
-   C: bevares
-   D: bevares
-   E bevares
>
>Portalen genstart:
>
-   C: bevares
-   D: bevares
-   E ødelagt
>
>Portalen Reimage:
>
-   C: bevares
-   D: ødelagt
-   E ødelagt

>Opgradering:
>
-   C: bevares
-   D: bevares
-   E ødelagt
>
>Node overførsel:
>
-   C: ødelagt
-   D: ødelagt
-   E ødelagt

>Bemærk, at på listen ovenfor e-drev repræsenterer den rolle roddrev, og må ikke være faste. I stedet bruge miljøvariablen % RoleRoot % til at repræsentere drevet.

>Installere en ny tjeneste med flere forekomster til arrangering serveren for at minimere nedetiden, når du opgraderer en enkelt forekomst tjeneste, og Udfør en VIP Byt om.

Under en automatisk opdatering evaluerer Azure-strukturen Controller med jævne mellemrum tilstanden for skytjeneste til at afgøre, når det er sikkert at vejlede næste UD. I denne tilstand beregning udføres på grundlag af per rolle og finder kun forekomster i den seneste version (det vil sige forekomster fra UDs, der har allerede blevet gennemført). Det kontrollerer, at et mindste antal forekomster rolle, for hver rolle har opnået terminal god stand.

### <a name="role-instance-start-timeout"></a>Rolle forekomst Start Timeout
Strukturen Controller vil vente 30 minutter for hver rolle forekomst når tilstanden Introduktion. Hvis timeout varigheden går, fortsætter strukturen Controller gå til den næste rolle forekomst.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Gendanne en opdatering
Azure giver fleksibilitet i administration af tjenester under en opdatering, fordi du kan starte flere handlinger på en tjeneste, når de indledende opdateringsforespørgsel er accepteret af Azure-strukturen Controller. En rollback kan kun udføres, når en opdatering (konfigurationsændring) eller opgraderingen er i tilstanden **i gang** på installationen. En opdatering eller opgradere betragtes som skal være i gang, så længe der er mindst én forekomst af den tjeneste, der endnu ikke er blevet opdateret til den nye version. Hvis du vil teste, om en rollback er tilladt, kontrollere værdien af flaget RollbackAllowed, returneres af handlingerne [Få installation](https://msdn.microsoft.com/library/azure/ee460804.aspx) og [Hent skyen Service egenskaber](https://msdn.microsoft.com/library/azure/ee460806.aspx) , er indstillet til sand.

> [AZURE.NOTE] Det kun giver mening at ringe til Rollback på en **lokal** opdatering eller opgradere, fordi VIP Udskift opgraderinger involverer erstatte én hele aktiv forekomst af din tjeneste med en anden.

Gendanne en igangværende opdatering har følgende effekter på installationen:

-   Alle forekomster af rolle som havde endnu ikke er opdateret eller opgraderet til den nye version er ikke opdateret eller opgraderet, fordi disse forekomster allerede kører target version af tjenesten.
-   Alle forekomster af rolle som havde allerede er opdateret eller opgraderet til den nye version af service (\*.cspkg) fil eller konfigurationen af tjenesten (\*.cscfg)-fil (eller begge filer) gendannes til den gamle version af filerne.

Dette er funktionelt leveret af følgende funktioner:

-   [Rollback opdatere eller opgradere](https://msdn.microsoft.com/library/azure/hh403977.aspx) handlingen, hvilket kan kaldes på en konfigurationsopdatering (som opkald [Skift Deployment Configuration](https://msdn.microsoft.com/library/azure/ee460809.aspx)) eller en opgradering (som opkald [Opgradere installation](https://msdn.microsoft.com/library/azure/ee460793.aspx)), så længe der er mindst én forekomst i den tjeneste, der endnu ikke er blevet opdateret til den nye version.
-   Elementet låst og elementet RollbackAllowed, som er returneret som en del af svar brødteksten i handlingerne [Få installation](https://msdn.microsoft.com/library/azure/ee460804.aspx) og [Hent skyen Service egenskaber](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  Låst element gør det muligt at registrere, hvornår en handling af mutating kan aktiveres på en given installation.
    2.  RollbackAllowed element gør det muligt at finde, når handlingen [Rollback opdatering eller opgradere](https://msdn.microsoft.com/library/azure/hh403977.aspx) kan kaldes for en given installation.

    For at udføre en rollback, har du ikke se både låst og RollbackAllowed elementerne. Det tilstrækkeligt for at bekræfte, at RollbackAllowed er indstillet til sand. Disse elementer, der kun returneres, hvis disse metoder kaldes ved hjælp af anmodning om headeren indstillet til "x-ms-version: 2011-10-01" eller en nyere version. Du kan finde flere oplysninger om versionsstyring sidehoveder, [Service Management versionsstyring](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Der er nogle situationer, hvor en rollback af en opdatering eller opgradering understøttes ikke, at dette er som følger:

-   Reduktion af lokale ressourcer - Hvis opdateringen øges de lokale ressourcer til en rolle Azure-platformen tillader ikke gå tilbage. 
-   Kvote begrænsninger - har Hvis opdateringen blev en skala ned handling, du kan ikke længere tilstrækkelige Beregn kvoter til at gennemføre handlingen rollback. Hver Azure abonnement har en kvote, der er knyttet til den, der angiver det maksimale antal kerner, som kan blive brugt i alle tilknyttede tjenester, der hører til dette abonnement. Hvis udføre en rollback af en bestemt opdatering, placerer dit abonnement over kvote derefter, der aktiveres en rollback ikke.
-   Kør betingelsen - Hvis den indledende opdatering er afsluttet, en rollback er ikke muligt.

Der er et eksempel på når annullering af en opdatering kan være praktisk, hvis du bruger handlingen [Opgradere installation](https://msdn.microsoft.com/library/azure/ee460793.aspx) i manuel tilstand til at styre den rente, hvormed hostet en større lokal opgradering til din Azure service er gennemført.

Under implementeringen af opgraderingen du ringe til [Opgradere installation](https://msdn.microsoft.com/library/azure/ee460793.aspx) i manuel tilstand og begynde at vejlede opgradering domæner. Hvis du er på et tidspunkt, som du overvåge opgraderingen, du være opmærksom på nogle forekomster af rolle i de første opgradering domæner, som du undersøge har reagerer, kan du ringe til handlingen [Rollback opdatering eller opgradere](https://msdn.microsoft.com/library/azure/hh403977.aspx) på den installation, hvilket vil efterlade uændret forekomster som ikke havde endnu er opgraderet og rollback forekomster som havde opgraderet til den forrige servicepakke og konfiguration af.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Start af flere mutating handlinger på en igangværende installation
I nogle tilfælde vil du måske at starte flere samtidige mutating handlinger på en igangværende installation. Du kan udføre en tjenesteopdatering til f.eks, mens opdateringen er rulles på tværs af din tjeneste, du vil foretage ændringer, f.eks. Hvis du vil vende tilbage til opdateringen, anvende en anden opdatering eller endda slette installationen. En sag, kan det være nødvendigt er, hvis en tjenesteopgraderingen indeholder buggy kode, der medfører, at en opgraderet rolle forekomst til flere gange ned. I dette tilfælde Azure-strukturen Controller ikke komme videre i anvendelse, der opgraderer, da en utilstrækkelig antallet af forekomster i opgraderede domænet er sund. Denne tilstand kaldes en *fast installation*. Du kan frigøre installationen ved at gå tilbage opdateringen eller anvende et nyt opdatering over toppen af fejlbehæftede en.

Når den oprindelige anmodning til at opdatere eller opgradere tjenesten har modtaget af Azure-strukturen Controller, kan du begynde efterfølgende mutation handlinger. Det vil sige, har du ikke at vente indledende handlingen er fuldført, før du kan starte en anden mutating handling.

Start af en anden opdateringshandling, mens den første opdatering foregår udfører ligner rollback-handling. Hvis den anden opdatering er i tilstanden med automatisk, opgraderes det første opgradering domæne med det samme, hvilket muligvis medfører forekomster fra flere opgradering domæner, der er offline på det samme sted i gang.

Handlingerne mutating er som følger: [Skift Deployment Configuration](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Opgradere installation](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Installation opdateringsstatus](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Slette installation](https://msdn.microsoft.com/library/azure/ee460815.aspx)og [Rollback opdatering eller opgradere](https://msdn.microsoft.com/library/azure/hh403977.aspx).

To handlinger, [Få installation](https://msdn.microsoft.com/library/azure/ee460804.aspx) og [Hent skyen Service egenskaber](https://msdn.microsoft.com/library/azure/ee460806.aspx), returnerer flaget låst, kan undersøges til at afgøre, om en handling af mutating kan aktiveres på en given installation.

For at ringe til den version af disse metoder, der returnerer flaget låst, skal du angive anmodning sidehoved til "x-ms-version: 2011-10-01" eller en nyere. Du kan finde flere oplysninger om versionsstyring sidehoveder, [Service Management versionsstyring](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Fordelingen af roller på tværs af opgradering domæner
Azure distribuerer forekomster af en rolle jævnt på tværs af et angivet antal opgradering domæner, der kan konfigureres som en del af service-definitionsfilen (.csdef). Det maksimale antal opgradering domæner er 20 og standarden er 5. Se [Azure Service Definition skema (.csdef filer)](cloud-services-model-and-package.md#csdef)du kan finde flere oplysninger om, hvordan du redigere filen service definition.

Eksempelvis hvis din rolle har ti forekomster, indeholder som standard hvert opgradering domæne to forekomster. Hvis din rolle har 14 forekomster, derefter fire ud af opgradering domænerne indeholder tre forekomster og et femte domæne indeholder to.

Opgradering domæner er identificeret med et baseret på nul indeks: det første opgradering domæne har et ID på 0, og det andet opgradering domæne har et ID 1, og så videre.

I følgende diagram vises, hvordan en tjeneste indeholder to roller fordeles, når tjenesten definerer to opgradering domæner. Tjenesten kører otte forekomster af rollen web og ni forekomster af rollen arbejder.

![Fordelingen af opgradering domæner] (media/cloud-services-update-azure-service/IC345533.png "Fordelingen af opgradering domæner")

> [AZURE.NOTE] Bemærk, at Azure styrer, hvordan forekomster fordeles på tværs af opgradering domæner. Det er ikke muligt at angive, hvilke forekomster er allokeret til hvilket domæne.

## <a name="next-steps"></a>Næste trin
[Sådan administreres Cloud Services](cloud-services-how-to-manage.md)  
[Hvordan du kan overvåge Cloud Services](cloud-services-how-to-monitor.md)  
[Sådan konfigureres Cloud Services](cloud-services-how-to-configure.md)  
