
<properties 
    pageTitle="Sikring af adgang til Azure RemoteApp og derover | Microsoft Azure"
    description="Få mere at vide hvordan sikker adgang til Azure RemoteApp ved hjælp af betinget adgang i Azure Active Directory"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Sikring af adgang til Azure RemoteApp og derover

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

I denne artikel giver vi en oversigt over, hvordan en administrator kan konfigurere en sikker adgang kanal starter fra slutbrugerens via Azure RemoteApp og slutter med en sikker ressource som en SQL-database eller et andet program back end. Målet er at sikre, at kun autoriserede brugere, der opfylder de ønskede betingelser kan få adgang til remote-programmer, og, at den secure back-end kun kan åbnes fra kontrolleret Azure RemoteApp miljø og ikke fra andre placeringer.

Der er 3 væsentlige områder administratoren skal se på:

![Azure RemoteApp betinget access overvejelser](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Læs på oplysninger og svarene på disse spørgsmål.

## <a name="who-can-access-the-collection"></a>Hvem kan få adgang til gruppen af websteder?
Administratoren vælger de brugere, der har adgang til remote programmer i gruppen af websteder. Du kan bruge Azure Active Directory (Azure AD) arbejds- eller skolekonti (tidligere kaldet, "organisationskonti") eller Microsoft-konti (fx @outlook.com). De fleste enterprise scenarier Brug Azure AD-konti de kan du bruge betinget access funktioner gennemgås senere, og der også er det kun valg til medlem af et domæne af websteder. I resten af artiklen antages, du bruger Azure AD-konti med Azure RemoteApp.

**Vi har nået:**

Brug af Azure AD-konti til at styre adgangen til Azure RemoteApp giver os to ting:

1.  Vi altid ved, hvem der kan få adgang til de programmer, vi har udgivet og få adgang til alle tilbage ender disse programmer oprette forbindelse til.
2.  Vi kan kontrollere den underliggende Azure AD, så vi kan oprette og slette brugerkonti, angive adgangskodepolitikker, bruge Multi-Factor authentication osv. 

## <a name="how-is-the-collection-accessed-from-where"></a>Sådan åbnes samlingen? Hvorfra?
Administratorer vil ofte definere politikker for at få adgang til et offentligt internettet-miljø, som Azure RemoteApp. Eksempelvis vil de sikre dig, at brugere, der bruger miljøet fra uden for virksomhedens netværk skal bruge Multi-Factor authentication (MFA) til at få adgang; eller måske de skal blokeres helt.

Azure RemoteApp-administratorer kan bruge de funktioner, der er tilgængelige via Azure AD Premium indstille betinget access politikker for deres Azure RemoteApp-miljø. De kan også bruge omfattende rapportering og advarer funktioner til at overvåge, hvor der er adgang til miljøet.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Hvordan du konfigurerer betinget adgang til Azure RemoteApp
Vi vil gennemgå en eksempel scenario – den Azure RemoteApp administrator ønsker at blokere adgang til miljøet, når brugerne er uden for virksomhedens netværk.

>[AZURE.NOTE] Vi antager, at du har opgraderet Azure AD til Premium-niveau og at du har oprettet mindst én Azure RemoteApp af websteder.

1.  Klik på fanen **Active Directory** Azure-portalen. Klik derefter på den mappe, du vil konfigurere.

    Husk: Betinget access er en egenskab af mappen og ikke af Azure RemoteApp, så alle konfigurationen er fuldført på mappeniveau. Det betyder også skal du være administrator for directory at foretage ændringerne.

2.  Klik på **programmer**, og klik derefter på **Microsoft Azure RemoteApp** oprette betinget adgang til. Bemærk, at du kan konfigurere betinget adgang til hvert program, "software som en tjeneste" i biblioteket separat.
![Konfiguration af betinget adgang til Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  **Konfigurer** under fanen Indstil **Aktivere adgangsregler** til til.
![Aktivere adgangsregler til Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  Du kan nu konfigurere forskellige regler og vælge, hvem der skal anvende dem til at:

    1. Vælg **Bloker adgang, når de ikke er på arbejde** til helt at forhindre brugere i at få adgang til Azure RemoteApp uden for det netværksmiljø, du angiver.
    2. Klik på indstillingen nedenfor til at definere de IP-adresseområder, der udgør "der er tillid til netværket". Alt uden for dem, der vil blive afvist.

5.  Test konfigurationen ved at starte Azure RemoteApp klienten fra en IP-adresse uden for det område, du har angivet. Når du logger på med legitimationsoplysningerne Azure AD skal du se en meddelelse som denne:

![Nægte adgang til Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Fremtidige betinget access-funktioner 
Azure Active Directory-teamet arbejder på nye funktioner i betinget adgang. Administratorer vil kunne oprette nye typer regler uden netværk placeringen af basis regler. En prøveversionen af de nye funktioner skal snart være tilgængelige.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Hvordan du kan overvåge adgang til Azure RemoteApp
Et godt funktion skal bruges sammen med betinget access er Azure Active Directory Premium rapporteringsfunktioner. Du kan bruge rapporter til skærm, hvem der har adgang til dit miljø og registrerer en mistænkelig aktivitet.

For eksempel kan du se navnene på de brugere, der åbnes Azure RemoteApp, hvor mange gange det er gjort og hvornår.

1.  Klik på **Active Directory**Azure-portalen, og klik derefter på adresselisten.

2.  Gå til fanen **rapporter** .

3.  Vælg **Brug af program** under **integrerede programmer**på listen over rapporter.

    Du får vist nogle aggregeret statistik for Azure RemoteApp. 
![Sammenlagt Azure RemoteApp access statistik](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Klik på program for at få vist oplysninger om brugere adgang til Azure RemoteApp.
![Bruger access statistik for Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Oversigt
Du kan konfigurere adgangsregler til Azure RemoteApp (og andre programmer som en tilgængelig via Azure AD-tjenesteprogrammer) med Azure Active Directory Premium. Regler er i øjeblikket er begrænset til politikker for placeringen af basis netværk, men i fremtiden udvides til andre aspekter af enterprise management.

Azure AD-Premium tilbyder også rapportering og overvågning egenskaber, som yderligere udvide kontrolelementet administratoren har over deres Azure RemoteApp-miljø.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>Hvordan jeg sikre, at min sikker ressource er kun tilgængelig fra mit Azure RemoteApp-miljø?
I forrige afsnit i denne artikel fokuserer vi på sikring af adgang til Azure RemoteApp-miljø. Vi har nået, ved at vælge de brugere, der har adgang og konfiguration af adgangsregler for at kontrollere yderligere hvordan de kan bruge tjenesten.

Et almindelige scenarie til Azure RemoteApp installationer er, at de eksterne programmer skal til at kommunikere med en back end-ressource, for eksempel en SQL-database. Denne ressource er hostet enten lokalt (fx på en virksomheds netværk) eller i skyen (fx i Azure IaaS). Administratorer vil ofte sikre dig, at back end-ressourcen kun kan åbnes af installeres via Azure RemoteApp-programmer og ikke for eksempel af et program, der kører direkte på en brugers PC og få adgang til offentlige internettet. Azure RemoteApp er ofte set som det centralt administrerede og sikkert miljø og dermed kun stien, som brugerne skal interagere med back end-ressourcen.

Løsningen er at placere både Azure RemoteApp-miljø og sikker ressourcen i den samme Azure virtuelle netværk (VNET). Hvis ressourcen, der er i et andet websted, kan du oprette et websted til websted VPN-forbindelse, for eksempel for at oprette en VNet, der strækker sig over datacenteret Azure og lokale kundemiljø.

Azure RemoteApp understøtter to typer af websteder-installationer, hvor du kan angive dine egne VNET:

-   Ikke-medlem af et domæne: programmerne har "linje af synlige" af de andre ressourcer i VNET. For eksempel dette kan bruges til at oprette forbindelse programmer til en SQL-database, der bruger SQL-godkendelse (programmer godkende brugeren direkte mod databasen)

-   Medlem af et domæne: de virtuelle maskiner, der bruges af Azure RemoteApp er tilsluttet et domænenavn fra domænecontrolleren i VNET. Dette er nyttigt, når programmerne, der skal godkende mod en Windows-domænecontrolleren for at få adgang til en back end-ressource.
![En samling medlem af et domæne i Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Sådan oprettes en sikker forbindelse mellem Azure og min lokale miljø
Der findes flere konfigurationsindstillinger til at oprette forbindelse til din Azure og lokale miljøer. En god oversigt over indstillingerne findes her.

Med Azure RemoteApp skal du konfigurere din VNet først og derefter bruge den under oprettelsen af af websteder. 

## <a name="the-complete-solution"></a>Komplet løsning
Diagrammet nedenfor viser den komplette løsning, hvor vi har opbygget en sikker adgang kanal fra slutbrugerens, via Azure RemoteApp (ARA), i back end-ressource.
![Secure Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) i trin 1 vi markeret brugerne og oprettet adgangsregler, som styrer, hvordan ARA kan åbnes. I eksemplet herunder tillader vi kun adgang til brugere, der arbejder fra virksomhedens netværk. Ikke-kompatibel brugere kan ikke få adgang til ARA miljøet overhovedet.
Vi har vises back end-ressourcen kun via VNet/VPN konfigurationen som vi styre i "Fase 2". Azure RemoteApp er placeret i den samme VNet. Afslut resultatet er, at ressourcen, der er kun tilgængeligt via ARA miljøet.


