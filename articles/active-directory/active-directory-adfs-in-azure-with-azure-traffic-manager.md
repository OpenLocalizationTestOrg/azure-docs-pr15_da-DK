<properties
    pageTitle="Høj tilgængelighed kryds-geografiske AD FS installation i Azure med Azure trafik Manager | Microsoft Azure"
    description="I dette dokument lærer du, hvordan du kan installere AD FS i Azure for høj tilgængelighed."
    keywords="Ad fs med Azure trafik manager, adfs med Azure trafik Manager geografiske, flere datacenter, geografiske datacentre, flere geografiske datacentre, installere AD FS i azure, implementere azure adfs, azure adfs, azure ad fs, installere adfs, installere ad fs adfs i azure, installere adfs i azure, installere AD FS i azure, adfs azure, Introduktion til AD FS, Azure, AD FS i Azure, iaas , ADFS, flytte adfs til azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Høj tilgængelighed kryds-geografiske AD FS installation i Azure med Azure trafik Manager

[AD FS-installation i Azure](active-directory-aadconnect-azure-adfs.md) indeholder en trinvis vejledning om, hvordan du kan implementere en enkel AD FS-infrastruktur for organisationen i Azure. Denne artikel indeholder de næste trin for at oprette en kryds-geografiske installation af AD FS i Azure ved hjælp af [Azure trafik Manager](../traffic-manager/traffic-manager-overview.md). Azure trafik Manager hjælper med at oprette en geografisk opslag høj tilgængelighed og høj ydeevne AD FS infrastruktur for din organisation ved at gøre brug af celleområde routing metoder til forskellige behov fra infrastrukturen.

En meget tilgængelige kryds-geografiske AD FS-infrastruktur kan:

* **Fjernelse af ét afsnit:** Med failoveregenskaber af Azure trafik Manager, kan du opnå en meget tilgængelige AD FS-infrastruktur, selv når en af datacentre i en del af globussen går ned
* **Forbedret ydeevne:** Du kan bruge den foreslåede installation i denne artikel for at give en høj ydeevne AD FS-infrastruktur, der kan hjælpe brugerne med godkende hurtigere. 

##<a name="design-principles"></a>Designprincipper

![Overordnede design](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Principperne for grundlæggende design bliver samme, som vises i designprincipper i artiklen AD FS-installation i Azure. Diagrammet ovenfor viser en enkel udvidelse af den grundlæggende installation til en anden geografisk område. Nedenfor finder du nogle punkter du bør overveje, når du udvider installationen til nye geografisk område

* **Virtuelle netværk:** Du skal oprette et nyt virtuelt netværk i et geografisk område, du vil installere ekstra infrastruktur til AD FS. I diagrammet ovenfor du kan se Geo1 VNET og Geo2 VNET som de to virtuelle netværk i hver geografisk område.

* **Domænecontrollere og AD FS-servere i nye geografiske VNET:** Det anbefales at installere domæne enheder i et nyt geografisk område, så AD FS-servere i det nye område ikke behøver at kontakte et domænenavn fra domænecontrolleren i en anden langt væk netværk for at afslutte en godkendelse og dermed forbedre ydeevnen.

* **Storage konti:** Lagerplads konti er knyttet til et område. Da du anvender computere i et nyt geografiske område, er du nødt til at oprette nye lagerplads konti der skal bruges i området.  

* **Netværk sikkerhedsgrupper:** Som netværk sikkerhedsgrupper, der er oprettet i et område-lagerplads konti ikke kan bruges i en anden geografisk område. Du skal derfor til at oprette nye netværk sikkerhedsgrupper ligner dem i den første geografisk område for HEL og DMZ undernet i et nyt geografisk område.

* **DNS etiketterne til offentlige IP-adresser:** Azure trafik Manager kan referere til slutpunkterne kun via DNS etiketter. Derfor, du skal oprette DNS-etiketter til den eksterne Indlæs balancere offentlige IP-adresser.

* **Azure trafik Manager:** Microsoft Azure trafik Manager kan du kontrollere fordelingen af brugeren trafik til tjenesten slutpunkterne kører i forskellige datacentre over hele verden. Azure trafik Manager fungerer på niveauet for DNS. DNS-svar bruges til at dirigere slutbrugerlicensaftale trafik til globalt distribueret slutpunkter. Klienter derefter opretter forbindelse til disse slutpunkter direkte. Med forskellige indstillinger for routing af ydeevne, vægtet og prioritet, kan du nemt vælge indstillingen routing, der passer bedst til organisationens behov. 

* **V redskaber til V-redskaber forbindelsen mellem to områder:** Du behøver ikke at have forbindelsen mellem de virtuelle netværk sig selv. Da hver virtuelt netværk har adgang til domænecontrollere og har AD FS og WAP server i sig selv, kan de arbejde uden en forbindelse mellem de virtuelle netværk i forskellige områder. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Trin til at integrere Azure trafik Manager

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Installere AD FS i et nyt geografisk område
Følg de trin og retningslinjer i [AD FS-installation i Azure](active-directory-aadconnect-azure-adfs.md) til at installere den samme topologi i et nyt geografisk område.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>DNS-navne til offentlige IP-adresser for Belastningskontroller Internet modstående (offentlig)
Som nævnt ovenfor, Azure trafik Manager kun kan referere til DNS-navne som slutpunkter og det er derfor vigtigt til at oprette DNS-etiketter for den eksterne Indlæs balancere offentlige IP-adresser. Under skærmbillede viser, hvordan du kan konfigurere dine DNS-navn til den offentlige IP-adresse. 

![DNS-etiket](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Implementere Azure trafik Manager

Følg nedenstående trin for at oprette en trafik manager profil. Du kan finde flere oplysninger, kan du også se til at [administrere en Azure trafik Manager profil](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Oprette en trafik Manager profil:** Giv din trafik manager profil et entydigt navn. Denne navnet på profilen, der er en del af DNS-navnet og fungerer som et præfiks for etiketten trafik Manager domæne navn. Navnet / præfiks føjes til. trafficmanager.net til at oprette en DNS-etiket til din trafik manager. Skærmbilledet nedenfor viser trafik manager DNS præfiks der angive som mysts og resulterende DNS etiket vil være mysts.trafficmanager.net. 

    ![Oprettelse af trafik Manager profil](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **Trafik routing metode:** Der findes tre routing indstillinger i trafik manager:

    * Prioritet 
    * Ydeevne
    * Vægtet
    
    **Ydeevnen** er anbefalede mulighed for at opnå meget svarede AD FS-infrastruktur. Dog kan du vælge en routing metode, der passer bedst til dine installationer. Funktionen AD FS påvirkes ikke af indstillingen routing markeret. Du kan få flere oplysninger i [trafik Manager trafik routing metoder](../traffic-manager/traffic-manager-routing-methods.md) . Skærmbillede, der kan se den valgte **ydeevne** metode i stikprøven.
   
3.  **Konfigurere slutpunkter:** Klik på slutpunkter trafik manager på siden, og vælg Tilføj. Dette åbner en Tilføj slutpunkt side minder om skærmbilledet herunder
 
    ![Konfigurere slutpunkter](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    De forskellige input, skal du benytte den aftale nedenfor:

    **Type:** Vælg Azure slutpunkt, som vi vil pege på en Azure offentlige IP-adresse.

    **Navn:** Oprette et navn, du vil knytte til slutpunktet. Dette er ikke DNS-navn og har ingen indflydelse på DNS-poster.

    **Målrette ressourcetype:** Vælg offentlige IP-adresse, som værdien, denne egenskab. 

    **Fokuserer på ressource:** Dette giver dig mulighed for at vælge mellem forskellige DNS etiketter, der er tilgængelige under dit abonnement. Vælg DNS-navn til.

    Tilføj slutpunkt for hver geografisk område, du vil Azure trafik Manager til at dirigere trafik til den.
    Flere oplysninger og detaljerede trin til at tilføje / konfigurere slutpunkter i trafik manager referere til at [tilføje, deaktivere, aktivere eller slette slutpunkter](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Konfigurer efterprøvning af af:** Klik på konfiguration på siden trafik manager. På konfigurationssiden vil du ændre skærmindstillinger for at sende forespørgsler på HTTP-port 80 og relativ sti /adfs/probe

    ![Konfigurere efterprøvning af af](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Sikre, at status for slutpunkterne er ONLINE, når konfigurationen er fuldført**. Hvis alle slutpunkter er i 'nedgraderede' tilstand, gøre Azure trafik Manager et bedste forsøg på at omdirigere den trafik Hvis, diagnosticering er forkert, og alle slutpunkter kan nås.

5. **Ændring af DNS-poster til Azure trafik Manager:** Tjenesten sammenslutning skal være en CNAME til Azure trafik Manager DNS-navn. Oprette en CNAME i offentlige DNS-posterne, så den person forsøger at få adgang til tjenesten sammenslutning faktisk når Azure trafik Manager.

    For eksempel punkt sammenslutning service fs.fabidentity.com til trafik Manager, du har brug at opdatere din DNS-ressource-post for at være følgende:

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Teste ruten og AD FS-logon   

###<a name="routing-test"></a>Routing test

En meget grundlæggende test for ruten ville være at prøve ping sammenslutning tjeneste DNS-navnet fra en maskine i hvert geografisk område. Afhængigt af den valgte routing metode, afspejles den faktisk pinger slutpunktet i visningen ping. Eksempelvis hvis du har valgt ydeevne ruten, kontaktes slutpunktet nærmest området af klienten-metoden. Nedenfor finder du øjebliksbillede af to pinger fra to forskellige område klientcomputere: én i EastAsia område og én i Vest USA. 

![Routing test](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS-logon test

Den nemmeste måde at teste AD FS er ved hjælp af siden IdpInitiatedSignon.aspx. For at kunne gøre, at det er påkrævet for at aktivere IdpInitiatedSignOn AD FS-egenskaber. Følg nedenstående trin for at bekræfte din AD FS-konfiguration
 
1. Køre den under cmdlet på AD FS-server, ved hjælp af PowerShell, aktiveret angivet det til. Sæt AdfsProperties - EnableIdPInitiatedSignonPage $true
2. Fra en ekstern maskine access https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Skal du se afsnittet siden AD FS som nedenfor:

    ![Test ADFS - godkendelse udfordring](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    og på vellykket logon, får du med en meddelelse som vist nedenfor:

    ![Test ADFS - godkendelse succes](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Relaterede links
* [Grundlæggende AD FS-installation i Azure](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure trafik Manager](../traffic-manager/traffic-manager-overview.md)
* [Trafik Manager trafik routing metoder](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Næste trin
* [Administrere en Azure trafik Manager-profil](../traffic-manager/traffic-manager-manage-profiles.md)
* [Tilføje, deaktivere, aktivere eller slette slutpunkter](../traffic-manager/traffic-manager-endpoints.md) 

