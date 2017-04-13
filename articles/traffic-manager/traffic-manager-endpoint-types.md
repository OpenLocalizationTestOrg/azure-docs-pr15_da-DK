<properties
    pageTitle="Trafik Manager slutpunkt typer | Microsoft Azure"
    description="I denne artikel forklares forskellige typer slutpunkter, der kan bruges med Azure trafik Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-endpoints"></a>Trafik Manager slutpunkter

Microsoft Azure trafik Manager kan du kontrollere, hvordan netværkstrafik distribueres til programmet installationer, der kører i forskellige datacentre. Du konfigurerer hver programmet Installation som et slutpunkt i trafik Manager. Når trafik Manager modtager en DNS-anmodning, vælger en tilgængelig slutpunkt, der skal returneres i svaret DNS. Trafik manager baserer valget på den aktuelle status slutpunkt og metoden trafik-routing. Du kan finde yderligere oplysninger finder [Sådan trafik Manager fungerer](traffic-manager-how-traffic-manager-works.md).

Der findes tre typer slutpunkt, der understøttes af trafik Manager:

- **Azure slutpunkter** bruges til tjenester, der findes i Azure.
- **Eksterne slutpunkter** bruges til tjenester, der er hostet uden for Azure, enten lokalt eller hos en anden udbyder.
- **Indlejrede slutpunkter** bruges til at kombinere trafik Manager profiler for at oprette mere fleksible trafik-routing farveskemaer til at understøtte af større og mere komplekse installationer.

Der er ingen begrænsninger for hvordan slutpunkterne for forskellige typer kombineres i en enkelt trafik Manager profil. Hver profil kan indeholde en blanding af slutpunkt typer.

I følgende afsnit beskrives hver slutpunkt type i større dybde.

## <a name="azure-endpoints"></a>Azure slutpunkter

Azure slutpunkter bruges til Azure-baserede tjenester i trafik Manager. Følgende Azure ressourcetyper understøttes:

- 'Klassisk' IaaS FOS og PaaS cloud services.
- Webapps
- PublicIPAddress ressourcer (som kan være forbundet med FOS enten direkte eller via en Azure justering af belastning). PublicIpAddress skal have et DNS-navn, der er tildelt til bruges i en trafik Manager profil.

PublicIPAddress ressourcer er Azure ressourcestyring ressourcer. De findes ikke i modellen Klassisk installation. Derfor er de eneste understøttede i trafik chefens Azure ressourcestyring oplevelser. De andre slutpunkt typer understøttes via både ressourcestyring og klassisk implementeringsmodel.

Når du bruger Azure slutpunkter, registrerer trafik Manager, når en 'Klassisk' IaaS VM, skybaseret tjeneste eller en WebApp afbrydes og startes. Denne status vises i slutpunkt status. Se [trafik Manager slutpunkt overvåge](traffic-manager-monitoring.md#endpoint-and-profile-status) få mere at vide. Når den underliggende tjeneste er stoppet, udfører trafik Manager ikke slutpunkt tilstandskontrol eller direkte trafik til slutpunktet. Ingen trafik Manager fakturering hændelser indtræffer for det holdt op med at forekomst. Når tjenesten genstartes, er fakturering cv'er og slutpunktet berettiget til at modtage trafik. Denne registrering gælder ikke for PublicIpAddress slutpunkter.

## <a name="external-endpoints"></a>Eksterne slutpunkter

Eksterne slutpunkter bruges til tjenester uden for Azure. For eksempel en tjeneste hostet lokalt eller med en anden udbyder. Eksterne slutpunkter kan anvendes enkeltvis eller kombineret med Azure slutpunkter i den samme trafik Manager profil. Kombinerer Azure slutpunkter med eksterne slutpunkter aktiverer forskellige scenarier:

- Brug Azure skal være øget redundans til et eksisterende lokalt program i enten en aktiv-aktiv eller aktive / passive failover model.
- Du kan reducere programmet ventetid for brugere over hele verden ved at udvide et eksisterende lokalt program til yderligere geografiske placeringer i Azure. Finde flere oplysninger, [trafik Manager 'Ydeevne' trafik routing](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Brug Azure til at levere yderligere kapacitet til en eksisterende lokale-program, enten hele tiden eller som en 'burst i skyen' løsning til at opfylde en samling i efter behov.

I visse tilfælde er det nyttigt at bruge eksterne slutpunkter til at referere til Azure-tjenester (eksempler, se [ofte stillede spørgsmål om](#faq)). I dette tilfælde kan faktureres tilstandskontrol med Azure slutpunkter hastighed, ikke den eksterne slutpunkter rente. Dog i modsætning til Azure slutpunkter, hvis du stopper eller slette tjenesten underliggende sundhed kontrollere fakturering fortsætter, indtil du deaktiverer eller slette slutpunkt i trafik Manager.

## <a name="nested-endpoints"></a>Indlejrede slutpunkter

Indlejrede slutpunkter kombinere flere trafik Manager profiler for at oprette fleksible trafik-routing farveskemaer og support af større komplekse installationer. Med indlejrede slutpunkter føjes en 'underordnet' profil som et slutpunkt til en 'overordnet' profil. Både underordnede og overordnede brugerprofiler kan indeholde andre slutpunkter af enhver type, herunder andre indlejrede profiler. Du kan finde yderligere oplysninger finder [indlejrede trafik Manager profiler](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps som slutpunkter

Nogle yderligere overvejelser gælder, når du konfigurerer Web Apps som slutpunkter i trafik Manager:

1. Kun Web Apps på den 'Standard' SKU eller derover er berettiget til brug med trafik Manager. Forsøg på at tilføje en Web App på en lavere SKU mislykkedes. Nedgradere SKU af en eksisterende Web App, resulterer i trafik Manager ikke længere sende trafik til den Web App.

2. Når et slutpunkt modtager en HTTP-anmodning, bruger den 'host' sidehovedet i anmodningen til at afgøre, hvilket WebApp skal behandle anmodningen. Overskriften host indeholder DNS-navn, der bruges til at aktivere anmodningen, for eksempel 'contosoapp.azurewebsites.net'. Hvis du vil bruge et andet DNS-navn til din online, skal være registreret DNS-navnet som et brugerdefineret domænenavn til Appen. Når du tilføjer et online slutpunkt som et Azure slutpunkt, registreres automatisk trafik Manager DNS profilnavnet for Appen. Denne registrering fjernes automatisk, når slutpunktet slettes.

3. Hver trafik Manager profil kan have mindst én online slutpunkt fra hver Azure område. Du kan løse for denne begrænsning, kan du konfigurere en Web-App som en eksterne slutpunkt. Yderligere oplysninger finder du se [ofte stillede spørgsmål](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Aktivering og deaktivering af slutpunkter

Deaktivere et slutpunkt i trafik Manager kan være praktisk at fjerne midlertidigt trafik fra et slutpunkt, der er i vedligeholdelsestilstand, eller der genaktiveres. Når slutpunktet kører igen, kan det være aktiveret igen.

Slutpunkter kan aktiveres og deaktiveres via portalen trafik Manager, PowerShell, CLI eller REST-API, som alle understøttes i både ressourcestyring og klassisk implementeringsmodel.

>[AZURE.NOTE] Deaktivere et Azure slutpunkt har ingen betydning for med dens installation tilstand i Azure. En Azure service (såsom en VM eller Web App forbliver kører og kunne modtage trafik selvom deaktiveret i trafik Manager. Trafik kan behandles direkte til forekomsten af i stedet for via trafik Manager DNS profilnavnet. Du kan finde flere oplysninger, se, [hvordan trafik Manager fungerer](traffic-manager-how-traffic-manager-works.md).

Den aktuelle berettigelse af hvert slutpunkt til at modtage trafik afhænger af følgende faktorer:

- Profil status (aktiveret/deaktiveret)
- Slutpunkt status (aktiveret/deaktiveret)
- Resultaterne af tilstanden kontrollerer for det pågældende slutpunkt

Yderligere oplysninger finder du [trafik Manager slutpunkt overvågning](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Da trafik Manager fungerer på niveauet for DNS, er det ikke kan påvirke eksisterende forbindelser til en hvilken som helst slutpunkt. Når et slutpunkt er tilgængeligt, dirigerer trafik Manager nye forbindelser til en anden tilgængelige slutpunkt. Host bag deaktiveret eller beskadiget slutpunktet kan dog fortsat modtage trafik via eksisterende forbindelser, indtil disse sessioner er afsluttet. Programmer skal begrænse session varigheden for at tillade trafik løbe ud fra eksisterende forbindelser.

Hvis alle slutpunkter i en profil er deaktiveret, eller hvis selve profilen er deaktiveret, sender trafik Manager et 'NXDOMAIN' svar til en ny DNS-forespørgsel.

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan jeg bruge trafik Manager med slutpunkter fra flere abonnementer?

Brug af slutpunkter fra flere abonnementer er ikke muligt med Azure Web Apps. Azure Onlines kræver, at et brugerdefineret domænenavn bruges sammen med Web Apps bruges kun i et enkelt abonnement. Det er ikke muligt at bruge online fra flere abonnementer med navnet på det samme domæne.

Det er muligt at bruge trafik Manager med slutpunkter fra mere end ét abonnement til andre typer af slutpunkt. Hvordan du konfigurerer trafik Manager, afhænger af, om du bruger den klassiske implementeringsmodel eller Ressourcestyring oplevelse.

- I ressourcestyring slutpunkter fra et abonnement du kan føje til trafik Manager, så længe den person, der er konfigurationen af trafik Manager profilen har læseadgang til slutpunktet. Disse tilladelser kan tildeles ved hjælp af [Azure ressourcestyring rollebaseret adgangskontrol (RBAC)](../active-directory/role-based-access-control-configure.md).
- Trafik Manager kræver, at Cloud Services eller Web Apps, der er konfigureret som Azure slutpunkter findes i det samme abonnement som trafik Manager profil i grænsefladen klassisk installation model. Skybaseret tjeneste slutpunkter i andre abonnementer kan tilføjes til trafik Manager som 'eksterne' slutpunkter. Disse eksterne slutpunkter faktureres som Azure slutpunkter i stedet for den eksterne rente.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan jeg bruge trafik Manager med skybaseret tjeneste 'Arrangere' pladser?

Ja. Skybaseret tjeneste 'arrangere' pladser kan konfigureres i trafik Manager som eksterne slutpunkter. Tilstandskontrol er stadig betale med Azure slutpunkter hastighed. Da den eksterne slutpunkt type er i brug, hentes ændringer til tjenesten underliggende ikke automatisk. Trafik Manager kan ikke registrerer med eksterne slutpunkter, når Skytjenesten er ikke længere eller slettes. Derfor fortsætter trafik Manager fakturering for tilstandskontrol, indtil slutpunktet er deaktiveret eller slettet.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Understøtter trafik Manager IPv6 slutpunkter?

Trafik Manager giver ikke i øjeblikket IPv6-addressible navneservere. Trafik Manager kan stadig bruges af IPv6-klienter opretter forbindelse til IPv6-slutpunkter. En klient gør ikke DNS-anmodninger direkte til trafik-Manager. I stedet bruger klienten en rekursiv DNS-tjeneste. En kun IPv6-klient sender anmodninger til rekursiv DNS-tjenesten via IPv6. Derefter skal tjenesten rekursiv være kan kontakte navneservere trafik Manager, ved hjælp af IPv4.

Trafik Manager svarer med DNS-navnet på slutpunktet. For at understøtter IPv6-ark, der skal være en DNS AAAA post, der peger slutpunkt DNS-navnet til IPv6-adresse. Trafik Manager tilstandskontrol understøtter kun IPv4-adresser. Tjenesten skal vise et IPv4 slutpunkt på det samme DNS-navn.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan jeg bruge trafik Manager med mere end én Web App i det samme område?

Trafik Manager bruges typisk til at dirigere trafik til programmer, der er implementeret i forskellige områder. Men det kan også bruges hvor et program har mere end én installation i det samme område. Trafik Manager Azure slutpunkterne tillader ikke mere end én Web App-slutpunkt fra den samme Azure område, der skal føjes til den samme trafik Manager profil.

Følgende trin indeholder en løsning til dette betingelse:

1. Kontrollér, at slutpunkterne er i forskellige web-app 'skalaenheder'. Et domænenavn skal knyttes til et enkelt websted i en given skala enhed. Derfor kan ikke to Web Apps i den samme enhed for tidsskala dele en trafik Manager profil.
2. Føje dit domænenavn vanity som en brugerdefineret hostname til hver Web App. Hver Web App skal være i en anden skala enhed. Alle Onlines skal tilhøre samme abonnement.
3. Tilføje én (og kun én) Web App-slutpunkt til din trafik Manager profil, som et Azure slutpunkt.
4. Tilføje hvert yderligere Web App-slutpunkt til din profil trafik Manager som en eksterne slutpunkt. Eksterne slutpunkter kan kun tilføjes ved hjælp af Ressourcestyring implementeringsmodel.
5. Oprette en DNS CNAME-post i domænet vanity, der peger på dine trafik Manager profilnavn DNS (<> …. trafficmanager.net).
6. Få adgang til webstedet via vanity domain name skal ikke trafik Manager DNS profilnavnet.

## <a name="next-steps"></a>Næste trin

- Få mere at vide, [hvordan trafik Manager fungerer](traffic-manager-how-traffic-manager-works.md).
- Få mere at vide om trafik Manager [slutpunkt overvågning og automatisk failover](traffic-manager-monitoring.md).
- Få mere at vide om trafik Manager [trafik routing metoder](traffic-manager-routing-methods.md).
