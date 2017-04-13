<properties
    pageTitle="Brug af Azure CDN med CORS | Microsoft Azure"
    description="Lær at bruge Azure indhold levering netværk (CDN) til med tværs Origin ressource deling (CORS)."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="casoper"/>
    
# <a name="using-azure-cdn-with-cors"></a>Brug af Azure CDN med CORS     

## <a name="what-is-cors"></a>Hvad er CORS?

CORS (Cross Origin Deling af ressourcer) er en HTTP-funktion, der gør det muligt for et webprogram kører under ét domæne at få adgang til ressourcer i et andet domæne. For at mindske risikoen for angreb via scripts på tværs af websteder, implementere alle moderne webbrowsere en sikkerhedsbegrænsning kendt som [samme origin politik](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Dette forhindrer en webside fra opkald API'er i et andet domæne.  CORS giver en sikker måde at tillade ét domæne (origin-domæne) til at ringe til API'er i et andet domæne.
 
## <a name="how-it-works"></a>Sådan fungerer det
1.  Browseren sender indstillinger anmodningen med en **Origin** HTTP sidehoved. Værdien af dette sidehoved er det domæne, der fungeret den overordnede side. Når en side fra https://www.contoso.com forsøger at få adgang til en brugers data i fabrikam.com-domænet, skal følgende anmodning om sidehovedet sendes til fabrikam.com: 
    
    `Origin: https://www.contoso.com`
 
2.  Serveren kan svare med følgende:
    - En **Access-kontrolelement-Tillad-Origin** sidehoved i dens svar, der angiver, hvilke origin websteder er tilladt. Eksempel:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - En fejlside, hvis serveren ikke tillader tværs origin anmodningen
    - En **Access-kontrolelement-Tillad-Origin** sidehoved med et jokertegn, der gør det muligt for alle domæner:
        
        `Access-Control-Allow-Origin: *`
 
Kompleks HTTP-anmodninger, der er en "Forhåndskontrol" anmodning færdig første til at afgøre, om det har tilladelse før afsendelse af hele anmodningen.
 
## <a name="wildcard-or-single-origin-scenarios"></a>Jokertegn eller enkelt origin scenarier

CORS på Azure CDN fungerer automatisk med ingen yderligere konfiguration, når **Access-kontrolelement-Tillad-Origin** sidehovedet er angivet til jokertegn (*) eller en enkelt origin.  CDN cachelagres første svaret og efterfølgende anmodninger om anvender den samme overskrift.
 
Hvis der allerede er foretaget anmodninger til CDN før CORS blive angivet på den origin, skal du fjerne indhold på dit slutpunkt indhold for at genindlæse indhold med **Access-kontrolelement-Tillad-Origin** sidehovedet.
 
## <a name="multiple-origin-scenarios"></a>Flere origin scenarier

Hvis du har brug at tillade en specifik liste over baggrunden skal have tilladelse til CORS, få ting lidt mere kompliceret. Problemet opstår, når CDN cachelagres **Access-kontrolelement-Tillad-Origin** overskriften for den første CORS origin.  Når en anden CORS origin opretter en efterfølgende anmodning, vil CDN served cachelagrede **Access-kontrolelement-Tillad-Origin** hovedet, som ikke passer.  Der er flere måder at løse problemet.
 
### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium fra Verizon

Den bedste måde at aktivere denne er at bruge **Azure CDN Premium fra Verizon**, som viser nogle avancerede funktioner. 
 
Du skal oprette [en regel](cdn-rules-engine.md) til at kontrollere **Origin** sidehovedet på anmodningen.  Hvis det er en gyldig origin, angive reglen **Access-kontrolelement-Tillad-Origin** sidehoved med origin indeholdt i anmodningen.  Hvis den origin, der er angivet i headeren **Origin** ikke er tilladt, bør reglen udelader **Access-kontrolelement-Tillad-Origin** hovedet som medfører, at browseren til at afvise anmodningen. 
 
Der er to måder at gøre dette med regler-program.  **Access-kontrolelement-Tillad-Origin** sidehovedet fra filens origin server er helt ignoreres i begge tilfælde, den CDN regler program administrerer helt de tilladte CORS baggrunden.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Én regulære udtryk med alle gyldige baggrunden
 
I dette tilfælde skal du oprette en regulære udtryk, der indeholder alle de baggrunden, du vil tillade: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **Azure CDN fra Verizon** bruger [Perl kompatible regulære udtryk](http://pcre.org/) som dens program for regulære udtryk.  Du kan bruge et værktøj som [normalt udtryk 101](https://regex101.com/) til at validere dine regulære udtryk.  Bemærk, at tegnet "/" er gyldige i regulære udtryk og ikke bruge escape-tegn, men slippe dette tegn betragtes som bedste fremgangsmåde og der forventes ved nogle regex validators.

Hvis det regulære udtryk svarer til, erstatter reglen **Access-kontrolelement-Tillad-Origin** sidehovedet (hvis relevant) fra origin med origin, sendes anmodningen.  Du kan også tilføje yderligere CORS overskrifter, som **Access--Tillad-metoder**.

![Eksempel på regler med regulære udtryk](./media/cdn-cors/cdn-cors-regex.png)
 
#### <a name="request-header-rule-for-each-origin"></a>Anmode om sidehoved regel for hver origin.

I stedet for almindelige udtryk, kan du oprette en separat regel for hver origin, du vil tillade brug af **Anmode om sidehoved jokertegn** [svarer til betingelse](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1)i stedet. Som med metoden regulære udtryk angiver regler programmet alene CORS overskrifterne. 
  
![Regler for eksempel uden regulære udtryk](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] I eksemplet ovenfor, brug af jokertegnet * fortæller regler-program, så det svarer til både HTTP og HTTPS.
 
### <a name="azure-cdn-standard"></a>Azure CDN Standard

På Azure CDN Standard profiler er den eneste metode til at tillade flere baggrunden uden brug af jokertegn origin at bruge [cachelagring af forespørgsel streng](cdn-query-string.md).  Du skal aktivere forespørgsel streng indstilling for CDN slutpunktet og derefter bruge en entydig forespørgselsstreng for anmodninger fra alle tilladte domæner. Dette medfører CDN cachelagring et separat objekt for hver entydige forespørgselsstreng. Denne metode er ikke ideel, men som den medfører flere kopier af den samme fil, der er cachelagret på CDN.  

