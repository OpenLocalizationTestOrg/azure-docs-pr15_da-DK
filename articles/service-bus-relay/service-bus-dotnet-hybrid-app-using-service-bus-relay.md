<properties
    pageTitle="Hybrid på-lokale/skyen program (.NET) | Microsoft Azure"
    description="Lær at oprette en .NET på-lokale/skyen hybrid-program ved hjælp af Azure Service Bus relay."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>.NET på-lokale/skyen hybrid program ved hjælp af Azure Service Bus Relay

## <a name="introduction"></a>Introduktion

I denne artikel beskrives, hvordan du opretter et hybrid skyen til computeren med Microsoft Azure og Visual Studio. Selvstudiet antages det, at du ikke har nogen foregående erfaring med Azure. På mindre end 30 minutter, vil du have et program, der bruger flere Azure ressourcer op og kører i skyen.

Du kan få mere at vide:

-   Om at oprette eller tilpasse en eksisterende webtjeneste til forbrug ved en webløsning.
-   Sådan bruges Azure Service Bus Relay-tjenesten til at dele data mellem et Azure-program og en webtjeneste hostet et andet sted.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Hvordan Service Bus relay hjælper med hybrid løsninger

Virksomhedsløsninger typisk består af en kombination af brugerdefineret kode, der er skrevet til at håndtere nye og entydige virksomhedens behov og eksisterende funktionalitet, der leveres af løsninger og systemer, der allerede findes på plads.

Løsningsarkitekter begynde at bruge skyen for nemmere håndtering af skala krav og reducere omkostningerne til funktionsdygtige. I at gøre dette, finde de, eksisterende service Aktiver de vil gerne udnytte som dokumentkomponenter til deres løsninger er inden for virksomhedens firewall og af nemt nå for adgang ved at løsningen skyen. Mange interne tjenester er ikke oprettet eller hostet på en måde, de kan være nemt vises på virksomhedens netværkskanten.

Service Bus relay er udviklet til at use case tage eksisterende Windows Communication Foundation (WCF) webtjenester og gøre disse tjenester sikkert tilgængelige til løsninger, der er placeret uden for virksomhedens kanten uden forstyrrende ændringer i virksomhedens netværksinfrastruktur. Sådan Service Bus relay-tjenester der er stadig på i deres eksisterende miljø, men de uddelegere lytter efter indgående sessioner og anmodninger om til skyen hostet Service Bus. Service Bus beskytter også disse tjenester mod uautoriseret adgang ved hjælp af [Delt Access signatur](../service-bus-messaging/service-bus-sas-overview.md) (SAS) godkendelse.

## <a name="solution-scenario"></a>Løsning scenarie

Du skal oprette en ASP.NET-websted, som gør det muligt at se en liste over produkter på siden produkt lager i dette selvstudium.

![][0]

Selvstudiet antages det, at du har produktoplysninger i et eksisterende lokalt system og bruger tjenesten Bus relay når til dette system. Dette er simuleret ved en webtjeneste, der kører i et enkelt console til computeren og understøttes af et i hukommelsen sæt af produkter. Du vil kunne køre dette console-program på din egen computer og installere rollen web til Azure. Gør det, kan du se hvordan rollen web kører i Azure datacenteret faktisk skal ringe til din computer, også selvom computeren er næsten helt sikkert placeret bag mindst én firewall og et netværk adresse oversættelse (NAT) lag.

Følgende er et skærmbillede af startsiden for det færdige webprogram.

![][1]

## <a name="set-up-the-development-environment"></a>Konfigurere udviklingsmiljøet

Før du kan begynde at Azure udviklingsprogrammer, få værktøjerne, og konfigurere dit udviklingsmiljø.

1.  Installer Azure SDK til .NET fra siden [få værktøjer og SDK][] .

2.  Klik på **Installer SDK** for versionen af Visual Studio, du bruger. Trinnene i dette selvstudium bruger Visual Studio-2015.

4.  Når du bliver bedt om at køre eller gemme installationsprogrammet, kan du klikke på **Kør**.

5.  Klik på **Installer** i **Web Platform Installer**, og fortsæt med installationen.

6.  Når installationen er fuldført, har du alt det er nødvendigt at begynde at udvikle appen. SDK indeholder funktioner, som du kan let at udvikle Azure-programmer i Visual Studio. Hvis du ikke har Visual Studio er installeret, installeres SDK også den gratis Visual Studio Express.

## <a name="create-a-namespace"></a>Oprette et navneområde

Hvis du vil begynde at bruge Service Bus funktioner i Azure, skal du først oprette en tjeneste navneområde. Et navneområde indeholder en angiver området objektbeholder til at håndtere Service Bus ressourcer i dit program.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Oprette en lokal server

Først skal du oprette et (mock) lokalt produkt katalog system. Det vil være ganske enkelt; Du kan se ud som repræsenterer en faktisk lokalt produkt katalog-systemet med et komplet service overflader, forsøger vi at integrere.

Dette projekt er et Visual Studio console-program, og bruger [Azure Service Bus NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) til at medtage Service Bus biblioteker og indstillinger for søgekonfiguration.

### <a name="create-the-project"></a>Oprette projektet

1.  Med administratorrettigheder kan du starte Microsoft Visual Studio. For at starte Visual Studio med administratorrettigheder, skal du højreklikke på programikonet **Visual Studio** , og klik derefter på **Kør som administrator**.

2.  Klik på **Ny**i Visual Studio, i menuen **filer** , og klik derefter på **projekt**.

3.  Klik på **Console program**under **Visual C#**, fra **Installeret skabeloner**. Skriv navnet **ProductsServer**i feltet **navn** :

    ![][11]

4.  Klik på **OK** for at oprette **ProductsServer** projekt.

7.  Hvis du allerede har installeret NuGet pakke manager til Visual Studio, kan du gå videre til næste trin. Ellers skal du besøge [NuGet][] og klikke på [Installere NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Følg vejledningen for at installere NuGet pakke manager, og derefter starte Visual Studio igen.

7.  I Solution Explorer, skal du højreklikke på **ProductsServer** projektet, og klik derefter **Administrere NuGet pakker**.

8.  Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus`. Klik på **Installer**, og Accepter vilkår for anvendelse.

    ![][13]

    Bemærk, at de krævede klient assemblies nu refereres til.

9.  Tilføje en ny klasse for dit produkt kontrakt. Højreklik på **ProductsServer** projektet i Solution Explorer, og klik på **Tilføj**, og klik derefter på **klasse**.

10. Skriv navnet **ProductsContract.cs**i feltet **navn** . Klik derefter på **Tilføj**.

11. Erstat navneområde definitionen i **ProductsContract.cs**, med følgende kode, der definerer kontrakten til tjenesten.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. Erstat navneområde definitionen i Program.cs, med følgende kode, der laves profile service og værten for den.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. Dobbeltklik på **App.config** -filen for at åbne den i Visual Studio editoren i Solution Explorer. I bunden af ** &lt;system. ServiceModel&gt; ** element (men stadig i &lt;system. ServiceModel&gt;), Tilføj følgende XML-kode. Sørg for at erstatte *yourServiceNamespace* med navnet på din navneområde og *yourKey* med tasten SAS, du tidligere har hentet fra portalen:

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Stadig i App.config, i den ** &lt;appSettings&gt; ** element, Erstat forbindelsen strengværdi med den forbindelsesstreng, du tidligere har fået fra portalen. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Tryk på **Ctrl + Shift + B** , eller klik på **Opret løsning** for at opbygge programmet og kontrollere nøjagtigheden af dit arbejde hidtil fra menuen **Build** .

## <a name="create-an-aspnet-application"></a>Oprette et ASP.NET-program

I dette afsnit kan du oprette en simpel ASP.NET-program, der viser data, der er hentet fra din produkt-tjeneste.

### <a name="create-the-project"></a>Oprette projektet

1.  Sørg for, at Visual Studio kører med administratorrettigheder.

2.  Klik på **Ny**i Visual Studio, i menuen **filer** , og klik derefter på **projekt**.

3.  Klik på **ASP.NET-webprogrammet**under **Visual C#**, **Installeret skabeloner**. Navngive projektet **ProductsPortal**. Klik derefter på **OK**.

    ![][15]

4.  På listen **Vælg en skabelon** , skal du klikke på **MVC**. 

6.  Markér afkrydsningsfeltet for **vært i skyen**.

    ![][16]

5. Klik på knappen **Skift godkendelse** . Klik på **Ingen godkendelse**i dialogboksen **Skift godkendelse** , og klik derefter på **OK**. I dette selvstudium, du anvender en app, som ikke har brug for et brugerlogon.

    ![][18]

6.  Sørg for, at **hoste i skyen** er markeret og, **App Service** er markeret på listen ned i afsnittet **Microsoft Azure** i dialogboksen **Ny ASP.NET-projekt** .

    ![][19]

7. Klik på **OK**. 

8. Nu skal du konfigurere Azure ressourcer til en ny WebApp. Følg trinnene i afsnittet [konfigurere Azure ressourcer til en ny WebApp](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Derefter tilbage til dette selvstudium, og gå videre til næste trin.

5.  I Solution Explorer, skal du højreklikke på **modeller** og derefter klikke på **Tilføj**, og klik derefter **klasse**. Skriv navnet **Product.cs**i feltet **navn** . Klik derefter på **Tilføj**.

    ![][17]

### <a name="modify-the-web-application"></a>Ændre webprogrammet

1.  Erstat eksisterende navneområde definitionen med følgende kode i filen Product.cs i Visual Studio.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  Udvid mappen **enheder** i Solution Explorer, og derefter dobbeltklikke på filen **HomeController.cs** for at åbne den i Visual Studio.

3. Erstat eksisterende navneområde definitionen i **HomeController.cs**, med følgende kode.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  Udvid mappen Views\Shared i Solution Explorer, og derefter dobbeltklikke på **_Layout.cshtml** for at åbne den i Visual Studio-editoren.

5.  Ændre alle forekomster af **Mine ASP.NET-program** til **LITWARES produkter**.

6. Fjerne linkene **Home**, **om**og **Kontakt** . Slet den markerede kode i det følgende eksempel.

    ![][41]

7.  Udvid mappen Views\Home i Solution Explorer, og derefter dobbeltklikke på **Index.cshtml** for at åbne den i Visual Studio-editoren.
    Erstat hele indholdet af filen med følgende kode.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  For at kontrollere nøjagtigheden af dit arbejde indtil videre, kan du trykke på **Ctrl + Shift + B** for at oprette projektet.


### <a name="run-the-app-locally"></a>Køre appen lokalt

Køre programmet for at kontrollere, at den fungerer.

1.  Sørg for, at **ProductsPortal** er det aktive projekt. Højreklik på projektnavnet i Solution Explorer, og vælg **Angiv som Start projekt**.
2.  I Visual Studio, skal du trykke på F5.
3.  Dit program skal vises, kører i en browser.

    ![][21]

## <a name="put-the-pieces-together"></a>Sammensat delene

Næste trin er at logge på den lokale produkter server med ASP.NET-program.

1.  Hvis den ikke allerede er åbne, i Visual Studio genåbnet **ProductsPortal** projektet du oprettede i afsnittet [oprette en ASP.NET-program](#create-an-aspnet-application) .

2.  Svarer til trinnet i afsnittet "Oprette en On-Premises Server" føje NuGet pakken til projekt-referencer. I Solution Explorer, skal du højreklikke på **ProductsPortal** projektet, og klik derefter **Administrere NuGet pakker**.

3.  Søg efter "Service Bus", og vælg elementet, **Microsoft Azure Service Bus** . Derefter fuldføre installationen, og Luk denne dialogboks.

4.  I Solution Explorer, skal du højreklikke på **ProductsPortal** projektet og derefter Klik på **Tilføj**, derefter **Eksisterende element**.

5.  Gå til filen **ProductsContract.cs** fra **ProductsServer** console projektet. Klik for at fremhæve ProductsContract.cs. Klik på pil ned ud for **Tilføj**, og derefter klikke på **Tilføj som Link**.

    ![][24]

6.  Åbn filen **HomeController.cs** i editoren Visual Studio nu, og Erstat navneområde definitionen med følgende kode. Sørg for at erstatte *yourServiceNamespace* med navnet på din tjeneste navneområde og *yourKey* med SAS nøglen. Dette vil aktivere klienten til at ringe til tjenesten lokalt, returnerer resultatet af opkaldet.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  I Solution Explorer, skal du højreklikke på **ProductsPortal** løsningen (Sørg for, at højreklikke på løsningen, ikke projektet). Klik på **Tilføj**, og derefter klikke på **Eksisterende projekt**.

8.  Gå til **ProductsServer** projektet og derefter dobbeltklikke på filen **ProductsServer.csproj** løsning for at tilføje den.

9.  **ProductsServer** skal køre for at vise data på **ProductsPortal**. Højreklik på **ProductsPortal** løsningen i Solution Explorer, og klik på **Egenskaber**. Dialogboksen **Egenskabssider** vises.

10. Klik på **Start projekt**i venstre side. Klik på **flere Start projekter**i højre side. Sørg for, at **ProductsServer** og **ProductsPortal** vises i rækkefølgen, med **Start** angive som handlingen for begge.

      ![][25]

11. Klik på **Projektafhængigheder** i venstre side stadig i dialogboksen **Egenskaber** .

12. Klik på **ProductsServer**på listen **projekter** . Sikre, at **ProductsPortal** er **ikke** markeret.

14. Klik på **ProductsPortal**på listen **projekter** . Sørg for, at **ProductsServer** er markeret. 

    ![][26]

15. Klik på **OK** i dialogboksen **Egenskabssider** .

## <a name="run-the-project-locally"></a>Kør projektet lokalt

Tryk på **F5**for at teste programmet lokalt, i Visual Studio. Lokal server (**ProductsServer**) skal starte første og derefter programmet **ProductsPortal** skal starte i et browservindue. Denne gang, vil du se, at produktlageret viser data, der er hentet fra produkt service lokalt system.

![][10]

Tryk på **Opdater** på siden **ProductsPortal** . Hver gang du opdaterer siden, du får vist appen server, der vises en meddelelse, når `GetProducts()` fra **ProductsServer** hedder.

Luk begge programmer, før du fortsætter til næste trin.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Anvende ProductsPortal-projektet til en Azure-WebApp

Næste trin er at konvertere **ProductsPortal** frontend til en Azure-WebApp. Først skal installere **ProductsPortal** projektet, efter alle trinnene i afsnittet [Implementer webprojektet til Azure WebApp](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Når installationen er fuldført, gå tilbage til dette selvstudium, og gå videre til næste trin.

> [AZURE.NOTE] Du kan se en fejlmeddelelse i browservinduet, når **ProductsPortal** webprojektet startes automatisk efter installationen. Dette er forventet og opstår, fordi **ProductsServer** programmet ikke kører på nuværende tidspunkt.

Kopiér URL-adressen til udløst web app, som du skal bruge URL-adressen i næste trin. Du kan også få denne URL-adresse fra vinduet Azure App serviceaktivitet i Visual Studio:

![][9] 

### <a name="set-productsportal-as-web-app"></a>Angiv ProductsPortal som WebApp

Før du kører programmet i skyen, skal du kontrollere, at **ProductsPortal** startes fra i Visual Studio som en WebApp.

1. Højreklik på **ProjectsPortal** projektet i Visual Studio, og klik derefter på **Egenskaber**.

3. Klik på **Web**i venstre kolonne.

5. Klik på knappen **Start URL-adressen** i sektionen **Starte handling** , og i tekstfeltet Angiv URL-adressen for din tidligere udløst online; for eksempel `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. Klik på **Gem alt**i menuen **filer** i Visual Studio.

7. Klik på **Genopbyg løsning**fra menuen Build i Visual Studio.

## <a name="run-the-application"></a>Køre programmet

2.  Tryk på F5 for at oprette og køre programmet. Lokal server ( **ProductsServer** console programmet) skal starte første og derefter programmet **ProductsPortal** skal starte i et browservindue, som vist på følgende skærmbillede. Bemærk igen, produktlageret viser data, der er hentet fra produkt service lokalt system, og viser dataene i WebApp. Markér URL-adressen at sikre, at **ProductsPortal** kører i skyen, som en Azure WebApp. 

    ![][1]

    > [AZURE.IMPORTANT] Programmet **ProductsServer** console skal køre og stand til at kunne levere dataene til programmet **ProductsPortal** . Hvis browseren viser en fejl, skal du vente nogle flere sekunder for **ProductsServer** at indlæse og få vist følgende meddelelse. Tryk derefter på **Opdater** i browseren.

    ![][37]

3. Tilbage i browseren, skal du trykke på **Opdater** på siden **ProductsPortal** . Hver gang du opdaterer siden, du får vist appen server, der vises en meddelelse, når `GetProducts()` fra **ProductsServer** hedder.

    ![][38]

## <a name="next-steps"></a>Næste trin  

Hvis du vil vide mere om Service Bus skal du se følgende ressourcer:  

* [Azure Service Bus][sbwacom]  
* [Sådan bruger du Bus servicekøer][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Få værktøjer og SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

