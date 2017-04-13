<properties
   pageTitle="Administrere virtuelt billedet på Azure Marketplace | Microsoft Azure"
   description="Detaljeret vejledning i at administrere dit virtuelt billede på Azure Marketplace efter indledende udgivelse."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Efter fremstilling vejledning til virtuelt tilbud i Azure Marketplace

Denne artikel forklares, hvordan du kan opdatere en direkte virtuelt tilbud i Azure Marketplace. Også fører dig gennem på processen med at tilføje en eller flere nye lagerenheder til et eksisterende tilbud og fjerne en direkte virtuelt tilbud eller SKU fra Azure Marketplace.

Når et tilbud/SKU midlertidigt på [Azure Portal](http://portal.azure.com), kan du ikke ændre de felter, der er angivet nedenfor:

- **Tilbyder id:** [Publicering portal -> virtuelle maskiner -> Vælg dit tilbud -> VM billeder under fanen -> tilbyder id]
- **SKU-id:** [Publicering portal -> virtuelle maskiner -> Vælg dit tilbud -> lagerenheder fanen -> Tilføj en anden SKU]
- **Publisher Namespace:** [Publicering portal -> virtuelle maskiner gennemgang fanen -> Fortæl os om dit firma (findes Under "Trin 2 registrere dit firma") -> -> Publisher Namespace -> Namespace]

Når tilbud/SKU er angivet i [Azure Marketplace](http://azure.microsoft.com/marketplace), kan du ikke ændre de felter, der er angivet nedenfor:

- **Tilbyder id:** [Publicering portal -> virtuelle maskiner -> Vælg dit tilbud -> VM billeder under fanen -> tilbyder id]
- **SKU-id:** [Publicering portal -> virtuelle maskiner -> Vælg dit tilbud -> lagerenheder fanen -> Tilføj en anden SKU]
- **Publisher Namespace:** [Publicering portal -> virtuelle maskiner gennemgang fanen -> -> fortælle os om dit firma (findes Under trin 2 registrere) Publisher Namespace -> Namespace]
- **Porte** [Publicering portal -> virtuelle maskiner -> Vælg dit tilbud -> VM billeder under fanen -> Åbn porte]
- **Priser ændring af viste SKU(s)**
- **Fakturering Model ændring af viste SKU(s)**
- **Fjernelse af fakturering områder af viste SKU(s)**
- **Ændre data disk antallet af viste SKU(s)**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. Sådan opdaterer du de tekniske detaljer i en anden SKU

Du kan tilføje en ny version til den angivne SKU og publicere dit tilbud ved at følge vejledningen nedenfor:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **VM billeder** i menuen i venstre side.
4. Find den SKU, du vil opdatere, i afsnittet **lagerenheder** **VM billeder** under fanen.
5. Tilføje en ny versionsnummeret for SKU, og klik på knappen **"+"** . Den nye version skal være af X.Y.Z format, hvor X, Y og Z er heltal. Versionsændringer skal kun være stigende.
6. Tilføje en delt adgang signatur URI oprettet for operativsystem Virtuelle i boksen **OS Virtuelle URL-adresse** og gemme ændringerne.

    >[AZURE.IMPORTANT] Du kan ikke øges eller sænkes data disk antallet af viste SKU. Du skal oprette en ny SKU i dette tilfælde. Se afsnittet [3. Sådan føjer du en ny SKU under et vises tilbud](#3-how-to-add-a-new-sku-under-a-live-offer) for at få detaljeret vejledning.

7. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. hvordan du opdaterer ikke-tekniske oplysninger om et tilbud eller en anden SKU

Du kan opdatere den tekniske (marketing, juridiske, understøtter, kategorier) oplysninger om din direkte tilbud eller SKU i Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 opdatere tilbud beskrivelse og logoer

Du kan opdatere tilbud detaljerne og publicere dit tilbud ved at benytte følgende fremgangsmåde:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **MARKETING** i menuen i venstre side.
4. Klik på knappen **ENGELSK (USA)** .
5. Klik på fanen **Detaljer** i menuen i venstre side. I afsnittet *Beskrivelse* af fanen **Detaljer** kan du opdaterer tilbud titlen, tilbyder oversigt, tilbyder lang oversigt og gemme ændringerne.

    >[AZURE.NOTE] Skal du tage højde for følgende mens du er ved at opdatere SKU oplysninger.
    **Angiv ikke dublerede tekst under beskrivelsen af tilbud og beskrivelsen af SKU. Angiv ikke dublerede tekst under overskriften SKU og tilbuddet længe oversigt. Angiv ikke dublerede tekst under overskriften SKU og tilbud oversigten.**

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. Du kan opdatere logoer under afsnittet *logoer* i fanen **Detaljer** . Men sikre, at logoer følger [Azure Marketplace retningslinjer](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (afsnittet trin 1: Angiv Marketplace marketing indhold -> detaljer -> Azure Marketplace Logo retningslinjer).

    >[AZURE.NOTE] Ikon for helt er valgfrit. Du kan vælge ikke at overføre et helt ikon. Men når helt ikon er overført, er der ingen bestemmelse at slette den fra udgivelse portal. Det er tilfældet, skal du følge de [helt ikonet retningslinjer](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (afsnittet trin 1: Angiv Marketplace marketing indhold -> detaljer -> yderligere retningslinjer for banneret helt logo).

7. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Se dette [link](marketplace-publishing-vm-image-test-in-staging.md)for at få detaljeret vejledning på Test dit tilbud i det midlertidige miljø.
8. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Opdatere beskrivelsen af SKU

Du kan opdatere SKU detaljerne og publicere dit tilbud ved at benytte følgende fremgangsmåde:

1. Logge på, [publicere portal](https://publish.windowsazure.com)
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **MARKETING** i menuen i venstre side.
4. Klik på knappen **ENGELSK (USA)** .
5. Klik på fanen **planer** i menuen i venstre side. Du kan opdatere SKU titel, SKU oversigt og SKU beskrivelse detaljer og gemme ændringerne under afsnittet *SKU'er* i fanen **plan** .

    >[AZURE.NOTE] Skal du tage højde for følgende mens du er ved at opdatere SKU oplysninger. **Angiv ikke dublerede tekst under beskrivelsen af tilbud og beskrivelsen af SKU. Angiv ikke dublerede tekst under den SKU titel og tilbuddet længe oversigt. Angiv ikke dublerede tekst under overskriften SKU og tilbud oversigten.**

6. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette link
7. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 ændre de eksisterende kæder eller tilføje nye links

Du kan ændre de eksisterende kæder eller tilføje nye links og derefter publicere dit tilbud ved at benytte følgende fremgangsmåde:

1. Logge på, [publicere portal](https://publish.windowsazure.com)
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **MARKETING** i menuen i venstre side.
4. Klik på knappen **ENGELSK (USA)** .
5. Klik på fanen **LINKS** fra menuen i venstre side.
6. Hvis du vil tilføje et nyt link, under *Links* afsnit klik derefter på knappen **Tilføj LINK** . Dialogboksen *"Tilføj Link"* åbnes. I denne dialogboks, kan du tilføje linket titel og URL-adressen felter og gemme ændringerne. Du kan angive et link, som indeholder oplysninger, der kan hjælpe kunderne.
7. Hvis du vil opdatere eller slette et eksisterende link, derefter vælge linket relevante og klikke på redigeringsknappen eller knappen Slet i overensstemmelse hermed.

    >[AZURE.NOTE] Sørg for, at de links, som du har angivet i dette afsnit fungerer korrekt, som disse links får valideres under din anmodning om produktionsproces.

8. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Se dette [link](marketplace-publishing-vm-image-test-in-staging.md)for at få detaljeret vejledning på Test dit tilbud i det midlertidige miljø.
9. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 ændre en eksisterende eksempelbillede eller tilføje en ny eksempelbillede

Du kan ændre en eksisterende eksempelbilleder eller tilføje nye eksempelbilleder og derefter publicere dit tilbud ved at benytte følgende fremgangsmåde:

>[AZURE.NOTE] Kun én eksempelbillede vises i [https://portal.azure.com](https://portal.azure.com).

1. Logge på, [publicere portal](https://publish.windowsazure.com)
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **MARKETING** i menuen i venstre side.
4. Klik på knappen **ENGELSK (USA)** .
5. Klik på fanen **EKSEMPELBILLEDERNE** i menuen i venstre side.
6. Hvis du vil tilføje en ny eksempelbillede, klik derefter på knappen **OVERFØR et nyt billede** under afsnittet *Eksempelbilleder* og derefter gemme ændringerne.

    >[AZURE.NOTE] Herunder et eksempelbillede er trin valgfrit.

7. Hvis du vil opdatere eller slette en eksisterende eksempelbillede og derefter finde relevante eksempel billedet, og klik derefter på knappen **Erstat billede** eller knappen Slet i overensstemmelse hermed.

8. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Se dette [link](marketplace-publishing-vm-image-test-in-staging.md)for at få detaljeret vejledning på Test dit tilbud i det midlertidige miljø.
9. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2,5 opdatere juridisk indhold

Du kan opdatere juridisk indhold og publicere dit tilbud ved at benytte følgende fremgangsmåde:

1. Logge på, [publicere portal](https://publish.windowsazure.com)
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **MARKETING** i menuen i venstre side.
4. Klik på knappen **ENGELSK (USA)** .
5. Klik på fanen **juridiske** fra menuen i venstre side. Du kan opdatere din politikker vilkår for anvendelse under afsnittet *juridiske* . Skriv eller Indsæt politikker/betingelser i tekstfeltet *Vilkår for anvendelse* og gemme ændringerne.
6. Tegngrænsen for de juridiske vilkår for anvendelse er 1.000.000 tegn.
7. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 opdatere supportoplysninger

Du kan opdatere supportoplysninger og publicere dit tilbud ved at benytte følgende fremgangsmåde:

1. Logge på, [publicere portal](https://publish.windowsazure.com)
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **SUPPORT** i menuen i venstre side.
4. Du kan opdatere kontaktoplysninger under afsnittet *Kontakte teknisk* **SUPPORT** under fanen. Disse oplysninger bruges til den interne kommunikation mellem partner og Microsoft kun.
5. Du kan opdatere Support kontaktoplysninger som **navn, mail, telefonopkald** og **URL-adresse til Support**under afsnittet *Kundesupport* i fanen **SUPPORT** . Disse oplysninger bruges til den interne kommunikation mellem partner og Microsoft kun.

    >[AZURE.NOTE] Hvis du vil give kun mail-support, skal du angive et dummy telefonnummer under afsnittet **Kundesupport** . I dette tilfælde bruges din medfølgende mail i stedet.

6. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette [link](marketplace-publishing-vm-image-test-in-staging.md)
7. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 opdatere kategorierne

Du kan opdatere sektionen kategorier til dit tilbud og publicere dit tilbud ved at benytte følgende fremgangsmåde:

1. Logge på, [publicere portal](https://publish.windowsazure.com)
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **kategorier** i menuen i venstre side.
4. Du kan opdatere kategorierne til dit tilbud og gemme ændringerne under sektionen *kategorier* . Du kan vælge op til fem kategorier til galleriet Azure Marketplace.
5. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette [link](marketplace-publishing-vm-image-test-in-staging.md)
6. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. hvordan du tilføjer en ny SKU under et vises tilbud

Du kan tilføje en ny SKU under dit live tilbud ved at følge vejledningen nedenfor:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. I menuen i venstre side, skal du klikke på fanen **lagerenheder** . Efter, klik på knappen **Tilføj A SKU**.  En ny dialogboks åbnes. Angiv et SKU id skrevet med små bogstaver. Markér afkrydsningsfeltet for få-dine-ejer billing model(BYOL), hvis du vil publicere den nye SKU med BYOL billing model. Ellers skal du fjerne markeringen i afkrydsningsfeltet for BYOL. Efter, klik på aksemærker mærket i dialogboksen for at oprette en ny SKU. Hvis du ikke vælger for BYOL fakturering modellen for den nye SKU, derefter indstilles fakturering modellen automatisk til hver time for den nye SKU. Hvis du vil aktivere 30days gratis prøveversion for hver time fakturering model, klik derefter på indstillingen "En måned" til "er en gratis prøveversion tilgængelig?". Ellers skal du vælge "Nej prøveversion". [Note: indstillingen "er en gratis prøveversion, der er tilgængelige?" vises kun, hvis du ikke har valgt BYOL i dialogboksen mens du opretter den nye SKU.]

    >[AZURE.IMPORTANT] Indstillingen "Skjule denne SKU fra Marketplace, fordi den skal altid har købt via en løsning skabelon" skal være markeret som "Ja" kun, hvis du er godkendt til publicering af en løsning skabelon tilbud i Azure Marketplace. Ellers skal denne indstilling altid være markeret som "Nej".

4. Klik på fanen **VM billeder** nu i menuen i venstre side, og find ud af den nye SKU, som du har oprettet.
5. For at konfigurere den nye SKU skal referere til trin 5 af dette [link](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) for at få vejledning.
6. For at tilføje det marketing materiale til den nye SKU skal referere til afsnittet trin 1: Angiv Marketplace marketing indhold -> detaljer -> tal 2 til 5 af dette [link](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. For at tilføje prisoplysninger for den nye SKU skal du se afsnittet 2.1. Angiv din VM priser af dette [link](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Gå til fanen **PUBLICER** når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette [link](marketplace-publishing-vm-image-test-in-staging.md)
9. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. hvordan du ændrer data disk antallet for en vises SKU

Du kan ikke øges eller sænkes data disk antallet af viste SKU. Du skal oprette en ny SKU i dette tilfælde. Se afsnittet [3. Sådan føjer du en ny SKU under et direkte tilbud](#3-how-to-add-a-new-sku-under-a-live-offer) for at få detaljeret vejledning.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. hvordan du kan slette et vises tilbud fra Azure Marketplace

Der findes forskellige aspekter, der skal være taget sig af i tilfælde af en anmodning om at fjerne et direkte tilbud. Følg nedenstående trin for at få vejledning fra supportteam fjerne et vises tilbud fra Azure Marketplace:

1.  Hæve en supportbilletter ved hjælp af dette [link](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Vælg problemtype som **"Administrere tilbud"** , og Vælg kategori som **"Ændring af et tilbud og/eller SKU allerede findes i fremstilling"**
3.  Sende anmodningen

Supportteamet vejleder dig gennem processen tilbud/SKU sletningen.

>[AZURE.NOTE] Du kan altid slette tilbuddet, mens det er i en kladdestatus (dvs., ikke findes i midlertidige eller fremstilling) ved at klikke på knappen **Slet kladde** under fanen **Oversigt** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. hvordan du kan slette en vises SKU fra Azure Marketplace

Du kan slette en vises SKU fra Azure Marketplace ved at følge vejledningen nedenfor:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **Lagerenheder** fra ruden venstre side.
4. Vælg den SKU, hvor du vil slette, og klik på knappen Slet mod SKU.
5. Når færdig, skal du gå til fanen PUBLICER i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere tilbuddet i Azure Marketplace.
6. Når tilbuddet udgives igen i Azure Marketplace, slettes SKU fra Azure Marketplace og Azure-portalen.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. hvordan du kan slette den aktuelle version af en vises SKU fra Azure Marketplace

Ved at følge nedenstående trin, kan du slette den aktuelle version af en vises SKU fra Azure Marketplace. Når processen er fuldført, vil SKU rulles tilbage til en tidligere version.

1. Log på den [publicering portal](https://publish.windowsazure.com).
2.  Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3.  Klik på fanen **VM billeder** fra ruden venstre side.
4.  Vælg den SKU, aktuelle version, du vil slette, og klik på knappen Slet mod denne version.
5.  Når færdig, skal du gå til fanen **PUBLICER** i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere tilbuddet i Azure Marketplace.
6.  Når tilbuddet udgives igen i Azure Marketplace, slettes den aktuelle version af den angivne SKU fra Azure Marketplace og Azure-portalen. SKU, der rulles tilbage til en tidligere version.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. hvordan du kan tilbageføre oversigt pris til fremstilling værdier
Jeg har ændret priser for en vises SKU (eller jeg har fjernet de fakturering områder af et vises SKU). Da den ikke understøttes i Azure Marketplace, vil jeg gendanne mine ændringer til fremstilling værdier. Hvordan jeg opnå?

Følg nedenstående trin:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **priser** i menuen i venstre side.
4. Vælg et område, hvis du vil nulstille priser under fanen priser.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. Nulstil priser for alle borekernerne i tilfælde af lagerenheder med hver time fakturering model, som de er i fremstilling for det valgte område. For lagerenheder med BYOL fakturering model, gøre SKU tilgængelig i området ved at markere afkrydsningsfeltet mod SKU under afsnittet EXTERNALLY-LICENSED (BYOL) SKU tilgængelighed (se skærmbilledet nedenfor).

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Klik nu på knappen **AUTOPRICE andre markeder baseret på priser på UNITED tilstand**.

    >[AZURE.NOTE] Knappens etiket kan være forskellige afhængigt af det område, du har valgt. Da vi har valgt USA under oprettelse af dette dokument, så knappen hedder som "Automatisk pris andre markeder, der er baseret på priser i USA" i skærmbilledet nedenfor.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. Guiden automatisk pris åbnes. Den første side viser markeringen for base marked. Foretag dine og flytte til den næste side ved at klikke på knappen **"->"** .

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Mulighed for at vælge kerner og planer vises på side 2. Vælg de ønskede planer og borekerner, og klik på "->" knappen.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Side 3 viser markeder/områder. Klik på knappen Slå alle for at markere alle områder eller manuelt Markér afkrydsningsfelterne ud for område. Klik på knappen "->" for at flytte til den næste side.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Side 4 viser kurserne. Klik på knappen Afslut for at fuldføre trinnene. Guiden nulstiller priser ud fra dit valg.

11. Nu skal du gå til fanen priser og klikke på knappen "Vis oversigt og ændringer".
Vælg "Kladde" i afsnittet "Vis Version" og "Fremstilling" i "Sammenlign med" afsnit (se skærmbilledet nedenfor). Hvis der vises ingen priser forskel, betyder det priser er blevet ændret til fremstilling værdierne korrekt.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Gå til fanen PUBLICER når du har foretaget ændringerne, og klik på knappen **PUSH til midlertidige**. Detaljeret vejledning i test dit tilbud i det midlertidige miljø i referere til dette [link](marketplace-publishing-vm-image-test-in-staging.md)
13. Når du har kontrolleret dit tilbud i midlertidige, gå til fanen PUBLICER i udgivelse portal og klik på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. hvordan du kan tilbageføre fakturering model til fremstilling værdier
Jeg har ændret af en vises SKU fakturering modellen. Da den ikke understøttes i Azure Marketplace, vil jeg gendanne mine ændringer til fremstilling værdier. Hvordan jeg opnå?

Følg nedenstående trin:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **SKU'ER** i menuen i venstre side.
4. Klik på knappen Rediger for at gendanne fakturering modellen. Der åbnes et vindue. Markér eller fjern markeringen i afkrydsningsfeltet **'fakturering og licenser er udført eksternt fra Azure (også kendt tage dine egne licens)'** i overensstemmelse hermed.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Én gang færdig, skal du henvise til svar på spørgsmål 8 i dette dokument for at vende tilbage på priser.
6. Når, gå til fanen **PUBLICER** i udgivelse portal og opslagsnål tilbud til at arrangere at teste den. Når du er færdig med test tilbuddet og derefter klikke på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. hvordan du kan tilbageføre indstilling for synlighed af en vises SKU til værdien fremstilling

Følg nedenstående trin:

1. Log på den [publicering portal](https://publish.windowsazure.com).
2. Gå til fanen **VIRTUELLE maskiner** , og vælg dit tilbud.
3. Klik på fanen **SKU'ER** i menuen i venstre side.
4. Vælg din SKU og gendanne indstillingen for synlighed af SKU til værdien fremstilling.

    ![tegning](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Når du er færdig med ændringerne og derefter klikke på knappen **Anmode om godkendelse til PUSH til fremstilling** til at publicere dit tilbud i Azure Marketplace.

## <a name="see-also"></a>Se også
- [Introduktion: Hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
- [Forstå sælgeren indsigt rapportering](marketplace-publishing-report-seller-insights.md)
- [Forstå udbetaling rapportering](marketplace-publishing-report-payout.md)
- [Hvordan du kan ændre god grund forhandler din skyen løsningsudbyder](marketplace-publishing-csp-incentive.md)
- [Foretage fejlfinding af almindelige problemer på markedet](marketplace-publishing-support-common-issues.md)
- [Få support som en udgiver](marketplace-publishing-get-publisher-support.md)
- [Oprette et VM billede i det lokale miljø](marketplace-publishing-vm-image-creation-on-premise.md)
- [Oprette en virtuel maskine, der kører Windows Azure preview-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
