
<properties 
    pageTitle="Tilpasse Swashbuckle oprettede API definitioner" 
    description="Lær, hvordan du tilpasser Swagger API definitioner, der genereres af Swashbuckle for en API-app i Azure App-tjeneste." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="bradygaster" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/29/2016" 
    ms.author="rachelap"/>

# <a name="customize-swashbuckle-generated-api-definitions"></a>Tilpasse Swashbuckle oprettede API definitioner 

## <a name="overview"></a>Oversigt

I denne artikel forklares det, hvordan du tilpasser Swashbuckle for at håndtere almindelige scenarier, hvor du overveje at ændre standardfunktionen:

* Swashbuckle genererer dublerede handlingen identifikationskoder for overloads af controller metoder
* Swashbuckle antager, at den kun gyldigt svar fra en metode er HTTP 200 (OK) 
 
## <a name="customize-operation-identifier-generation"></a>Tilpasse oprettelsen af handlingen id

Swashbuckle genererer Swagger handlingen id'er ved at sammenkæde controller navnet og metode. Dette mønster opretter et problem, når du har flere overloads for en metode: Swashbuckle genererer dublerede handling-id'er, som er ugyldige Swagger JSON.

For eksempel får følgende controller kode Swashbuckle til at oprette tre Contact_Get handlingen id'er.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Du kan løse problemet manuelt ved at give metoderne entydige navne, som følgende i dette eksempel:

* Hent
* GetById
* GetPage

I stedet kan du udvide Swashbuckle for at gøre det automatisk oprette entydige handlingen id'er.

Følgende trin viser, hvordan du tilpasser Swashbuckle ved hjælp af den *SwaggerConfig.cs* -fil, der skal medtages i projektet ved projektskabelon Visual Studio API Apps Preview.  Du kan også tilpasse Swashbuckle i Web API-projekt, som du konfigurere til installation som en API-app.

1. Oprette en brugerdefineret `IOperationFilter` implementering 

    Den `IOperationFilter` interface indeholder en udvidelsesmuligheder af punkt til Swashbuckle brugere, der vil tilpasse forskellige aspekter af processen Swagger metadata. Følgende kode demonstrerer en metode til at ændre funktionsmåden id-oprettelsen af handlingen. Koden føjer parameternavne til id handlingsnavn.  

        using Swashbuckle.Swagger;
        using System.Web.Http.Description;
        
        namespace ContactsList
        {
            public class MultipleOperationsWithSameVerbFilter : IOperationFilter
            {
                public void Apply(
                    Operation operation,
                    SchemaRegistry schemaRegistry,
                    ApiDescription apiDescription)
                {
                    if (operation.parameters != null)
                    {
                        operation.operationId += "By";
                        foreach (var parm in operation.parameters)
                        {
                            operation.operationId += string.Format("{0}",parm.name);
                        }
                    }
                }
            }
        }

2. I *App_Start\SwaggerConfig.cs* fil skal ringe på `OperationFilter` metode til at give Swashbuckle at bruge den nye `IOperationFilter` implementering.

        c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

    ![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

    Filen *SwaggerConfig.cs* , der er placeret af Swashbuckle NuGet pakken indeholder mange kommenterede fra eksempler på udvidelsesmuligheder af punkter. Yderligere kommentarer vises ikke her. 

    Når du har foretaget denne ændring din `IOperationFilter` implementering bruges og får entydige handlingen id'er skal genereres.
 
    ![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
    
## <a name="allow-response-codes-other-than-200"></a>Tillad Svarkoder end 200

Swashbuckle antages det, at en HTTP 200 (OK) svar er det *kun* gyldige svar fra en Web API metode som standard. I nogle tilfælde vil du måske returnere andre Svarkoder uden at forårsage klienten til at hæve en undtagelse.  Den følgende Web API-kode demonstrerer eksempelvis et scenarie, hvor du vil klienten til at acceptere en 200 eller en 404 som gyldige svar.

    [ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

I dette scenarie skal angiver den Swagger, Swashbuckle opretter som standard kun én gyldige HTTP-statuskode, HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Da Visual Studio benytter definitionen Swagger API til at generere kode for klienten, opretter klienten kode, der viser en undtagelse for eventuelle svar end en HTTP 200. Nedenstående kode er fra en C#-klient genereret for denne eksempel Web API metode.

    if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle er der to metoder til tilpasning af listen over forventede HTTP Svarkoder, der oprettes ved hjælp af XML-kommentarer eller `SwaggerResponse` attributter. Attributten er nemmere, men det er kun tilgængelig i Swashbuckle 5.1.5 eller senere. Skabelonen API Apps preview nyt projekt i Visual Studio 2013 indeholder Swashbuckle version 5.0.0, så hvis du har brugt skabelonen og ikke ønsker at opdatere Swashbuckle, kun er at bruge XML-kommentarer. 

### <a name="customize-expected-response-codes-using-xml-comments"></a>Tilpasse forventede Svarkoder ved hjælp af XML-kommentarer

Brug denne metode til at angive Svarkoder, hvis din Swashbuckle version er ældre end 5.1.5.

1. Først skal du føje XML-dokumentationskommentarer over de metoder, du vil angive HTTP-svar koderne for. Tage stikprøvernes Web API ville-handling ovenstående og anvende XML-dokumentationen til den resultere i kode som i følgende eksempel. 

        /// <summary>
        /// Returns the specified contact.
        /// </summary>
        /// <param name="id">The ID of the contact.</param>
        /// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
        /// <response code="200">OK</response>
        /// <response code="404">Not Found</response>
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
        
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
        
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }

1. Tilføje instruktioner i filen *SwaggerConfig.cs* til at dirigere Swashbuckle at gøre brug af XML dokumentationsfil.

    * Åbn *SwaggerConfig.cs* og Opret en metode på klassen *SwaggerConfig* til at angive stien til dokumentation XML-fil. 

            private static string GetXmlCommentsPath()
            {
                return string.Format(@"{0}\XmlComments.xml", 
                    System.AppDomain.CurrentDomain.BaseDirectory);
            }

    * Rul ned i filen *SwaggerConfig.cs* , indtil du ser den kommenterede fra linje af kode, der ligner skærmbilledet nedenfor. 

        ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
    
    * Fjern kommentar fra på linjen for at aktivere XML-kommentarerne behandling ved oprettelse af Swagger. 
    
        ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
    
1. Gå til projektets egenskaber for at oprette XML-dokumentation-fil, og Aktivér dokumentation XML-fil som vist i skærmbilledet nedenfor. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png) 

Når du har udført disse trin, vil Swagger JSON genereret Swashbuckle afspejle HTTP svar koderne, du har angivet i XML-kommentarer. Skærmbilledet nedenfor viser denne nye JSON-data. 

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Når du bruger Visual Studio til at gendanne Klientkoden til REST-API, accepterer C#-kode både HTTP OK og ikke fundet status-koder uden at oprette en undtagelse, så din arket kode for at tage beslutninger om, hvordan du håndterer returnering af en null post med en kontaktperson. 

        if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
        {
            HttpOperationException<object> ex = new HttpOperationException<object>();
            ex.Request = httpRequest;
            ex.Response = httpResponse;
            ex.Body = null;
            if (shouldTrace)
            {
                ServiceClientTracing.Error(invocationId, ex);
            }
                throw ex;
        }

Koden for denne demonstration kan findes i [denne GitHub-lager](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse). Sammen med Web API er markeret med XML-dokumentationskommentarer et projekt i Console-program, der indeholder en genererede klient til denne API. 

### <a name="customize-expected-response-codes-using-the-swaggerresponse-attribute"></a>Tilpasse forventede Svarkoder ved hjælp af attributten SwaggerResponse

Attributten [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) er tilgængelige i Swashbuckle 5.1.5 eller nyere. I tilfælde af, at du har en tidligere version i dit projekt, starter denne sektion med et beskrive, hvordan du opdaterer pakken Swashbuckle NuGet, så du kan bruge denne attribut.

1. Højreklik på projektet Web API i **Solution Explorer**, og klik på **Administrer NuGet pakker**. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Klik på knappen *Opdater* ud for pakken *Swashbuckle* NuGet. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Føje *SwaggerResponse* attributter til Web API handling metoder, du vil angive gyldige HTTP Svarkoder. 

        [SwaggerResponse(HttpStatusCode.OK)]
        [SwaggerResponse(HttpStatusCode.NotFound)]
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();

            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }

2. Tilføje en `using` -sætningen for den attribut navneområde:

        using Swashbuckle.Swagger.Annotations;
        
1. Gå til */swagger/docs/v1* URL-adressen på dit projekt, og de forskellige HTTP Svarkoder vil være synlig i den Swagger JSON. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Koden for denne demonstration kan findes i [denne GitHub-lager](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Sammen med Web API er dekoreret med attributten *SwaggerResponse* et projekt i Console-program, der indeholder en genererede klient til denne API. 

## <a name="next-steps"></a>Næste trin

I denne artikel er vist, hvordan at tilpasse den måde, Swashbuckle genererer handlingen id'er og gyldigt Svarkoder. Du kan finde flere oplysninger [Swashbuckle på GitHub](https://github.com/domaindrivendev/Swashbuckle).
 
