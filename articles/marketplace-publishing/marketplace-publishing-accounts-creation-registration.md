<properties
   pageTitle="Oprette og registrere kontoen publisher | Microsoft Azure"
   description="Instruktioner til at oprette en Microsoft Developer-konto, så efter godkendelse, kan du sælge forskellige tilbyder typer på Azure Marketplace."
   services="Azure Marketplace"
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
   ms.date="08/18/2016"
   ms.author="hascipio"/>

# <a name="create-a-microsoft-developer-account"></a>Oprette en Microsoft Developer-konto
I denne artikel fører dig gennem oprettelsen af det er nødvendigt og registreringsprocessen at blive en godkendt Microsoft Developer på Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Opret en Microsoft-konto
Hvis du vil starte publiceringsprocessen, skal du oprette en Microsoft-konto. Denne konto bruges til at registrere i både **Microsoft Developer Center** og **Azure Udgivelsesportal**. Du skal have kun én Microsoft-konto til Azure Marketplace-tilbud. Den må ikke være specifikke for tjenester eller tilbud.

Den adresse, der danner brugernavnet skal være på dit domæne og styres af din IT-teamet. Alle de publicering relaterede aktiviteter skal udføres via denne konto.

  >[AZURE.WARNING] Ord som **"Azure"** og **"Microsoft"** understøttes ikke for Microsoft-kontoregistrering. Undgå at bruge disse ord for at fuldføre oprettelse af en konto og registreringsprocessen.

### <a name="instructions"></a>Vejledning

1. Oprette en distributionsliste (DL) eller sikkerhedsgruppe (SA) inden for virksomhedens domæne. Ved hjælp af en DL gør det muligt for flere personer til at modtage besked via mail, der er vigtige for rapportering af udbetaling oplysninger. Det sikrer også, at ejerskabet af Microsoft-kontoen kan overføres og ikke er knyttet til en enkelt person.
Følg vejledningen nedenfor.

    1. Føje teamet onboarding til DL.
    2. Sikre, at DL/salgs aktive mailadresse og er i stand til at modtage mails, fordi betalingen, oplysninger om skat og rapportering er distribueres via denne konto.
    3. Det anbefales at bruge noget i retning af marketplace@partnercompany.com som mailadressen til DL/salgs.

2. Åbn en ny Chrome Inkognito eller Internet Explorer InPrivate-browsersession for at sikre, at du ikke er logget på en eksisterende konto.
3. Registrere DL oprettede i trin 1 som en Microsoft-konto ved hjælp af linket [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Følg vejledningen nedenfor.

    1. Under registrering af din konto som en Microsoft-konto, skal du angive et gyldigt telefonnummer til systemet for at sende en konto verifikationskoden som en tekstmeddelelse eller et automatiseret opkald.
    2. Under registrering af din konto som en Microsoft-konto, skal du angive en gyldig mail-id for at modtage en automatiseret mail til konto bekræftelse.

4. Kontrollér den mailadresse, der er sendt til DL.
5. Du er nu klar til at bruge den nye Microsoft-konto i Microsoft Developer Center.

## <a name="2-create-your-microsoft-developer-center-account"></a>2. Opret kontoen Microsoft Developer Center
Microsoft Developer Center bruges til at registrere firmaoplysninger én gang. Registrantens side skal være en gyldig repræsentant på firmaet, og de skal angive deres personlige oplysninger som en metode til at validere deres identitet. Den person, der registrerer skal bruge en Microsoft-konto, som er delt for virksomheden, **og den samme konto skal bruges i portalen Azure publicering.** Du skal kontrollere for at sikre at din virksomhed ikke har en Microsoft Developer Center konto allerede, før du forsøger at oprette en. Under processen skal vi indsamle adresse firmaoplysninger, bank kontooplysninger, og meget moms oplysninger. Dette er typisk opnås fra økonomi eller business-kontakter.

> [AZURE.IMPORTANT] For at gå videre gennem de forskellige faser af oprettelse af tilbud og installation skal du udføre følgende udvikler profil komponenter.


| Udvikler profil | Starte kladde | Midlertidige | Publicere gratis og løsning skabelon | Publicere kommercielle |
|----|----|----|----|----|
|Firmaets registrering | Skal have | Skal have | Skal have | Skal have |
|Moms profil-ID | Valgfri | Valgfri | Valgfri | Skal have |
|Bankkonto | Valgfri | Valgfri | Valgfri | Skal have |


> [AZURE.NOTE] Få dine egne licens (BYOL) understøttes kun for virtuelle maskiner og betragtes som en **gratis** tilbud.


### <a name="register-your-company-account"></a>Registrere din firmakonto
1. Åbn en ny Internet Explorer InPrivate- eller Chrome Inkognito-browsersession for at sikre, at du ikke er logget på en personlig konto.

2. Gå til [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) til at registrere dig selv som en sælger i den Udviklercenter. Læs følgende vigtige note, før du fortsætter.

    >[AZURE.IMPORTANT] Kontrollér, at mail-id eller distributionsliste listen (en distributionsliste anbefales at fjerne afhængighed fra enkeltpersoner) som du vil bruge til registrering i den Udviklercenter er på først er registreret som en Microsoft-konto. Hvis ikke, derefter skal du registrere ved hjælp af dette [link](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Desuden **en e-mail-id under Microsoft firmadomæne det vil sige @microsoft.com kan ikke bruges** til Udviklercenter registrering.

    ![tegning][img-signin]

3. Fuldføre guiden "Hjælp os med at beskytte din konto", der bekræfter din identitet via telefonnummer eller mail mailadresse.

    ![tegning][img-verify]

4. Vælg din **konto land/område** i afsnittet "Registrering-kontooplysninger" i rullemenuen.

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

    > [AZURE.WARNING] **"Sælge fra" lande:** For at sælge dine tjenester på Azure Marketplace, skal din registrerede enhed være mellem en af de godkendte "sælge fra" lande ovenfor. Denne begrænsning gælder for udbetaling og afgifter årsager. Vi aktivt ønsker at udvide denne liste over lande inden for kort tid, så Vær klar. Se [Marketplace deltagelse politikker](http://go.microsoft.com/fwlink/?LinkID=526833)kan finde flere oplysninger.

5. Vælg din "kontotype" som **virksomhed** , og klik derefter på knappen **Næste** .

    > [AZURE.IMPORTANT] For bedre at forstå kontotyper, og hvad der passer bedst for dig at vælge, kan du få vist siden [kontotyper, placeringer, og gebyrer](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)

6. Angiv den **Publisher visningsnavn**, typisk navnet på din virksomhed.

    > [AZURE.TIP] Publisher visningsnavnet, der er angivet i den Udviklercenter vises ikke i Azure Marketplace, når dit tilbud går vises. Men dette skal angives for at fuldføre registreringsprocessen.

7. Du kan angive **kontaktoplysninger** for konto bekræftelsen.

    > [AZURE.IMPORTANT] Du skal angive nøjagtige kontaktoplysninger, fordi den bruges i vores bekræftelsesprocessen for din virksomhed skal godkendes i Developer Center.

8. Angiv kontaktoplysningerne for **Virksomhed godkender**. Virksomhed godkenderen er den person, der kan du kontrollere, at du har tilladelse til at oprette en konto i det Udviklercenter på vegne af din organisation. Klik på **Næste** til at flytte til den **"Afsnittet med betaling"** , når du er færdig.

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)

9. Angiv dine betalingsoplysninger til at betale for din konto. Hvis du har en kampagnekode, der dækker omkostningerne for registrering, kan du angive, her. Ellers skal give dine kreditkortoplysninger (eller PayPal på understøttede markeder). Klik på **Næste** til at gå videre til **"Gennemse skærmen"**, når du er færdig.

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)

10. Gennemse dine kontooplysninger, og Bekræft, at alt er korrekt. Derefter Læs og Accepter vilkår og betingelser i [Microsoft Azure Marketplace Publisher aftale](http://go.microsoft.com/fwlink/?LinkID=699560). Markér afkrydsningsfeltet for at angive, du har læst og accepteret vilkårene.

11. Klik på **Udfør** for at bekræfte din tilmelding. Vi sender en bekræftelsesmeddelelse til din mailadresse.

12. Hvis du planlægger at publicere kun gratis tilbud, skal du klikke på kan **Gå til Azure Marketplace Udgivelsesportal** , og du gå til afsnit 3 i dette dokument, [registrere din konto på portalen publicering](#3-register-your-account-in-the-publishing-portal).

Hvis du planlægger at publicere kommercielle tilbyder (fx virtuelt udbyder med hver time fakturering model) skal du klikke på **opdatere dine kontooplysninger** hvor skal du udfylde de moms og bankoplysninger i kontoen Developer Center.

Hvis du foretrækker at opdatere dine moms og bank oplysninger senere, skal du kan flytte til næste afsnit det vil sige punkt 3 i dette dokument, [registrere din konto på portalen publicering](#3-register-your-account-in-the-publishing-portal), og vender tilbage senere ved hjælp af links i portalen Azure publicering.

> [AZURE.IMPORTANT] I tilfælde af kommercielle tilbud kan du ikke overføre dine tilbud til fremstilling uden at færdiggøre oplysningerne om moms og bankkonto.

Hvis du foretrækker at opdatere dine moms og bank oplysninger senere, kan du gå til afsnit 3, skal du [registrere din konto på portalen publicering](#3-register-your-account-in-the-publishing-portal), og vender tilbage senere ved hjælp af links i portalen Azure publicering.

### <a name="add-tax-and-banking-information"></a>Tilføje moms og banker oplysninger
 Hvis du vil publicere kommercielle tilbud købes, skal du også føje udbetaling, oplysninger og sende det til datavalidering i Developer Center. Hvis du vil udgive kun gratis tilbud (eller BYOL tilbyder), derefter behøver du ikke at tilføje disse oplysninger. Du kan tilføje den senere, men det tager lidt tid på at validere skatteoplysninger. Hvis du ved, at du vil blive tilbudt kommercielle tilbud købes, anbefaler vi, at du tilføje den så tidligt som muligt.

**Bankoplysninger**

1. Log på [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med din Microsoft-konto.

2. Klik på **udbetaling konto** i menuen til venstre, klik på **bankkonto** eller **PayPal**under **Vælg betalingsmetode** .

    > [AZURE.IMPORTANT] Hvis du har kommercielle tilbud, der kunder købe på markedet, er dette kontoen, der hvor du vil modtage udbetaling for disse køb.

3. Angiv betalingsoplysninger, og klik på **Gem** , når du er færdig.

    > [AZURE.IMPORTANT] Hvis du vil opdatere eller ændre din udbetaling konto skal du følge de samme trin herover, erstatter de aktuelle oplysninger med de nye oplysninger. Ændre kontoen udbetaling kan forsinke dine betalinger med op til én betaling cyklus. Denne forsinkelse opstår, fordi vi har brug at bekræfte Skift konto samme måde, som vi har, når du først konfigurere kontoen udbetaling. Du kan stadig få har betalt for det fulde beløb når kontoen er blevet bekræftet; en hvilken som helst betalinger for den aktuelle udbetaling cyklus, føjes til den næste.

4. Klik på **Næste**.

**Oplysninger om skat**

1. Log på [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med din Microsoft-konto (hvis det er nødvendigt).

2. Klik på **moms profil** i menuen til venstre.

3. Vælg det land eller område, hvor du har permanent residency på siden **Konfigurer moms formularen** , og vælg derefter det land eller område, hvor du holder primære medborgerskab. Klik på **Næste**.

4. Angiv din moms oplysninger, og klik derefter på **Næste**.

> [AZURE.WARNING] Du vil ikke kunne overføre til fremstilling din kommerciel tilbyder uden at færdiggøre moms og bankkonto oplysningerne i din Microsoft Developer Center-konto.

Hvis du har problemer med Developer Center registrering, skal du logge en supportbilletter som nedenfor

1. Gå til support link [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. **Kontakt os** sektionen, kan du klikke på knappen **Send en hændelse** , (som vist i skærmbilledet nedenfor)

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)

3. Vælg "Hjælp med Udviklercenter" som **problemtype** og "Publicer og administrere apps" som **kategori**. Klik på knappen "Start mail", hvis du vil herefter.

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)

4. Du får en logonside. Bruge en Microsoft-konto Log på. Hvis du ikke har en Microsoft-konto, oprette en ved hjælp af dette [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Udfyld oplysningerne om problemet og subit brugertilladelse ved at klikke på knappen **Send** .

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. registrere din konto på portalen publicering
[Publicere portal](http://publish.windowsazure.com) bruges til at udgive og administrere dit tilbud.

1. Åbn en ny Chrome Inkognito eller Internet Explorer InPrivate-browsersession for at sikre, at du ikke er logget på en personlig konto.

2. Gå til [http://publish.windowsazure.com](http://publish.windowsazure.com).

3. Hvis du er ny bruger og logge på udgivelse portalen for første gang, derefter skal du logge på med den samme e-mail-id, som er registreret kontoen Udviklercenter. På denne måde kan kontoen Udviklercenter og publicere portalen konto sammenkædes med hinanden. Du kan altid tilføje de andre medlemmer på firmaet, der arbejder på programmet, som en co-administrator i udgivelse portalen ved at følge trinnene nedenfor.

Hvis du er tilføjet som en co-administrator i udgivelse portalen, derefter kan du logge på med din co-administratorkonto.

  > [AZURE.TIP] Politikkerne, der deltagelse er beskrevet på [Azure websted](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. trin for at tilføje en co-administrator i udgivelse portal
**Hvis resultatet af, at du er administrator,** nedenstående er trinnene for at tilføje en co-administrator.

>[AZURE.NOTE] **For de nye brugere,** før du tilføjer en co-administrator i udgivelse portal, sikre, at du har oprettet mindst ét program i udgivelse portal. Dette er påkrævet, som fanen **UDGIVERE** , der vises kun efter oprettelse af mindst ét program i udgivelse portal.

1. Sikre, at co-administrator e-mail-id'et er en Microsoft-account(MSA). Hvis ikke, registrere den som en MSA ved hjælp af dette [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Sørg for, at der er mindst ét program under kontoen Administrator, før du forsøger at tilføje en co-administrator.
3. Når ovenstående trin er færdigt, skal logge på udgivelse portalen med co-administrator e-mail-id og derefter på log af.
4. Nu logon til udgivelse portalen med administrator e-mail-id.
5. Gå til udgivere -> Vælg din konto -> administratorer -> Tilføj co-administratoren (skærmbillede givet nedenfor)

  ![tegning](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. trin for at slette en co-administrator i udgivelse portal
**Hvis resultatet af, at du er administrator,** nedenstående er trinnene for at slette en co-administrator.

1. Logge på udgivelse portalen med administrator e-mail-id.
2. Gå til **Udgivere** -> Vælg din konto -> **Administratorer** -> **Flere administratorer**.
3. Klik på knappen **X** ud for den co-administrator, du vil Forsikringssummer i alt delete (skærmbillede givet nedenfor).

    ![tegning](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Næste trin
Nu, hvor din konto er oprettet og registreret, sikrer du opfylde eller opfylder alle de tekniske forudsætninger publicere dit tilbud ved at gennemgå [tekniske forudsætninger](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Se også
- [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
