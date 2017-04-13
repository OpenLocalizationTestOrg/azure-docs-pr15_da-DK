<properties
   pageTitle="Installere dit tilbud til Azure Marketplace | Microsoft Azure"
   description="Få mere at vide om, og gennemgå vejledningen for at installere dit tilbud – virtuelt billede udvikler service datatjeneste, og – til Azure Marketplace."
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
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Installere dit tilbud til Azure Marketplace
Når du er tilfreds med dit tilbud (det vil sige, du har testet kundescenarier, marketing indhold, osv.), og du er klar til at starte, anmode om **Push fremstilling** under fanen **Publicer** .  

1. De fire trin under GENNEMGANGEN siden i udgivelse portal skal være færdige og grøn. Sørg for, at der følges følgende retningslinjer for virtuelt tilbud.

    ![tegning][img-pubportal-walkthru-checked]

2. Vælg fanen **Publicer** på listen i venstre side.

    ![tegning][img-pubportal-menu-publish]

3. Klik på knappen **anmode om godkendelse til at overføre til fremstilling**. Når der foretages anmodningen, godkendelse teamet udfører en endeligt gennemsyn, og derefter dit tilbud vil være tilgængelig i Azure Marketplace.

    ![tegning][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] Når du klikker på knappen godkendelse til at overføre til fremstilling, udføres følgende trin i tilfælde af virtuelle maskiner, bag scenen. Du kan få vist status for hvert trin under fanen PUBLICER i udgivelse portal. Du skal tjekke denne side til almindelig interval (indtil status viser "Listen") for eventuelle manglende oplysninger, som skal rettelse fra din ende.

> - I første omgang går fremstilling anmodningen til den gruppe, certificering der validere den virtuelle harddisk. Men hvis du er ved at opdatere din allerede vises tilbud og anmodningen har fået kun marketing ændring, derefter trinnet certificering er ignoreret.
> - På næste trin komme anmodningen til den gruppe, indhold validering der bekræfte marketing indholdet af tilbuddet.
> - Hvis ovenstående trin er fuldført, skal godkendes tilbuddet i fremstilling. På nuværende tidspunkt status bliver "vises" i portalen publicering. Men denne "Listen" status ikke indikerer, at processen er fuldført. Følgende trin skal være fuldført, før tilbuddet er tilgængelig i Azure Marketplace.
> - Når tilbuddet er godkendt i fremstilling i ovenstående trin, begynde replikering af tilbuddet på tværs af alle Azure datacentre. Det normalt tager 24-48hours for gentagelse til at fuldføre, men du kan gå op til en uge, afhængigt af størrelsen på den virtuelle harddisk. Hvis du er ved at opdatere din allerede vises tilbud, og det har fået kun marketing ændring, derefter er replikering dog hurtigere.
> - Når replikering er fuldført, derefter bliver tilbuddet tilgængelig i Azure Marketplace.

> Du kan altid slette tilbuddet, mens det er i en **kladde** status (det vil sige aldrig **Push til midlertidige** eller **Push fremstilling**). Klik på knappen **Slet kladde** nederst på siden for at slette en kladde under fanen **Oversigt** .


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Fremstilling Tjekliste for alle virtuelt tilbud

- Sørg for, at du er en Microsoft Azure certificeret partner
- Under fanen lagerenheder skal indstillingen "skjule denne SKU fra Marketplace fordi det skal altid har købt via en løsning skabelon" markeres som Ja kun, hvis SKU er en del af en løsning skabelon. I alle andre tilfælde skal altid være markeret denne indstilling som Nej.
- Husk: Du skal ikke ændrer SKU synlighed indstillingen når SKU, der vises. Vi understøtter ikke denne funktion.
- Sørg for, at logoer overholde de Azure Marketplace logo retningslinjer, der er angivet under.
- Beskrivelse af tilbud og SKU ikke bør være det samme.
- SKUS titel og tilbyder lang oversigt ikke bør være det samme.
- SKU titel og tilbyder oversigt ikke bør være det samme.
- SKU titler må ikke være identiske for et tilbud med flere lagerenheder.

**Retningslinjer for Azure Marketplace-logo**

- Azure designet har en simpel farvepalet. Holde antallet af primær og sekundær farver på dit logo lave.
- Temafarver af portalen Azure er hvide og sort. Derfor undgå at bruge disse farver som baggrundsfarven på dine logoer. Bruge nogle farve, der ville få din logoer fremtrædende i portalen Azure. Vi anbefaler enkel primære farver. Hvis du bruger gennemsigtig baggrund, og Sørg for, at logo/teksten ikke er hvid eller sort.
- Brug ikke en gradueret baggrund på logoet.
- Undgå at placere tekst, selv din virksomhed eller brand navn på logoet.
- Udseendet af dit logo skal være 'flad' og skal undgå gradueringer.
- Logoet skal ikke strækkes.

**Yderligere retningslinjer for helt logo:**

- Helt logoet er valgfrit. Udgiveren kan vælge ikke at overføre et helt logo. **Men én gang overførte ikonet helt ikke kan slettes fra udgivelse portal. På det pågældende tidspunkt Følg partner Azure Marketplace retningslinjerne for helt ikoner mere tilbuddet ikke vil blive godkendt til fremstilling.**
- Publisher visningsnavnet, SKU titel og længe oversigt tilbuddet vises i hvide skriftfarve. Du skal derfor undgå at have en hvilken som helst lys farve i baggrunden af ikonet helt. Sort, hvid og gennemsigtig baggrund er ikke tilladt for helt ikoner.
- Udgiveren Vis navn, SKU titel, tilbuddet længe oversigt og knappen Opret er integreret fra et program i helt logoet, når tilbuddet går vises. Du skal ikke angive noget tekst, mens du er ved at designe helt logoet. Lad tomt område på højre fordi teksten (dvs. Publisher Vis navn, SKU titlen, tilbuddet længe oversigt) der skal inkluderes fra et program ved os over der. Den tomme plads til teksten skal være 415 x 100 til højre (og den er forskudt ved 370px fra venstre).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Yderligere fremstilling Tjekliste til allerede vises virtuel maskine tilbyder

- Kontroller, om der allerede er et tilbud med det samme tilbud navn i virksomheden. Hvis det er tilfældet, skal du tilføje en ny version af SKU i eksisterende tilbuddet i stedet for at oprette et nyt dublerede tilbud.
- Datadisk skal ikke skifter du mellem de to versioner af den samme SKU.
- Azure Marketplace understøtter ikke priser ændring af den angivne Lagerenheder, som det påvirker fakturering for de eksisterende kunder. Sørg for, at du ikke ændrer priser for de angivne SKU'er i de områder, hvor SKU er tilgængelig. Du kan tilføje nye lagerenheder eller tilføje nye områder til en eksisterende SKU.


## <a name="next-steps"></a>Næste trin
Når tilbuddet går direkte, kan du teste kundescenarier for at kontrollere, at alle aftaler og funktionalitet fungerer korrekt i produktionsmiljø som testet og godkendt i det midlertidige miljø.

## <a name="see-also"></a>Se også
- [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
