<properties
    pageTitle="Azure AD-forbindelse synkronisering: Sådan foretager en ændring af standard konfigurationen | Microsoft Azure"
    description="Vejleder dig gennem Sådan foretager en ændring af konfigurationen i Azure AD-forbindelse synkronisering."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD-forbindelse synkronisering: Sådan foretager en ændring af standard konfigurationen
Formålet med dette emne er til at vejlede dig gennem Sådan foretages ændringer i standardkonfigurationen i Azure AD-forbindelse synkronisering. Den indeholder trin til nogle almindelige scenarier. Med denne viden, skal du kunne foretage nogle enkle ændringer til din egen konfiguration, der er baseret på dine egne forretningsregler.

## <a name="synchronization-rules-editor"></a>Synkronisering regler Editor
Synkronisering regler Editor bruges til at få vist og ændre standardkonfigurationen. Du kan finde det i menuen Start under gruppen **Azure AD-forbindelse** .  
![Menuen Start med synkronisering regel Editor](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Når du åbner det, kan du se standard out of box regler.

![Synkroniser regel Editor](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigere i editoren
-Rullelister øverst i editoren giver dig mulighed for hurtigt at finde en bestemt regel. Hvis du vil se reglerne, hvor attributten proxyAddresses er inkluderet, vil du ændre-rullelister med følgende:  
![SRE filtrering](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Hvis du vil nulstille filtrering og indlæse en ny konfiguration, skal du trykke på **F5** på tastaturet.

Øverst til højre har du en knappen **Tilføj ny regel**. Denne knap bruges til at oprette dine egne brugerdefinerede regel.

Til bunden, og har du knapper til optræder på en markeret Synkroniser regel. **Redigere** og **slette** gøre hvad du forventer. **Eksportere** giver en PowerShell-script for at oprette nye Synkroniser reglen. Denne fremgangsmåde kan du flytte en synkronisering regel fra en server til en anden.

## <a name="create-your-first-custom-rule"></a>Oprette din første brugerdefineret regel
De mest almindelige ændringen er ændringer til attribut flyder. Dataene i din kildemappe muligvis ikke som Azure AD. I eksemplet i dette afsnit vil du kontrollere navnet på en bruger er altid med **stort forbogstav**.

### <a name="disable-the-scheduler"></a>Deaktiver planlæggeren
[Opgavestyring](active-directory-aadconnectsync-feature-scheduler.md) kører hver 30 minutter som standard. Du vil kontrollere, at det ikke starter, mens du foretager ændringer og fejlfinding i forbindelse med din nye regler. Du kan midlertidigt deaktivere planlæggeren ved start PowerShell, og Kør`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Deaktiver planlæggeren](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Oprette reglen

1. Klik på **Tilføj ny regel**.
2. Angiv følgende oplysninger på siden **Beskrivelse** :  
![Indgående regel filtrering](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Navn: Giv reglen et beskrivende navn.
    - Beskrivelse: Nogle præcisering så andre kan forstå, hvad reglen er beregnet til.
    - Forbindelse system: objektet kan findes i systemet. I dette tilfælde Vælg vi Active Directory Connector.
    - Forbundne System/Metaverse objekttype: Markere henholdsvis **bruger** og **Person** .
    - Kædetype: Ændre denne værdi til at **deltage**.
    - Prioritet: Angive en værdi, der er entydige i systemet. En lavere numeriske værdi angiver højere prioritet.
    - Mærke: Lad være tomt. Kun out of box regler fra Microsoft har dette felt, der er udfyldt med en værdi.
3. Angiv **givenName ISNOTNULL**på siden **Scoping filter** .  
![Indgående regel angivelse af område filter](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
Dette afsnit bruges til at definere, hvilke objekter reglen skal gælde for. Hvis stå tomt, skal reglen gælde for alle user-objekter. Men, der medtages mødelokalerne, tjenestekonti og andre ikke-personer brugerobjekter.
4. På den **deltage i regler**, lade feltet være tomt.
5. Ændre FlowType til **udtryk**på siden **transformationer** . Vælg Destinationsattributten **givenName**, og Angiv i kilde `PCase([givenName])`.
![Indgående regel transformationer](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
Synkroniser program skelnes både på funktionens navn og navnet på attributten. Hvis du skriver noget galt, vises der en advarsel, når du har tilføjet reglen. Redigeringsprogrammet kan du gemme og fortsætte, så at genåbne reglen, og ret reglen.
6. Klik på **Tilføj** for at gemme reglen.

Den nye brugerdefinerede regel skal være synlige med andre Synkroniser reglerne i systemet.

### <a name="verify-the-change"></a>Bekræfte ændringen
Med denne nye ændring vil du at sikre, at den fungerer som forventet, og ikke aktiverende eventuelle fejl. Afhængigt af antallet objekter, du har, er der to forskellige måder at udføre dette trin.

1. Køre en komplet synkronisering på alle objekter
2. Køre en preview og fuld synkronisering på et enkelt objekt

Starte **Tjenesten Brugerprofilsynkronisering** fra startmenuen. Trinene i dette afsnit er alle i dette værktøj.

1. **Fuld Synkroniser på alle objekter**  
Vælg **forbindelser** øverst. Identificere den forbindelse, du har oprettet en ændring i forrige afsnit i dette tilfælde Active Directory-domænetjenester, og vælg det. Vælg **Kør** fra handlinger, og vælg **Fuld synkronisering** og **OK**.
![Fuld synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Objekterne er nu opdateret i metaverse. Du vil nu se på objektet i metaverse.

2. **Vis udskrift og fuld Synkroniser på et enkelt objekt**  
Vælg **forbindelser** øverst. Identificere den forbindelse, du har oprettet en ændring i forrige afsnit i dette tilfælde Active Directory-domænetjenester, og vælg det. Vælg **Søg Connector-plads**. Brug omfang til at finde et objekt, du vil bruge til at teste ændringen. Vælg objektet, og klik på **eksempel**. Vælg **Udfør Preview**i vinduet Ny.
![Udfør preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
Ændringen er nu bekræftet til metaverse.

**Se på objektet i metaverse**  
Du vil nu for at vælge et par eksempel objekter at sikre, at værdien forventet og, reglen anvendes. Vælg **Metaverse-søgning** fra toppen. Tilføj de filter, skal du finde de relevante objekter. Åbn et objekt fra søgeresultatet. Se på attributværdierne og også kontrollere i kolonnen **Synkroniser regler** , som den regel, der er anvendt som forventet.  
![Metaverse-søgning](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Aktivere planlæggeren
Hvis alt er som forventet, kan du aktivere planlæggeren igen. Kør fra PowerShell, `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Andre almindelige attribut flow ændringer
Den forrige sektion beskrevet Sådan foretages ændringer i en attribut forløb. I dette afsnit, der gives nogle flere eksempler. Vejledning til at oprette reglen for synkronisering er forkortet, men du kan finde fuld trinnene i forrige afsnit.

### <a name="use-another-attribute-than-the-default"></a>Bruge en anden attribut end standard
Hos Fabrikam er der et område, hvor det lokale alfabet bruges til fornavn, Efternavn og vist navn. Latinsk Tegnrepræsentation af disse attributter kan findes i lokalnummer attributter. Når du opbygger den globale adresseliste i Azure AD og Office 365, organisationen ønsker disse attributter, der skal bruges i stedet.

Med en standardkonfiguration ser et objekt fra det lokale område sådan ud:  
![Attributten flow 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Hvis du vil oprette en regel med andre attributter flyder, skal du gøre følgende:

- Starte **Synkronisering regel Editor** fra startmenuen.
- Med **indgående** stadig er markeret til venstre, skal du klikke på knappen **Tilføj ny regel**.
- Giv reglen et navn og beskrivelse. Vælg lokalt Active Directory og de relevante objekttyper.  Vælg **Deltag**i **Kædetype**. Vælg det nummer, der ikke bruges af en anden regel for prioritet. Out of box reglerne start med 100, så værdien 50 kan bruges i dette eksempel.
![Attributten flow 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Tomt område (det vil sige, skal gælde for alle user-objekter i området).
- Tomt joinforbindelse regler (det vil sige, lade out of box reglen håndtere eventuelle joinforbindelser).
- Opret følgende flyder i transformationer:  
![Attributten flow 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Klik på **Tilføj** for at gemme reglen.
- Gå til **Synkronisering Service Manager**. Vælg den forbindelse, hvor vi har tilføjet reglen på **forbindelser**. Vælg **Kør**, og **fuld synkronisering**. En komplet synkronisering genberegnes alle objekter ved hjælp af regler i den aktuelle.

Dette er resultatet for det samme objekt med denne brugerdefineret regel:  
![Attributten flow 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Længden af attributter
Strengattributter er som standard skal være kan indekseres, og den maksimale længde 448 tegn. Hvis du arbejder med strengattributter, der kan indeholde mere, derefter Sørg for at medtage følgende i attribut strømmen:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Ændre userPrincipalSuffix
UserPrincipalName-attributten i Active Directory kendes ikke altid af brugerne og kan ikke være passende som det logon-ID. Azure AD-forbindelse Synkroniser installationsguiden kan vælge en anden attribut, mail, f.eks. Men i nogle tilfælde attributten skal beregnes. For eksempel har virksomheden Contoso to Azure AD-kataloger, én til fremstilling og én til test. Brugerne i deres test lejer at bruge en anden suffiks i det logon-ID.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

I dette udtryk tage alt til venstre for først @-sign (Word) og sammenkædning med en fast streng.

### <a name="convert-a-multi-value-to-a-single-value"></a>Konvertere en med flere værdier til en enkelt værdi
Nogle attributter i Active Directory er med flere værdier i skemaet, selvom de ser enkelt værdi i Active Directory-brugere og computere. Et eksempel er attributten beskrivelse.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

I dette udtryk i tilfælde af attributten har en værdi, vi tage det første element (punkt) i attributten, fjerne foranstillede og efterstillede mellemrum (Trim) og derefter holde først 448 tegnene (venstre) i strengen.

### <a name="do-not-flow-an-attribute"></a>Ikke dataflow en attribut
Få baggrundsoplysninger om dette scenario til dette afsnit, du se [kontrollere attribut flow processen](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Der er to måder at dataflow ikke en attribut. Først er tilgængelig i installationsguiden og giver dig mulighed for at [fjerne markerede attributter](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Denne indstilling virker, hvis du aldrig har synkroniseret attributten før. Men hvis du har startet at synkronisere denne attribut og fjerne den senere med denne funktion, derefter Synkroniser program Stop administrere attributten og de eksisterende værdier er tilbage i Azure AD.

Hvis du vil fjerne værdien af en attribut, og Sørg for, at det ikke løber fremover, skal du oprette en brugerdefineret regel i stedet.

Hos Fabrikam, har vi opnået, at nogle af de attributter, vi synkronisere til skyen ikke skal være der. Vi vil sikre dig følgende attributter er fjernet fra Azure AD.  
![Forkert filtypenavn attributter](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Oprette en ny regel for indgående synkronisering og udfylde beskrivelsen af ![beskrivelser](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Oprette attributten flyder af typen **udtryk** og med kilden **AuthoritativeNull**. Ordret **AuthoritativeNull** angiver, at værdien skal være tomme, i MV, selvom en lavere prioritet Synkroniser regel forsøger at udfylde værdien.
![Transformation for lokalnummer attributter](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Gemme Synkroniser reglen. Starte **Tjenesten Brugerprofilsynkronisering**, find forbindelsen, Vælg **Kør**, og **Fuld synkronisering**. Dette trin genberegnes alle attribut flyder.
- Kontrollere, at de ønskede ændringer ved at eksporteres ved at søge connector-plads.
![Faseinddelt Slet](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Næste trin

- Få mere at vide om af konfigurationsmodellen i [Forstå deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Læs mere om udtrykssproget i [Forstå deklarativ klargøring udtryk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
