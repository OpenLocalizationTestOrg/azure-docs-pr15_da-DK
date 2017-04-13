<properties
    pageTitle="Fejlfinding i forbindelse med Azure CDN slutpunkter returnere 404 status | Microsoft Azure"
    description="Fejlfinding af 404 svar fejlkoder med Azure CDN slutpunkter."
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
    ms.date="07/28/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Fejlfinding i forbindelse med CDN slutpunkter returnere 404 statusser

I denne artikel hjælper dig med at foretage fejlfinding af problemer med [CDN slutpunkter](cdn-create-new-endpoint.md) returnere 404 fejl.

Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, kan du kontakte de Azure eksperter på [MSDN Azure og forummerne stakoverløb](https://azure.microsoft.com/support/forums/). Alternativt kan du også sende en Azure support-hændelse. Gå til det [Azure supportwebsted](https://azure.microsoft.com/support/options/) , og klik på **Få Support**.

## <a name="symptom"></a>Symptom

Du har oprettet en CDN profil og et slutpunkt, men dit indhold synes ikke at være tilgængelig på CDN.  Brugere, der forsøger at få adgang til indholdet via CDN URL-adressen modtager HTTP 404 statuskoder. 

## <a name="cause"></a>Årsag

Der er flere mulige årsager, herunder:

- Filens origin er ikke synligt for CDN
- Slutpunktet er forkert konfigureret, forårsager CDN til at slå det forkerte sted
- Værten afviser host sidehovedet fra CDN
- Slutpunktet havde ikke tid at overføre i hele CDN

## <a name="troubleshooting-steps"></a>Trin til fejlfinding

> [AZURE.IMPORTANT] Når du har oprettet et CDN slutpunkt, kan det med det samme ikke bruges, som det tager tid om at overføre via CDN registrering.  For <b>Azure CDN fra Akamai</b> profiler fuldfører overførsel normalt inden for et minut.  For <b>Azure CDN fra Verizon</b> profiler overførsel fuldfører normalt inden for 90 minutter, men i nogle tilfælde kan tage længere tid.  Hvis du har fuldført trinnene i dette dokument, og du får stadig 404 svar, kan du overveje at vente et par timer at kontrollere igen, før du åbner en supportbilletter.

### <a name="check-the-origin-file"></a>Markér filen origin

Først skal vi skal kontrollere den pågældende den fil, vi vil cachelagrede er tilgængelig på vores origin og er offentligt tilgængelig.  Der er den hurtigste måde at gøre det til at åbne en browser i en i-privat eller Inkognito session og gå direkte til filen.  Bare skrive eller indsætte URL-adressen i adressefeltet og se Hvis, der resulterer i den fil, du forventer.  I dette eksempel skal jeg vil bruge en fil, jeg har i en Azure-lager-konto, der er tilgængelige på `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Som du kan se passerer den korrekt testen.

![Succes!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] Det er den hurtigste og nemmeste måde at kontrollere din fil er offentligt tilgængelig, kan nogle netværkskonfigurationer i din organisation give dig illusionen, denne fil er offentligt tilgængelig, når det er faktisk kun synlige for brugere af dit netværk (også selvom de ligger i Azure).  Hvis du har en ekstern browser, hvorfra du kan teste, som en mobil enhed, der ikke har forbindelse til din organisations netværk eller en virtuel maskine i Azure, det er bedst.

### <a name="check-the-origin-settings"></a>Kontrollér indstillingerne for origin

Nu hvor vi har bekræftet filen er offentligt tilgængelig på internettet, skal vi kontrollere vores origin indstillinger.  Gå til din CDN profil i [Azure Portal](https://portal.azure.com), og klik på det slutpunkt, du foretager fejlfinding.  Klik på origin i bladet resulterende **slutpunkt** .  

![Slutpunkt blade med origin fremhævet](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Bladet **Origin** vises. 

![Origin blade](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Origin type og hostname

Bekræft **Origin type** er korrekte, og Bekræft **Origin hostname**.  I min eksempel `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, hostname del af URL-adressen er `cdndocdemo.blob.core.windows.net`.  Dette er korrekte, som du kan se i skærmbilledet.  Feltet **Origin hostname** er til Azure-lager, Web App og skybaseret tjeneste baggrunden, en rulleliste, så vi ikke behøver at bekymre dig om stavekontrol den korrekt.  Men hvis du bruger en brugerdefineret origin, det er *absolut kritiske* din hostname er stavet forkert!

#### <a name="http-and-https-ports"></a>HTTP- og HTTPS-porte

De andre ting, du skal kontrollere her er din **HTTP** og **HTTPS-porte**.  80 og 443 er korrekte, og du kræver ingen ændringer i de fleste tilfælde.  Men hvis den oprindelige server lytter på en anden port, skal, der repræsenteres her.  Hvis du ikke er sikker på, kun se URL-adressen for origin filen.  HTTP- og HTTPS specifikationer Angiv port 80 og 443 som standard. I min URL-adresse, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, en port ikke er angivet, så antages standarden af 443 og mine indstillinger er korrekte.  

Men sig URL-adressen til origin filen, som du tidligere har kontrolleret er `http://www.contoso.com:8080/file.txt`.  Bemærk de `:8080` i slutningen af segmentet hostname.  Det fortæller browseren til at bruge port `8080` til at oprette forbindelse til webserveren på `www.contoso.com`, så du skal angive 8080 i feltet **HTTP-port** .  Det er vigtigt at være opmærksom på, at disse portindstillinger kun påvirker hvilke slutpunktet bruges til at hente oplysninger fra origin port.

> [AZURE.NOTE] **Azure CDN fra Akamai** slutpunkter tillader ikke det hele TCP portområde til baggrunden.  Du kan finde en liste over origin porte, som ikke er tilladt, [Azure CDN fra Akamai tilladte Origin porte](https://msdn.microsoft.com/library/mt757337.aspx).  
  
### <a name="check-the-endpoint-settings"></a>Kontrollér indstillingerne for slutpunkt

Klik på knappen **Konfigurer** tilbage på bladet **slutpunkt** .

![Slutpunkt blade med Konfigurer knappen fremhævet](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Det slutpunkt **Konfigurer** blade vises.

![Konfigurere blade](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoller

Kontrollér, at den protokol, der bruges af klienterne, der er markeret til **protokoller**.  Den samme protokol, der bruges af klienten bliver den, der bruges til at få adgang til origin, så det er vigtigt at origin porte, der er konfigureret korrekt i det forrige afsnit.  Slutpunktet lytter kun på standard HTTP og HTTPS portene (80 og 443), uanset origin porte.

Lad os gå tilbage til vores hypotetiske eksempel med `http://www.contoso.com:8080/file.txt`.  Som du kan huske, Contoso angivet `8080` som deres HTTP port, men lad os også antage de angivet `44300` som deres HTTPS-port.  Hvis de har oprettet et slutpunkt med navnet `contoso`, der ville være deres LEVERANDØR(er) slutpunkt hostname `contoso.azureedge.net`.  En anmodning om `http://contoso.azureedge.net/file.txt` er en HTTP-anmodning, så slutpunktet bruger HTTP på port 8080 til at hente det fra startpunkt.  En sikker anmodning over HTTPS, `https://contoso.azureedge.net/file.txt`, medfører slutpunktet til at bruge HTTPS på port 44300 når retriving filen fra startpunkt.

#### <a name="origin-host-header"></a>Origin host sidehoved

**Origin host sidehoved** er værdien fra host sidehoved sendes til origin med hver enkelt anmodning.  I de fleste tilfælde, skal det være den samme som **Origin hostname** vi bekræftet tidligere.  En forkert værdi i dette felt generelt medføre ikke 404 statusser, men der kan medføre andre 4xx statusangivelser, afhængigt af hvad origin forventer.

#### <a name="origin-path"></a>Origin sti

Endelig skal vi kontrollere vores **Origin sti**.  Som standard er tom.  Du skal kun bruge dette felt, hvis du vil begrænse omfanget af de origin hostet ressourcer, du vil gøre tilgængelige på CDN.  

I min slutpunkt, skal jeg for eksempel ville alle de ressourcer på min lagerplads konto skal være tilgængeligt, så jeg mod venstre **Origin sti** .  Det betyder, at en anmodning om at `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` resulterer i en forbindelse fra min slutpunkt til `cdndocdemo.core.windows.net` , der anmoder om `/publicblob/lorem.txt`.  På samme måde en anmodning om `https://cdndocdemo.azureedge.net/donotcache/status.png` resulterer i slutpunkt anmoder om `/donotcache/status.png` fra startpunkt.

Men hvad nu, hvis jeg ikke vil bruge CDN for hver sti på min origin?  Sig I ville kun at få vist den `publicblob` sti.  Hvis jeg angiver */publicblob* i feltet min **Origin sti** , der medfører en slutpunktet indsætte */publicblob* før hver anmodning om henvisninger til origin.  Det betyder, at anmodningen om `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` tager nu anmodning om del af URL-adressen, `/publicblob/lorem.txt`, og Føj `/publicblob` til begyndelsen. Dette resulterer i en anmodning om `/publicblob/publicblob/lorem.txt` fra startpunkt.  Hvis ikke løser denne sti til en faktisk datafil, returnerer origin en 404 status.  Den korrekte URL-adresse til at hente lorem.txt i dette eksempel faktisk ville være `https://cdndocdemo.azureedge.net/lorem.txt`.  Bemærk, at vi ikke indsætte */publicblob* stien overhovedet, fordi anmodning om del af URL-adressen er `/lorem.txt` og slutpunktet tilføjer `/publicblob`, resulterende i `/publicblob/lorem.txt` de anmodningen sendes til origin.
