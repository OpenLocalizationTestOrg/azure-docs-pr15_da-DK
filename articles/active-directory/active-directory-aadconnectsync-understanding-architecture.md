<properties
   pageTitle="Azure AD-forbindelse synkronisering: om arkitekturen | Microsoft Azure"
   description="Dette emne beskriver arkitekturen i Azure AD-forbindelse Synkroniser, og de ord, der bruges."
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

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD-forbindelse synkronisering: om arkitekturen
I dette emne beskrives de grundlæggende arkitektur til synkronisering af Azure AD-forbindelse. I mange aspekter er det svarer til de foregående opgaver MIIS 2003, ILM 2007 og FIM 2010. Azure AD-forbindelse synkronisering er udviklingen af disse teknologier. Hvis du er bekendt med en af disse tidligere teknologier, vil indholdet af dette emne også have kendskab til dig. Hvis du er ny bruger af synkronisering, er dette emne for dig. Det er dog ikke et krav til at se flere detaljer i dette emne kan lykkes i foretage tilpasninger til synkronisering af Azure AD-forbindelse (kaldet Synkroniser program i dette emne).

## <a name="architecture"></a>Arkitektur
Synkroniser programmet opretter en integreret visning af objekter, der er gemt i flere forbundne datakilder og administrerer identitetsoplysninger i de pågældende datakilder. Denne integrerede visning bestemmes af de identitetsoplysninger, der er hentet fra forbundne datakilder og et regelsæt for at bestemme, hvordan til at behandle disse oplysninger.

### <a name="connected-data-sources-and-connectors"></a>Forbundne datakilder og forbindelser
Synkroniser program behandler identitetsoplysninger fra forskellige typer lagre, som Active Directory eller en SQL Server-database. Hver datalager, der organiserer dataene i et databaselignende format og, der indeholder standard dataadgang metoder er en potentiel datakilde kandidat til synkronisering af programmet. De typer datalagre, der er synkroniseret ved synkronisering program kaldes **forbundet datakilder** eller **forbindelse kataloger** (CD).

Synkroniser program indkapsler interaktion med en forbundne datakilde i et modul, kaldet en **forbindelse**. Hver type af forbundne data imidlertid har en bestemt forbindelse. Forbindelsen oversætter en påkrævet handling i det format, som den forbundne datakilde forstår.

Forbindelser ringe API til at udveksle oplysninger om identitet (både læse og skrive) med en forbundne datakilde. Du kan også tilføje en brugerdefineret forbindelse ved hjælp af extensible connectivity framework. Følgende illustration viser, hvordan en forbindelse forbinder forbundne datakilde til synkronisering af programmet.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Kan dataflow i den ønskede retning, men den kan ikke dataflow i begge retninger samtidigt. Med andre ord, en forbindelse kan konfigureres til at give mulighed at flyde fra forbundne datakilden for at synkronisere program eller Synkroniser program til den forbundne datakilde, men kun én af disse handlinger kan opstå på én gang for et objekt og attributter. Retningen kan være forskellige for forskellige objekter og andre attributter.

Hvis du vil konfigurere en forbindelse, skal angive du de objekttyper, du vil synkronisere. Angive objekttyperne definerer omfanget af objekter, der er inkluderet i processen til synkronisering. Næste trin er at vælge attributterne til at synkronisere, som kaldes en liste med attributter optagelse. Disse indstillinger kan ændres helst svar ændringer til dine forretningsregler. Når du bruger installationsguiden af Azure AD-forbindelse, konfigureres disse indstillinger for dig.

Hvis du vil eksportere objekter til en forbundet datakilde, skal Attributlisten skal indgå indeholde mindst de mindste attributter, der kræves for at oprette en bestemt objekttype i en forbundet datakilde. For eksempel **sAMAccountName** -attribut, der skal medtages på listen attribut optagelse eksportere et brugerobjekt til Active Directory, fordi alle user-objekter i Active Directory skal have en **sAMAccountName** -attribut, der er defineret. Installationsguiden understøtter igen, denne konfiguration for dig.

Hvis forbindelsen datakilden anvender strukturel komponenter, som partitioner eller beholdere til at organisere objekter, kan du begrænse de områder i forbundne datakilden, der bruges til en given løsning.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Interne struktur af navneområdet Synkroniser program
Navneområdet hele Synkroniser program består af to navneområder, gemmer oplysningerne om identitet. De to navneområder er:

- Connector-plads (CS)
- Metaverse (MV)

**Connector-plads** er midlertidigt område, der indeholder repræsentationer af de angivne objekter fra en forbundet datakilde, og de attributter, der er angivet på listen attribut optagelse. Synkroniser program bruger connector-plads til at bestemme, hvad der er ændret i forbundne datakilden og fase indgående ændringer. Synkroniser program bruger også connector-plads til at fase udgående ændringer til eksport til den forbundne datakilde. Synkroniser program fører en entydige connector-plads som en midlertidige for hver forbindelse.

Synkroniser program forbliver uafhængigt af de forbundne datakilder ved hjælp af en midlertidige, og påvirkes ikke af deres tilgængelighed og hjælp til handicappede. Som et resultat, kan du behandle identitetsoplysninger når som helst ved hjælp af dataene i det midlertidige område. Synkroniser program kan anmode om de ændringer, der er foretaget i forbundne datakilden siden den sidste kommunikationssessionen blev afsluttet eller push ud alene identitetsoplysninger, som den forbundne datakilde, der endnu ikke modtaget, hvilket reducerer netværkstrafik mellem engine synkronisering og den forbundne datakilde.

Desuden gemmer Synkroniser program statusoplysninger om alle objekter, den faser i connector-plads. Når der modtages nye data, evaluerer Synkroniser program altid, om dataene er blevet synkroniseret.

**Metaverse** er et lagerområde, der indeholder aggregeret identitetsoplysninger fra flere forbundne datakilder, give en enkelt globale, integreret visning af alle kombinerede objekter. Metaverse objekter er oprettet ud fra oplysningerne om identitet, der er hentet fra de forbundne datakilder og et sæt regler, så du kan tilpasse processen til synkronisering.

Følgende illustration viser navneområdet forbindelse plads og metaverse navneområdet i programmet til synkronisering.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synkroniser program identitet objekter
Objekter i programmet til synkronisering er repræsentationer af enten objekter i den forbundne datakilde eller integreret visning, der synkroniseres program har disse objekter. Hver synkronisering program objekt skal have et globalt entydigt id (GUID). GUID sikre dataintegritet og udtrykkelig relationer mellem objekter.

### <a name="connector-space-objects"></a>Forbindelse mellemrum objekter
Når Synkroniser program kommunikerer med en forbundne datakilde, læser identitetsoplysninger i forbundne datakilden og bruger disse oplysninger til at oprette en repræsentation af objektet identitet i connector-plads. Du kan ikke oprette eller slette disse objekter enkeltvis. Dog kan du manuelt slette alle objekter i et område af forbindelse.

Alle objekter i den forbindelse plads har to attributter:

- Et globalt entydigt id (GUID)
- Et entydigt navn (DN)

Hvis den forbundne datakilde tildeles en entydig attribut på objektet, kan objekter i den forbindelse plads også have attributten anker. Attributten anker identificerer entydigt et objekt i forbundne datakilden. Synkroniser program bruger anker til at finde den tilsvarende repræsentation af dette objekt i forbundne datakilden. Synkroniser program antages, at anker af et objekt aldrig ændres i objektet levetid.

Mange af forbindelserne bruger et kendt entydigt id til at oprette et anker automatisk for de enkelte objekter, når den er importeret. For eksempel bruger Active Directory Connector attributten **objectGUID** for et anker. For forbundne datakilder, der ikke har et klart definerede entydigt id, kan du angive anker generering af som en del af konfigurationen forbindelse.

I den valgte fald anker er oprettet fra et eller flere entydige egenskaber for et objekt, hverken af hvilke ændringer, og, som entydigt identificerer objektet i connector-plads (for eksempel en medarbejder tal eller et bruger-ID).

En forbindelse mellemrum objekt kan være et af følgende:

- Et midlertidige objekt
- En pladsholder

### <a name="staging-objects"></a>Midlertidige objekter
Et midlertidige objekt repræsenterer en forekomst af de angivne objekttyper fra forbundne datakilden. Ud over GUID og det entydige navn har en midlertidig objekt altid en værdi, der angiver objekttypen.

Midlertidige objekter, der er blevet importeret altid indeholde en værdi for attributten anker. Midlertidige objekter, der har klargjort nyligt ved synkronisering program og er i gang med der oprettes i forbundne datakilden har ikke en værdi for attributten anker.

Midlertidige objekter udføre også aktuelle værdier over business attributter og drift oplysninger, der skal bruges ved synkronisering program til at udføre processen til synkronisering. Have oplysninger omfatter flag, som angiver typen af opdateringer, der er midlertidigt på midlertidige objektet. Hvis et midlertidige objekt har modtaget nye identitetsoplysninger fra forbundne datakilden, der endnu ikke er blevet behandlet, er objektet markeret som **ventende import**. Hvis en midlertidig objekt har nye identitetsoplysninger, der endnu ikke er blevet eksporteret til forbundne datakilden, er det markeret som **ventende Eksportér**.

Et midlertidige objekt kan være en import eller eksport objekt. Synkroniser programmet opretter et importobjekt ved hjælp af objektoplysninger modtaget fra forbundne datakilden. Når Synkroniser program modtager oplysninger om eksistensen af et nyt objekt, der svarer til en af de objekttyper, der er valgt i forbindelsen, oprettes der et ActiveX-objektet i connector-plads som en repræsentation af objektet i forbundne datakilden.

Følgende illustration viser et importobjekt, der repræsenterer et objekt i forbundne datakilden.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Synkroniser programmet opretter et Eksportér objekt ved hjælp af objektoplysninger i metaverse. Eksportér objekter eksporteres til den forbundne datakilde i den næste kommunikation session. Fra perspektivet Synkroniser-program findes Eksportér objekter ikke i den forbundne datakilde endnu. Attributten anker for et Eksportér objekt er derfor ikke tilgængelig. Når den modtager objektet fra Synkroniser program, opretter den forbundne datakilde en entydig værdi for attributten anker for objektet.

Følgende illustration viser, hvordan der oprettes et Eksportér objekt ved hjælp af id-oplysninger i metaverse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Synkroniser program bekræfter eksport af objektet ved genimporter objektet fra forbundne datakilden. Eksportér objekter bliver Importer objekter, når Synkroniser program modtager dem under næste importen fra den pågældende forbundne datakilde.

### <a name="placeholders"></a>Pladsholdere
Synkroniser program bruger en flad navneområde til at gemme objekter. Men bruge nogle forbundne datakilder som Active Directory en hierarkisk struktur. Hvis du vil omdanne oplysninger fra en hierarkisk struktur til en flad navneområde, bruger synkronisering program pladsholdere til at bevare hierarkiet.

Hver enkelt pladsholder repræsenterer en komponent (for eksempel en organisationsenhed) i et objekt hierarkiske navn, der ikke er importeret til synkronisering program, men er påkrævet for at oprette hierarkiske navnet. Udfylde huller, der er oprettet af referencer i forbundne datakilden til objekter, der ikke arrangere objekter i connector-plads.

Synkroniser program bruger også pladsholdere til at gemme der refereres til objekter, som endnu ikke er blevet importeret. Eksempelvis hvis synkronisering er konfigureret til at medtage attributten manager for objektet *Abbie Sørensen* og den modtagne værdi er et objekt, der ikke er blevet importeret endnu, f.eks *CN = Lee Sperry, CN = brugere, DC = fabrikam, DC = com*, manager oplysningerne gemmes som pladsholdere i connector-plads. Hvis objektet manager senere importeres, overskrives objektet pladsholder midlertidige objektet, der repræsenterer manager.

### <a name="metaverse-objects"></a>Metaverse objekter
Et metaverse objekt indeholder visningen aggregeret pågældende synkroniser program har midlertidige objekter i connector-plads. Synkroniser program opretter metaverse objekter ved hjælp af oplysningerne i Importér objekter. Flere forbindelse mellemrum objekter kan sammenkædes med et enkelt metaverse objekt, men et forbindelse mellemrum objekt kan ikke være kædet sammen med mere end ét metaverse objekt.

Metaverse objekter kan ikke oprettes manuelt eller slettes. Synkroniser program sletter automatisk metaverse objekter, der ikke har en kæde til en hvilken som helst forbindelse mellemrum objekt i connector-plads.

For at tilknytte objekter inden for en forbundet datakilde til en tilsvarende objekttype i metaverse, giver Synkroniser engine et extensible skema med et foruddefineret sæt objekttyper og tilknyttede attributter. Du kan oprette nye objekttyper og attributter for metaverse objekter. Attributter kan være enkeltværdi- eller med flere værdier, og typerne attributten kan være strenge, referencer, tal og booleske værdier.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relationer mellem midlertidige og metaverse objekter
I navneområdet Synkroniser program aktiveres dataflow som link relationen mellem midlertidige og metaverse objekter. Et midlertidige objekt, der er sammenkædet med et metaverse objekt kaldes en **tilsluttet sig objekt** (eller **connector-objektet**). Et midlertidige objekt, der ikke er knyttet til et metaverse objekt kaldes en **afbrudt objekt** (eller **Disconnector-objekt**). Betingelserne deltager og afbrudt foretrukne ikke forveksles med de forbindelser, der er ansvarlig for at importere og eksportere data fra en forbundet mappe.

Pladsholdere er aldrig sammenkædet med et metaverse objekt

Et joinforbundne objekt består af et midlertidige objekt og dets sammenkædede forhold til et enkelt metaverse objekt. Joinforbundne objekter bruges til at synkronisere attributværdier mellem en forbindelse mellemrum objekt og et metaverse objekt.

Når et midlertidige objekt bliver et joinforbundne objekt under synkronisering, kan attributter dataflow mellem den midlertidige objekterne og metaverse. Attributten flow er tovejs og er konfigureret ved hjælp af Importer attributregler og Eksportér attributregler.

Et enkelt stik mellemrum objekt kan sammenkædes med kun én metaverse objekt. Hvert metaverse objekt kan dog sammenkædes med flere forbindelse mellemrum objekter i den samme eller i forskellige forbindelse mellemrum, som vist i nedenstående illustration.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

Sammenkædede forholdet mellem det midlertidige objekt og et metaverse objekt er fast og kan fjernes kun af regler, du angiver.

Et afbrudt objekt er et midlertidige objekt, der ikke er knyttet til en hvilken som helst metaverse objekt. Attributten værdier i et afbrudt objekt ikke er behandlet foretage yderligere i metaverse. Attributten værdierne i det tilsvarende objekt i forbundne datakilden opdateres ikke ved at synkronisere program.

Ved hjælp af afbrudt objekter, kan du gemme identitetsoplysninger i Synkroniser program og behandle den senere. Holde styr på et midlertidige objekt som en afbrudt objekt i connector-plads har mange fordele. Det er ikke nødvendigt at oprette en repræsentation af dette objekt igen under næste importen fra forbundne datakilden, fordi systemet har allerede gemt de nødvendige oplysninger om dette objekt. Denne måde, Synkroniser program har altid et komplet øjebliksbillede af forbundne datakilden, selvom der er ingen aktuelle forbindelse til den forbundne datakilde. Afbrudt objekter kan konverteres til joinforbundne objekter og omvendt, afhængigt af de regler, du angiver.

Et ActiveX-objektet er oprettet som et afbrudt objekt. Et Eksportér objekt skal være et joinforbundne objekt. System-logik gennemtvinger denne regel og sletter alle Eksportér objekt, der ikke er et joinforbundne objekt.

## <a name="sync-engine-identity-management-process"></a>Synkroniser program identitet management-proces
Processen identitet management styrer, hvordan identitetsoplysninger opdateres mellem forskellige forbundne datakilder. Identitet management forekommer i tre processer:

- Importér
- Synkronisering
- Eksportere

Under importen evaluerer Synkroniser program indgående identitet oplysningerne fra en forbundet datakilde. Når ændringer registreres, det enten opretter nye midlertidige objekter eller opdaterer eksisterende midlertidige objekter i den forbindelse plads til synkronisering.

Under synkroniseringen, Synkroniser program opdateres metaverse for at afspejle ændringer, der er opstået i connector-plads og connector-plads for at afspejle ændringer, der er opstået i metaverse.

Under eksporten flytter Synkroniser program ud ændringer, der er gemt på midlertidige objekter og, der er markeret med flag som afventer Eksportér.

De følgende illustration viser, hvor hver af processerne, der foretages som identitet oplysninger flyder fra én forbundne datakilde til en anden.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Importprocessen
Under importen evaluerer Synkroniser program opdateringer identitetsoplysninger. Synkroniser program sammenligner de identitetsoplysninger, der er modtaget fra forbundne datakilden med identitetsoplysninger om et midlertidige objekt og bestemmer, om objektet midlertidige kræver opdateringer. Hvis det er nødvendigt at opdatere objektet midlertidige med nye data, er det midlertidige objekt markeret som afventer import.

Ved at arrangere objekter i connector mellemrummet før synkronisering, kan synkronisere program behandle kun de identitetsoplysninger, der er blevet ændret. Denne proces indeholder følgende fordele:

- **Effektiv synkronisering**. Mængden data behandles under synkronisering er minimeret.
- **Effektiv ny synkronisering**. Du kan ændre, hvordan synkronisering program behandler identitetsoplysninger uden at genoprette forbindelse til synkronisering af programmet til datakilden.
- **Mulighed for at få vist synkronisering**. Du kan få vist synkronisering for at bekræfte, at dine antagelser om identitet management processen er korrekte.

For hvert objekt, der er angivet i forbindelsen, forsøger Synkroniser programmet først at lokalisere en gengivelse af objektet i connector-plads for forbindelsen. Synkroniser program undersøger alle midlertidige objekter i connector-plads og forsøger at finde et tilsvarende midlertidige objekt, der har en tilsvarende anker attribut. Hvis ingen eksisterende midlertidige objekt har en tilsvarende anker-attribut, forsøger Synkroniser program til at finde en tilsvarende midlertidige objekt med det samme entydige navn.

Når Synkroniser program finder et midlertidige objekt, der svarer til af entydigt navn, men ikke af anker, sker speciel der følgende:

- Hvis det objekt, der er placeret i den forbindelse plads har ingen anker, derefter Synkroniser program fjerner dette objekt fra connector-plads og markerer objektet metaverse den sammenkædes med som **Prøv igen klargøring af næste synkronisering køre**. Det opretter derefter det nye import-objekt.
- Hvis det objekt, der er placeret i den forbindelse plads har et anker, derefter Synkroniser program antages det, at dette objekt er blevet omdøbt eller slettet i mappen forbundne. Den tildeler en midlertidig, ny entydigt navn for objektet forbindelse mellemrum, så den kan fase objektet indgående. Det gamle objekt bliver derefter **midlertidige**, venter på forbindelsen for at importere Omdøb eller sletning for at løse problemet.

Hvis Synkroniser program finder et midlertidige objekt, der svarer til det objekt, der er angivet i forbindelsen, bestemmer hvilken slags anvende ændringerne. For eksempel Synkroniser program kan omdøbe eller slette objektet i forbundne datakilden, eller den kan kun opdatere objektets attributter værdier.

Midlertidige objekter med opdaterede data er markeret som afventer import. Forskellige typer afventer importerer er tilgængelige. Afhængigt af resultatet af importprocessen har et midlertidige objekt i connector-plads et af følgende afventer typer import:

- **Ingen**. Ingen ændringer af enhver af egenskaber for objektet midlertidige er tilgængelige. Synkroniser program markerer ikke denne type som afventer import.
- **Tilføje**. Midlertidige objektet er et nyt Importér objekt i connector-plads. Synkroniser program markerer denne type som afventer import til yderligere behandling i metaverse.
- **Opdater**. Synkroniser program finder en tilsvarende midlertidige objekt i connector-plads og markerer denne type som afventer Importér, så opdateringer til attributterne kan behandles i metaverse. Opdateringer indeholder objekt omdøbe.
- **Slette**. Synkroniser program finder en tilsvarende midlertidige objekt i connector-plads og markerer denne type som afventer Importér, så det sammenkædede objekt kan slettes.
- **Slet/tilføje**. Synkroniser program finder en tilsvarende midlertidige objekt i connector-plads, men objekttyperne stemmer ikke overens. I dette tilfælde en Slet-Tilføj ændring er midlertidigt. A Slet-Tilføj ændring angiver til synkronisering-program, skal være en komplet ny synkronisering af dette objekt, da forskellige typer regler gælder for dette objekt, når objekttype ændringer.

Det er muligt at reducere mængden af data, der er behandlet under synkronisering, fordi gøre, så kan der behandle kun de objekter, der har opdateret data betydeligt ved at angive ventende importstatus for en midlertidig objekt.

### <a name="synchronization-process"></a>Synkroniseringsprocessen
Synkronisering består af to relaterede processer:

- Indgående synkronisering, når indholdet af metaverse opdateres ved hjælp af dataene i den forbindelse plads.
- Udgående synkronisering, når indholdet af det tomme område forbindelsen opdateres ved hjælp af data i metaverse.

Ved hjælp af de oplysninger, der er midlertidigt gemt i den forbindelse plads, opretter processen til indgående synkronisering i metaverse integreret visning af de data, der er gemt i de forbundne datakilder. Alle midlertidige objekter, eller kun dem med en ventende importere oplysninger sammenlagt afhængigt af hvordan reglerne, der er konfigureret.

Processen udgående synkroniseringsopdateringer eksportere objekter, når metaverse objekter ændres.

Indgående synkronisering opretter visningen integreret i metaverse identitet oplysninger, der er modtaget fra forbundne datakilder. Synkroniser program kan behandle identitetsoplysninger når som helst ved hjælp af de nyeste identitetsoplysninger, der er fra forbundne datakilden.

**Indgående synkronisering**

Indgående synkronisering indeholder følgende processer:

- **Klargøring** (også kaldet **projicering** Hvis det er vigtigt at skelne denne proces fra udgående synkronisering klargøring). Synkroniser programmet opretter et nyt metaverse-objekt, der er baseret på en midlertidig objekt og sammenkæder dem. Klargøring er en handling på objektniveau.
- **Deltage i**. Synkroniser program link til et midlertidige objekt til et eksisterende metaverse objekt. En joinforbindelse er handlingen på objektniveau.
- **Importér attribut forløb**. Synkroniser program opdaterer attributværdier, kaldet attribut flow, for objektet i metaverse. Importér attribut flow er en attribut niveau handling, der kræver en forbindelse mellem en midlertidig objekt og et metaverse objekt.

Klargøring er den eneste proces, der opretter objekter i metaverse. Klargøring påvirker kun importere objekter, der er afbrudt objekter. Under klargøring opretter Synkroniser program et metaverse-objekt, der svarer til objekttypen for importobjektet og opretter et link mellem begge objekter, og som derfor oprette et joinforbundne objekt.

Deltag i processen opretter også et link mellem Importer objekter og et metaverse objekt. Forskellen mellem deltage i og klargøring er, at deltage i processen kræver, at ActiveX-objektet er knyttet til et eksisterende metaverse objekt, hvor klargøring processen opretter et nyt metaverse objekt.

Synkroniser program forsøger at deltage i et ActiveX-objektet til et metaverse objekt ved hjælp af kriterier, der er angivet i synkronisering regel konfigurationen.

Synkroniser program links under processer klargøring og deltage i et afbrudt objekt til et metaverse-objekt, hvilket gør dem tilsluttet sig. Når du har udført disse handlinger på objektniveau, kan Synkroniser program opdatere attributten værdierne i det tilknyttede metaverse objekt. Denne proces kaldes Importér attribut forløb.

Importér attribut flow opstår på alle Importér objekter udføre nye data, der er sammenkædet med et metaverse objekt.

**Udgående synkronisering**

Udgående synkroniseringsopdateringer eksportere objekter, når et metaverse objekt ændres, men slettes ikke. Formålet med udgående synkronisering er at vurdere om ændringer til metaverse objekter kræver opdateringer til arrangering objekter i felterne forbindelse. I nogle tilfælde ændringerne kan kræve, at midlertidigt objekter i alle forbindelse mellemrum opdateres. Midlertidige objekter, der er ændret, markeres som afventer Eksportér, hvilket gør dem eksportere objekter. Disse Eksportér objekter senere publiceres til den forbundne datakilde under eksporten.

Udgående synkronisering har tre processer:

- **Klargøring**
- **Ophævelse af klargøring**
- **Eksportere attribut flow**

Klargøring og ophævelse af klargøring er begge objektniveau handlinger. Ophævelse af klargøring, afhænger af, klargøring, fordi kun klargøring kan starte den. Ophævelse af klargøring udløses, når klargøring fjerner sammenkædningen mellem et metaverse objekt og en Eksportér objekt.

Klargøring af udløses altid, når ændringerne anvendes på objekter i metaverse. Når der foretages ændringer til metaverse objekter, kan Synkroniser program udføre følgende opgaver som en del af processen klargøring:

- Oprette sammenkædede objekter, hvor en metaverse objektet er kædet til et nyoprettet Eksportér objekt.
- Omdøbe en joinforbundne objekt.
- Disjoin kæder mellem et metaverse objekt og arrangere objekter, oprette et afbrudt objekt.

Hvis klargøring kræver synkronisering program til at oprette en ny forbindelse objekt, midlertidige objektet som metaverse objektet er sammenkædet er altid et Eksportér objekt objektet findes endnu ikke i den forbundne datakilde.

Ophævelse af klargøring udløses, hvis klargøring kræver synkronisering engine til disjoin et joinforbundne objekt, oprette et afbrudt objekt. Processen deprovisioning sletter objektet.

Under ophævelse af klargøring, sletning af et objekt Eksportér sletter fysisk ikke objektet. Objektet er markeret som **slettet**, hvilket betyder, at sletningen er midlertidigt på objektet.

Eksportér attribut flow opstår også under udgående synkroniseringen, på samme måde, at importere attribut flow opstår under indgående synkronisering. Eksportér attribut flow opstår kun mellem metaverse og eksport af objekter, der er sammenføjet.

### <a name="export-process"></a>Eksporten
Under eksporten undersøger Synkroniser program alle Eksportér objekter, der er markeret med flag som afventer Eksportér på den forbindelse plads, og derefter sender opdateringer til den forbundne datakilde.

Synkroniser programmet kan se en vellykket eksport, men det tilstrækkeligt bestemmes ikke, identitet management processen er fuldført. Objekter i den forbundne datakilde kan altid ændres af andre processer. Det er ikke tilstrækkelig til at foretage antagelser om egenskaberne for et objekt i kun baseret på en meddelelse om at eksportere forbundne datakilden, fordi Synkroniser program ikke har en fast forbindelse til den forbundne datakilde.

For eksempel en proces i forbundne datakilden kunne ændre objektets attributter tilbage til de oprindelige værdier (det vil sige, forbundne datakilden kunne overskrive værdierne umiddelbart efter dataene ved at synkronisere program overføres og anvendt i forbundne datakilden).

Synkroniser program butikker eksportere og importere statusoplysninger om hvert midlertidige objekt. Hvis værdierne i de attributter, der er angivet på listen attribut optagelse er blevet ændret siden den seneste eksport, opbevaring af Importer og Eksporter status kan synkronisere program, du vil reagere korrekt. Synkroniser program bruger importen til at bekræfte attributværdier, der er eksporteret til den forbundne datakilde. En sammenligning mellem de importerede og eksporterede oplysninger, kan som vist i nedenstående illustration synkronisere program til at afgøre, om eksporten er fuldført, eller hvis den skal gentages.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Eksempelvis hvis Synkroniser program eksporterer attribut C, som har en værdi af 5, til en forbundet datakilde, gemmes den C = 5 i hukommelsen dens Eksportér status. Hver ekstra Eksportér på dette objekt resultater i et forsøg på at eksportere C = 5 til den forbundne datakilde igen, da Synkroniser program antager, at denne værdi ikke er vedvarende anvendt på objektet (det vil sige, medmindre en anden værdi er importeret senest fra forbundne datakilden). Eksportér hukommelse ryddes, når der modtages C = 5 under en import på objektet.

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
