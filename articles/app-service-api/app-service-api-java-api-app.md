<properties
    pageTitle="Opbygge og anvende en Java API app i Azure App Service"
    description="Lær at oprette en Java API app-pakke og installere den på Azure App Service."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Opbygge og anvende en Java API app i Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Dette selvstudium viser, hvordan du opretter en Java-program og installerer det til Azure App Service API Apps ved hjælp af [ciffer]. Vejledningen i dette selvstudium kan efterfølges på operativsystemer, der kan køre Java. Koden i dette selvstudium er oprettet ved hjælp af [Maven]. [Jax RS] bruges til at oprette RESTful tjenesten, og der genereres baseret på metadata [Swagger] specifikation ved hjælp af [Swagger Editor].

## <a name="prerequisites"></a>Forudsætninger

1. [Java Developer Kit 8] \(eller nyere)
1. [Maven] installeret på computeren udvikling
1. [Ciffer] installeret på computeren udvikling
1. Et betalt eller [gratis prøveversion] abonnement på [Microsoft Azure]
1. En HTTP testprogram som [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold API ved hjælp af Swagger.IO

Ved hjælp af swagger.io online editor, kan du angive Swagger JSON eller YAML kode, der repræsenterer strukturen i din API. Når du har området API surface designet, kan du eksportere kode for en række forskellige platforme og rammer. I næste afsnit, vil blive ændret scaffolded koden for at medtage mock funktionalitet. 

I denne demonstration begynder med Swagger JSON brødtekst, du vil indsætte i redigeringsprogrammet swagger.io, der skal bruges til at generere kode gøre brug af JAX RS til at få adgang til et REST-API slutpunkt. Derefter skal du redigere scaffolded koden for at returnere mock data simulere en indbygget oven en data brugerdata ordning REST-API.  

1. Kopier den følgende Swagger JSON-kode til din Udklipsholder:

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Gå til den [Online Swagger Editor]. Når der, klikke på menupunktet **Filer -> Indsæt JSON** .

    ![Indsæt JSON menupunkt][paste-json]

1. Indsæt kontakter listen API Swagger JSON du kopierede tidligere. 

    ![Indsætte JSON kode i Swagger][pasted-swagger]

1. Se dokumentationen sider og API oversigt gengives i editoren. 

    ![Vis Swagger genereres dokumenter][view-swagger-generated-docs]

1. Vælge menuindstillingen **generere Server-> JAX RS** til scaffold på serversiden kode, du vil redigere senere for at tilføje mock implementering. 

    ![Generere kode menupunkt][generate-code-menu-item]

    Når koden er oprettet, får du angivet en ZIP-fil til overførsel. Denne fil indeholder koden scaffolded af Swagger kode generator og alle tilknyttede opbygge scripts. Udpakke hele biblioteket til en mappe på computeren udvikling. 

## <a name="edit-the-code-to-add-api-implementation"></a>Redigere koden for at tilføje API-implementering

I dette afsnit, skal du erstatte den oprettede Swagger kode serversiden implementering med den brugerdefinerede kode. Den nye kode vil returnere en ArrayList Kontakt enheder til opkald klienten. 

1. Åbn filen *Contact.java* model, der er placeret i mappen *src/kasse/java/EY/swagger/model* ved hjælp af [Visual Studio-kode] eller foretrukne tekstredigeringsprogrammet. 

    ![Åbn kontakter modelfil][open-contact-model-file]

1. Tilføj følgende parametre til klassen **Kontakt** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Åbn filen med *ContactsApiServiceImpl.java* tjeneste implementering, der er placeret i mappen *src/main/java/EY/swagger/api/impl* ved hjælp af [Visual Studio-kode] eller foretrukne tekstredigeringsprogrammet.

    ![Åbn kontakter kode Servicefil][open-contact-service-code-file]

1. Overskrive koden i filen med denne nye kode til at føje en mock implementering til tjenesten koden. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Åbn en kommandoprompt, og Skift mappe til rodmappen i dit program.

1. Udfør følgende kommando for Maven at bygge koden og køre ved hjælp af Jetty app server lokalt. 

        mvn package jetty:run

1. Du bør se kommandovinduet afspejle, Jetty har startet din kode for port 8080. 

    ![Åbn kontakter kode Servicefil][run-jetty-war]

1. Bruge [Postman] til at foretage en anmodning om "Hent alle kontakter" API metode på api-http://localhost:8080/kontakter.

    ![Ringe op til kontaktpersoner API][calling-contacts-api]

1. Bruge [Postman] til at foretage en anmodning om "Hent specifik Kontakt" API metoden findes i http://localhost:8080/api/kontakter/2.

    ![Ringe op til kontaktpersoner API][calling-specific-contact-api]

1. Til sidst skal opbygge Java KRIG (Webarkiv)-fil ved at udføre følgende kommando for Maven i Microsoft Management console. 

        mvn package war:war

1. Når filen KRIG er oprettet, placeres i **destinationsmappen** . Gå til **destinationsmappen** , og Omdøb filen KRIG til **ROOT.war**. (Kontroller store bogstaver svarer til dette format).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Til sidst skal du udføre følgende kommandoer fra rodmappen af programmet for at oprette en **installere** mappe for at bruge til at installere filen KRIG til Azure. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicere output til Azure App Service

I dette afsnit skal du se, hvordan du opretter en ny API-App ved hjælp af portalen Azure, forberede vært Java-programmer, der API App og anvende filen nyoprettet KRIG til Azure App Service til at køre din nye API-App. 

1. Oprette en ny API-app i [Azure-portalen], ved at klikke på menupunktet **Ny -> Web + Mobile -> API app** , indtaste din app oplysninger om og derefter klikke på **Opret**.

    ![Oprette en ny API-App][create-api-app]

1. Når din API app er blevet oprettet, Åbn din app **Indstillinger** blade, og klik derefter på menupunktet **Programindstillinger** . Vælg de seneste Java-versioner fra de tilgængelige indstillinger og derefter vælge den seneste Tomcat i menuen **Web objektbeholder** , og klik derefter på **Gem**.

    ![Konfigurere Java i bladet API App][set-up-java]

1. Klik på menupunktet **installation legitimationsoplysninger** indstillinger, og angive et brugernavn og adgangskode, du vil bruge til publicering af filer til din API-App. 

    ![Angiv installation legitimationsoplysninger][deployment-credentials]

1. Klik på menupunktet **installation kilde** indstillinger. Når der, klikke på knappen **Vælg kilde** , Vælg indstillingen **Lokale ciffer lager** , og klik derefter på **OK**. Dette vil oprette et ciffer lager kører i Azure, der indeholder en tilknytning til din API-App. Hver gang du foretager kode til den *overordnede* gren i din ciffer lager publiceres din kode til din direkte aktiv forekomst af API App. 

    ![Konfigurere en ny lokal ciffer lager][select-git-repo]

1. Kopiere den nye ciffer lagringsstedet URL-adresse til Udklipsholder. Gemme, som det er vigtigt, i et øjeblik. 

    ![Konfigurere et nyt ciffer lager til din app][copy-git-repo-url]

1. Ciffer push KRIG filen til den online lager. For at gøre dette, gå til den **installere** mappe, du oprettede tidligere, så du nemt kan bekræfte koden op til lageret kører i din App-tjeneste. Når du er i konsoltræet og navigere til den mappe, hvor mappen WebApp er placeret, udstede følgende ciffer kommandoer for at starte processen og brand deaktivere en installation. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Når du udsteder **push** -anmodningen, bliver du spurgt, om en adgangskode, du oprettede tidligere for installation legitimationsoplysninger. Når du indtaster dine legitimationsoplysninger, skal du se din portal, få vist, at opdateringen blev installeret.

1. Hvis du bruger igen Postman for at ramme API nyligt installeret app'en kører i Azure App Service, får du vist, at funktionsmåden er ensartet og, nu den returnerer kontaktdata som forventet, og brug af simple kodeændringer i Swagger.io scaffolded Java-kode. 

    ![Brug af Java kontakter REST-API direkte i Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Næste trin

I denne artikel, du var i stand til at starte med en Swagger JSON-fil og nogle scaffolded Java-kode, som fås fra Swagger.io editor. Derfra ændringerne enkle og et ciffer implementere processen udløste har en funktionelle API-app, der er skrevet i Java. De næste selvstudium viser hvordan du kan [bruge API apps fra JavaScript klienter ved hjælp af CORS][App Service API CORS]. Nyere selvstudierne i serien viser, hvordan du implementere godkendelse og autorisation.

For at opbygge i dette eksempel skal du kan få mere at vide om [Lagerplads SDK til Java] bevares JSON BLOB. Eller du kan bruge [Dokument DB Java SDK] til at gemme dataene Kontakt Azure dokument DB. 

Du kan finde flere oplysninger om brug af Java i Azure, [Java Developer Center].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Azure-portalen]: https://portal.azure.com/
[Dokument DB Java SDK]: ../documentdb/documentdb-java-application.md
[gratis prøveversion]: https://azure.microsoft.com/pricing/free-trial/
[Ciffer]: http://www.git-scm.com/
[Java Developer Center]: /develop/java/
[Java Developer Kit 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Online Swagger Editor]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Lagerplads SDK til Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger Editor]: http://editor.swagger.io/
[Visual Studio-kode]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
