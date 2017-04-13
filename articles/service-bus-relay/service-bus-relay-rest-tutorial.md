<properties
    pageTitle="Tjenesten Bus RESTEN selvstudium bruger videresendes messaging | Microsoft Azure"
    description="Oprette et simpelt Service Bus relay værtsprogram, der viser en RESTEN-baserede grænseflade."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Tjenesten Bus Relay RESTEN selvstudium

Dette selvstudium beskriver, hvordan du opretter et simpelt Service Bus værtsprogram, der viser en RESTEN-baserede grænseflade. RESTEN gør det muligt for en webklient, som en webbrowser, adgang til tjenesten Bus API'er via HTTP-anmodninger.

Dette selvstudium bruger Windows Communication Foundation (WCF) RESTEN programming model for at oprette en RESTEN tjeneste på Service Bus. Se [WCF RESTEN Programming Model](https://msdn.microsoft.com/library/bb412169.aspx) og [designe og implementere tjenester](https://msdn.microsoft.com/library/ms729746.aspx) kan finde flere oplysninger i dokumentationen til WCF.

## <a name="step-1-create-a-service-namespace"></a>Trin 1: Oprette et service navneområde

Det første trin er at oprette et navneområde, og du får fat i en delt Access signatur (SAS) nøgle. Et navneområde indeholder en programmet grænse for hvert program fremvises Service Bus. En SAS nøgle genereres automatisk af systemet, når der oprettes et service navneområde. Kombinationen af tjenesten navneområde og SAS nøgle yder legitimationsoplysninger for tjenesten Bus til at godkende adgang til et program.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Trin 2: Definer en WCF RESTEN-baseret tjeneste kontrakt til brug med Service Bus

Som med andre tjenester Service Bus, skal når du opretter en RESTEN typografi-tjeneste, du definere kontrakten. Kontrakten angiver, hvilke handlinger, der er vært understøtter. Handlingen service kan betragtes som metode til en webtjeneste. Kontrakter oprettes ved at definere en C++, C# eller Visual Basic-grænseflade. Hver enkelt metode i grænsefladen svarer til en bestemt servicehandling. Attributten [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) skal anvendes på hver grænseflade, og attributten [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) skal anvendes på hver handling. Hvis en metode i en grænseflade, der indeholder [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) ikke har [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), vises ikke denne metode. Den kode, der bruges til disse opgaver vises i eksemplet følge fremgangsmåden.

Den primære forskel mellem en grundlæggende Service Bus kontrakt og en RESTEN-typografi kontrakt er tilføjelsen af en egenskab til [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Denne egenskab gør det muligt at tilknytte en metode i din grænseflade til en metode på anden siden af brugergrænsefladen. I dette tilfælde vil vi bruge [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) linke en metode til HTTP GET. Dette giver mulighed for tjenesten Bus til præcist at hente og fortolke kommandoer, der sendes til brugergrænsefladen.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Oprette en Service Bus kontrakt med en grænseflade

1. Åbne Visual Studio som administrator: Højreklik på programmet i menuen **Start** , og klik derefter på **Kør som administrator**.

2. Oprette et nyt console programmet projekt. Klik på fanen **filer** og vælge **Ny**og derefter vælge **projekt**. I dialogboksen **Nyt projekt** , skal du klikke på **Visual C#**, Vælg skabelonen, **Console-program** og kald den **ImageListener**. Brug standard **placering**. Klik på **OK** for at oprette projektet.

3. For et C#-projekt, Visual Studio opretter en `Program.cs` fil. Denne klasse indeholder en tom `Main()` metode, der kræves til et projekt til console-program til at opbygge korrekt.

4. Føje referencer til tjenesten Bus og **System.ServiceModel.dll** til projektet ved at installere Service Bus NuGet pakken. Denne pakke tilføjer automatisk referencer til Service Bus biblioteker, samt WCF **System.ServiceModel**. Højreklik på **ImageListener** projektet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**. Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus`. Klik på **Installer**, og Accepter vilkår for anvendelse.

5. Eksplicit skal du føje en reference til **System.ServiceModel.Web.dll** til projektet:

    en. Højreklik på mappen **referencer** under projektmappen i Solution Explorer, og klik derefter på **Tilføj Reference**.

    b. Klik på fanen **Framework** i venstre side og i feltet **Søg** skal du skrive **System.ServiceModel.Web**i dialogboksen **Tilføj Reference** . Markér afkrydsningsfeltet **System.ServiceModel.Web** , og klik derefter på **OK**.

6. Tilføj følgende `using` sætninger øverst i filen Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) er det navneområde, som gør det muligt for programmeringsmæssig adgang til grundlæggende funktioner i WCF. Service Bus bruger mange af objekterne og attributter i WCF til at definere servicekontrakter. Du skal bruge dette navneområde i de fleste af dine Service Bus relay-programmer. [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) med på samme måde til at definere den kanal, hvilket er det objekt, som du kommunikerer med tjenesten Bus og klient-webbrowser. Til sidst skal indeholder [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) de typer, der gør det muligt at oprette web-baserede programmer.

7. Omdøbe den `ImageListener` navneområde til **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Direkte efter åbning klammeparentes af angivelsen navneområde definere en ny brugergrænseflade med navnet **IImageContract** og anvende attributten **ServiceContractAttribute** til brugergrænsefladen med en værdi på `http://samples.microsoft.com/ServiceModel/Relay/`. Værdien navneområde er forskellig fra det navneområde, du bruger i hele omfanget af din kode. Værdien navneområde bruges som et entydigt id for denne aftale, og bør have versionsoplysninger. Du kan finde yderligere oplysninger finder [Service versionsstyring](http://go.microsoft.com/fwlink/?LinkID=180498). Angiver navneområdet eksplicit forhindrer navneområde standardværdien i at blive føjet til kontrakten.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. I den `IImageContract` brugergrænseflade, erklære en metode til handlingen enkelt den `IImageContract` kontrakt Fremviser i grænsefladen og anvende den `OperationContractAttribute` attribut til den metode, du vil vise som en del af den offentlige Service Bus kontrakt.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Føj **WebGet** værdien i attributten **OperationContract** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    At gøre dette gør det Service Bus at distribuere HTTP GET-anmodninger til `GetImage`, og til at oversætte returværdier af `GetImage` i en HTTP GETRESPONSE svar. Senere i dette selvstudium skal bruge du en webbrowser til at få adgang til denne metode, og at få vist på billede i browseren.

11. Lige efter den `IImageContract` definition, erklære en kanal, der nedarver fra begge de `IImageContract` og `IClientChannel` grænseflader.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    En kanal er WCF-objektet som tjenesten og klienten sender oplysninger til hinanden. Senere, opretter du kanalen i din værtsprogram. Service Bus bruger derefter denne kanal til at overføre HTTP GET-anmodninger fra browseren til implementeringen **GetImage** . Service Bus også bruger kanalen til at tage **GetImage** returværdien og oversætte den til en HTTP GETRESPONSE for klient-browseren.

12. Klik på **Opret løsning** for at bekræfte nøjagtigheden af dit arbejde hidtil fra menuen **Build** .

### <a name="example"></a>Eksempel

Følgende kode viser en grundlæggende brugergrænseflade, der definerer en Service Bus kontrakt.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Trin 3: Implementere en WCF RESTEN-baseret tjeneste kontrakt bruge Service Bus

Oprette en RESTEN-typografi Service Bus tjeneste kræver, at du først opretter kontrakt, der er defineret ved hjælp af en grænseflade. Næste trin er at implementere grænsefladen. Dette omfatter oprettelse af en klasse, kaldet **ImageService** , der implementerer grænsefladen brugerdefinerede **IImageContract** . Når du har implementeret kontrakten, kan du derefter konfigurere grænsefladen ved hjælp af en App.config-fil. Af konfigurationsfilen indeholder nødvendige oplysninger til programmet, som navnet på tjenesten, navnet på kontrakten, og typen af protokollen, der bruges til at kommunikere med Service Bus. Den kode, der bruges til disse opgaver er angivet i eksemplet følge fremgangsmåden.

Som i de tidligere trin, er der meget lille forskel mellem implementere en kontrakt RESTEN-typografi og en grundlæggende Service Bus kontrakt.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>At implementere en RESTEN-typografi Service Bus kontrakt

1. Oprette en ny klasse med navnet **ImageService** umiddelbart efter definitionen af grænsefladen **IImageContract** . Klassen **ImageService** implementerer grænsefladen **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Svarer til andre interface-installationer, du kan implementere definitionen i en anden fil. Men til dette selvstudium implementeringen vises i den samme fil som interfacedefinitionen og `Main()` metode.

2. Anvend attributten [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) klassen **IImageService** indikerer, at klassen er en implementering af en WCF-kontrakt.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Som tidligere nævnt er dette navneområde ikke en traditionel navneområde. I stedet, det er en del af den WCF-arkitektur, der identificerer kontrakten. Få mere at vide i emnet [Data kontraktnavne](https://msdn.microsoft.com/library/ms731045.aspx) i dokumentationen til WCF.

3. Tilføje en .jpg-billede til projektet.  

    Dette er et billede, som tjenesten, der vises i browseren modtagelse. Højreklik på dit projekt, og derefter klikke på **Tilføj**. Klik derefter på **Eksisterende element**. Brug dialogboksen **Tilføj eksisterende element** for at finde frem til en passende .jpg, og klik derefter på **Tilføj**.

    Når du tilføjer fil, Kontrollér, at **Alle filer** er markeret på listen rullelisten ud for den **filnavn:** felt. Resten af dette selvstudium antager, at navnet på billedet er "billede.jpg". Hvis du har en anden fil, kan du skal omdøbe billedet eller ændre din kode som kompensation.

4. For at sikre dig, at der kører tjenesten kan finde billedfilen, i **Solution Explorer** skal du højreklikke på billedfilen, og klik derefter på **Egenskaber**. Angiv **kopi til outputmappe** til **kopi, hvis nyere**i ruden **Egenskaber** .

5. Føje en reference til samlingen **System.Drawing.dll** til projektet, og Tilføj også følgende knyttet `using` sætninger.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Tilføj følgende parametre, der indlæser bitmap og forbereder at sende den til klient-browseren i klassen **ImageService** .

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Direkte efter den forrige kode, skal du tilføje følgende **GetImage** metode i klassen **ImageService** til at returnere en HTTP-meddelelse, der indeholder billedet.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Denne implementering bruger **MemoryStream** til at hente billedet og forberede til streaming til browseren. Det starter stream position ved nul, erklærer stream-indhold som JPEG-format og streamer oplysningerne.

8. Klik på **Opret løsning**fra menuen **Build** .

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>At definere konfigurationen til kørsel af webtjenesten på Service Bus

1. I **Solution Explorer**, skal du dobbeltklikke på **App.config** for at åbne den i Visual Studio editor.

    **App.config** -filen ligner en WCF-konfigurationsfil og indeholder navnet på tjenesten, slutpunkt (det vil sige på det sted, som Service Bus fremviser for klienter og værter til at kommunikere med hinanden) og binding (typen af protokollen, der bruges til at kommunikere). Den største forskel er, at det konfigurerede service slutpunkt refererer til en [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) binding, som ikke er en del af .NET Framework.

2. Den `<system.serviceModel>` XML-element er en WCF-element, der definerer en eller flere tjenester. Det er her, bruges til at definere tjenestenavnet og slutpunkt. I bunden af den `<system.serviceModel>` element (men stadig i `<system.serviceModel>`), tilføje en `<bindings>` element, der har følgende indhold. Dette definerer de bindinger, der bruges i programmet på computeren. Du kan definere flere bindinger, men i dette selvstudium du definerer kun én.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Dette trin definerer en Service Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) binding med **relayClientAuthenticationType** indstillet til **ingen**. Denne indstilling angiver, at en slutpunktet ved hjælp af denne binding ikke kræver en klient-legitimationsoplysninger.

3. Når du har den `<bindings>` element, tilføje en `<services>` element. Svarer til bindingerne, du kan angive flere tjenester i en enkelt konfigurationsfil. Men dette selvstudium skal du definere kun én.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Dette trin konfigurerer en tjeneste, der bruger tidligere defineret standard **webHttpRelayBinding**. Det bruges også standard **sbTokenProvider**, der er defineret i næste trin.

4. Når du har den `<services>` element, oprette en `<behaviors>` elementet med følgende indhold, erstatte "SAS_KEY" med tasten *Delt Access signatur* (SAS), du tidligere har fået fra [Azure-portalen][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Stadig i App.config, i den `<appSettings>` element, Erstat hele forbindelsen strengværdi med den forbindelsesstreng, du tidligere har fået fra portalen. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. Klik på **Opret løsning** for at opbygge hele løsningen fra menuen **Build** .

### <a name="example"></a>Eksempel

Følgende kode viser en kontrakt og service gennemførelse til en RESTEN-baseret tjeneste, der kører på Service Bus ved hjælp af **WebHttpRelayBinding** binding.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

I følgende eksempel viser App.config-filen, der er knyttet til tjenesten.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Trin 4: Hoste RESTEN-baserede WCF-tjenesten for at bruge Service Bus

Dette trin beskriver, hvordan du kører en webtjeneste, ved hjælp af en console-program på Service Bus. En komplet oversigt over den kode, der er skrevet i dette trin er angivet i eksemplet følge fremgangsmåden.

### <a name="to-create-a-base-address-for-the-service"></a>Oprette en basisadresse for tjenesten

1. I den `Main()` funktionen definitioner af poster, skal du oprette en variabel for at gemme navneområdet for projektet Service Bus. Sørg for at erstatte `yourNamespace` med navnet på navneområdet for den tjeneste, du tidligere har oprettet.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus bruger navnet på din navneområde til at oprette en entydig URI.

2. Oprette en `Uri` forekomst for basisadressen af tjenesten, som er baseret på navneområdet.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Oprette og konfigurere webværten-tjenesten

- Oprette webværten-tjenesten, ved hjælp af URI-adressen, der oprettede tidligere i dette afsnit.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Tjenesteværten for er WCF-objektet, der starter programmet host. I dette eksempel overfører dem typen host, du vil oprette (en **ImageService**), og den adresse, som du vil vise værtsprogrammet.

### <a name="to-run-the-web-service-host"></a>Køre webværten-tjenesten

1. Åbn tjenesten.

    ```
    host.Open();
    ```
    Tjenesten kører nu.

2. Få vist en meddelelse om, at tjenesten kører, og hvordan du stopper tjenesten.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Luk Tjenesteværten for, når du er færdig.

    ```
    host.Close();
    ```

## <a name="example"></a>Eksempel

I følgende eksempel indeholder servicekontrakten og implementering fra de forrige trin i selvstudiet og vært for tjenesten i et console til computeren. Samle følgende kode i en eksekverbar fil med navnet ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Kompilering af koden

Gør følgende for at køre programmet efter opbygning af løsningen:

1. Tryk på **F5**, eller gå til den eksekverbare filplacering (ImageListener\bin\Debug\ImageListener.exe) til at køre tjenesten. Huske app'en kører, når det er påkrævet, at du udfører de næste trin.

2. Kopiere og indsætte adressen fra kommandoprompten i en browser for at se billedet.

3. Når du er færdig, skal du trykke på **Enter** i kommandopromptvinduet for at lukke appen.

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet et program, der bruger Service Bus relay-tjenesten, kan du se følgende artikler for at få mere at vide om videresendes messaging:

- [Azure Service Bus arkitektonisk oversigt](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Sådan bruger du Service Bus Relay-tjenesten](service-bus-dotnet-how-to-use-relay.md)

[Azure-portalen]: https://portal.azure.com