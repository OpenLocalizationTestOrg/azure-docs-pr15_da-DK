<properties
    pageTitle="Deling (CORS) Support i tværs Origin ressource | Microsoft Azure"
    description="Lær, hvordan du aktiverer CORS Support til Microsoft Azure-lager-tjenester."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Tværs Origin ressourcedeling (CORS) understøttelse af tjenesterne Azure-lager

Begynder med version 2013-08-15, understøtter Azure lagerplads tjenesterne tværs Origin ressource deling (CORS) for Blob, tabel, kø og filer. CORS er en HTTP-funktion, der gør det muligt for et webprogram kører under ét domæne at få adgang til ressourcer i et andet domæne. Webbrowsere implementere en sikkerhedsbegrænsning kendt som [samme origin politik](http://www.w3.org/Security/wiki/Same_Origin_Policy) , der forhindrer en webside fra opkald API'er i et andet domæne CORS giver en sikker måde at tillade ét domæne (origin-domæne) til at ringe til API'er i et andet domæne. Se [CORS specifikation](http://www.w3.org/TR/cors/) få mere at vide om CORS.

Du kan angive CORS regler individuelt for hver af tjenesterne lagerplads, ved at ringe til [Angive egenskaber for Blob tjenesten](https://msdn.microsoft.com/library/hh452235.aspx), [Angive egenskaber for kø tjenesten](https://msdn.microsoft.com/library/hh452232.aspx)og [Angive egenskaber for tabel tjeneste](https://msdn.microsoft.com/library/hh452240.aspx). Når du indstiller CORS reglerne for tjenesten, skal derefter en korrekt godkendte anmodninger mod tjenesten fra et andet domæne evalueres for at bestemme, om det er tilladt ifølge de regler, du har angivet.

>[AZURE.NOTE] Bemærk, at CORS ikke er en godkendelsesmetoden. Alle anmodninger mod en lagerplads ressource, når CORS er aktiveret skal enten have et stort godkendelse signatur, eller skal gøres mod en offentlige ressource.

## <a name="understanding-cors-requests"></a>Forstå CORS anmodninger

En anmodning om CORS fra et domæne, der origin kan bestå af to separate anmodninger:

- En forhåndskontrol anmodning, som forespørger de CORS begrænsninger, som følge af tjenesten. Forhåndskontrol anmodningen er påkrævet, medmindre metoden anmodning om en [enkelt metode](http://www.w3.org/TR/cors/), hvilket betyder, at få, afsnit eller INDLÆG.

- Den faktiske anmodning foretaget forhold til den ønskede ressource.

### <a name="preflight-request"></a>Forhåndskontrol anmodning

Forhåndskontrol anmodning om forespørgsler på CORS begrænsninger, der er oprettet for tjenesten storage af ejeren af kontoen. Webbrowseren (eller andre brugeragent) sender en anmodning om en indstillinger, som indeholder den anmodning om sidehoveder, metode og origin domæne. Tjenesten storage evaluerer den ønskede handling, der er baseret på en forudkonfigurerede sæt CORS regler, der angiver, hvilke origin domæner, anmodning om metoder og anmodning om sidehoveder kan angives en faktisk ønsker mod en lagerplads ressource.

Hvis CORS er aktiveret for tjenesten, og der er en CORS regel, der svarer til Forhåndskontrol anmodningen, tjenesten svarer med statuskode 200 (OK) og omfatter påkrævet adgangskontrol til overskrifterne i svaret.

Hvis CORS ikke er aktiveret for tjenesten eller ingen CORS regel svarer til Forhåndskontrol anmodningen, reagerer tjenesten med statuskode 403 (forbudt).

Hvis anmodningen indstillinger ikke indeholder nødvendige CORS overskrifterne (Origin og Access-kontrolelement-anmodning-metoden overskrifterne), reagerer tjenesten med statuskode 400 (forkert anmodning).

Bemærk, at en forhåndskontrol anmodning evalueres mod tjenesten (Blob, kø og tabel) og ikke mod den ønskede ressource. Kontoejeren skal har aktiveret CORS som en del af egenskaberne for tjenesten konto for at anmodningen kan udføres.

### <a name="actual-request"></a>Faktisk anmodning

Når besvares Forhåndskontrol anmodningen og svaret returneres, sender browseren faktisk anmodningen mod lagerplads ressourcen. Browseren kan nægte den faktiske anmodning med det samme, hvis Forhåndskontrol anmodningen er afvist.

Faktisk anmodningen behandles som normal kaldet mod tjenesten storage. Tilstedeværelsen af overskriften Origin angiver, at anmodningen er en anmodning om CORS og tjenesten skal kontrollere de tilsvarende CORS regler. Hvis der findes en værdi, føjes til svaret adgangskontrol til sidehoveder og sendes tilbage til klienten. Hvis der ikke findes en værdi, returneres CORS adgangskontrol overskrifterne ikke.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Aktivere CORS for tjenesterne, Azure-lager

CORS regler er angivet på niveauet for tjenesten, så du behøver at aktivere eller deaktivere CORS for hver tjeneste (Blob, kø og tabel) separat. CORS er som standard deaktiveret for hver tjeneste. Hvis du vil aktivere CORS, skal du indstille egenskaberne for relevante tjenesten ved hjælp af version 2013-08-15 eller nyere, og Føj CORS regler til egenskaberne for tjenesten. Få mere at vide om, hvordan du aktiverer eller deaktiverer CORS til en tjeneste og hvordan du kan angive CORS regler, skal du se [Angive egenskaber for Blob tjenesten](https://msdn.microsoft.com/library/hh452235.aspx), [Angive egenskaber for kø tjenesten](https://msdn.microsoft.com/library/hh452232.aspx)og [Angive egenskaber for tabel tjeneste](https://msdn.microsoft.com/library/hh452240.aspx).

Her er et eksempel på en enkelt CORS regel, skal angives ved hjælp af handlingen Angiv Service egenskaber:

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Hvert element, der er omfattet af CORS reglen er beskrevet nedenfor:

- **AllowedOrigins**: origin domænerne, der har tilladelse til at foretage en anmodning om mod tjenesten storage via CORS. Origin domænet er det domæne, som anmodningen stammer fra. Bemærk, at origin skal små bogstaver med de produkter, der bruger alder sender til tjenesten. Du kan også bruge jokertegnet ' *' at give alle origin domæner til at anmode via CORS. I det foregående eksempel kan domæner [http://www.contoso.com](http://www.contoso.com) og [http://www.fabrikam.com](http://www.fabrikam.com) gøre anmodninger på tjenesten ved hjælp af CORS.

- **AllowedMethods**: metoder (HTTP-anmodning om verber), der kan benytte origin domænet for en CORS anmodning. I det foregående eksempel må kun få vist og læg anmodninger.

- **AllowedHeaders**: anmodning overskrifterne, origin domænet kan angiver på CORS anmodningen. Alle metadata sidehoveder, der starter med x-ms-metadata, x-ms-metakoden-målet, og x-ms-metakoden-abc er tilladt i det foregående eksempel. Bemærk, at Jokertegnet ' *' angiver, at en hvilken som helst sidehoved, der begynder med det angivne præfiks er tilladt.

- **ExposedHeaders**: overskrifterne svar, der kan sendes i svaret CORS anmodningen, og der vises af browseren til anmodning om udsteder. I eksemplet ovenfor er browseren bedt om at få vist et sidehoved, der begynder med x-ms-metakoden.

- **MaxAgeInSeconds**: maksimum klokkeslættet, en browser skal cache INDSTILLINGERNE for forhåndskontrol anmode om.

Azure-lager services til at understøtte angive præfikset sidehoveder til både **AllowedHeaders** og **ExposedHeaders** elementer. Hvis du vil tillade en kategori af sidehoveder, kan du angive et almindelige præfiks til den pågældende kategori. For eksempel, der angiver *x-ms-metakoden** som et prefixed sidehoved opretter en regel, der opfylder alle overskrifter, der starter med x-ms-metakoden.

Følgende begrænsninger gælder for CORS regler:

- Du kan angive op til fem CORS regler per lagringstjeneste (Blob, tabel og kø).
- Den maksimale størrelse på alle CORS regler indstillinger på anmodningen, med undtagelse af XML-koder, må ikke overstige 2 KB.
- Længden af en tilladte sidehoved, udsatte sidehoved eller tilladte origin må ikke overstige 256 tegn.
- Tilladte sidehoveder og udsatte sidehoveder kan være enten:
  - Ordret overskrifterne, hvor den nøjagtige overskriftsnavnet leveres som **x-ms-metakoden-behandles**. Op til 64 ordret sidehoveder kan angives på anmodningen.
  - Præfikset sidehoveder, hvor et præfiks i sidehovedet leveres som **x-ms-metadata***. Angive et præfiks på denne måde giver eller viser en hvilken som helst kolonneoverskrift, der begynder med det angivne præfiks. Op til to præfikset sidehoveder kan angives på anmodningen.
- De metoder (eller HTTP-verber), som angivet i elementet **AllowedMethods** skal overholde de metoder, der understøttes af Azure lagringstjeneste API'er. Understøttede metoder er Slet, få, afsnit, FLET, INDLÆG, indstillinger og læg.

## <a name="understanding-cors-rule-evaluation-logic"></a>Forstå CORS regel evaluering logik

Når en lagringstjeneste modtager en forhåndskontrol eller faktiske anmodning, evalueres det pågældende anmodning, der er baseret på de CORS regler, du har oprettet for tjenesten via operationen angive egenskaber for tjenesten. CORS regler evalueres i den rækkefølge, hvori de blev angivet i anmodningsteksten af handlingen Angiv egenskaber for tjenesten.

CORS regler evalueres som følger:

1. Først skal tjekkes origin domænet for din anmodning mod domæner er angivet for elementet **AllowedOrigins** . Hvis origin domænet er inkluderet på listen, eller alle domæner, der er tilladt med Jokertegnet ' *', derefter regler evaluering fortsætter. Hvis origin domænet ikke er inkluderet, mislykkes anmodningen.

2. Metode (eller HTTP verber) for din anmodning tjekkes ud, mod metoderne i elementet **AllowedMethods** . Hvis metoden, der er medtaget på listen, fortsætter regler evaluering; Hvis ikke, mislykkes anmodningen.

3. Hvis anmodningen svarer til en regel i sit origin domæne, og måde, vælges denne regel for processen anmodningen og ingen yderligere regler evalueres. Før anmodningen kan lykkes, men kontrolleres alle overskrifter, der er angivet i anmodningen mod overskrifterne er angivet i elementet **AllowedHeaders** . Hvis overskrifterne sendes ikke stemmer overens tilladte overskrifterne, mislykkes anmodningen.

Da reglerne behandles i den rækkefølge, de er til stede i anmodningsteksten, anbefaler bedste fremgangsmåder, at du angiver de mest restriktiv regler i forhold til baggrunden først på listen, så disse evalueres først. Angiv regler, der er mindre restriktiv – for eksempel en regel, der tillader alle baggrunden – i slutningen af listen.

### <a name="example--cors-rules-evaluation"></a>Eksempel – CORS regler beregning

I følgende eksempel viser en delvis anmodningsteksten for en handling til at angive CORS regler for lagerplads tjenesterne. Se [Angive egenskaber for Blob tjenesten](https://msdn.microsoft.com/library/hh452235.aspx), [Angive egenskaber for kø tjenesten](https://msdn.microsoft.com/library/hh452232.aspx)og [Angive egenskaber for tabel Service](https://msdn.microsoft.com/library/hh452240.aspx) få at vide om bygning af anmodningen.

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

Derefter skal du overveje følgende CORS anmodningerne:

Anmode om||| Svar||
---|---|---|---|---
**Metode** |**Origin** |**Anmode om sidehoveder** |**Regel overens** |**Resultat**
**LÆG** | http://www.contoso.com |x-ms-blob-indholdstype | Første regel |Succes
**HENT** | http://www.contoso.com| x-ms-blob-indholdstype | Anden regel |Succes
**HENT** | http://www.contoso.com| x-ms-blob-indholdstype | Anden regel | Fejl

Den første anmodning svarer til den første regel – origin domænet stemmer overens med de tilladte baggrunden, metoden svarer til de tilladte metoder og overskrift stemmer overens med tilladte overskrifterne – og så lykkes.

Anden anmodningen stemmer ikke overens med den første regel, fordi metoden, der ikke svarer til de tilladte metoder. Det, men matcher den anden regel, så det lykkes.

Tredje anmodningen svarer til den anden regel i dets origin domæne og metode, så ingen yderligere regler evalueres. Dog er *x-ms-klient-anmodning-id sidehoved* ikke tilladt i den anden regel, så anmodningen mislykkes trods, semantik tredje reglens ville have gjort den kan udføres.

>[AZURE.NOTE] Selvom i dette eksempel vises en mindre restriktiv regel før en mere restriktiv, er den bedste fremgangsmåde generelt til at få vist de mest restriktiv regler først.

## <a name="understanding-how-the-vary-header-is-set"></a>Forstå, hvordan varierer sidehovedet er indstillet

*Varierer* sidehovedet er et standard HTTP/1.1 sidehoved bestående af et sæt felter i brevhoveder i anmodningen, der anbefales, at browseren eller bruger agent om de kriterier, der blev valgt af serveren at behandle anmodningen. Overskriften *varierer* bruges mest til cachelagring af proxyer, browsere og CDNs, som bruges til at finde ud af, hvor svaret skal gemmes i cachen. Yderligere oplysninger finder du specifikation for [varierer sidehoved](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Når browseren eller en anden brugeragent cachelagres svar fra en CORS anmodning, cachelagrede origin domænet tilladte udgangspunkt. Når et andet domæne problemer samme anmodning om en ressource lagerplads, mens cachen er aktiv, henter brugeragenten cachelagrede origin domænet. Domænet på andet stemmer ikke overens cachelagrede domænet, så anmodningen mislykkes, når den ellers vil det lykkes. I visse tilfælde angiver Azure-lager varierer sidehovedet til **Origin** til Fortæl brugeragent til at sende efterfølgende CORS anmodningen til tjenesten, når anmoder om domænet er forskellig fra den cachelagrede origin.

Azure-lager indstiller *varierer* sidehovedet til **Origin** for faktisk få/hoved-anmodninger i følgende tilfælde:

- Når anmodningen origin stemmer nøjagtigt overens med den tilladte origin defineret af en regel for CORS. Hvis du vil være en nøjagtig match, CORS reglen må ikke indeholde et jokertegn ' * ' tegn.

- Der er ingen regel, der svarer til anmodning om origin, men CORS er aktiveret for tjenesten lagerplads.

I de tilfælde, hvor en anmodning om få/dyr svarer til en CORS regel, der gør det muligt for alle baggrunden angiver svaret, at alle baggrunden er tilladt, og brugeren agent cachen Tillad efterfølgende anmodninger fra en hvilken som helst origin domæne, mens cachen er aktiv.

Bemærk, at anmodninger ved hjælp af metoder end GET/dyr, lagerplads tjenesterne ikke kan angive varierer sidehoved, da svar på disse metoder ikke er cachelagret af brugeragenter.

Den følgende tabel angiver, hvordan Azure lagerplads vil svare på Hent/dyr anmodninger baseret på de tidligere nævnte tilfælde:

Anmode om|Indstilling af kontoen og resultatet af evalueringen af en regel|||Svar|||
---|---|---|---|---|---|---|---|---
**Origin sidehoved findes på anmodning** | **CORS regler, der er angivet for denne tjeneste** | **Findes sammenholdelsespolitik regel, der gør det muligt for alle baggrunden (*)** | **Findes tilsvarende regel for nøjagtige origin match** | **Svar, der indeholder varierer sidehoved, der er angivet til Origin** | **Svar, der indeholder Access-kontrolelement-tilladt-Origin: "*"** | **Svar, der indeholder Access-kontrolelement-vises-sidehoveder**
Nej|Nej|Nej|Nej|Nej|Nej|Nej
Nej|Ja|Nej|Nej|Ja|Nej|Nej
Nej|Ja|Ja|Nej|Nej|Ja|Ja
Ja|Nej|Nej|Nej|Nej|Nej|Nej
Ja|Ja|Nej|Ja|Ja|Nej|Ja
Ja|Ja|Nej|Nej|Ja|Nej|Nej
Ja|Ja|Ja|Nej|Nej|Ja|Ja


## <a name="billing-for-cors-requests"></a>Fakturering CORS anmodninger om

Vellykket Forhåndskontrol anmodninger er faktureret, hvis du har aktiveret CORS på grund af lagerplads tjenesterne til din konto (ved at ringe til [Angive egenskaber for Blob tjenesten](https://msdn.microsoft.com/library/hh452235.aspx), [Angive egenskaber for kø tjenesten](https://msdn.microsoft.com/library/hh452232.aspx)eller [Angive egenskaber for tabel Service](https://msdn.microsoft.com/library/hh452240.aspx)). Overvej at angive elementet **MaxAgeInSeconds** i CORS regler til en stor værdi, så brugeragenten cachelagres anmodningen for at minimere gebyrer.

Mislykkes Forhåndskontrol anmodninger vil ikke blive faktureret.

## <a name="next-steps"></a>Næste trin

[Angive egenskaber for Blob-tjenesten](https://msdn.microsoft.com/library/hh452235.aspx)

[Angive egenskaber for kø Service](https://msdn.microsoft.com/library/hh452232.aspx)

[Angive egenskaber for tabel-tjenesten](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C tværs Origin ressourcedeling specifikation](http://www.w3.org/TR/cors/)
