<properties
   pageTitle="Forstå din faktura | Microsoft Azure"
   description="Lær at læse og forstå forbrug og faktura for abonnementet Azure"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/31/2016"
   ms.author="erihur;genli"/>


# <a name="understand-your-bill-for-microsoft-azure"></a>Forstå din faktura til Microsoft Azure

> [AZURE.NOTE] Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, skal du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) for at få dit problem løses hurtigt.

Gebyrerne for Microsoft Azure abonnementer varierer afhængigt af rente plan. Nogle rente-planer, som abonnenter Visual Studio Enterprise (MPN) indeholder månedlige kredit, som du kan bruge på en hvilken som helst Azure service, der er baseret på dine behov.

Bemærk, at op til 24 timers latente brug fra din tidligere faktureringsperiode kan rapporteres i din aktuelle faktureringsperiode.

Se [Microsoft Azure Købsmuligheder siden](https://azure.microsoft.com/pricing/purchase-options/)kan finde flere oplysninger om forbrug og rente planer.

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### <a name="view-or-download-a-bill-for-microsoft-azure"></a>Få vist eller hente en faktura til Microsoft Azure:

1. Log på [Account Centers](https://account.windowsazure.com/subscriptions) med din Microsoft-Account eller organisatorisk-ID.

2. Klik på det abonnement, som du gerne vil se detaljer for og brugen.

3. Klik på **Faktureringshistorik**

    ![Oversigt - faktureringshistorik -1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)

4. Sektionen **Faktureringshistorik for** lister din sætninger til tidligere faktureringsperiode plus den aktuelle faktureret periode. Sætningen for den aktuelle periode er et skøn over gebyrer tidspunktet for den estimatet blev oprettet. Disse oplysninger er kun opdateres dagligt og kan ikke indeholde alle brugen påløbet til dato. Din månedlige faktura kan variere fra dette estimat.  

    ![Oversigt over faktureringshistorik 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Klik på **Vis aktuelle erklæring** for at få vist en estimering af gebyrer tidspunktet for den estimatet blev oprettet. Disse oplysninger er kun opdateres dagligt og kan ikke indeholde alle brugen påløbet til dato. Din månedlige faktura kan variere fra dette estimat.

    ![Oversigt over faktureringshistorik 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Oversigt over faktureringshistorik 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Klik på **Hent fakturaen** for at få vist en kopi af din tidligere faktura.

    ![Oversigt over faktureringshistorik 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)


> [AZURE.NOTE] Gebyrer, der er anført på fakturering sætninger til internationale kunder er henblik skøn kun som banker har forskellige omkostninger for konvertering satser.

Følgende er nogle eksempel-sætningerne for to forskellige tilbud, der er tilgængelige på Microsoft Azure.

 Tilbud type | Beskrivelse | Download |
 :--------- |:-------- | :-------|
Pay-As-You-Go | Betale hver måned i skatterestancer | [Eksempel på en fil](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
Anvendelsen tilbud | Brug trukket fra dit forudbetalt engagement | [Eksempel på en fil](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)

## <a name="account-information"></a>Kontooplysninger

Afsnittet firmaoplysninger identificerer relevante oplysninger om din brugen og profil.

![sidehoved](./media/billing-understand-your-bill/Header.png)

| Ord                | Beskrivelse                                                                                         |
|---------------------|-----------------------------------------------------------------------------------------------------|
| Fakturanr.         | Et entydigt faktura id til sporingsformål                                                   |
| Fakturering cyklus       | Det tidsrum, hvori brugen har fundet sted                                                       |
| Fakturadato        | Dato, fakturaen blev oprettet                                                                 |
| Betalingsmetode      | Betalingstype, der bruges på kontoen, (faktura eller kreditkort)                                   |
| Faktura til             | Microsoft Azure ydelser adresse                                                                    |
| Abonnementstilbud  | Type abonnementstilbud, der blev købt (Pay-As-You-Go, BizSpark Plus, Azure gang osv.) |
| Konto ejer mail | Den konto-mailadresse, der er registreret Microsoft Azure-konto under                      |

## <a name="understand-the-invoice-summary"></a>Forstå oversigten faktura

Sektionen **Oversigt over faktura** på faktura opsummerer transaktioner siden din seneste faktura og din aktuelle Brugsgebyrer.

![Oversigt over faktura](./media/billing-understand-your-bill/InvoiceSummary.png)

Forrige saldo, betalinger og udestående saldo sektion på faktura opsummerer transaktioner siden din seneste faktura.

| Ord                                              | Beskrivelse                                                                              |
|---------------------------------------------------|------------------------------------------------------------------------------------------|
| Forrige saldo                                  | Samlet beløb forfaldsdato fra din sidste faktura                                                 |
| Ydelser                                          | Samlede betalinger, der er anvendt på din sidste faktura                                                 |
| Udestående saldo (fra forrige faktureringscyklussen) | Eventuelle faktura justeringer (kredit eller saldi) anvendt på din konto siden din seneste faktura  |

## <a name="understand-the-current-charges"></a>Forstå de aktuelle gebyrer
Den aktuelle gebyrer sektion på faktura indeholder oplysninger om månedlige gebyrer. Linkene er organiseret i de følgende underafsnit.

| Ord          | Beskrivelse                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Brugsgebyrer | Brugsgebyrer er samlede månedlige gebyrer på et abonnement. Du er faktureret i skatterestancer for at se dine seneste måned formatet.                                                                                                                                                                                                                                                                                                                                       |
| Rabatter     | Tjenesten rabatter, der anvendes til din aktuelle faktura vil kunne ses i denne post.                                                                                                                                                                                                                                                                                                                                              |
| Justeringer   | Diverse justeringer er diverse kredit eller udestående gebyrer, der er anvendt på din aktuelle faktura. Hvis du har siden Visual Studio Enterprise med MSDN tilbud, skal du se en månedlig kredit i denne post. Hvis du annullerer dit abonnement, skal du se gebyrer for månedlige forbrug over månedlige kredit inkluderet i dit tilbud fra starten af din aktuelle faktureringsperiode til dit abonnement Annulleringsdato.|

## <a name="footer-information"></a>Oplysninger i sidefoden
![sidefod](./media/billing-understand-your-bill/footerinformation.png)

## <a name="understand-the-additional-information"></a>Forstå de yderligere oplysninger
Siden Yderligere oplysninger får du henvisninger til andre ressourcer til at forstå din faktura og links til at få vist din brugen og andre relevante oplysninger til din faktura.

![Yderligere oplysninger](./media/billing-understand-your-bill/AdditionalInformation.png)

### <a name="detailed-usage"></a>Detaljeret brugen
Et link i feltet under **Brug af detaljerede** omdirigerer dig til konto Center, hvor du kan få vist din detaljerede brugen for dette abonnement.  Der er nu to versioner kan hentes: **.csv version 1** indeholder felterne gamle navnekonventionen og forbrug og **.csv version 2** indeholder kunde fulde navne for hver af kategorierne plus ekstra felter, der kan hjælpe dig med at forstå, hvad tjenester du bruger Microsoft Azure. Bemærk, at i .csv-version 1, der er ingen Azure ressourcestyring detaljer. Azure ressourcestyring oplysninger kan findes i .csv-version 2.

### <a name="additional-information-and-useful-resources"></a>Yderligere oplysninger og nyttige ressourcer
Dette afsnit indeholder links til enkel spørgsmål om Beregn forekomst størrelser, SQL DB gebyrer og nyttige links kan hjælpe dig med svar på yderligere spørgsmål.

| Ord                 | Beskrivelse                                                                                                                            |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Solgt til              | Dette er udfyldt på forhånd med profil adressen på kontoen                                                                           |
| Betalingsvejledning | Dette afsnit er betalingsvejledning af, hvor du kan sende kontrol, tråd overførsler eller overnatning kontrol, hvis din betalingsmetode er faktura |

## <a name="understand-detailed-usage-charges"></a>Forstå detaljerede Brugsgebyrer

Som en del af vores løbende engagement for at hjælpe kunder, der er nemt at administrere deres Azure brug, har vi udvidet brugen overførselsfilen,-rapporter baseret på dine Azure services forbrug og omkostninger.  Hyperlinket Hent indeholder to versioner af filen brugen:

- **Version 1** bruger det eksisterende format

- **Version 2** indeholder flere oplysninger og opdateret kolonnenavne i sektionen daglige brugen.  

Brugsgebyrer er samlede **månedlige** gebyrer på et abonnement mindre et kredit- eller rabat. Du er faktureret i skatterestancer for at se dine seneste måned formatet.  Den øverste del af filen viser oplysninger på de tjenester, du er der faktureres under faktureringscyklussen for den forrige måned.  Ovenstående tabel viser navnene på kolonnerne for hver af .csv-version filerne.

Version 1 |  Version 2  |  Beskrivelse|
:---------------| :---------------- | --------|
Fakturering periode | Fakturering periode | Den faktureringsperiode, hvor ressourcen blev consumed.
Navn | Dokumentmåler kategori | Identificerer tjenesten på øverste niveau, denne brugen tilhører.
Type | Dokumentmåler underkategori | Azure service kan defineres nærmere efter type i denne kolonne, der kan påvirke rente.
Ressource | Dokumentmåler navn | Identificerer måleenheden for den ressource, der consumed.
Område | Dokumentmåler område | Identificerer placeringen af datacenteret for visse tjenester, baseret på datacenter placering.
SKU | SKU | Identificerer entydigt system-id for hver Azure ressource.
Enhed | Enhed | Identificerer den enhed, og som registreres tjenesten i. GB, timer, 10,000s.
Consumed | Brugte antal | Indeholder mængden af den ressource, der er brugt i de faktureringsperiode.
Inkluderet | Inkluderet antal | Indeholder mængden af den ressource, der følger gratis i din aktuelle faktureringsperiode.
Fakturerbar | Forældede antal | Hvis forbrugt beløb overstiger det beløb, der er inkluderet, vises denne kolonne forskellen. Du er faktureret for dette beløb. For Pay-As-You-Go tilbud med intet beløb, der er inkluderet med tilbuddet, vil denne total være den samme som forbrugt antal.
I anvendelsen | I anvendelsen | Indeholder de ressource gebyrer, der er ned, fra din anvendelsen beløb, der er knyttet til dit 6 eller 12 måned tilbud. Ressource gebyrer er ned, fra anvendelsen beløbet i kronologisk rækkefølge.
Valuta | Valuta | Identificerer den valuta, afspejles i din aktuelle faktureringsperiode.
Overskud | Overskud | Indeholder de ressource gebyrer, der overskrider din anvendelsen beløb, der er knyttet til dit 6 eller 12 måned tilbud.
Anvendelsen rente | Anvendelsen rente | Indeholder den anvendelsen rente, der er baseret på dit samlede anvendelsen beløb, der er knyttet til dit 6 eller 12 måned tilbud.
Rente | Rente | Rente viser den rente, du betaler per fakturerbar enhed.
Værdi | Værdi | Viser resultatet af at multiplicere kolonnen fakturerbar ved kolonnen. Hvis forbrugt beløb ikke overstiger den afstand, der er inkluderet, vil der være gratis i denne kolonne.

## <a name="analyze-daily-usage-data"></a>Analysere data om brug af daglige
Afhængigt af din brugen kan der være tusinde rækker med data om brug af daglige. Hvis du vil analysere dataene, skal du klikke på **Hent brugen** og vælge en fil med semikolonseparerede variable fil (.csv) version for at se dine daglige Brugsdata for den relevante faktureringsperiode.  Som reference, kan du hente et eksempel .csv-fil til hver version nedenfor.

 Navn | Download |
 :----------:| :-------: |
  Detaljeret brugen .csv Version 1|  [Eksempel på en fil](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
  Detaljeret brugen .csv Version 2 | [Eksempel på en fil](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



Elementerne i .csv-filen, er opdelt at få vist en liste over hvor meget af hver ressource, der blev brugt i den aktuelle faktureringsperiode.

![csv-øjebliksbillede](./media/billing-understand-your-bill/csvsnapshotportal.png)

Følgende kolonner vises oplysninger, der påvirker satserne i starten af den faktureringsperiode:

Version 1 |   Version 2   |  Beskrivelse |
:---------------| :----------------| -----|
Brug dato | Brug dato | Den dato, hvor ressourcen blev udledes.
Navn | Dokumentmåler kategori | Identificerer tjenesten på øverste niveau, denne brugen tilhører.
Ressource-GUID | Tæller-ID | Fakturerede dokumentmåler-id.  Dette kan bruges som det id, der bruges til at pris fakturering brugen.
Type | Dokumentmåler underkategori | Azure service kan defineres nærmere efter type i denne kolonne, der kan påvirke rente.
Ressource | Dokumentmåler navn | Identificerer måleenheden for den ressource, der consumed.
Område | Dokumentmåler område | Identificerer placeringen af datacenteret for visse tjenester, baseret på datacenter placering.
Enhed | Enhed | Identificerer den enhed, og som registreres tjenesten i. GB, timer, 10,000s.
Consumed | Brugte antal | Indeholder mængden af den ressource, der er brugt for den pågældende dag.
Sub område | Ressource placering | Identificerer det datacenter, hvor ressourcen kører.
Tjenesten | Anvendte Service | Denne kolonne benyttes for at spore tjenesten individuelle Azure-platformen, der ikke kan specielt identificeret i kolonnen navn. Denne tjeneste kolonne angiver, hvilke service Brug en opgavevisning sammen.
I/T. | Ressourcegruppe | _**Tilføjelse af nye kolonne.**_ Hvor udløst ressourcen kører i ressourcegruppen. Referere til [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md)
Komponent | Forekomst-ID | Id for den ressource, der kører. Id indeholder det navn, du angiver for ressourcen, når det blev oprettet.
I/T. | Mærker | _**Tilføjelse af nye kolonne.**_ Nye ressourcetyper i Azure giver dig mulighed for at mærke ressourcer. Referere til at [organisere dine Azure ressourcer med mærker](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)
Yderligere oplysninger | Yderligere oplysninger | Ekstra metadata, der er relateret til tjenesten.
Tjenesteoplysninger 1 | Tjenesteoplysninger 1 | Denne kolonne indeholder det projektnavn, tjenesten hører til på dit abonnement.
Tjenesteoplysninger 2 | Tjenesteoplysninger 2 | Dette er et ældre felt, der registrerer valgfrit tjenestens metadata.

Foruden nogle nye felter og navneændringer til CSV-Version 2, der skal standardiseres formateringen for dataene i den under felter:

- **Forekomst-ID**: id-forekomst felt repræsenterer den brugerdefinerede id for tjenesten klargjort. I øjeblikket, der er to formater, der er repræsenteret forekomst-ID: det er enten navnet på ressourcen, eller det fulde ressource-ID. Microsoft Azure services skifter for at repræsentere forekomst-ID i et standardiseret format for fuldstændige ressource-ID _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. Som services overgang til det nye format vises datafeltet forekomst-ID, ændre fra ressourcenavnet til ressource-ID. Ressource-ID er det format, der bruges af [Azure ressourcestyring API](https://msdn.microsoft.com/library/azure/dn790567.aspx) til at identificere ressourcer i et abonnement.

![InstanceId](./media/billing-understand-your-bill/instanceid.png)

- **Yderligere oplysninger**: de yderligere oplysninger kolonne i .csv-brugen Angiver tjenestens metadata. For eksempel en billedtype for en VM. I øjeblikket er en tjeneste udsender tjenestens metadata i flere kolonner: Yderligere oplysninger, Service Info1 og Service oplysninger 2. Microsoft Azure tjenester vil standardisere udsendelse tjenestens metadata i kolonnen yderligere oplysninger.  Se den under øjebliksbillede af standardiseret format:

![additionalinfo_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **Mærker**: kolonnen skal indeholde den angivne bruger ressource mærker. Koderne kan bruges til at gruppere fakturering poster. Du kan for eksempel bruge mærker til at distribuere omkostninger efter afdeling ved hjælp af tjenesten. Lær mere om [Brug af mærker for at organisere Azure ressourcer](../resource-group-using-tags.md). Tjenester, der understøtter udsendelse mærker er:  

    - Virtuelle maskiner

    - Lagring og

    - Netværk services klargjort ved hjælp af [Azure ressourcestyring API](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![mærker](./media/billing-understand-your-bill/tags.png)


## <a name="next-steps"></a>Næste trin

- [Konfigurere fakturering beskeder](../billing-set-up-alerts.md)

- [Administrere dine betalingsmetoder](../billing-how-to-change-credit-card.md)

- [Forstå gebyrer Azure Marketplace](../billing-understand-your-azure-marketplace-charges.md)

- [Azure fakturering og abonnement ofte stillede spørgsmål](../billing-subscription-faq.md)

> [AZURE.NOTE] Hvis du stadig har yderligere spørgsmål, skal du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) for at få dit problem løses hurtigt.

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->
