<properties 
    pageTitle="Sådan bruger du API inspektion spore kalder i Azure API Management" 
    description="Lær at spore opkald ved hjælp af API inspektion i Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Sådan bruger du API inspektion spore kalder i Azure API Management

API Administration indeholder et værktøj til API inspektion for at hjælpe dig med fejlfinding og foretage fejlfinding af din API'er. API inspektion kan bruges fra et program, og du kan også bruge direkte fra portalen udvikler. 

Ud over sporing af handlinger sporer API inspektion også [politikudtryk](https://msdn.microsoft.com/library/azure/dn910913.aspx) evaluering. En demonstration se [skyen dækker afsnit 177: mere API administrationsfunktioner](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) og hurtig frem til 21:00.

Denne vejledning indeholder en gennemgang af ved hjælp af API inspektion.

>[AZURE.NOTE] API inspektion sporinger genereres kun og gjort tilgængelige for anmodninger, der indeholder abonnement taster, der hører til [administratorkontoen](api-management-howto-create-groups.md) .

## <a name="trace-call"></a> Brug API inspektion til at spore et opkald

Hvis du vil bruge API inspektion, tilføje en **ocp-apim-sporing: SAND** anmode om sidehoved til opkaldet handling og derefter kan du hente og undersøge sporet ved hjælp af den URL-adresse, der er angivet med svarheaderen **ocp-apim-sporing-placering** . Dette kan udføres programmeringsmæssigt, og du kan også gøre direkte fra portalen udvikler.

Dette selvstudium viser, hvordan du bruger API inspektion til spor handlinger ved hjælp af grundlæggende Lommeregner API, der er konfigureret i [Administrer din første API](api-management-get-started.md) få Introduktion selvstudium. Hvis du ikke har fuldført dette selvstudium kræver det kun et øjeblik på at importere grundlæggende Lommeregner API, eller du kan bruge en anden API efter eget valg som API'EN ekko. Hver forekomst af API Management service leveres forudkonfigurerede med en ekkoet API, der kan bruges til at eksperimentere med og få mere at vide om administration af API. Ekkoet API returnerer tilbage, uanset input der sendes til den. For at bruge det, du kan aktivere en HTTP-verber samt returværdien bliver blot hvad du har sendt. 



For at komme i gang skal du klikke på **udvikler portal** i portalen Azure klassisk for din API Management-tjenesten. Handlinger kan kun kaldes direkte fra portalen udvikler, som indeholder en nem måde at få vist og teste handlinger for en API.

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

![Administration af API udvikler portal][api-management-developer-portal-menu]

Klik på **API'er** fra den øverste menu, og klik derefter på **Grundlæggende Lommeregner**.

![Ekkoet API][api-management-api]

Klik på **Prøv det** for at prøve handlingen **Tilføj to heltal** .

![Prøv det][api-management-open-console]

Behold standardnavnet parameterværdier, og vælg tasten abonnement for det produkt, du vil bruge fra **abonnement-nøgle** i rullemenuen.

Som standard i portalen udvikler **Ocp-Apim-sporing** er sidehoved allerede indstillet til **Sand**. Denne overskrift konfigurerer, uanset om der genereres en sporing.

![Send][api-management-http-get]

Klik på **Send** for at aktivere handlingen.

![Send][api-management-send-results]

I svaret bliver sidehoveder en **ocp-apim-sporing-placering** , hvor en værdi, der ligner følgende eksempel.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

Sporet kan hentes fra den angivne placering og gennemset som vist i næste trin.

## <a name="inspect-trace"> </a>Undersøge sporet

For at få vist værdierne i sporingen, kan du hente denne fil fra **ocp-apim-sporing-placering** URL-adressen. Det er en tekstfil i JSON-format og indeholder poster, der ligner følgende eksempel.

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>Næste trin

-   Se en demonstration af sporing politik udtryk i [skyen dækker afsnit 177: mere API administrationsfunktioner](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Spole frem til 21:00 for at se videoen.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 