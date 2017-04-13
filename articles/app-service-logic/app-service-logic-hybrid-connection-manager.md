<properties 
    pageTitle="Ved hjælp af Forbindelsesstyring Hybrid | Microsoft Azure" 
    description="Installere og konfigurere Forbindelsesstyring hybride og oprette forbindelse til lokale forbindelser i logik Apps" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Oprette forbindelse til lokale forbindelser med Forbindelsesstyring Hybrid

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen. Logik Apps generelt tilgængelig (GA) bruger en gateway for lokale forbindelse. Få mere at vide om ny [gateway](app-service-logic-gateway-connection.md) og [Logik Apps GA](https://azure.microsoft.com/documentation/services/logic-apps/).

Hvis du vil bruge et lokalt system, bruger logik Apps Forbindelsesstyring Hybrid. Nogle forbindelser kan oprette forbindelse til et lokalt system, som SQL Server, SAP, SharePoint og så videre. 

Hybrid forbindelse Manager (HCM) er et enkelt klik-én gang installationsprogram, der er installeret på en IIS-server i dit netværk, bag firewallen. Ved hjælp af en Azure Service Bus relay godkender HCM det lokale system med forbindelsen i Azure. 

> [AZURE.NOTE] Hybrid Forbindelsesstyring er påkrævet, kun, hvis du opretter forbindelse til en lokal ressource bag firewallen. Hvis du ikke opretter forbindelse til et lokalt system, skal ikke du Forbindelsesstyring Hybrid.

For at komme i gang skal bruge du:

- Azure Service Bus relay navneområde SAS forbindelsesstreng. Se [Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/) til at bestemme, hvilke niveau omfatter relæer.
- Lokalt system logon oplysninger, herunder brugernavn og din adgangskode. Hvis du opretter forbindelse til en lokal SQL Server, skal du eksempelvis den SQL Server logon-konto og adgangskode.
- Lokal serveroplysninger, herunder port tal og servernavn. Hvis du opretter forbindelse til en lokal SQL Server, skal du eksempelvis SQL Server-navn og TCP-portnummer.

## <a name="get-the-service-bus-connection-string"></a>Få vist Service Bus forbindelsesstreng

Kopiere Service Bus roden SAS forbindelsesstreng i Azure-portalen. Den Azure forbindelse forbindelsesstrengen opretter forbindelse til dit lokale system. 

1. Vælg din Service Bus navneområde i [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885), og vælg **Forbindelsesoplysninger**:

    ![][SB_ConnectInfo]

2. Kopiér forbindelsesstrengen SAS:

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Installere Forbindelsesstyring Hybrid

1. Vælg den forbindelse, du har oprettet på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). For at åbne den, kan du vælge **Gennemse**, Vælg **API Apps**og derefter vælge din forbindelse eller API App. 
<br/><br/>
Konfigurationen er **ufuldstændig**i **Hybride forbindelsen**:
<br/>
![][2] 

2. Vælg **Hybrid forbindelse**. Forbindelsesstrengen Service Bus, du tidligere har angivet vises.
3. Kopiér den **primære konfiguration streng**:
<br/>
![][PrimaryConfigString]

4. Du kan hente Hybrid forbindelse Manager eller installere det direkte fra portalen under **Lokale Hybrid Forbindelsesstyring**. 
<br/><br/>
Gå til din lokale IIS-server, gå til portalen, og vælg **Download og Konfigurer**for at installere direkte fra portalen.
<br/><br/>
Hvis du vil hente Forbindelsesstyring Hybrid, gå til din lokale IIS-server, og gå til **ClickOnce program** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). Installationen starter automatisk, så du kan køre programmet.

5. Skriv den **Primære konfiguration streng** , du tidligere har indsat (i trin 3) i vinduet **Opsætning af lytteren** og vælge **Installer**.

Når installationen er fuldført, vises følgende:
<br/>
![][3] 

Når du navigerer til forbindelsen igen, er hybrid forbindelsesstatus nu **tilsluttet**. Du kan være nødvendigt at lukke connector og åbner det igen:
<br/>
![][4] 

> [AZURE.NOTE] Kør konfigurationen af hybride forbindelsen igen for at skifte til den sekundære forbindelsesstreng, og skriv **Sekundære konfiguration streng**.


## <a name="tcp-ports-and-security"></a>TCP-porte og sikkerhed

Når du opretter en hybrid forbindelse, der oprettes et websted på din lokale lokalt IIS-server. IIS-serveren kan være i en DMZ. TCP portkravene på IIS-serveren, omfatter:

TCP-Port | Hvorfor
--- | ---
 | Ingen indgående TCP-porte er påkrævet.
9350 - 9354 | Disse porte bruges til overførsel af data. Service Bus relay manager sonder port 9350 til at afgøre, om TCP connectivity er tilgængelig. Hvis det er tilgængeligt, derefter antages det, at port 9352 også er tilgængelige. Datatrafik handler om port 9352. <br/><br/>Tillad udgående forbindelser til disse porte.
5671 | Når port 9352 bruges til datatrafik, port 5671 bruges som den salgskanalen kontrolelement. <br/><br/>Tillad udgående forbindelser til denne port. 
80, 443 | Hvis port 9352 og 5671 ikke er brugbare, er *derefter* port 80 og 443 fallback porte, der bruges til overførsel af data og salgskanalen kontrolelement.<br/><br/>Tillad udgående forbindelser til disse porte.
Lokalt system port | Åbn den port, som systemet på det lokale system. For eksempel bruger SQL Server typisk port 1433. Åbn denne TCP-port.

[Vært bag en Firewall med Service Bus](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Fejlfinding i forbindelse med lokalt

1. Bekræft rollen IIS web er installeret og alle IIS-tjenesterne er startet på IIS-serveren.
2. Bekræft Forbindelsesstyring Hybrid er installeret og kører på IIS-serveren:
 - I IIS Manager (inetmgr), webstedet ***MicrosoftAzureBizTalkHybridListener*** skal være angivet og køre. 
 - Dette websted bruger ***HybridListenerAppPool*** , der kører som kontoen *Netværkstjeneste* lokal indbyggede bruger. Denne AppPool bør også startes.
3. Kontrollér forbindelsen er installeret og køre på IIS-serveren: 
 - Der oprettes et websted på forbindelsen. Eksempelvis hvis du har oprettet en SQL-forbindelse, er der et ***MicrosoftSqlConnector_nnn*** websted. I IIS Manager (inetmgr): Bekræft dette websted er angivet, og i gang. 
 - Dette websted bruger sin egen IIS-programgruppen med navnet ***HybridAppPoolnnn***. Denne AppPool kører som *Netværkstjeneste* lokale indbyggede brugerkontoen. Dette websted og AppPool bør begge startes. 
 - Find den lokale forbindelse. Eksempelvis hvis webstedet forbindelse bruger port 6569, gå til http://localhost:6569. Et standarddokument er ikke konfigureret så en `HTTP Error 403.14 - Forbidden error` der forventes.
4. Bekræft de TCP-porte, der er angivet i dette emne er åbne i din firewall.
5. Se på systemet kilde eller destination:
 - Nogle lokale systemer kræver ekstra afhængighed filer. Hvis du opretter forbindelse til SAP lokalt, skal der for eksempel installeres nogle yderligere SAP-filer på IIS-serveren.
 - Kontrollér forbindelsen til systemet med logonkontoen. For eksempel skal den TCP-port, der bruges af systemet være åben, som port 1433 til SQL Server. På logon-konto, du har angivet i portalen Azure skal have adgang til systemet.
6. På IIS-serveren, i hændelsesloggen for fejl. 
7. Oprydning og geninstallere Forbindelsesstyring Hybrid: 
 - I IIS, du manuelt slette webstedet forbindelse og programgruppen. 
 - Kør Forbindelsesstyring Hybrid, og Bekræft, at du angiver den korrekte **Primære konfiguration streng** på forbindelsen.



### <a name="in-the-azure-classic-portal"></a>På portalen Azure klassisk

1. Bekræft navneområdet Service Bus har en **aktiv** tilstand.
2. Når du opretter forbindelsen, kan du angive Service Bus SAS forbindelsesstrengen. Angiv ikke ACS forbindelsesstrengen.


## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

**Spørgsmål**: der er to Hybrid forbindelse ledere. Hvad er forskellen? 

**Answer**: der er den [Hybrid forbindelser](../biztalk-services/integration-hybrid-connection-overview.md) teknologi, der bruges først og fremmest af Web Apps (tidligere websteder) og Mobile-Apps (tidligere mobile services) til at oprette forbindelse til en lokal installation. Denne Hybrid forbindelser Manager er sin egen [konfiguration](../biztalk-services/integration-hybrid-connection-create-manage.md) og bruger en Azure BizTalk Service (bag kulisserne). Det understøtter kun TCP og HTTP-protokoller.

Med Azure App Service forbindelser har vi også en Hybrid Forbindelsesstyring.  Denne Hybrid Forbindelsesstyring betyder *ikke* bruge en Azure BizTalk Service (bag kulisserne) og understøtter mere end TCP- og HTTP-protokollerne. Se [forbindelser og API listen over Apps](app-service-logic-connectors-list.md).

Begge bruge Azure Service Bus til at oprette forbindelse til det lokale system.

**Spørgsmål**: Når jeg opretter en brugerdefineret API App, kan jeg bruge App Service Hybrid Forbindelsesstyring til at oprette forbindelse til en lokal installation? 

**Answer**: ikke på den traditionelle måde. Du kan bruge et indbygget stik, Konfigurer App Service Hybrid Forbindelsesstyring til at oprette forbindelse til det lokale system. Derefter kan bruge denne forbindelse til din brugerdefinerede API-App, der muligvis ved hjælp af en logik App. Du kan ikke i øjeblikket, udvikle eller oprette dine egne hybride API App (som SQL-forbindelse eller fil forbindelse).

Hvis din brugerdefinerede API bruger en TCP- eller HTTP-port, kan du bruge [Hybrid forbindelser](../biztalk-services/integration-hybrid-connection-overview.md) og dens Hybrid Forbindelsesstyring. I dette scenarie skal bruges en Azure BizTalk Service. [Opret forbindelse til lokal SQL Server fra en WebApp](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) kan hjælpe.  


## <a name="read-more"></a>Få mere at vide

[Overvåge dine logik Apps](app-service-logic-monitor-your-logic-apps.md)<br/>
[Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
