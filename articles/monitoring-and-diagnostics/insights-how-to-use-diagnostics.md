<properties
    pageTitle="Aktivér overvågning og diagnosticering i Microsoft Azure | Microsoft Azure "
    description="Lær, hvordan du konfigurerer diagnosticering til dine ressourcer i Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="enable-monitoring-and-diagnostics"></a>Aktivér overvågning og diagnosticering

Du kan konfigurere omfattende og hyppige, overvågning og diagnosticering data om dine ressourcer i [Azure-portalen](https://portal.azure.com). Du kan også bruge [REST-API](https://msdn.microsoft.com/library/azure/dn931932.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) til at konfigurere diagnosticering fra et program.

Diagnostics, overvågning og metrisk data i Azure er gemt i en lagerplads konto efter eget valg. Dette kan du bruge andet værktøj, du vil læse dataene fra en lagerplads explorer til Power BI til tredjepart værktøj.

## <a name="when-you-create-a-resource"></a>Når du opretter en ressource

De fleste tjenester giver dig mulighed at aktivere diagnosticering, når du først oprette dem i [Azure-portalen](https://portal.azure.com).

1. Gå til **Ny** og vælge den ressource, du er interesseret i.

2. Vælg **valgfri konfiguration**.
    ![Diagnosticering blade](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Vælg **diagnosticering**, og klik **på**. Du skal vælge lagerplads konto, du vil diagnosticering skal gemmes til. Du kan betale normal data satser for lager og transaktioner når du sender diagnosticering til en lagerplads konto.

4. Klik på **OK** , og oprette ressourcen.

## <a name="change-settings-for-an-existing-resource"></a>Ændre indstillinger for en eksisterende ressource

Hvis du allerede har oprettet en ressource, og du vil ændre diagnosticeringsindstillingerne for (Hvis du vil ændre niveauet for dataindsamling, for eksempel), kan du gøre højre på portalen Azure.

1. Gå til ressourcen, og klik på kommandoen **Indstillinger** .

2. Vælg **diagnosticering**.

3. Bladet **diagnosticering** har alle de mulige diagnosticering og overvågningsdata af websteder for den pågældende ressource. For nogle ressourcer, kan du også vælge **en opbevaringspolitik for dataene, til at rydde op fra kontoen lagerplads** .
    ![Lagerplads diagnosticering](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Når du har valgt indstillingerne, skal du klikke på kommandoen **Gem** . Det kan tage lidt mens for overvågning af data for at få vist, hvis du ønsker at aktivere det for første gang.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Kategorier af indsamling af data til virtuelle maskiner
For virtuelle maskiner registreres alle målepunkter og hændelseslogge med ét minut intervaller, så du kan altid har de seneste oplysninger om din computer.

- **Grundlæggende målepunkter** : sundhed målepunkter om din virtuelle maskine som processor og hukommelse
- **Netværks- og web målepunkter** : målepunkter om dine netværksforbindelser og webtjenester
- **.NET målepunkter** : målepunkter om .NET og ASP.NET programmer, der kører på din virtuelle maskine
- **SQL-metrik** : Hvis du kører Microsoft SQL-tjeneste, dens parametre for ydeevne
- **Windows-program hændelseslogge** : Windows hændelser, der sendes til kanalen, programmet
- **Windows-system hændelseslogge** : Windows hændelser, der sendes til systemkanalen,. Dette omfatter også alle begivenheder fra [Microsoft Antimalwareprogram](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Windows-sikkerhed hændelseslogge** : Windows hændelser, der sendes til kanalen, sikkerhed
- **Diagnostics infrastruktur logfiler** : logføring om diagnostics samling infrastruktur
- **IIS-logfilerne** : logge om din IIS-server

Bemærk, at på nuværende tidspunkt visse fordeling af Linux understøttes ikke, og Gæst Agent skal være installeret på den virtuelle maskine.

## <a name="next-steps"></a>Næste trin

* [Modtag beskeder om meddelelser](insights-receive-alert-notifications.md) , når funktionsdygtige begivenheder sker eller målepunkter på tværs af en tærskelværdi.
* [Overvåge tjenesten målepunkter](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
* [Skalere forekomst Tæl automatisk](insights-how-to-scale.md) at sikre, at din tjeneste skala, der er baseret på behov.
* [Overvåg programmet ydeevne](../application-insights/app-insights-azure-web-apps.md) , hvis du ønsker at forstå præcis hvordan din kode klarer sig i skyen.
* [Se begivenheder og overvågningslogge for](insights-debugging-with-events.md) at lære alt det, der er sket i din tjeneste.
* [Registrer tjenestetilstand](insights-service-health.md) til at finde ud af, når Azure fik ydeevne er forringet eller tjeneste afbrydelser.
