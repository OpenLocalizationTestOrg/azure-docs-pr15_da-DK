<properties 
    pageTitle="Send personlige meddelelse med Azure Mobile aftale" 
    description="Hvordan du kan sende personlige meddelelser ved at medtage brugerprofiloplysninger i meddelelserne, som deres navne"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Tilpasse beskeder ved at medtage brugernavn

I din efterspørgsel gøre beskeder mere tiltalende ud for din app-brugere, bør du overveje at tilpasse dem. En effektiv metode består af selektivt ved hjælp af brugernavne app til at løse meddelelser til at gøre dem mere personlig. En lille advarsel her - du skal løse tilføje brugernavne til meddelelserne grundigt fordi Hvis overforbrug af denne strategi derefter den kan komme på tværs af som lumske for nogle app-brugere. Du skal også sørge for, at du lade brugeren vælge i til at give disse personlige oplysninger for dig fuld samtykke i mobilappen før du begynder at bruge dette. 

Teknisk set med Azure Mobile aftale, kan du udføre tilpasse meddelelserne ved at følge nedenstående trin, hvor vi vil bruge dette scenario til at medtage brugernavn i meddelelserne. Du vil bruge som repræsenterer App-oplysninger, eller hvis værdier enten blev overført af SDK'er mærker integreret i Mobile-App eller via API'er. Disse App-Infos eller mærker kan derefter bruges:

1. for at målrette meddelelser til bestemte brugere, der er baseret på værdierne i App-oplysninger eller 
2. som pladsholdere i de meddelelser, som bliver udskiftet med værdier, der er specifikke for enhed/brugeren under afsendelse af meddelelser til den pågældende enhed. 

> [AZURE.IMPORTANT] Bemærk, at hastigheden for at sende beskeder får vist en reduktion på grund af denne yderligere behandling af erstatter app-info værdier med hver beskeder. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registrere App-oplysninger i portalen Mobile aftale

1) Du starte med at registrere App oplysninger eller mærker i portalen Azure. Gå til **Indstillinger** -> **mærke (App-Info)** til dette.  

![][1]  

2) Klik på **Nyt mærke (app-info)** og navn som *brugernavn* og typen som en *streng* , og klik på **Send**. 

![][2]

3) Der vises til sidst i denne app-oplysninger, der er registreret som følger:

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Sende App-oplysninger fra klient-SDK

Vi bruger her i Universal til Windows app-eksempel, men tilsvarende metoder findes for vores andre SDK'er også. 

Hvis du har en metode i mobilappen, hvor du får profiloplysninger fra brugeren, som deres navne sandsynligvis efter godkendelse af dem, du ringer `SendAppInfo` metode her og udfylde værdien af den `user_name` app oplysninger, du har registreret tidligere i back-end med Mobile aftale tjeneste. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Send personlige meddelelser

Nu er du klar til at sende meddelelser ved hjælp af denne **brugernavn**. 

1) Gå til Mobile aftale Portal på fanen **kommer til** at oprette en meddelelse, og du kan bruge denne pladsholder i følgende format overalt i overskriften meddelelse eller brødteksten. 

![][4]  

> [AZURE.NOTE] Alle brugere, hvor brugernavn app oplysninger ikke er angivet, vil ikke får en meddelelse. Hvis du kører meddelelse kampagnens i test-tilstand, og hvis du ikke har app-info angive derefter vi sender '?' tegn erstatte pladsholderen. 

2) Når Mobile aftale vælger en enhed til at sende meddelelsen, og klik derefter det ser ud på denne app-info og Erstat værdien i pladsholderen.  
Eksempelvis hvis vi har angivet `str = "Scott"` for en bruger end enheden registrering er knyttet til app-oplysninger i **brugernavn = Søren** for denne bruger og denne bruger får vist en forældet opslagsnål appmeddelelse i følgende format. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

