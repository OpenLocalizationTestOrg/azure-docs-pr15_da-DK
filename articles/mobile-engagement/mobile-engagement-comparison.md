<properties
    pageTitle="Sammenligne Azure Mobile aftale med andre lignende Azure tjenester"
    description="Sammenligne Azure Mobile aftale med andre lignende Azure services - HockeyApp, AppInsights, meddelelse om Hubs"
    services="mobile-engagement"
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Sammenligne Azure Mobile aftale med andre lignende Azure tjenester

Listen over tjenester, som Microsoft Azure udvide nogensinde, og nogle gange du undrer dig over, hvordan er Azure Mobile aftale forskellig fra denne tjeneste, som du lige har læse eller høre om. I denne artikel forsøger at fjerne forvirring og omdirigerer dig til at vælge Azure Mobile aftale, når det er mest passende for din brugen. 
 
Azure Mobile aftale er en tjeneste rettet specifikt **for digital du/CMOs** , men kunne bruges af en **mobilapp ejer eller publisher** som ønsker at øge forbrug, opbevaring og monetization af deres mobilapps. 

*Det er en software som en tjeneste (SaaS) bruger aftale platform, der indeholder databaserede indsigt i appforbrug, realtid bruger segmentering, og gør det muligt for konteksten opmærksom pushmeddelelser og i app'en messaging.* 

Opdele denne definition, har vi følgende vigtigste karakteristika som også fremhæver dens entydig værdi alles behov:

1.  **Konteksten opmærksom pushmeddelelser og i app-beskeder**
        
    Dette er core fokus af produktet - udføre målrettede og tilpassede pushmeddelelser. Og for at dette kan ske, vi indsamler omfattende funktionsmæssige analytics-data. 

2.  **Databaserede indsigt i appforbrug**

    Vi giver dig flere platforme SDK'er at indsamle funktionsmæssige analyser om app-brugere. Bemærk ord funktionsmæssige analyser (over ydeevne analytics), fordi vi fokusere på hvordan app-brugere ved hjælp af appen. Vi indsamler grundlæggende ydeevne analytics-data om fejl, nedbrud osv, men det vil sige ikke core fokus i produktet. 

3.  **Realtid bruger segmentering**

    Når du har indsamlet app brugernes funktionsmæssige analytics-data, vi giver dig mulighed for målgruppen målgruppen baseret på forskellige parametre og indsamles data til gør det muligt at køre målrettede opslagsnål kampagner. 

4.  **Software som-en-tjenesten (SaaS):**

    Vi har en separat fra administrationsportalen Azure, som er udviklet til at interagere og få vist omfattende funktionsmæssige analytics om app-brugere og køre opslagsnål marketingkampagner portal. Produktet er rettet for at hjælpe dig i gang på ingen tid!   
 
Her er, hvordan vi sammenligne dem med andre tilsyneladende lignende Azure tilbud - note, i artiklen ikke gøre en sammenligning af niveau, men tager et scenarie med flere baseret fremgangsmåde for at definere, hvilket produkt der fungerer bedst med dette sæt af graduering i hånden:
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) er den Microsoft mobil DevOps løsning. Med det, kan du distribuere builds til beta-tester, indsamle nedbrud data og få feedback fra brugere. Det kan også integreres med Visual Studio Team Services aktivere nemt build installationer og integration af arbejde element. 
    
    Der er fokus her på DevOps og indsamle ydeevne analytics-data om apps til mobil. Du kan ender med integrering af begge HockeyApps og Mobile aftale i din app og, der er ikke usædvanlige, fordi selvom der findes nogle overlapning i de indsamlede data, core fokus på produkterne, der er anderledes, og de gør det nemmere i forskellige mål for dig.  

2.  [Programmet indsigt](../application-insights/app-insights-overview.md) Hvis din mobilapp har en serversiden, derefter skal du bruge programmet indsigt til at overvåge web serversiden af din app, men du skal bruge HockeyApp for klient side-apps til mobil. 

3.  [Meddelelse om Hubs](https://azure.microsoft.com/services/notification-hubs/) udbyder lette i mening, at du ikke har brug for en integreret i mobilappen SDK og du kan have fuld kontrol over din mobile-app, og det giver mulighed for at sende pushmeddelelser med grundlæggende funktioner til mærkning. Dette er velegnet til en hvilken som helst app ejer, der er mindre om målretning og mere om at sende/kommunikere oplysninger med det samme for app brugerne (udsendelse til en stor population). 

    Bemærk fokuset på at sende forrygende hurtig meddelelser med grundlæggende segmentering egenskab. 

Lad os nogle scenarier:

1.  Tim er en del af digitale marketing produktteamet i Adventure Works store som publicerer mobilapps til brugere. Tims mål er at sikre, at de brugere, der henter deres mobilapps fortsætte med at bruge den og ofte. Denne ikke kun øger et mærke vedhæfte med app-brugere, men også øges monetization, når app-brugerne foretager køb ved hjælp af mobilappen. I dette skal Tim sende målrettede beskeder til app-brugere, som de være nyttigt at opfordre dem til at åbne appen og vende tilbage til det ofte. Her finder Tim vil Mobile aftale nyttige. 

2.  Joann er en del af produktteamet udvikling af apps til mobil på Adventure Works og søger efter et produkt logge oplysninger om går ned, undtagelser, og få ydeevne telemetri fra en app. Her finder Joann vil HockeyApp nyttige. Joann kan integrere både HockeyApp for sin udvikler fokuseret scenarier og Azure Mobile aftale for Tim i samme appen for at få bedst af begge. 

3.  Robin er en del af produktteamet udvikling af apps til mobil på Contoso nyheder Netværks- og alle hun er at sende bryde nyheder beskeder for alle brugere uden meget segmentering, så snart nyheder påmindelsen udløses. Dette er hvor Robin finder meddelelse Hubs nyttige. I dette scenarie skal du kan dog, som mobilappen bliver større, der er et krav kan du gøre meget bedre segmentering og få mere at vide om app-brugerens funktionsmåde. Robin nødt til at evaluere Azure Mobile aftale på nuværende tidspunkt. 
 
Hvis du vil opsummere, formålet med Mobile aftale, der er er ikke bare for at indsamle analytics - det er ikke "endnu en anden Analytics produkt fra Microsoft". Det er om at sende målrettede pushmeddelelser og for denne målretning vi indsamler funktionsmæssige analytics-data, men stadig fokus på at sende pushmeddelelser som gør mest mening for app-brugere, så den ikke kommer på tværs af som spam. 

Du kan finde flere oplysninger - nærmere på denne [korte video](mobile-engagement-overview.md) om Mobile aftale i en nøddeskal. 

