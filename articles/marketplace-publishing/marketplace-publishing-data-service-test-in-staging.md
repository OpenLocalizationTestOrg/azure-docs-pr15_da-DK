<properties
   pageTitle="Test dit datatjeneste tilbud på Marketplace | Microsoft Azure"
   description="Forstå, hvordan du kan teste din datatjeneste tilbud på Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="testing-your-data-service-offer-in-staging"></a>Test dit datatjeneste tilbud i midlertidige

>[AZURE.IMPORTANT] **På nuværende tidspunkt vi er ikke længere er onboarding alle nye datatjeneste udgivere. Ny dataservices kan ikke få godkendt til listen.** Du kan finde flere oplysninger, hvis du har en SaaS forretningsprogram, du vil have til at publicere AppSource [her](https://appsource.microsoft.com/partners). Hvis du har en IaaS programmer eller udvikler tjeneste, du gerne vil publicere Azure Marketplace du kan finde flere oplysninger [her](https://azure.microsoft.com/marketplace/programs/certified/).

Når du har fuldført de første to trin for at [oprette kontoen Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) og [oprettelse af dine Data Service tilbyder i Udgivelsesportal](marketplace-publishing-data-service-creation.md) er du klar til at oprette dit tilbud, der er tilgængelige i Azure Marketplace. Dette emne fører dig gennem den første, mellemliggende trin kaldet "Midlertidige"

Midlertidige betyder, at implementere dit tilbud i en privat "sandkassetilstand", hvor du kan teste og bekræfte funktionaliteten før skubbe fremstilling. Tilbuddet vises arrangere samme måde som det ville gøre til en kunde, der har installeret den.

## <a name="step-1-pushing-your-offer-to-staging"></a>Trin 1. Trykke på dit tilbud om midlertidige
Trykke på dit tilbud om midlertidige, kan du teste tilbuddet, før den bliver tilgængelig for fremtidige abonnenter.  Du kan se, hvordan dit tilbud vises og fungerer for dem, at abonnere på dine data.  

  ![tegning](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Logon til [publicering af Portal](https://publish.windowsazure.com)
2.  Vælg **Data Services** i venstre navigationsvindue
3.  Vælg dit tilbud, du vil overføre til midlertidige. Du får vist skærmbilledet ovenfor.
4.  Klik på knappen **Push til midlertidige** .  
5.  Hvis der er problemer med de tilbud, der skal bruges til at være fuldført, før du trykke på til midlertidige, vises en liste, som vises.  Ret disse elementer ved at klikke på hvert element på listen. Når alle rettelser foretaget, skal du klikke på knappen **Push til midlertidige** igen.

Hvis der er ingen problemer med dit tilbud får du vist pop op-vinduet nedenfor.  

Hvis du er ikke planlægning/ikke godkendt til at overflade dit tilbud Azure-portalen (aktuelt har begrænset kapacitet), blot lukke pop op-vinduet.

Hvis du vil afprøve dine datatjeneste Azure-portalen (ud over portalen DataMarket), skal du en Azure abonnement-ID til at teste med.  Dette abonnement-ID, identificerer den konto, der er tilladt at teste dit tilbud.  

Klippe og indsætte dit abonnement-ID, og klik på markering for at fortsætte.

  ![tegning](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Disse Azure abonnementer id'er er kun påkrævet for at teste og arrangere [Azure Management Portal](https://manage.windowsazure.com). De behøver ikke at teste i Azure Marketplace.

Det næste skærmbillede, der vises, viser, at publicere finder sted ved at vise ikonet "i gang" fremhævet gul nedenfor. Skubbe til midlertidige tager mellem 10 til 15 minutter.  Hvis det tager længere tid, første opdatere din browser (Tryk på F5 i Internet Explorer).  I de sjældne tilfælde, hvor dit tilbud stadig skubber til midlertidige efter en time, skal du klikke på hyperlinket Kontakt os til at fortælle os, at der er et problem.

  ![tegning](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Når opslagsnål til midlertidige er afsluttet ikonet "i gang" vil stoppe med at flytte og status opdateres til "Faseinddelt".  Du er nu klar til at teste dit tilbud.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Trin 2. Teste din faseinddelt tilbud i DataMarket

Klik på linket efter teksten **"Se din tjeneste, der tilbyder 1t opgivet …"** for at åbne skærmbilledet, abonnement vises, når dit tilbud går til fremstilling og vises i DataMarket.

  ![tegning](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Teste eller kontrollerer hvert af de 12 elementer markeret over at sikre, at alle logoer, priser/transaktioner, tekst, billeder, dokumentation og links er korrekte og fungerer korrekt.  Dette er et godt tidspunkt at sikre, at du har angivet, når du opretter dit tilbud test værdier er blevet erstattet med faktiske værdier.

1. Tilbud-logo
2. Tilbud navn
3. Publisher navn/link til din virksomheds websted
4. Søgekategorier til dit tilbud
5. Dit tilbud support link hjælper abonnenter
6. Kontekstafhængig beskrivelse til dit tilbud
7. Tilbud plan, der viser faktureringsdetaljer
8. Oprette et link til implementeringskode
9. På eksempelbilleder, der illustrerer brug af tilbud data
10. Input/Output metadataene for hver tjeneste i tilbuddet
11. Tilbuddet fra vilkår for anvendelse
12. Eksempel på de tilbud data


Til sidst skal du markere tjenesten vil gennemgå Datamarket ved at klikke på linket "Udforske dette DATASÆT".  Der åbnes et nyt vindue i værktøjet vi kalder "Service Stifinder" så du kan få vist resultaterne af en forespørgsel med din tjeneste.  I dette vindue, kan du angive de parametre, det er nødvendigt og se resultaterne vises fra en forespørgsel til din tjeneste.   Desuden er vises URL-adressen til din forespørgsel.  

> [AZURE.NOTE] Sørg for at gennemse tekstbaseret beskrivelse af tjenesten vises øverst.  Og hvis dit tilbud består af mere end én tjeneste opkald skal du klikke på fanerne i bunden for at skifte til næste service for at gennemse og teste.



## <a name="next-step"></a>Næste trin
Hvis du har problemer med og har brug for hjælp til løsning af dem skal du kontakte [Azure Publisher Support]( http://go.microsoft.com/fwlink/?LinkId=272975).

Hvis du er tilfreds og klar til at udgive Læs dit tilbud i dokumentationen til [Godkendelse af anmodning Push til fremstilling](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Se også
- [Introduktion: Hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
