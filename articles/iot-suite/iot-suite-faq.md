<properties
  pageTitle="Azure IoT pakke ofte stillede spørgsmål om | Microsoft Azure"
  description="Ofte stillede spørgsmål om IoT pakke"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Ofte stillede spørgsmål om IoT pakke

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Hvad er forskellen på at slette en ressourcegruppe i portalen Azure og klikke på Slet på en forudkonfigureret løsning i azureiotsuite.com?

- Hvis du sletter den forudkonfigurerede løsning i [azureiotsuite.com][lnk-azureiotsuite], du slette alle de ressourcer, der blev klargjort, da du oprettede den forudkonfigurerede løsning Hvis du har tilføjet yderligere ressourcer til ressourcegruppen, slettes disse også. 

- Hvis du sletter ressourcegruppe i [Azure portal][lnk-azure-portal], du kun slette ressourcerne i denne ressourcegruppe Du skal også slette programmet Azure Active Directory, der er knyttet til den forudkonfigurerede løsning på [Azure klassisk portal][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hvor mange forekomster af IoT Hub kan jeg Klargør i et abonnement? 

Ti. Du kan oprette en [Azure understøtter brugertilladelse] [ link-azuresupportticket] øger denne grænse, men som standard, du kan kun klargøre ti IoT Hubs per abonnement, som beskrevet i [Azure abonnement begrænsninger][link-azuresublimits]. Da hver forudkonfigurerede løsning danner en ny IoT-Hub, kan du som et resultat kun klargøre op til ti forudkonfigurerede løsninger i et bestemt abonnement. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Hvor mange forekomster af DocumentDB kan jeg Klargør i et abonnement?

50. Du kan oprette en [Azure understøtter brugertilladelse] [ link-azuresupportticket] øger denne grænse, men som standard, kan du kun klargøre 50 DocumentDB forekomster per abonnement. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hvor mange gratis Bing Maps-API'er kan jeg Klargør i et abonnement?

To. Du kan oprette kun to interne transaktioner niveau 1 Bing Maps for Enterprise-planer i et Azure-abonnement. Remote overvågning løsningen er klargjort som standard sammen med den interne transaktioner niveau 1-plan. Som et resultat, kan du kun tildele op til to remote overvågning løsninger i et abonnement med nogen ændringer.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Jeg har en remote overvågning løsningsinstallation med et statisk kort over, hvordan kan jeg føje et interaktivt Bing kort? 
1. Få dit Bing Maps-API til Enterprise QueryKey fra [Azure portal][lnk-azure-portal]: 
 1. Gå til gruppen ressource, hvor dit Bing Maps-API til Enterprise er [Azure portal][lnk-azure-portal].
 2. Klik på alle indstillinger, derefter Key Management. 
 3. Vil du bemærke to nøgler: MasterKey og QueryKey. Kopiere værdien for QueryKey.

     > [AZURE.NOTE] Ikke har en Bing Maps-API til Enterprise-konto? Oprette et i [Azure portal] [ lnk-azure-portal] ved at klikke på + ny, søge efter Bing Maps API til Enterprise og følge instruktionerne til at oprette.

2. Rullemenu med den seneste kode fra [Azure-IoT-Remote-overvågning][lnk-remote-monitoring-github].

3. Køre et lokalt eller skybaseret installation efter kommandolinjen installationsvejledning i mappen /docs/ i lageret. 

4. Når du har kørt et lokalt eller skybaseret miljø, se i rodmappen til den *. user.config-fil, der er oprettet under installationen. Åbne denne fil i et tekstredigeringsprogram. 

5. Ændre følgende linje for at medtage den værdi, du har kopieret fra din QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jeg oprette en forudkonfigureret løsning, hvis jeg har Microsoft Azure til DreamSpark?
Du kan ikke oprette en forudkonfigureret løsning på nuværende tidspunkt med en [Microsoft Azure for DreamSpark] [ lnk-dreamspark] konto. Men du kan oprette en [gratis prøveversion konto til Azure] [ lnk-30daytrial] i blot et par minutter, der kan du oprette en forudkonfigureret løsning.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hvordan sletter jeg en AAD-lejer?

Se Eric Golpe blogindlæg [gennemgang af sletning af en Azure AD-lejer][lnk-delete-aad-tennant].

## <a name="next-steps"></a>Næste trin

Du kan også udforske nogle af de andre funktioner og egenskaber de IoT pakke forudkonfigureret løsninger:

- [Skønnet vedligeholdelse forudkonfigureret løsning oversigt][lnk-predictive-overview]
- [IoT sikkerhed fra bunden][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
