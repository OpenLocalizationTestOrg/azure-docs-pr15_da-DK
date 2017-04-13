<properties
    pageTitle="OAUTH sikkerhed i SaaS forbindelser og API Apps | Azure"
    description="Læs om OAUTH sikkerhed i forbindelser og API Apps i Azure App Service; microservices arkitektur; saas"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>Få mere at vide om OAUTH sikkerhed i SaaS forbindelser

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Mange af softwaren som en tjeneste (SaaS)-forbindelser som Facebook, Twitter, DropBox og så videre brugerne skal godkende ved hjælp af OAUTH-protokollen.  Når du bruger disse SaaS forbindelser fra logik Apps, giver vi en forenklet brugeroplevelsen, hvor du klikker på "Godkend" i logik Apps designer. Når du **godkende**, du bliver bedt om at logge ind (hvis det ikke allerede) og give tilladelse til at oprette forbindelse til tjenesten SaaS på dine vegne. Når du give samtykke og Godkend, kan dine logik Apps derefter få adgang til disse SaaS tjenester.

## <a name="create-your-own-saas-app"></a>Oprette din egen SaaS-app
Denne forenklet oplevelse er muligt, fordi vi tidligere har oprettet og registreret vores program i disse SaaS tjenester.  I visse tilfælde vil du måske at registrere og bruge dit eget program.  Det er nødvendigt, når du vil bruge disse SaaS forbindelser i din brugerdefinerede programmer, f.eks. I dette eksempel bruges DropBox forbindelsen, men processen er det samme for alle forbindelser, der er afhænger af OAUTH.

Du kan bruge dit eget program i stedet for ved hjælp af standardprogrammet, som vi giver dig selv i forbindelse med logik Apps. Hvis knappen "Godkend" ikke til at oprette forbindelse, kan du prøve at oprette din egen app. I følgende afsnit beskrives disse trin for Twitter forbindelsen:

1. Åbn Twitter-forbindelsen i portalen Azure preview. Gå til **Gennemse** > **API Apps**. Vælg Twitter-forbindelsen:  
    ![][1]

2. Vælg **Indstillinger** > **godkendelse**:  
    ![][2]

3. Kopiér værdien **Omdirigere URI** :  
    ![][3]

4. Gå til [Twitter-](http://apps.twitter.com) og **oprette en ny App**. Indsæt værdien **Omdirigere URI** kopieret fra Twitter-forbindelsen i egenskaben **Tilbagekald URL-adresse** : ![][4]  
5. Når din Twitter-app er oprettet, skal du vælge **nøgle og Access Tokens**. Kopiere disse værdier.
6. Indsæt disse værdier i egenskaberne **Klient-ID** og **Klienten hemmeligt** i godkendelsesindstillinger din Twitter forbindelsen:   
    ![][5]  
7. Gemme dine indstillinger for forbindelsen.  

Nu skal du kunne bruge forbindelsen fra logik Apps. Når du bruger denne forbindelse fra logik Apps, bruger dit program i stedet for til standardprogrammet.  

> [AZURE.NOTE] Hvis du tidligere har godkendt en app, kan du skal godkende igen på app.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png
