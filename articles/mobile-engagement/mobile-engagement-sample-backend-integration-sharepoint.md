<properties 
    pageTitle="Azure Mobile aftale - back end-integration" 
    description="Oprette forbindelse Azure Mobile aftale med en SharePoint back-end til at oprette kampagner fra SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile aftale - API-integration

I et automatiseret marketing system sker oprette og aktivering af marketingkampagner også automatisk. Hertil - aktiverer Azure Mobile aftale oprette sådanne automatiseret marketingkampagner ved hjælp af API'er samt. 

Typisk brug kunder grænsefladen Mobile aftale front-end til at oprette meddelelser/afstemninger osv som en del af deres marketingkampagner. Men efterhånden som de marketingkampagner bliver fuldt udviklet, der er behov for at udnytte de data, der er låst i back end-systemer (som en hvilken som helst CRM-systemet eller CMS system som SharePoint), så du kan oprette en fuldautomatisk rørledning, opretter kampagner i Mobile aftale dynamisk baseret på de data, der flyder i fra back end-systemer. 

![][5]

Dette selvstudium går gennem et scenarie, hvor erhvervsbruger SharePoint udfylder en SharePoint-liste med marketing data og en automatiseret proces, der henter elementer på listen og opretter forbindelse til Mobile aftale systemet ved hjælp af de tilgængelige REST API'er til at oprette en markedsføringskampagne fra SharePoint-data. 
 
> [AZURE.IMPORTANT] Generelt, kan du bruge dette eksempel som udgangspunkt for at forstå, hvordan du kan ringe en hvilken som helst Mobile aftale REST-API, som indeholder oplysninger om de to vigtige aspekter af kald API'er - godkender og nær parametre. 

## <a name="sharepoint-integration"></a>SharePoint-integration
1. Her er, hvordan SharePoint-listen eksempel ser ud. **Titel**, **kategori**, **NotificationTitle**, **meddelelse** og **URL-adresse** bruges til at oprette meddelelsen. Der findes en kolonne med navnet **IsProcessed** , som bruges af processen eksempel automatisering i form af et console-program. Du kan enten kører denne konsol program som en Azure WebJob, så du kan planlægge det, eller du kan bruge SharePoint-arbejdsproces til automatisk oprettelse og aktivering meddelelsen, når et element er indsat i SharePoint-listen direkte. I dette eksempel bruger vi programmet console der går gennem de forskellige elementer på SharePoint-listen og oprette meddelelse i Azure Mobile aftale for hver af dem og derefter markerer flaget **IsProcessed** for at være sande på oprettelse af vellykket meddelelse til sidst.

    ![][1]

2. Vi bruger kode fra *Remote godkendelse i SharePoint Online ved hjælp af objektmodellen Client* stikprøven [her](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) for at godkende med SharePoint-listen.
 
3. Når godkendt, vi løkke på listeelementer for at finde alle de nyligt oprettede elementer (som har **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integration af mobile aftale
1.  Når vi finde et element, der kræver behandling - vi udtrække de oplysninger, der kræves for at oprette en meddelelse fra listeelement, og kald `CreateAzMECampaign` til at oprette den og derefter `ActivateAzMECampaign` aktivere den. Dette er grundlæggende REST-API opkald ringer for at den Mobile aftale backend-version. 

2.  Mobile aftale REST API'er kræver et **grundlæggende auth farveskema authorization HTTP-header** som består af den `ApplicationId` og `ApiKey` som hentes fra Azure-portalen. Sørg for, at du bruger nøglen i afsnittet **api nøgler** og *ikke* i afsnittet **sdk taster** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. For at oprette meddelelsen type campaign - [dokumentation](https://msdn.microsoft.com/library/azure/mt683750.aspx)til. Du har brug at sikre dig, at du er ved at angive kampagnens `kind` som *meddelelse* og [data](https://msdn.microsoft.com/library/azure/mt683751.aspx) og sende det som FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Når du har oprettet meddelelsen, får du vist noget i retning af følgende på portalen Mobile aftale (Bemærk, at tilstanden = kladde og aktiveret = i/t)

    ![][3]

5. `CreateAzMECampaign`opretter en meddelelse kampagnens og returnerer dens-Id til kalderen. `ActivateAzMECampaign`kræver dette Id som parameteren for at aktivere kampagnens. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Når du har aktiveret meddelelsen, får du vist noget i retning af følgende på portalen Mobile aftale:

    ![][4]

7. Så snart kampagnens bliver aktiveret, starter en hvilken som helst enheder, der opfylder kriterierne for kampagnens se beskeder. 

8. Vil du også bemærke, at listeelementet markeret med IsProcessed = FALSK, der er angivet til sand, når meddelelsen kampagnens er oprettet.  

Dette eksempel oprettes en enkelt meddelelse kampagnens angive hovedsageligt de ønskede egenskaber. Du kan tilpasse den, så meget, kan du fra portalen ved hjælp af oplysningerne [her](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
