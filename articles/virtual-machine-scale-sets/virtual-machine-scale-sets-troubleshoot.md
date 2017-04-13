<properties
    pageTitle="Fejlfinding i forbindelse med Autoskalering med virtuelt skala sæt | Microsoft Azure"
    description="Foretage fejlfinding af Autoskalering med virtuelt skala datasæt. Forstå typiske problemer og hvordan du kan løse problemerne."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Fejlfinding i forbindelse med Autoskalering med virtuelt skala datasæt

**Problem** – du har oprettet en Autoskalering infrastruktur i Azure ressourcestyring ved hjælp af VM skala sæt – f.eks ved at anvende en skabelon sådan ud: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – du har defineret skala regler, og det fungerer fint, bortset fra at uanset hvor meget du placerer på FOS indlæsning det ikke Autoskalering.

## <a name="troubleshooting-steps"></a>Trin til fejlfinding

Nogle ting, du skal overveje, omfatter:

- Hvor mange kerner har hver VM, og du indlæser hver kerne?
 Ovenstående eksempel Azure Hurtig start til skabelonen har et do_work.php script, som indlæser en enkelt kerne. Hvis du bruger en VM, der er større end en enkelt kerne VM størrelse som Standard_A1 eller D1 og derefter skal du køre denne belastning flere gange. Se, hvor mange processorkerner der bruges dine FOS ved at gennemgå [størrelser til Windows virtuelle maskiner i Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Hvor mange FOS i det VM skala sæt, laver arbejdet på hver enkelt?

    En skala ud begivenhed vil kun finde sted, når gennemsnitlige CPU på tværs af **alle** FOS i et sæt skala overskrider grænseværdien, over tid interne defineret i Autoskalering regler.

- Du går glip af enhver skala begivenheder?

    Markér audit logger på portalen Azure for skala begivenheder. Måske der opstod en skala op og en skala ned som blev mistede. Du kan filtrere efter "Skala"..

    ![Overvågningslogge][audit]

- Er din skala i og skala ud tærskler tilstrækkeligt forskellige?

    Antag, at du angiver en regel til at skalere ud, når gennemsnitlige CPU er større end 50% over 5 minutter, og vil skalere i når gennemsnitlige CPU er mindre end 50%. Dette vil forårsage problemer med "flapping", når CPU-brug er tæt på denne grænse med skala handlinger konstant øges og formindske størrelsen på sæt. På grund af dette, vil Autoskalering tjenesten forsøge at forhindre "vingerne", som kan forekomme, som ikke skalering. Derfor Sørg for, at din skala ud og skalering i tærskler er tilstrækkeligt forskellige tillade nogle mellemrum mellem skalering.

- Du skrive din egen JSON-skabelon?

    Det er nemt at foretage fejl, så du kan starte med en skabelon, som den, over hvilken er dokumenterede kan arbejde, og foretag small trinvise ændringer. 

- Kan du manuelt skalere ind eller ud?

    Prøv at geninstallere den VM skala angive ressource med en anden "kapacitet" indstilling for at ændre antallet af FOS manuelt. Et på eksempelskabelon du gør dette, er her: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – du skal muligvis redigere skabelonen for at sikre, at den har den samme maskine størrelse, som din skala angive bruger. Hvis du korrekt kan ændre antallet af FOS manuelt, så du ved, problemet, er isolerede til Autoskalering.

- Kontrollere din Microsoft.Compute/virtualMachineScaleSet og Microsoft.Insights ressourcer i [Azure ressource Explorer](https://resources.azure.com/)

    Dette er en absolut nødvendigt fejlfindingsværktøj, kan du se status for dine Azure ressourcestyring ressourcer. Klik på dit abonnement, og se på den ressourcegruppe, du foretager fejlfinding. Se på det VM skala sæt du oprettede under Beregn ressource udbyder og Kontrollér visningen forekomst, som viser du tilstanden for en installation. Kan også se visningen forekomst af FOS i det VM skala sæt. Derefter gå til provideren Microsoft.Insights ressource og kontrollere reglerne, der Autoskalering ser godt ud.

- Er filtypenavnet diagnosticering arbejde og udsender ydelsesdata?

    __Opdater:__ Azure Autoskalering er blevet forbedret, hvis du vil bruge en værten baseret målepunkter rørledning som ikke længere kræver filtypenavnet diagnosticering skal være installeret. Det betyder, at den næste par afsnit gælder ikke længere, hvis du opretter et Autoskalering program ved hjælp af den nye rørledning. Eksempler på Azure skabeloner, som er blevet konverteret til brug host pipeline: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    Brug af værten baseret målepunkter for Autoskalering er bedre af følgende årsager:

    - Færre bevægelige dele som uden diagnosticering filtypenavn skal være installeret.
    - Nemmere skabeloner. Føje lige indsigt Autoskalering regler til en eksisterende skala sæt skabelon.
    - Mere pålidelig rapportering og hurtigere lancering af nyt FOS.

    Kun grundene til kan du vil fortsætte med at bruge filtypenavnet diagnosticering ville være, hvis du har brug for Hukommelsesdiagnosticering rapportering/skalering. Host baseret målepunkter rapportere ikke hukommelse.

    Med dette for øje, kun gennemføre resten af denne artikel, hvis du stadig bruger diagnosticering udvidelser til din Autoskalering.

    Autoskalering i Azure ressourcestyring kan arbejde (men ikke længere skal) ved hjælp af en VM lokalnummer kaldet filtypenavnet diagnosticering. Det udsender ydelsesdata til en lagerplads-konto, du definerer i skabelonen. Disse data sammenlægges derefter af tjenesten Azure skærm.

    Hvis tjenesten indsigt ikke kan læse data fra FOS, er det meningen kunne sende en mail – for eksempel, hvis FOS blev ned, så Hold din mail (den, du har angivet, når du opretter Azure-konto).

    Du kan også gå og kig på dataene dig selv. Se på kontoen Azure-lager ved hjælp af en sky Stifinder. For eksempel på brug af [Visual Studio skyen Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), log på, og vælg Azure abonnementet du bruger, og der refereres til diagnosticering lagerplads kontonavnet i diagnosticering lokalnummer definitionen i skabelonen installation..

    ![Skyen Explorer][explorer]

    Her får du vist en gruppe af tabeller, hvor dataene fra hver VM bliver gemt. Tage Linux og CPU-metrik som et eksempel, se på de seneste rækker. Visual Studio skyen explorer understøtter et forespørgselssprog, så du kan køre en forespørgsel som "tidsstempel BT datetime'2016-02-02T21:20:00Z'" at sikre, at du får vist de seneste hændelser (forudsætter klokkeslæt er i UTC). Indeholder de data, du ser i, der svarer til reglerne, der skala du konfigurerer? I eksemplet herunder startes CPU for maskine 20 øget til 100% over de sidste 5 minutter..

    ![Lagerplads tabeller][tables]

    Hvis dataene ikke er der, derefter indebærer det problemet, der er med filtypenavnet diagnosticering kører i FOS. Hvis dataene er der, betyder det, at der er enten et problem med skala regler eller med tjenesten indsigt. Kontrollere [Status for Azure](https://azure.microsoft.com/status/).

    Når du har været følge disse trin, hvis du stadig har problemer med Autoskalering du kunne Prøv de forskellige fora på [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)eller [stable overløb](http://stackoverflow.com/questions/tagged/azure)eller logger et supportopkald. Vær klar til at dele skabelonen og en oversigt over ydelsesdata.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
