<properties 
    pageTitle="Brug af .NET SDK til at få adgang til Azure Mobile aftale Service API'er" 
    description="Beskriver, hvordan du bruger Mobile aftale .NET SDK til at få adgang til Azure Mobile aftale Service API'er"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Brug af .NET SDK til at få adgang til Azure Mobile aftale Service API'er

Azure Mobile aftale viser et sæt API'er for dig at administrere enheder, rækkevidde/Push kampagner osv. For at interagere med disse API'er, også giver vi dig en [Swagger fil](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) , du kan bruge med værktøjer til at generere SDK'er til dit foretrukne sprog. Vi anbefaler ved hjælp af værktøjet [AutoRest](https://github.com/Azure/AutoRest) til at oprette din SDK fra vores Swagger-fil. 

Vi har oprettet en .NET SDK på samme måde som du kan bruge til at interagere med disse API'er ved hjælp af en C#-slikpapir, og du behøver ikke at gøre den token forhandling godkendelse og opdatere dig selv.  

Dette eksempel gennemgår sæt af trin til at følge for at bruge .NET SDK:

1. Først, skal du konfigurere godkendelse for din API'er ved hjælp af Azure Active Directory som beskrevet [her](mobile-engagement-api-authentication.md#authentication). I slutningen af disse trin, bør du have en gyldig **SubscriptionId**, **TenantId**, **ApplicationId** og **hemmeligt**. 

2. Vi bruger en enkelt Windows-konsollen app til at vise, arbejde med .NET SDK med dette scenario til oprettelse af en meddelelse campaign. Så åbne Visual Studio og oprette et **Console-program**.   

3. Derefter skal du hente .NET SDK, som er tilgængelige som **Microsoft Azure aftale Management bibliotek** i galleriet Nuget [her](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
Hvis du installerer Nuget fra Visual Studio, skal du sikre dig, at du har markeres indstillingen **Medtag foreløbig version** mens der søges til pakken:

    ![][1]

4. I den `Program.cs` fil, skal du tilføje følgende navneområder:

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. Derefter skal du angive følgende konstanter, vi vil bruge til godkendelse af og interaktion med Appen Mobile aftale, hvor du opretter meddelelse kampagnens:

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Definere den `EngagementManagementClient` variabel, som vi skal bruge til at ringe til Mobile aftale SDK metoder:

        static EngagementManagementClient engagementClient; 

7. Tilføj følgende til din `Main` metode:

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Definere den følgende metode, der tager sig af initialisering af den `EngagementManagementClient` ved først godkendelse og knytte selve til Appen Mobile aftale, hvor du planlægger at oprette meddelelsen kampagnens:

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] Bemærk, at du skal bruge **App ressourcenavn** , som defineret i portalen Azure management for parameteren programnavn. 

9. Endelig definere metoden CreateCampaign som tager sig af brug af tidligere initialiseret EngagementClient til at oprette en simpel **når som helst** & **meddelelse kun** kampagnens med en titel og -meddelelse: 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Kør console-app, og du får vist følgende på korrekt oprettelse kampagnens:

    **Kampagnens-Id '159' blev oprettet, og det er i ""-kladdetilstand**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
