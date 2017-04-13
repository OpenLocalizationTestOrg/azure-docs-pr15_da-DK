<properties
    pageTitle="Azure AD-forbindelse: Fejlfinding under synkronisering | Microsoft Azure"
    description="Forklarer, hvordan du udfører fejlfinding af fejl under synkronisering med Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>Fejlfinding under synkronisering
Fejl kan opstå, når synkroniseres identitetsdata fra Windows Server Active Directory (AD DS) til Azure Active Directory (Azure AD). I denne artikel giver et overblik over forskellige typer synkroniseringsfejl, nogle af de mulige scenarier, der kan medføre disse fejl og mulige måder at rette en fejl. I denne artikel indeholder almindelige fejl og kan ikke omfatte alle mulige fejl.

 I denne artikel antages læseren er fortrolig med den underliggende [designe begreberne Azure AD og Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Med den seneste version af Azure AD-forbindelse \(August 2016 eller højere\), en rapport over synkroniseringsfejl, der er tilgængelig i [Azure-portalen](https://aka.ms/aadconnecthealth) som en del af Azure AD forbinde tilstand for synkronisering.


Starte September 1 2016 [Azure Active Directory duplikere attribut fleksibilitet](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funktion aktiveres som standard for alle de *nye* Azure Active Directory-lejere. Denne funktion vil automatisk blive aktiveret for eksisterende lejere i de kommende måneder.

Azure AD-forbindelse udfører 3 typer handlinger fra de mapper, det holder synkroniseret: Importér, synkronisering og Eksportér. Fejl kan finde sted i alle handlinger. I denne artikel fokuserer hovedsageligt på fejl under eksport til Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fejl under Eksportér til Azure AD
Følgende afsnit beskrives forskellige typer synkroniseringsfejl, der kan opstå under eksporten til Azure AD ved hjælp af Azure AD-forbindelse. Denne forbindelse kan blive identificeret ved navneformatet der "contoso. *onmicrosoft.com*".
Fejl under Eksportér til Azure AD angive, at handlingen \(tilføje, opdatere og slette osv\) forsøgte at med Azure AD-forbindelse \(Synkroniser program\) på Azure Active Directory mislykkedes.

![Eksportere fejl oversigt](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Data uoverensstemmelsesfejl
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Beskrivelse
- Når Azure AD Connect \(Synkroniser program\) får Azure Active Directory tilføjes eller opdateres objekter, Azure AD svarer til det indgående objekt ved hjælp af attributten **sourceAnchor** til attributten **immutableId** objekter i Azure AD. Denne forskel kaldes en, **Så det svarer til hårde**.
- Når Azure AD **ikke finder** objekter, der svarer til **immutableId** attribut med attributten **sourceAnchor** for objektet indgående, inden du klargør et nyt objekt, går det tilbage for at bruge attributterne ProxyAddresses og UserPrincipalName fundet et match. Denne forskel kaldes en, **Så det svarer til bløde**. Bløde svarer er udviklet til at matche objekter, der allerede findes i Azure AD (som er hentet fra i Azure AD) med de nye objekter bliver tilføjet/opdateret under synkronisering, der repræsenterer den samme enhed (brugere, grupper) lokalt.
- **InvalidSoftMatch** fejl, når det hårde Sammenlign ikke finder tilsvarende objekter **og** bløde svarer finder en tilsvarende objekt, men objektet har en anden værdi af *immutableId* end objektet indgående *SourceAnchor*, viser, at det tilsvarende objekt blev synkroniseret med et andet objekt fra lokalt Active Directory.

Med andre ord i rækkefølge for bløde match til at fungere sammen, bør objektet skal være bløde matchet med ikke have alle værdier for *immutableId*. Hvis et objekt med *immutableId* angivet med er en værdi ned hårde matcher men opfylder kriteriet bløde match-handlingen resulterer i en InvalidSoftMatch synkroniseringsfejl.

Azure Active Directory-skemaet tillader ikke to eller flere objekter skal have den samme værdi af følgende egenskaber. \(Dette er ikke en omfattende liste.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ObjectId

>[AZURE.NOTE] [Azure AD-attribut Dupliker attribut fleksibilitet](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funktion er også rulles som standardfunktionsmåden for Azure Active Directory.  Dette vil reducere antallet af synkroniseringsfejl, der ses af Azure AD-forbindelse (samt andre Synkroniser klienter) ved at gøre Azure AD mere robust i den måde, den håndterer dublerede ProxyAddresses og UserPrincipalName attributter, der er findes i på lokale AD miljøer. Denne funktion løser ikke kopiering fejl. Så skal dataene stadig rettes. Men det giver mulighed for klargøring af nye objekter, som blokeres ellers fra ved at blive klargjort på grund af dublerede værdier i Azure AD. Dette vil også reducere antallet af synkroniseringsfejl, der returneres til synkronisering af klienten.
Hvis denne funktion er aktiveret for din lejer, kan du ikke kan se de InvalidSoftMatch synkroniseringsfejl, der vises under klargøring af nye objekter.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Eksempelscenarier til InvalidSoftMatch
1. To eller flere objekter med den samme værdi i ProxyAddresses-attributten findes i lokalt Active Directory. Kun én få klargøres i Azure AD.
2. Der findes to eller flere objekter med den samme værdi af userPrincipalName i lokalt Active Directory. Kun én få klargøres i Azure AD.
3. Et objekt blev tilføjet i de lokale miljø til Active Directory med den samme værdi i ProxyAddresses-attributten som et eksisterende objekt i Azure Active Directory. Det objekt, der er tilføjet lokalt er ikke få klargjort i Azure Active Directory.
4. Et objekt blev tilføjet i lokalt Active Directory med den samme værdi af userPrincipalName-attribut som en konto i Azure Active Directory. Objektet er ikke få klargjort i Azure Active Directory.
5. En synkroniseret konto blev flyttet fra skov A til skov B. Azure AD-forbindelse (Synkroniser engine) brugte ObjectGUID attribut til at beregne SourceAnchor. Værdien af SourceAnchor er forskellige efter flytning skov. Det nye objekt (fra skov B) går ned til at synkronisere med det eksisterende objekt i Azure AD.
6. Et synkroniseret objekt har ved et uheld slettet fra lokalt Active Directory og et nyt objekt blev oprettet i Active Directory for det samme objekt (som bruger) uden at slette kontoen i Azure Active Directory. Den nye konto kan ikke synkronisere med det eksisterende Azure AD-objekt.
7. Azure AD-forbindelse er blevet fjernet og installeret igen. En anden attribut blev valgt som SourceAnchor under installationen igen. Alle de objekter, der tidligere havde synkroniseres ikke længere synkronisere med InvalidSoftMatch fejl.

#### <a name="example-case"></a>Eksempel på store og små bogstaver:
1. **Bob Smith** er en synkroniseret bruger i Azure Active Directory fra lokalt Active Directory på *contoso.com*
2. BOB Smith **UserPrincipalName** er angivet som **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** er **SourceAnchor** beregnes ved hjælp af Bob Smith **objectGUID** fra til Azure AD-forbindelse anlæg Active Directory, hvilket er **immutableId** for Bob Smith i Azure Active Directory.
4. BOB har også følge værdier for **proxyAddresses** -attributten:
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  En ny bruger, **Bob Taylor**, føjes til de til lokale Active Directory.
6. BOB Taylor **UserPrincipalName** er angivet som **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** er **sourceAnchor** beregnes ved hjælp af Bob Taylor **objectGUID** fra til Azure AD-forbindelse anlæg Active Directory. BOB Taylor objekt har ikke synkroniseret til Azure Active Directory endnu.
8. BOB Taylor har følgende værdier for proxyAddresses-attributten
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Under synkronisering, vil Azure AD-forbindelse genkende tilføjelsen af Bob Taylor i lokalt Active Directory og stil Azure AD til samme ændringen.
10. Azure AD først udfører hårde match. Det vil sige, søges der hvis et objekt med immutableId er lig med "abcdefghijkl0123456789 ==". Hårde Match mislykkes, når ingen andre objekter i Azure AD har immutableId.
11. Azure AD derefter forsøger at bløde match Bob Taylor. Det vil sige, søges der hvis et objekt med proxyAddresses er lig med de tre værdier, herundersmtp:bob@contoso.com
12. Azure AD finder Bob Smith objekt til at matche de bløde søgekriterier. Men dette objekt har værdien af immutableId = "abcdefghijklmnopqrstuv ==". hvilket angiver dette objekt blev synkroniseret fra et andet objekt fra lokalt Active Directory. Derfor Azure AD ikke bløde match disse objekter og medfører en **InvalidSoftMatch** synkroniseringsfejl.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Sådan rettes InvalidSoftMatch fejl
Den mest almindelige årsag til fejlen InvalidSoftMatch er to objekter med forskellige SourceAnchor \(immutableId\) har den samme værdi for de ProxyAddresses og/eller UserPrincipalName attributter, der er brugt under processen bløde match på Azure AD. For at løse ugyldige bløde matcher

1.  Identificere de dublerede proxyAddresses, userPrincipalName eller andre attributværdien, der forårsager fejlen. Også identificere, hvilke to \(eller flere\) objekter er involveret i konflikten. Rapporten genereres af [Azure AD forbinde tilstand for synkronisering](https://aka.ms/aadchsyncerrors) kan hjælpe dig med at identificere de to objekter.
2. Identificere, hvilket objekt bør fortsætte med at få den duplikerede værdi og hvilket objekt skal ikke.
3. Fjern den duplikerede værdi fra det objekt, der ikke bør have den pågældende værdi. Vær opmærksom på, skal du foretage ændringen i den mappe, hvor objektet, der leveres til. I nogle tilfælde kan du vil slette et af objekterne i konflikt.
4. Hvis du har foretaget ændringen i de til lokale AD, kan du lade Azure AD-forbindelse synkronisere ændringen.

Bemærk, at synkronisere fejlrapport i Azure AD forbinde sundhed synkroniseres til opdateres hver 30 minutter og indeholder fejl fra det seneste forsøg på synkronisering.

>[AZURE.NOTE] ImmutableId, bør ved at definition, ikke ændre i levetiden for objektet. Hvis Azure AD-forbindelse ikke blev er konfigureret med nogle af scenarierne huske fra ovenstående liste, du kan afslutte i en situation, hvor Azure AD-forbindelse beregner en anden værdi af SourceAnchor for objektet AD, der repræsenterer den samme enhed (samme bruger/gruppe/Kontakt osv), der har et eksisterende Azure AD-objekt, du vil fortsætte med at bruge.

#### <a name="related-articles"></a>Relaterede artikler
- [Duplikerede eller ugyldige attributter forhindrer katalogsynkronisering i Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Beskrivelse
Når Azure AD forsøger at blød svarer til to objekter, det er muligt, at to objekter forskellige "objekttype" (som bruger, gruppe, Kontakt osv.) har de samme værdier for de attributter, der bruges til at udføre bløde matcher. Som kopiering af disse attributter ikke er tilladt i Azure AD, kan handlingen medføre fejl ved synkronisering af "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Eksempelscenarier for ObjectTypeMismatch fejl
- En mail aktiveret sikkerhedsgruppe er oprettet i Office 365. Administrator tilføjer en ny bruger eller en kontakt i lokalt AD (som ikke er synkroniseret til Azure AD endnu) med den samme værdi for ProxyAddresses-attributten som Office 365-gruppe.

#### <a name="example-case"></a>Eksempel på store og små bogstaver

1. Administratoren opretter en ny mail aktiveret sikkerhedsgruppe i Office 365 for afdelingen moms og indeholder en mailadresse som tax@contoso.com. Dette tildeler ProxyAddresses-attributten for denne gruppe med værdien af**smtp:tax@contoso.com**
2. En ny bruger sammenkæder Contoso.com og en konto er oprettet for brugeren lokalt med proxyAddress som**smtp:tax@contoso.com**
3. Når Azure AD-forbindelse der synkroniserer den nye brugerkonto, får det fejlen "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Sådan rettes ObjectTypeMismatch fejl
Den mest almindelige årsag til fejlen ObjectTypeMismatch er to objekter af anden type (brugeren, gruppen, Kontakt osv.) har samme værdi i ProxyAddresses-attributten. For at løse ObjectTypeMismatch:

1.  Identificere de dublerede proxyAddresses (eller andre attributter) værdi, der er årsag til fejlen. Også identificere, hvilke to \(eller flere\) objekter er involveret i konflikten. Rapporten genereres af [Azure AD forbinde tilstand for synkronisering](https://aka.ms/aadchsyncerrors) kan hjælpe dig med at identificere de to objekter.
2. Identificere, hvilket objekt bør fortsætte med at få den duplikerede værdi og hvilket objekt skal ikke.
3. Fjern den duplikerede værdi fra det objekt, der ikke bør have den pågældende værdi. Vær opmærksom på, skal du foretage ændringen i den mappe, hvor objektet, der leveres til. I nogle tilfælde kan du vil slette et af objekterne i konflikt.
4. Hvis du har foretaget ændringen i de til lokale AD, kan du lade Azure AD-forbindelse synkronisere ændringen. Synkroniser fejlrapport i Azure AD forbinde sundhed synkroniseres til bliver opdateret hver 30 minutter og omfatter fejl fra det seneste forsøg på synkronisering.


## <a name="duplicate-attributes"></a>Duplikere attributter
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Beskrivelse
Azure Active Directory-skemaet tillader ikke to eller flere objekter skal have den samme værdi af følgende egenskaber. Det er de enkelte objekter i Azure AD er nødt til at have en entydig værdi af disse attributter på en given forekomst.

- ProxyAddresses
- UserPrincipalName

Hvis Azure AD-forbindelse forsøger at tilføje et nyt objekt eller opdatere et eksisterende objekt med en værdi for de ovenfor attributter, der allerede er tildelt til et andet objekt i Azure Active Directory, medfører handlingen "AttributeValueMustBeUnique" synkroniseringsfejl.
#### <a name="possible-scenarios"></a>Mulige scenarier:
1. Duplikerede værdi, der er tildelt til et allerede synkroniseret objekt, som er i konflikt med en anden synkroniseret objekt.

#### <a name="example-case"></a>Eksempel på store og små bogstaver:
1. **Bob Smith** er en synkroniseret bruger i Azure Active Directory fra lokalt Active Directory på contoso.com
2. BOB Smith **UserPrincipalName** lokalt er angivet som **bobs@contoso.com**.
3. BOB har også følge værdier for **proxyAddresses** -attributten:
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. En ny bruger, **Bob Taylor**, føjes til de til lokale Active Directory.
5. BOB Taylor **UserPrincipalName** er angivet som **bobt@contoso.com**.
6. **Bob Taylor** har følgende værdier for **ProxyAddresses** -attributten i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. BOB Taylor objekt er synkroniseret med Azure AD.
8. Administrator besluttede dig for at opdatere Bob Taylor **ProxyAddresses** -attributten med følgende værdi: jeg. **smtp:bob@contoso.com**
9. Azure AD forsøger at opdatere Bob Taylor objekt i Azure AD med værdien ovenfor, men som handlingen mislykkes som, ProxyAddresses værdi allerede er tildelt til Bob Smith, hvilket resulterer i "AttributeValueMustBeUnique" fejl.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Sådan rettes AttributeValueMustBeUnique fejl
Den mest almindelige årsag til fejlen AttributeValueMustBeUnique er to objekter med forskellige SourceAnchor \(immutableId\) har den samme værdi for attributterne, ProxyAddresses og/eller UserPrincipalName. For at rette en fejl med AttributeValueMustBeUnique

1.  Identificere de dublerede proxyAddresses, userPrincipalName eller andre attributværdien, der forårsager fejlen. Også identificere, hvilke to \(eller flere\) objekter er involveret i konflikten. Rapporten genereres af [Azure AD forbinde tilstand for synkronisering](https://aka.ms/aadchsyncerrors) kan hjælpe dig med at identificere de to objekter.
2. Identificere, hvilket objekt bør fortsætte med at få den duplikerede værdi og hvilket objekt skal ikke.
3. Fjern den duplikerede værdi fra det objekt, der ikke bør have den pågældende værdi. Vær opmærksom på, skal du foretage ændringen i den mappe, hvor objektet, der leveres til. I nogle tilfælde kan du vil slette et af objekterne i konflikt.
4. Hvis du har foretaget ændringen i de til lokale AD, du kan synkronisere ændringen for fejlen, til at få faste Azure AD-forbindelse.

#### <a name="related-articles"></a>Relaterede artikler
-[Duplikerede eller ugyldige attributter forhindrer katalogsynkronisering i Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Data valideringsfejl
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Beskrivelse
Azure Active Directory håndhæves forskellige begrænsninger på selve dataene, før du tillader disse data kan skrives til mappen. Dette er at sikre, at brugere får de bedst mulige oplevelser når du bruger de programmer, der er afhænger af disse data.
#### <a name="scenarios"></a>Scenarier
en. Attributværdien UserPrincipalName er ugyldig eller ikke understøttet tegn.
b. UserPrincipalName-attributten overholder ikke det ønskede format.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Sådan rettes IdentityDataValidationFailed fejl

en. Sørg for, at userPrincipalName-attributten har understøttes tegn og krævede format.

#### <a name="related-articles"></a>Relaterede artikler
- [Forberede klargøring af brugere via katalogsynkronisering med Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Beskrivelse
Dette er en meget bestemte sag, der resulterer i en **"DataValidationFailed"** synkroniseringsfejl, når suffikset i en brugers UserPrincipalName ændres fra et medlem af organisationsnetværket domæne til et andet medlem af organisationsnetværket domæne.

#### <a name="scenarios"></a>Scenarier
For en synkroniseret bruger er UserPrincipalName suffikset ændret fra et medlem af organisationsnetværket domæne til et andet medlem af organisationsnetværket domæne lokalt. For eksempel *UserPrincipalName = bob@contoso.com * blev ændret til *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Eksempel
1. BOB Smith, en konto til Contoso.com, bliver tilføjet som en ny bruger i Active Directory med UserPrincipalNamebob@contoso.com
2. BOB flyttes til en anden opdeling af Contoso.com kaldet Fabrikam.com og sin UserPrincipalName er ændret tilbob@fabrikam.com
3. Både contoso.com og fabrikam.com domæner er domæner med Azure Active Directory.
4. Bobs userPrincipalName opdateret ikke og resulterer i en "DataValidationFailed" synkroniseringsfejl.

#### <a name="how-to-fix"></a>Sådan rettes problemet
Hvis en bruger UserPrincipalName suffiks blev opdateret fra bob@ **contoso.com** til bob@ **fabrikam.com**, hvor både **contoso.com** og **fabrikam.com** er **domæner**, derefter skal du følge den under trin til at løse synkroniseringsfejlen

1. Opdatere brugerens UserPrincipalName i Azure AD fra bob@contoso.com til bob@contoso.onmicrosoft.com. Du kan bruge følgende PowerShell-kommando med Azure AD PowerShell-modulet:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Tillad, at den næste synkronisering cyklus forsøger synkronisering. Udføres denne gang synkronisering og den opdateres UserPrincipalName Bob til bob@fabrikam.com som forventet.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Beskrivelse
Når en attribut overstiger den maksimalt tilladte størrelse, længdebegrænsningen eller antal, der er angivet af Azure Active Directory-skema, medfører handlingen synkronisering synkroniseringsfejlen **LargeObject** eller **ExceededAllowedLength** . Denne fejl opstår typisk for følgende attributter

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Mulige scenarier
1. Bobs userCertificate attribut er lagring af for mange certifikater, der er tildelt til Bob. Disse kan indeholde ældre, udløbne certifikater.
2. Bobs thmubnailPhoto angivet i Active Directory er for stor til at blive synkroniseret i Azure AD.
3. Under automatisk populationen af ProxyAddresses-attributten i Active Directory, har du fået tildelt et objekt > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Sådan rettes problemet

1. Sikre, at den attribut, der er årsag til fejlen er inden for de tilladte begrænsning.

## <a name="related-links"></a>Relaterede links
- [Find Active Directory-objekter i Active Directory Administrative Center] (https://technet.microsoft.com/library/dd560661.aspx)
- [Sådan Azure Active Directory-forespørgsel til et objekt ved hjælp af Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
