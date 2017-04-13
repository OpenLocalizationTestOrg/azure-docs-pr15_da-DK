<properties 
    pageTitle="Hvordan du sikrer back end-tjenester ved hjælp af klienten certifikatgodkendelse i Azure API Management" 
    description="Lær at sikre back end-tjenester ved hjælp af klientcertifikat i Azure API Management." 
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

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Hvordan du sikrer back end-tjenester ved hjælp af klienten certifikatgodkendelse i Azure API Management

API administration giver mulighed for at sikre adgangen til back end-tjenesten for en API ved hjælp af klientcertifikater. Denne vejledning viser, hvordan du administrerer certifikater i portalen API publisher, og hvordan du konfigurerer en API for at bruge et certifikat til at få adgang til dens back end-tjeneste.

Du kan finde oplysninger om administration af certifikater ved hjælp af API Management REST-API [Azure API Management RESTEN API certifikat enhed][].

## <a name="prerequisites"> </a>Forudsætninger

Denne vejledning viser, hvordan du konfigurerer din API Management service forekomst for at bruge certifikatgodkendelse for klient til at få adgang til back end-tjenesten for en API. Før du følger trinnene i dette emne, skal du har din back end-tjeneste, der er konfigureret til klientcertifikat ([Hvis du vil konfigurere certifikatgodkendelse i Azure websteder, der refererer til denne artikel][]) og har adgang til certifikatet og adgangskoden til certifikatet for overførsel i portalen API Management publisher.

## <a name="step1"> </a>Overføre et klientcertifikat

For at komme i gang skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![API Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **sikkerhed** i menuen **API administration** i venstre side, og klik på **klientcertifikater**.

![Klientcertifikater][api-management-security-client-certificates]

For at overføre et nyt certifikat skal du klikke på **Overfør certifikat**.

![Overføre certifikat][api-management-upload-certificate]

Gå til dit certifikat, og derefter indtaste adgangskoden for certifikatet.

>Certifikatet, der skal være i **.pfx** -format. Selvsignerede certifikater er tilladt.

![Overføre certifikat][api-management-upload-certificate-form]

Klik på **Overfør** for at overføre certifikatet.

>Certifikat adgangskoden er blevet godkendt på nuværende tidspunkt. Hvis det er forkerte vises en fejlmeddelelse.

![Certifikat, der er overført][api-management-certificate-uploaded]

Når certifikatet, der er overført, vises den under fanen **klientcertifikater** . Hvis du har flere certifikater, lave en note for emnet, eller de sidste fire tegn i miniature, som bruges til at vælge certifikatet, når du konfigurerer en API for at bruge certifikater, som beskrevet i afsnittet følgende [konfiguration af en API til at bruge et klientcertifikat for gateway-godkendelse][] .

>Du kan deaktivere certifikat kæde validering når du bruger, for eksempel et selvsigneret certifikat ved at benytte følgende fremgangsmåde i ofte stillede spørgsmål om [element](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Slette et klientcertifikat

Hvis du vil slette et certifikat, skal du klikke på **Slet** ud for det ønskede certifikat.

![Slette certifikat][api-management-certificate-delete]

Klik på **Ja, Slet den** for at bekræfte.

![Bekræft sletning][api-management-confirm-delete]

Hvis certifikatet, der er i brug af en API, vises der en advarsel skærm. Hvis du vil slette certifikatet, der skal du først fjerne certifikatet fra en hvilken som helst API'er, der er konfigureret til at bruge den.

![Bekræft sletning][api-management-confirm-delete-policy]

## <a name="step2"> </a>Konfigurere en API for at bruge et klientcertifikat til gateway-godkendelse

Klik på **API'er** i menuen **API administration** i venstre side, klik på navnet på den ønskede API, og klik på fanen **sikkerhed** .

![API sikkerhed][api-management-api-security]

Vælg **klientcertifikater** fra rullelisten **med legitimationsoplysninger** .

![Klientcertifikater][api-management-mutual-certificates]

Vælg det ønskede certifikat fra rullelisten **klientcertifikat** . Hvis der er flere certifikater kan du se emnet eller de sidste fire tegn i miniature, som angivet i forrige afsnit for at finde ud af det rigtige certifikat.

![Vælg certifikat][api-management-select-certificate]

Klik på **Gem** for at gemme ændringen konfiguration til API.

>Ændringen træder i kraft med det samme, og kald til datahandlinger af pågældende API anvender certifikatet til at godkende på back end-serveren.

![Gemme ændringer i API][api-management-save-api]

>Når et certifikat er angivet for gateway-godkendelse til back end-tjenesten af en API, bliver en del af politikken for API og kan ses i politikeditoren.

![Certifikatpolitik for][api-management-certificate-policy]

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om andre måder til at sikre dine back end-tjeneste, såsom hemmeligt HTTP grundlæggende eller delte godkendelse, i den følgende video.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introduktion til Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance

[Azure API Management RESTEN API certifikat enhed]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Hvis du vil konfigurere certifikatgodkendelse i Azure websteder, der refererer til denne artikel]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Konfigurere en API for at bruge et klientcertifikat til gateway-godkendelse]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
