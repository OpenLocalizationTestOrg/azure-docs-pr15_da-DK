<properties
    pageTitle="Begrænse adgangen til dit Azure CDN indhold efter land | Microsoft Azure"
    description="Lær at begrænse adgangen til Azure CDN indholdet ved hjælp af funktionen geografisk-filtrering."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="akucer"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="Lichard"/>

#<a name="restrict-access-to-your-content-by-country---akamai"></a>Begrænse adgangen til dit indhold efter land - Akamai

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai Standard](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Oversigt

Når en bruger anmoder om dit indhold, som standard, vises indholdet uanset hvor brugeren har foretaget denne anmodning fra. I nogle tilfælde kan du vil begrænse adgangen til dit indhold efter land. Dette emne forklares det, hvordan du bruger funktionen **Geografisk-filtrering** for at konfigurere tjenesten til at tillade eller blokere adgang efter land.

> [AZURE.IMPORTANT] Verizon og Akamai produkterne har de samme funktioner geografisk-filtrering, men brugergrænsefladen varierer. Dette dokument beskrives grænsefladen til **Azure CDN Standard fra Akamai**. Se [begrænse adgangen til dit indhold efter land - Verizon](cdn-restrict-access-by-country.md)geografisk-filtrering med **Azure CDN Standard/Premium fra Verizon**.

Finde oplysninger om overvejelser, der gælder for konfiguration af denne type begrænsning, i afsnittet [overvejelser i forbindelse med](cdn-restrict-access-by-country.md#considerations) i slutningen af emnet.  

![Land filtrering](./media/cdn-filtering/cdn-country-filtering-akamai.png)

##<a name="step-1-define-the-directory-path"></a>Trin 1: Definere stien til den mappe

Vælg din slutpunkt i portalen, og find fanen geografisk-filtrering på navigationslinjen til venstre til at finde denne funktion.

Når du konfigurerer et land filter, skal du angive den relative sti til den placering, som brugerne skal have eller nægtet adgang. Du kan anvende filtrering af geografisk til alle dine filer med "/" eller markeret mapper ved at angive directory stier "/ billeder /". Du kan også anvende geografisk-filtrering på en enkelt fil ved at angive fil, og udelade den efterfølgende skråstreg "/ pictures/city.png".

Eksempel directory sti filter:

    /                                 
    /Photos/
    /Photos/Strasbourg/
    /Photos/Strasbourg/city.png

##<a name="step-2-define-the-action-block-or-allow"></a>Trin 2: Definer handlingen: blokere eller tillade

**Bloker:** Brugere fra de angivne lande adgang ikke til aktiver, der anmodes om fra denne rekursiv sti. Hvis ingen andre land filtreringsindstillingerne er konfigureret for den nye placering, skal derefter alle andre brugere have adgang.

**Tillad:** Kun brugere fra de angivne lande skal have adgang til aktiver, der anmodes om fra denne rekursiv sti.

##<a name="step-3-define-the-countries"></a>Trin 3: Definere landene

Vælg de lande, du vil blokere eller tillade for stien. Du kan finde yderligere oplysninger finder [Azure CDN fra Akamai landekoder](https://msdn.microsoft.com/library/mt761717.aspx).

For eksempel filtrerer reglen for blokering af /Photos/Strasbourg/filer herunder:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Landekoder

Funktionen **Geografisk filtrering** bruger landekoder til at definere de lande, hvorfra en anmodning om der er tilladt eller blokeret for en sikker mappe. Du kan finde landekoderne i [Azure CDN fra Akamai landekoder](https://msdn.microsoft.com/library/mt761717.aspx). 

##<a id="considerations"></a>Overvejelser i forbindelse med

- Der kan gå til et par minutter, før ændringer i dit land filtrering konfiguration kan træde i kraft.
- Denne funktion understøtter ikke jokertegn (for eksempel ' *').
- Filtrering af geografisk konfigurationen er knyttet til den relative sti bliver gælder for denne sti.
- Kun én regel kan anvendes på den samme relative sti (du ikke kan oprette flere land filtre, der peger på den samme relative sti. En mappe kan dog have flere land filtre. Dette er på grund af hvilke rekursiv land filtre. En undermappe i en tidligere konfigurerede mappe kan med andre ord tildeles som et andet land filter.

