<properties
    pageTitle="ved hjælp af programmering overvåge job på Stream Analytics | Microsoft Azure"
    description="Lær at overvåge Stream Analytics-job, der er oprettet via REST API'er, Azure SDK eller Powershell fra et program."
    keywords=".NET skærm, jobbet skærm, overvågning app"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Oprette en Stream Analytics job skærm fra et program
 Denne artikel beskrives, hvordan du aktiverer overvågning for en Stream Analytics sag. Stream Analytics job, der er oprettet, via REST API'er, Azure SDK eller Powershell gør har ikke overvågning aktiveret som standard.  Du kan manuelt aktivere dette i portalen Azure ved at navigere til det job skærm side og klikke på knappen Aktivér, eller du kan automatisere denne proces ved at følge trinnene i denne artikel. Den overvågningsdata vises på fanen "Skærm" i portalen Azure for Stream Analytics tingene.

![jobbet skærm under fanen job](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Forudsætninger
Før du begynder i denne artikel, skal du have følgende:

- Visual Studio 2012 eller 2013.
- Hent og Installer [Azure.NET SDK](https://azure.microsoft.com/downloads/).
- Et eksisterende Stream Analytics-job, der skal overvågning aktiveret.

## <a name="setup-a-project"></a>Konfiguration af et projekt

1.  Oprette et Visual Studio C# .net console-program.
2.  Kør følgende kommandoer for at installere NuGet pakkerne i pakke Manager-konsollen. Den første del er Azure Stream Analytics Management .NET SDK. Den anden opgave er i Azure skærm SDK, som bruges til at aktivere overvågning. Den sidste, der er den Azure Active Directory-klient, der skal bruges til godkendelse.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Føje afsnittet nedenfor appSettings til App.config-filen.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Erstatte værdier for *SubscriptionId* og *ActiveDirectoryTenantId* med abonnementet Azure og lejer id'er. Du kan få disse værdier ved at køre følgende PowerShell-cmdlet:

    ```
    Get-AzureAccount
    ```
4.  Tilføj følgende ved hjælp af sætninger til kildefilen (Program.cs) i projektet.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Tilføje en hjælper godkendelsesmetode.

        public static string GetAuthorizationHeader()
            {
                AuthenticationResult result = null;
                var thread = new Thread(() =>
                {
                    try
                    {
                        var context = new AuthenticationContext(
                            ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                            ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                        result = context.AcquireToken(
                            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                            clientId: ConfigurationManager.AppSettings["AsaClientId"],
                            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                            promptBehavior: PromptBehavior.Always);
                    }
                    catch (Exception threadEx)
                    {
                        Console.WriteLine(threadEx.Message);
                    }
                });

                thread.SetApartmentState(ApartmentState.STA);
                thread.Name = "AcquireTokenThread";
                thread.Start();
                thread.Join();

                if (result != null)
                {
                    return result.AccessToken;
                }

                throw new InvalidOperationException("Failed to acquire token");
        }

## <a name="create-management-clients"></a>Oprette Management-klienter
Følgende kode vil konfigurere de nødvendige variabler og administration af klienter.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Aktivere overvågning For en eksisterende Stream Analytics sag

Følgende kode giver mulighed for overvågning for et **eksisterende** Stream Analytics-job. Den første del af koden udfører en GET-anmodning mod Stream Analytics-tjenesten til at hente oplysninger om det pågældende Stream Analytics-job. Det bruger egenskaben "Id" (hentet fra GET-anmodning) som en parameter for metoden læg i den anden halvdel af koden, der sender en læg anmode om at tjenesten indsigt til at aktivere overvågning for Stream Analytics jobbet.

> [AZURE.WARNING]
> Hvis du tidligere har aktiveret overvågning til et andet Stream Analytics-job, enten via portalen Azure eller fra et program via den under kode, **det anbefales, at du angiver det samme lagerplads kontonavn, som du gjorde, da du tidligere har aktiveret overvågning.**
>
> Kontoen lagerplads er knyttet til det område, du har oprettet Stream Analytics tingene i, ikke specifikt til selve jobbet.
>
> Alle Stream Analytics job (og alle andre Azure ressourcer) i den pågældende samme region dele denne lagerplads konto for at gemme overvågningsdata. Hvis du angiver en anden lagerplads-konto, kan det medføre utilsigtede side effekter til overvågning af dine andre Stream Analytics-job og/eller andre Azure ressourcer.
>
> Kontonavn lager, bruges til at erstatte ```“<YOUR STORAGE ACCOUNT NAME>”``` nedenfor skal være en lagerplads-konto, som er i samme-abonnement, som Stream analyser job du aktiverer overvågning.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Få support
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
