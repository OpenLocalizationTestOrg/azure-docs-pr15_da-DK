<properties
    pageTitle="Azure AD-forbindelse synkronisering: konfiguration af filtrering | Microsoft Azure"
    description="Beskriver, hvordan du konfigurerer filtrering i Azure AD-forbindelse synkronisering."
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
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD-forbindelse synkronisering: konfigurere filtrering
Med filtrering, kan du styre, hvilke objekter skal vises i Azure AD fra din lokale mappe. Standardkonfiguration tager alle objekter i alle domæner i konfigurerede områder. Generelt er dette den anbefalede konfiguration. Slutbrugere ved hjælp af Office 365 arbejdsbelastninger, som Exchange Online og Skype for Business, drage fordel af en komplet globale adresseliste, så de kan sende mail og ringe til alle. Med standardkonfiguration, vil de får den samme oplevelse de ville gøre med en lokal implementering af Exchange- eller Lync.

I nogle tilfælde er det nødvendigt at foretage ændringer af standardkonfigurationen. Her er nogle eksempler:

- Du planlægger at bruge [flere Azure AD-directory topologi](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Derefter skal du anvende et filter for at bestemme, hvilket objekt skal synkroniseres til en bestemt Azure AD-mappe.
- Du kører et pilotprojekt til Azure- eller Office 365, og du kun vil et undersæt af brugere i Azure AD. I den lille pilotprojekt er det ikke vigtigt at have en komplet globale adresseliste til at vise funktionaliteten.
- Du har mange tjenestekonti og andre ikke personlige konti, du ikke vil i Azure AD.
- Du sletter overholdelse årsager ikke en hvilken som helst brugerkonti i det lokale miljø. Du kun deaktivere dem. Men i Azure AD du kun vil aktive konti, der skal være til stede.

I denne artikel omhandler, hvordan du konfigurerer de forskellige spamfiltrering metoder.

> [AZURE.IMPORTANT]Microsoft understøtter ikke, ændring eller driften af Azure AD-forbindelse Synkroniser uden for disse handlinger formelt dokumenteret. Nogle af disse handlinger kan medføre en konflikt eller ikke-understøttet tilstanden for synkronisering af Azure AD-forbindelse, og som et resultat Microsoft yde ikke teknisk support til disse installationer.

## <a name="basics-and-important-notes"></a>Grundlæggende funktioner og vigtige noter
Du kan aktivere filtrering når som helst i Azure AD-forbindelse synkronisering. Hvis du starte med en standardkonfiguration af katalogsynkronisering og derefter konfigurere filtrering, synkroniseres ikke længere de objekter, der er filtreret til Azure AD. Som et resultat af denne ændring slettes alle objekter i Azure AD, der tidligere er blevet synkroniseret, men derefter er blevet filtreret i Azure AD.

Før du begynder at foretage ændringer til filtrering, Sørg for, at du [deaktivere den planlagte opgave](#disable-scheduled-task) så du ikke ved et uheld eksporterer ændringer, du endnu ikke har bekræftet for at være korrekt.

Da filtrering kan fjerne mange objekter på samme tid, vil du kontrollere din nye filtre er korrekte, før du går i gang med at eksportere eventuelle ændringer til Azure AD. Når du har gennemført konfigurationstrinnene, anbefales det, at du følger [trinnene bekræftelse](#apply-and-verify-changes) , før du eksporterer og foretage ændringer i Azure AD.

Hvis du vil beskytte dig i at slette mange objekter ved et uheld, er funktion [Undgå utilsigtede sletninger](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) aktiveret som standard. Hvis du sletter mange objekter på grund af filtrering (500 som standard), skal du følge trinnene i denne artikel for at tillade sletter at gå til Azure AD.

Hvis du bruger et build før November 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), foretager en ændring filter konfiguration, og du bruger synkronisering af adgangskoder, skal du have en komplet synkronisering af alle adgangskoder, når du har fuldført konfigurationen. Trin til at udløse en komplet synkronisering af adgangskoder i [udløse en komplet synkronisering af alle adgangskoder](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Hvis du er på 1.0.9125 eller nyere, derefter beregner handlingen regelmæssig **komplette synkronisering** også hvis adgangskoder skal synkroniseres, og dette ekstra trin er ikke længere påkrævet.

Hvis **user** -objekter ved et uheld er blevet slettet i Azure AD på grund af fejlen filtrering, kan du genskabe user-objekter i Azure AD ved at fjerne din filtrering konfigurationer og synkronisere dine adresselister igen. Denne handling gendanner brugerne fra papirkurven i Azure AD. Du kan dog sletningen andre objekttyper. Eksempelvis hvis du kommer til at slette en sikkerhedsgruppe og den blev brugt til ACL kan en ressource, gruppen og dens ACLs ikke gendannes.

Azure AD-forbindelse sletter kun objekter det én gang har anses for at være i omfang. Hvis der er objekter i Azure AD, der er oprettet af en anden synkronisering-program, og disse objekter ikke er i omfang, tilføje filtrering fjerner ikke dem. Eksempelvis hvis du starter med en DirSync-server og den oprettet en fuldstændig kopi af hele mappen i Azure AD, og du installere en ny Azure AD-forbindelse Synkroniser server parallelt med filtrering er aktiveret, fra begyndelsen, fjernes ikke de ekstra objekter, der er oprettet af DirSync.

Filtrering konfigurationen bevares, når du installere eller opgradere til en nyere version af Azure AD-forbindelse. Det er altid den bedste fremgangsmåde at bekræfte, at konfigurationen ikke er ved et uheld ændret efter en opgradering til en nyere version inden du kører den første synkronisering cyklusdiagram.

Hvis du har mere end ét område, skal de spamfiltrering konfigurationer, der er beskrevet i dette emne anvendes til alle skov (Hvis du vil have den samme konfiguration til dem alle).

### <a name="disable-scheduled-task"></a>Deaktivere planlagt opgave
Hvis du vil deaktivere indbyggede planlæggeren, der udløser en synkronisering cyklus hver 30 minutter, skal du følge disse trin:

1. Gå til en PowerShell-prompt.
2. Køre `Set-ADSyncScheduler -SyncCycleEnabled $False` til at deaktivere planlæggeren.
3. Foretag ændringerne, som beskrevet i dette emne.
4. Køre `Set-ADSyncScheduler -SyncCycleEnabled $True` at aktivere planlæggeren igen.

**Hvis du bruger et Azure AD-forbindelse build før 1.1.105.0**  
Hvis du vil deaktivere den planlagte opgave, der udløser en synkronisering cyklus hver 3 timer, skal du følge disse trin:

1. Starte **Opgavestyring** fra startmenuen.
2. Direkte under **Opgave Scheduler bibliotek**, find den opgave, med navnet **planlæggeren Azure Active Directory-synkronisering**, skal du højreklikke og derefter vælge **Deaktiver**.  
![Opgavestyring](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Du kan nu foretage ændringer i konfigurationen og køre programmet Synkroniser manuelt fra konsollen **synkronisering service manager** .

Når du har fuldført alle ændringerne filtrering, Glem ikke at komme tilbage og **aktivere** opgaven igen.

## <a name="filtering-options"></a>Filtreringsindstillinger
Følgende spamfiltrering konfiguration typer kan anvendes på værktøjet Directory-synkronisering:

- [**Gruppen baseret**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtrering baseret på en enkelt gruppe kan kun konfigureres på indledende installation ved hjælp af installationsguiden. Det er ikke længere omfattet i dette emne.

- [**Domæne-baserede**](#domain-based-filtering): denne indstilling kan du vælge hvilke domæner, der skal synkroniseres til Azure AD. Det gør det også muligt at tilføje og fjerne domæner fra Synkroniser program konfigurationen, hvis du foretager ændringer i din lokale infrastruktur, når du har installeret Azure AD-forbindelse synkronisering.

- [**Organisatorisk-enhed – baseret**](#organizational-unitbased-filtering): denne indstilling for spamfiltrering gør det muligt at vælge, hvilke afdelinger synkroniserer til Azure AD. Denne indstilling er for alle objekttyper i markerede afdelinger.

- [**Attribut-baserede**](#attribute-based-filtering): denne indstilling kan du filtrere objekter, der er baseret på attributværdier i objekterne. Du kan også have forskellige filtre for forskellige objekttyper.

Du kan bruge flere filtreringsindstillingerne på samme tid. For eksempel kan du bruge OU-baseret filtrering for kun at medtage objekter i en OU og på samme tid attribut-baseret filtrering af for at filtrere objekter yderligere. Når du bruger flere spamfiltrering metoder, bruge filtrene en logisk og mellem filtrene.

## <a name="domain-based-filtering"></a>Domæne-baseret filtrering
Dette afsnit giver dig trinnene for at konfigurere dit domæne filter. Hvis du har tilføjet eller fjernet domæner i dit område, når du har installeret Azure AD-forbindelse, har du også opdatere spamfiltrering konfigurationen.

Den foretrukne metode til at ændre domæne-baseret filtrering er ved at køre installationen guiden og ændre [domæne og afdelinger filtrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Installationsguiden automatisere de opgaver, der er beskrevet i dette emne.

Du skal kun følge disse trin, hvis du eller anden grund ikke kan køre installationsguiden.

Domæne-baseret filtrering konfiguration består af følgende:

- [Vælg domænerne](#select-domains-to-be-synchronized) , der skal medtages i synkroniseringen.
- Justere [køre profiler](#update-run-profiles)for hvert tilføjede eller slettede domæne.
- [Anvend og bekræfte ændringer](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Vælg domæner til at blive synkroniseret
**For at angive filteret domæne skal du gøre følgende trin:**

1. Log på den server, der kører synkronisering af Azure AD-forbindelse ved hjælp af en konto, der er medlem af sikkerhedsgruppen **ADSyncAdmins** .
2. Starte **Tjenesten Brugerprofilsynkronisering** fra startmenuen.
3. Vælg **forbindelser** , og vælg forbindelsen med typen **Active Directory Domain Services**i listen **forbindelser** . Vælg **Egenskaber for** **Handlinger**.  
![Egenskaber for forbindelse](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Klik på **Konfigurer Directory partitioner**.
5. Vælg på listen **Vælg directory partitioner** og fjerne markeringen af domænerne efter behov. Kontrollér, at kun de partitioner, du vil synkronisere er markeret.  
![Partitioner](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Hvis du har ændret dine lokale AD-infrastruktur og tilføjet eller fjernet domæner fra området, klik derefter på knappen **Opdater** for at få en opdateret liste. Når du opdaterer, bliver du spurgt om legitimationsoplysninger. Angive en hvilken som helst legitimationsoplysninger med læseadgang til din lokale Active Directory. Det behøver ikke at være den bruger, der er udfyldt på forhånd i dialogboksen.  
![Opdatering er nødvendig](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Når du er færdig, skal du lukke dialogboksen **Egenskaber** ved at klikke på **OK**. Hvis du har fjernet domæner fra området, der en meddelelse pop op-vindue, siger et domæne er blevet fjernet og konfigurationen ryddes op.
7. Fortsætte med at justere [køre profiler](#update-run-profiles).

### <a name="update-run-profiles"></a>Opdatere køre profiler
Hvis du har opdateret dit domæne filter, skal du også opdatere de køre profiler.

1. Sørg for, at den forbindelse, du har ændret i det forrige trin er markeret på listen **forbindelser** . Vælg **Konfigurere køre profiler** **Handlinger**.  
![Forbindelse køre profiler](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Du har brug at justere følgende profiler:

- Fuldstændig Import
- Fuld synkronisering
- Deltaimport
- Delta synkronisering
- Eksportere

For hver af de fem profiler skal du benytte følgende fremgangsmåde for hvert domæne, der er **tilføjet** :

1. Markér den profil, køre, og klik på **Ny trin**.
2. Vælg Trintypen med samme navn som den profil, du vil konfigurere i rullelisten **Filtype** på siden **Konfigurer trin** . Klik derefter på **Næste**.  
![Forbindelse køre profiler](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Vælg navnet på det domæne, du har føjet til dit domæne filter i rullemenuen **Partition** på siden **Konfiguration af Connector** .  
![Forbindelse køre profiler](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Klik på **Udfør**for at lukke dialogboksen **Konfigurer køre profil** .

For hver af de fem profiler skal du benytte følgende fremgangsmåde for hvert **fjernet** domæne:

1. Vælg den køre profil.
2. Hvis **værdien** for attributten **Partition** er en GUID, Vælg det trin, køre, og klik på **Slet trin**.  
![Forbindelse køre profiler](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

Resultatet skal være, at hver domæne, du vil synkronisere skal være angivet som et trin i hver køre profil.

Klik på **OK**for at lukke dialogboksen **Konfigurer køre profiler** .

- At fuldføre konfigurationen, [Anvend og bekræfte ændringer](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Organisatorisk-enhed – baseret filtrering
Den foretrukne metode til at ændre OU-baseret filtrering er ved at køre installationen guiden og ændre [domæne og afdelinger filtrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Installationsguiden automatisere de opgaver, der er beskrevet i dette emne.

Du skal kun følge disse trin, hvis du eller anden grund ikke kan køre installationsguiden.

**Benyt følgende fremgangsmåde for at konfigurere organisatoriske-enhed – baseret filtrering:**

1. Log på den server, der kører synkronisering af Azure AD-forbindelse ved hjælp af en konto, der er medlem af sikkerhedsgruppen **ADSyncAdmins** .
2. Starte **Tjenesten Brugerprofilsynkronisering** fra startmenuen.
3. Vælg **forbindelser** , og vælg forbindelsen med typen **Active Directory Domain Services**i listen **forbindelser** . Vælg **Egenskaber for** **Handlinger**.  
![Egenskaber for forbindelse](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Klik **Konfigurere Directory partitioner**, Vælg det domæne, du vil konfigurere, og klik derefter på **beholdere**.
5. Når du bliver bedt om det, du give nogen legitimationsoplysninger med læseadgang til din lokale Active Directory. Det behøver ikke at være den bruger, der er udfyldt på forhånd i dialogboksen.
6. Fjern de afdelinger, som du ikke vil synkronisere med mappen skyen, og klik derefter på **OK**i dialogboksen **Vælg beholdere** .  
![OU](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - Objektbeholderen **computere, der** skal vælges for computerne Windows 10 skal synkroniseres korrekt med Azure AD. Hvis dit domæne er joinforbundne computere er placeret i andre afdelinger skal du sørge for dem, der er markeret.
  - Objektbeholderen **ForeignSecurityPrincipals** skal være markeret, hvis du har flere områder med tillidsforhold. Denne objektbeholder kan tværs af områder medlemskab af sikkerhedsgrupper løses.
  - **RegisteredDevices** OU skal være markeret, hvis du har aktiveret funktionen enhed tilbageførsel. Hvis du bruger en anden tilbageførsel funktion, som gruppen tilbageførsel kontrollere disse placeringer er markeret.
  - Vælg eventuelle andre OU, hvor brugere, iNetOrgPersons, grupper, kontakter og computere er placeret. Alle dette er placeret i den ManagedObjects OU på billedet.
7. Når du er færdig, skal du lukke dialogboksen **Egenskaber** ved at klikke på **OK**.
8. At fuldføre konfigurationen, [Anvend og bekræfte ændringer](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Attribut-baseret filtrering
Kontrollér, at du er på November 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) eller senere Opbyg for disse trin for at arbejde.

Attributten baseret filtrering er den mest fleksible metode til at filtreringsobjekter. Du kan bruge en potens af [deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md) til at styre næsten alle aspekter af, når et objekt skal synkroniseres til Azure AD.

Filtrering kan anvendes begge på den [indgående](#inbound-filtering) fra Active Directory til metaverse og [udgående](#outbound-filtering) fra metaverse til Azure AD. Det anbefales at anvende filtrering på indgående, da det er lettest at vedligeholde. Udgående filtrering bør kun bruges, hvis det er påkrævet for at deltage i objekter fra mere end én skov, før evalueringen kan finde sted.

### <a name="inbound-filtering"></a>Indgående filtrering
Indgående baseret filtrering bruger standardkonfiguration hvor objekter, gå til Azure AD skal have metaverse attributten cloudFiltered ikke er indstillet til en værdi, der skal synkroniseres. Hvis denne attributværdien er indstillet til **Sand**, er objektet ikke synkroniseret. Den må ikke være angivet til **Falsk** ved design. For at sikre, at andre regler har mulighed for at bidrage til en værdi, denne attribut er kun skulle har værdierne **Sand** eller **NULL** (fraværende).

I den indgående filtrering, kan du bruge en potens af **omfang** til at bestemme, hvilke objekter skal eller skal ikke synkroniseres. Dette er, hvor du foretage justeringer passer til din egen organisation behov. Modulet omfang har **gruppe** og **delsætningen** til at bestemme, hvis en regel for synkronisering skal være i omfang. En **gruppe** indeholder en eller flere **delsætningen**. Der er en logisk og mellem flere delsætninger og et logisk eller mellem flere grupper.

Lad os se på et eksempel:  
![Omfang](./media/active-directory-aadconnectsync-configure-filtering/scope.png) dette skal læses som **(afdeling = IT) eller (afdeling = salgs-og c = US)**.

I eksempler og nedenstående trin, du bruger brugerobjektet som et eksempel, men du kan bruge dette for alle objekttyper.

I eksemplerne nedenfor starter rangorden for værdien med 500. Denne værdi sikrer, at disse regler evalueres efter out of box reglerne (lavere prioritet, højere numeriske værdi).

#### <a name="negative-filtering-do-not-sync-these"></a>Negative filtrering, "ikke synkroniserer disse"
I eksemplet nedenfor du filtrere (synkroniseres ikke) alle brugere, hvor **extensionAttribute15** har værdien **NoSync**.

1. Log på den server, der kører synkronisering af Azure AD-forbindelse ved hjælp af en konto, der er medlem af sikkerhedsgruppen **ADSyncAdmins** .
2. Starte **Synkronisering regler Editor** fra startmenuen.
3. Sørg for **indgående** er markeret, og klik på **Tilføj ny regel**.
4. Giv reglen et beskrivende navn, f.eks. "*i fra AD – bruger DoNotSyncFilter*". Vælg den korrekte skov, **bruger** som **CS objekttype**og **Person,** som den **MV objekttype**. Vælg **deltage** som **Kædetype**og prioritering skriver en værdi, der er i øjeblikket ikke bruges af en anden synkronisering regel (for eksempel 500), og klik derefter på **Næste**.  
![Indgående 1 Beskrivelse](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. Klik på **Tilføj gruppe**i **Scoping filter**, klik på **Tilføj delsætning**, og vælg **ExtensionAttribute15**i attribut. Sørg for operatoren er indstillet til **lig med** og skrive værdien **NoSync** i feltet værdi. Klik på **Næste**.  
![Indgående 2 omfang](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Tomt reglerne **deltage i** , og klik derefter på **Næste**.
7. Klik på **Tilføj Transformation**, vælge **FlowType** til **konstant**, vælg destinationsattributten **cloudFiltered** og i tekstfeltet kilde Skriv **True**. Klik på **Tilføj** for at gemme reglen.  
![Indgående 3 transformation](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. At fuldføre konfigurationen, [Anvend og bekræfte ændringer](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positive filtrering, "kun synkronisere disse"
Kan være sværere at udtrykke positive filtrering, da du skal også overveje objekter, der ikke er indlysende der skal synkroniseres, såsom mødelokalerne.

Positive Filtreringsindstillingen kræver to Synkroniser regler. En (eller flere) med det korrekte omfanget af objekter til at synkronisere, og en anden regel opbevaringsstedet synkronisering, filtrere alle objekter, der endnu ikke er identificeret som et objekt, der skal synkroniseres.

I eksemplet nedenfor kan du kun synkronisere user-objekter, hvor attributten afdeling har værdien **Salg**.

1. Log på den server, der kører synkronisering af Azure AD-forbindelse ved hjælp af en konto, der er medlem af sikkerhedsgruppen **ADSyncAdmins** .
2. Starte **Synkronisering regler Editor** fra startmenuen.
3. Sørg for **indgående** er markeret, og klik på **Tilføj ny regel**.
4. Giv reglen et beskrivende navn, som "*i fra AD-bruger salg synkroniserer*". Vælg den korrekte skov, **bruger** som **CS objekttype**og **Person,** som den **MV objekttype**. Vælg **deltage** som **Kædetype**og prioritering skriver en værdi, der er i øjeblikket ikke bruges af en anden synkronisering regel (for eksempel 501), og klik derefter på **Næste**.  
![Indgående 4 beskrivelse](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. Klik på **Tilføj gruppe**i **Scoping filter**, klik på **Tilføj delsætning**, og vælg **afdeling**i attribut. Sørg for operatoren er indstillet til **lig med** og skrive værdien **Salg** i feltet værdi. Klik på **Næste**.  
![Indgående 5 omfang](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Tomt reglerne **deltage i** , og klik derefter på **Næste**.
7. Klik på **Tilføj Transformation**, vælge **FlowType** til **konstant**, Vælg Target attributten **cloudFiltered** og i feltet kilde skal du skrive **Falsk**. Klik på **Tilføj** for at gemme reglen.  
![Indgående 6 transformation](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Dette er et særligt tilfælde, hvor du angiver cloudFiltered eksplicit til False.

    Vi har nu oprette reglen for opbevaringsstedet synkronisering.

8. Giv reglen et beskrivende navn, f.eks. "*i fra AD-bruger opbevaringsstedet filter*". Vælg den korrekte skov, **bruger** som **CS objekttype**og **Person,** som den **MV objekttype**. Vælg **deltage i** , og skriv en værdi, der i øjeblikket ikke bruges af en anden synkronisering regel (for eksempel 600) i rangorden for som **Kædetype**. Du har valgt en rangorden for værdi højere (lavere prioritet) end den forrige synkronisering regel, men også venstre plads, så vi kan tilføje flere spamfiltrering Synkroniser regler senere, når du vil starte synkronisering af flere afdelinger. Klik på **Næste**.  
![Indgående 7 beskrivelse](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Tomt **Scoping filter** , og klik på **Næste**. Et tomt filter angiver reglen skal gælde for alle objekter.
10. Tomt reglerne **deltage i** , og klik derefter på **Næste**.
11. Klik på **Tilføj Transformation**, vælge **FlowType** til **konstant**, vælg destinationsattributten **cloudFiltered** og i tekstfeltet kilde Skriv **True**. Klik på **Tilføj** for at gemme reglen.  
![Indgående 3 transformation](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. At fuldføre konfigurationen, [Anvend og bekræfte ændringer](#apply-and-verify-changes).

Hvis du vil, kan du oprette flere regler for den første type, hvor du medtager flere objekter i vores synkronisering.

### <a name="outbound-filtering"></a>Udgående filtrering
Det er nødvendigt at udføre filtrering, når objekterne, der deltager i metaverse i nogle tilfælde. Det kan, for eksempel være påkrævet for at se på attributten mail fra den ressourceskov og userPrincipalName-attributten fra konto skov til at bestemme, hvis et objekt skal synkroniseres. I disse tilfælde kan oprette du filtrering på den udgående regel.

I dette eksempel skal du ændre de spamfiltrering så kun brugere, hvor både mail og userPrincipalName slutter med @contoso.com synkroniseres:

1. Log på den server, der kører synkronisering af Azure AD-forbindelse ved hjælp af en konto, der er medlem af sikkerhedsgruppen **ADSyncAdmins** .
2. Starte **Synkronisering regler Editor** fra startmenuen.
3. Klik på **udgående**under **Regler Type**.
4. Find den regel, der hedder **ud til AAD – bruger deltage i SOAInAD**. Klik på **Rediger**.
5. I pop op-vinduet, svar **Ja** for at oprette en kopi af reglen.
6. På siden **Beskrivelse** skal du ændre rangorden for til en ubrugt værdi, for eksempel 50.
7. Klik på **Scoping filter** på navigationslinjen til venstre. Klik på **Tilføj-delsætning**, i attributten Vælg **mail**, operatoren Vælg **ENDSWITH**og værditype **@contoso.com**. Klik på **Tilføj-delsætning**, i attributten Vælg **userPrincipalName**, operatoren Vælg **ENDSWITH**og værditype **@contoso.com**.
8. Klik på **Gem**.
9. At fuldføre konfigurationen, [Anvend og bekræfte ændringer](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Anvende og bekræfte ændringer
Når du har foretaget dine ændringer i konfigurationen, skal disse anvendes på de objekter, der allerede findes i systemet. Det kan også være, at objekter ikke i øjeblikket i Synkroniser engine skal behandles og synkronisere program skal læse kildesystemet igen for at bekræfte dens indhold.

Hvis du har ændret konfiguration med **domæne** eller **organisationsenhed** filtrering, skal du gøre **Fuldstændig import** efterfulgt af **Delta synkronisering**.

Hvis du har ændret konfiguration med **attributten** filtrering, skal du gøre **komplette synkronisering**.

Gøre følgende:

1. Starte **Tjenesten Brugerprofilsynkronisering** fra startmenuen.
2. Vælg **forbindelser** , og vælg den forbindelse, hvor du har oprettet en konfiguration, ændre tidligere i listen **forbindelser** . Fra **Handlinger**skal du vælge **Kør**.  
![Køre forbindelse](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. **Køre profiler**, Vælg den handling, der er nævnt i forrige afsnit. Hvis du vil køre to handlinger skal køre andet, når den første opgave er fuldført (kolonnen **tilstand** er **inaktiv** for den markerede forbindelse).

Efter synkronisering, er det alle ændringer, der er nødvendige for at kunne eksporteres. Før du rent faktisk foretage ændringer i Azure AD, vil du bekræfte, at alle ændringerne er korrekte.

1. Starte en cmd prompt, og gå til`%Program Files%\Microsoft Azure AD Sync\bin`
2. Køre:`csexport "Name of Connector" %temp%\export.xml /f:x`  
Navnet på forbindelsen kan findes i tjenesten Brugerprofilsynkronisering. Den har et navn, der svarer til "contoso.com – AAD" til Azure AD.
3. Køre:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Du har nu en fil i % temp % med navnet export.csv, der kan undersøges i Microsoft Excel. Denne fil indeholder alle ændringer, der er ved at eksporteres.
5. Foretage ændringer i dataene og konfiguration og køre disse trin igen (Importér, Synkroniser, og Bekræft), indtil de ændringer, der er ved at eksporteres forventes.

Når du er færdig, kan du eksportere ændringerne til Azure AD.

1. Vælg **forbindelser** , og vælg den Azure AD-forbindelse på listen **forbindelser** . Fra **Handlinger**skal du vælge **Kør**.
2. **Køre profiler**, Vælg **eksportere**.
3. Hvis dine ændringer i konfigurationen sletter mange objekter, så vist du en fejl på Eksportér når antallet er større end den konfigurerede tærskelværdi (som standard 500). Hvis du ser denne fejl, skal du midlertidigt deaktivere funktionen [Undgå utilsigtede sletninger](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Nu er det tid til at aktivere planlæggeren igen.

1. Starte **Opgavestyring** fra startmenuen.
2. Direkte under **Opgave Scheduler bibliotek**, find den opgave, med navnet **planlæggeren Azure Active Directory-synkronisering**, skal du højreklikke og derefter vælge **Aktivér**.

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
