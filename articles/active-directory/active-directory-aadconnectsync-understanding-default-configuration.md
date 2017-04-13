<properties
    pageTitle="Azure AD-forbindelse synkronisering: forstå standardkonfiguration | Microsoft Azure"
    description="I denne artikel beskrives indstillingerne standard i Azure AD-forbindelse synkronisering."
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD-forbindelse synkronisering: forstå standardkonfiguration
Denne artikel forklares out of box konfiguration reglerne. Dokumenter, reglerne, samt hvordan disse regler påvirke konfigurationen. Det også vejleder dig gennem standardkonfigurationen af synkronisering af Azure AD-forbindelse. Målet er at læseren forstår hvordan konfigurationsmodellen, med navnet deklarativ klargøring, der arbejder i et eksempel. Denne artikel forudsætter, at du allerede har installeret og konfigurerer Azure AD-forbindelse synkronisere ved hjælp af installationsguiden.

Du kan finde oplysninger om af konfigurationsmodellen, kan du læse [Om deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Ud for feltet regler fra det lokale til Azure AD
Følgende udtryk kan findes i out of box konfigurationen.

### <a name="user-out-of-box-rules"></a>Bruger out of box regler
Disse regler gælder også for objekttype iNetOrgPerson.

Et brugerobjekt skal opfylde følgende for at blive synkroniseret:

- Skal have en sourceAnchor.
- Når objektet, der er oprettet i Azure AD, ændre ikke sourceAnchor. Hvis værdien er ændret lokalt, stopper objektet synkronisering, indtil sourceAnchor ændres tilbage til den forrige værdi.
- Have skal attributten accountEnabled (Brugerkontokontrol) udfyldt. Med et lokalt Active Directory er denne attribut altid Præsenter og på forhånd.

Følgende brugerobjekter er **ikke** synkroniseret til Azure AD:

- `IsPresent([isCriticalSystemObject])`. Sikre mange out of box-objekter i Active Directory, som den indbyggede administratorkonto, som ikke er synkroniseret.
- `IsPresent([sAMAccountName]) = False`. Sikre brugerobjekter med ingen sAMAccountName-attribut ikke, synkroniseres. Dette tilfælde vil kun næsten ske i et domæne, der er opgraderet fra NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synkroniseres ikke den tjenestekonto, der bruges af Azure AD-forbindelse synkronisering og dens tidligere versioner.
- Synkroniseres ikke Exchange-konti, der ikke fungerer i Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Synkroniseres ikke objekter, der ikke fungerer i Exchange Online.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Denne bitmask (og H21C07000) vil bortfiltrere de følgende objekter:
    - Mailbaserede offentlig mappe
    - System Attendant postkasse
    - Postkasse Database postkasse (System postkasse)
    - Universel sikkerhedsgruppe (ville ansøge om en bruger, men er til stede ældre årsager)
    - Ikke-universel gruppe (ville ansøge om en bruger, men er til stede ældre årsager)
    - Postkasse Plan
    - Søgepostkasse
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkroniseres ikke alle gentagelse offer objekter.

Følgende attributter regler gælder:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Attributten sourceAnchor er ikke bidraget fra en sammenkædet postkasse. Det antages, at hvis en sammenkædet postkasse er fundet, den faktiske konto er tilsluttet sig senere.
- Exchange relateret attributter synkroniseres kun, hvis attributten **mailNickName** har en værdi.
- Når der er flere områder, consumed attributter i følgende rækkefølge:
    1. Attributter med relation til logon (for eksempel userPrincipalName) bidraget fra området med en aktiveret konto.
    2. Attributter, der kan findes på Exchange globale Adresseliste (Global adresseliste) bidraget fra områder med en Exchange-postkasse.
    3. Hvis ingen postkasse kan findes, kan følgende attributter komme fra en hvilken som helst skov.
    4. Exchange relateret attributter (tekniske attributter ikke er synligt i den globale Adresseliste) er bidraget fra området, der hvor `mailNickname ISNOTNULL`.
    5. Hvis der er flere områder, der opfylder en af disse regler, så bruges oprettelse af rækkefølgen (dato og klokkeslæt) af forbindelser (skov) til at bestemme, hvilke skov bidrager attributter.

### <a name="contact-out-of-box-rules"></a>Kontakte out of box regler
Et kontakter objekt skal opfylde følgende for at blive synkroniseret:

- Kontakten skal være mailaktiverede. Det er bekræftet med følgende regler:
    - `IsPresent([proxyAddresses]) = True)`. ProxyAddresses-attributten skal udfyldes.
    - En primær mailadresse kan findes i proxyAddresses-attributten eller attributten mail. Tilstedeværelsen af en @ bruges til at kontrollere, at indholdet er en e-mailadresse. En af disse to regler skal evalueres til sand.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Er der en post med "SMTP:" og hvis der er, kan en @ findes i strengen?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Er udfyldt attributten mail, og hvis det er, kan en @ findes i strengen?

Følgende kontakter objekter er **ikke** synkroniseret til Azure AD:

- `IsPresent([isCriticalSystemObject])`. Sørg for ikke er markeret som kritiske kontaktobjekter synkroniseres. Ikke bør være en med en standardkonfiguration.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Disse objekter duer i Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkroniseres ikke alle gentagelse offer objekter.

### <a name="group-out-of-box-rules"></a>Gruppere out of box regler
En gruppeobjektet skal opfylde følgende for at blive synkroniseret:

- Mindre end 50.000 medlemmer skal have. Denne tæller er antallet af medlemmer i gruppen i det lokale miljø.
    - Hvis der er flere medlemmer, før synkroniseringen starter første gang, synkroniseres ikke gruppen.
    - Hvis antallet af medlemmer stige fra når det blev oprettet, derefter når det når 50.000 medlemmer stopper den synkronisering indtil medlemskab antallet er lavere end 50.000 igen.
    - Bemærk: 50.000 medlemskab antallet gennemtvinges også af Azure AD. Du kan ikke synkronisere grupper med flere medlemmer, selvom du ændre eller fjerne denne regel.
- Hvis gruppen er en **Distributionsgruppe**, skal det også være mail aktiveret. Se [Kontakt ud for feltet regler](#contact-out-of-box-rules) for denne regel gennemtvinges.

Følgende gruppeobjekter er **ikke** synkroniseret til Azure AD:

- `IsPresent([isCriticalSystemObject])`. Sikre mange out of box-objekter i Active Directory, som den indbyggede administratorgruppe, som ikke er synkroniseret.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Ældre gruppe, der bruges af DirSync.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Rollegruppen.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkroniseres ikke alle gentagelse offer objekter.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out of box regler
FSPs er sammenføjet til "en" (\*) objekt i metaverse. I virkeligheden sker dette Deltag kun for brugere og sikkerhedsgrupper. Denne konfiguration sikrer, at tværs af områder medlemskaber er løst og repræsenteret korrekt i Azure AD.

### <a name="computer-out-of-box-rules"></a>Computer out of box regler
En computerobjekt skal opfylde følgende for at blive synkroniseret:

- `userCertificate ISNOTNULL`. Kun Windows 10 computere udfylde denne attribut. Alle computerobjekter med en værdi i attributten synkroniseres.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Forstå scenariet out of box regler
I dette eksempel bruger vi en installation med én konto skov (A), en ressourceskov (R) og én Azure AD-mappe.

![Billede med scenarie beskrivelse](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

I denne konfiguration forudsættes det der er en aktiveret konto i området, der konto og en deaktiveret konto i området ressource med en sammenkædet postkasse.

Vores mål med standardkonfigurationen er:

- Attributter, der er relateret til logon er synkroniseret fra områder med den konto, der er aktiveret.
- Attributter, der kan findes i den globale Adresseliste (Global adresseliste) synkroniseres fra områder med postkassen. Hvis ingen postkasse kan findes, bruges andre skov.
- Hvis der findes en sammenkædet postkasse, skal den sammenkædede aktiverede konto findes for objektet, der skal eksporteres til Azure AD.

### <a name="synchronization-rule-editor"></a>Synkronisering regel Editor
Konfigurationen kan vises og ændres med værktøjet synkronisering regler Editor (SRE), og du kan finde en genvej til det i startmenuen.

![Ikon for synkronisering regler Editor](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE er en resource kit-værktøj, og den er installeret med synkronisering af Azure AD-forbindelse. Hvis du vil kunne starte det, skal du være medlem af gruppen ADSyncAdmins. Når den starter, kan du se nogenlunde sådan ud:

![Indgående regler for synkronisering](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

I denne rude skal se du alle synkronisering regler, der oprettes til din konfiguration. Hver linje i tabellen er en regel for synkronisering. Til venstre under regeltyper, vises de to forskellige typer: indgående og udgående. Indgående og udgående er fra visningen af metaverse. Du går hovedsageligt til at fokusere på indgående regler i denne oversigt. Fundet skemaet afhænger af den faktiske liste over regler for synkronisering i AD. På billedet ovenfor, konto skov (fabrikamonline.com) har ikke en hvilken som helst tjenester, som Exchange og Lync, og ingen synkronisering regler, der er oprettet for disse tjenester. I området ressource (res.fabrikamonline.com), skal du imidlertid finde synkronisering regler for disse tjenester. Indholdet af reglerne, der er forskellige, afhængigt af den version, der er fundet. For eksempel i en installation med Exchange 2013 er der flere attribut flyder konfigureret end i Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synkronisering regel
En synkronisering regel er en konfigurationsobjekt med en række attributter, der flyder, når en betingelse er opfyldt. Det er også bruges til at beskrive, hvordan et objekt i en connector-plads er relateret til et objekt i metaverse, kaldet **join** eller **svarer til**. Synkronisering af reglerne har en rangorden for værdi, der angiver, hvordan de relaterer til hinanden. En regel for synkronisering med en lavere numeriske værdi har højere prioritet, og i en attribut flow konflikt højere prioritet vinder konfliktløsning.

Som et eksempel, se på reglen synkronisering **i fra AD – bruger AccountEnabled**. Markere denne linje i SRE, og klik på **Rediger**.

Da denne regel er en regel for out of box, modtager du en advarsel, når du åbner reglen. Du skal ikke foretage [ændringer til ud for feltet regler](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), så du bliver spurgt, hvad din hensigt er. I dette tilfælde skal vil du kun have vist reglen. Vælg **Nej**.

![Synkronisering af regler i advarsel](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

En regel for synkronisering har fire konfiguration sektioner: beskrivelse, angivelse af område filtrere, Deltag i regler og transformationer.

#### <a name="description"></a>Beskrivelse
Den første sektion indeholder grundlæggende oplysninger som et navn og beskrivelse.

![Beskrivelse af fanen synkroniseret regeleditor ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Du også finde oplysninger om, hvilke tilsluttede system denne regel er relateret til, hvilket objekt type i det tilsluttede system, der gælder for, og metaverse objekttypen. Objekttype metaverse er altid person uanset, når kildetypen objekt er en bruger, iNetOrgPerson eller kontakt. Objekttype metaverse bør aldrig ændres, så den er oprettet som en generisk type. Den kædetype kan angives til at deltage i, StickyJoin eller klargøring. Denne indstilling fungerer sammen med afsnittet deltage i regler og dækkes senere.

Du kan også se, at reglen Synkroniser bruges til synkronisering af adgangskoder. Hvis en bruger er i området for denne regel synkronisering, synkroniseres adgangskoden fra det lokale skyen (Hvis du har aktiveret synkroniseringsfunktion adgangskode).

#### <a name="scoping-filter"></a>Angivelse af område filter
Sektionen angivelse af område Filter bruges til at konfigurere, når en synkronisering regel skal anvendes. Da navnet på reglen synkronisering, du kigger på angiver den kun skal anvendes for aktiverede brugere, omfanget er konfigureret, så AD-attribut **Brugerkontokontrol** ikke må have bit 2 angive. Når Synkroniser program finder en bruger i AD, gælder reglen synkronisering, når **Brugerkontokontrol** er angivet til Decimalværdien 512 (aktiveret normal bruger). Det gælder ikke reglen, når brugeren har **Brugerkontokontrol** indstillet til 514 (deaktiveret normal bruger).

![Angivelse af område under fanen Synkroniser regeleditor ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Filteret angiver området har grupper og delsætninger, der kan være indlejret. Alle delsætninger i en gruppe skal være opfyldt for en synkronisering regel anvendes. Når flere grupper er defineret, skal mindst én gruppe være opfyldt for reglen skal gælde. Det vil sige, et logisk eller evalueres mellem grupper og en logisk og evalueres i en gruppe. Et eksempel på denne konfiguration kan findes i den udgående synkronisering regel **ud til AAD – deltage i gruppen**. Der er flere synkronisering filter grupper, for eksempel en for sikkerhedsgrupper (`securityEnabled EQUAL True`) og én til distributionsgrupper (`securityEnabled EQUAL False`).

![Angivelse af område under fanen Synkroniser regeleditor ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Denne regel bruges til at definere, hvilke grupper skal klargøres til Azure AD. Distributionsgrupper skal være aktiveret til at blive synkroniseret med Azure AD mail, men til sikkerhedsgrupper en mail er ikke påkrævet.

#### <a name="join-rules"></a>Deltage i regler
Den tredje sektion bruges til at konfigurere, hvordan objekter i connector-plads, der er relateret til objekter i metaverse. Den regel, du har set tidligere har ikke en hvilken som helst konfiguration for regler, deltage i, så i stedet vil du kigge på **i fra AD-brugere deltage i**.

![Deltage i under fanen regler for synkronisering regeleditor ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Indholdet af joinforbindelse reglen, afhænger af den tilsvarende indstilling, der er valgt i installationsguiden. Evalueringen starter med et objekt i boksen kilde forbindelse til en indgående regel og hver gruppe i join-regler evalueres i rækkefølge. Hvis en kildeobjektet evalueres så det svarer til ét objekt i metaverse ved hjælp af én af reglerne for joinforbindelse, er objekterne, der sammenføjet. Hvis alle regler for har evalueret og det er ikke nogen match, bruges kædetype på siden beskrivelse. Hvis denne konfiguration er indstillet til **klargøring**, oprettes et nyt objekt i destinationen, metaverse. Hvis du vil klargøre et nyt objekt til metaverse kaldes også til **projekt** et objekt mod metaverse.

Deltag i reglerne evalueres kun én gang. Når en forbindelse mellemrum objekt og et metaverse objekt er optaget, forbliver de joinforbundne, så længe omfanget af synkronisering reglen er stadig er opfyldt.

Ved evaluering af regler for synkronisering, skal bare én regel for synkronisering med joinforbindelse regler, der er defineret i omfang. Hvis der findes flere synkronisering regler med joinforbindelse regler for ét objekt, er der opstod en fejl. Derfor er den bedste fremgangsmåde at have bare én regel for synkronisering med joinforbindelse, der er defineret, når flere synkronisering regler er i området for et objekt. I sektionen out of box konfiguration for synkronisering af Azure AD-forbindelse, kan disse regler findes ved at kigge på navnet og finde dem med ord, **deltage** i slutningen af navnet. En synkronisering regel uden alle join-regler, der er defineret gælder attributten flyder, når en anden synkronisering regel sammenføjes objekterne eller klargjort et nyt objekt i mållejerens.

Hvis du ser på billedet ovenfor, kan du se, at reglen prøver at deltage i **objectSID** med **msExchMasterAccountSid** (Exchange) og **msRTCSIP-OriginatorSid** (Lync), som er, hvad vi forvente i en konto er ressourcerelaterede skov topologi. Du kan finde den samme regel på alle områder. Det antages, at hver skov kan være enten et firma eller en ressource område. Denne konfiguration fungerer også, hvis du har konti, der bor i et enkelt område og har ikke skal sammenkædes.

#### <a name="transformations"></a>Transformationer
Sektionen transformation definerer alle attribut flyder, der gælder for målobjektet, når objekterne, der er sammenføjet og filteret omfang er opfyldt. Gå tilbage til den **i fra AD – bruger AccountEnabled** synkronisering regel, skal du finde følgende transformeringer:

![Transformationer fanen synkroniseret regeleditor ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

For at placere denne konfiguration kontekst, i en konto er ressourcerelaterede skov installation, er det forventede at finde en aktiveret konto i området, der konto og en deaktiveret konto i ressource området med indstillinger for Exchange og Lync. Synkronisering af reglen du kigger på indeholder de attributter, der kræves til logon og følgende attributter skal løber fra området, der hvor der ikke er en aktiveret konto. Alle disse attribut flyder sammenføjes i en regel for synkronisering.

En transformation kan have forskellige typer: konstant, direkte og udtryk.

- En konstant strøm flyder altid en hårdt værdi. I ovenstående tilfælde, angiver den altid værdien **Sand** i metaverse attribut med navnet **accountEnabled**.
- En direkte flow flyder altid værdien af attributten i kilden til attributten målet som-er.
- Den tredje flowtype er udtryk, og gør det muligt for mere avancerede konfigurationer.

Udtrykssproget er VBA (Visual Basic for Applications), så personer erfaring med Microsoft Office eller VBScript genkende formatet. Attributter er omsluttet af kantede parenteser [attributeName]. Attributten navne og funktionsnavne skelnes, men redigeringsprogrammet synkronisering regler evalueres udtrykkene og giver en advarsel, hvis udtrykket ikke er gyldig. Alle udtryk udtrykkes på en enkelt linje med indlejrede funktioner. For at få vist en potens på sproget, der konfiguration, er her strømmen til pwdLastSet, men med flere kommentarer, der indsættes:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Se [Forstå deklarativ klargøring udtryk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) for flere oplysninger om udtrykssproget for attributten flyder.

### <a name="precedence"></a>Rangorden for
Du har nu set på nogle enkelte synkronisering regler, men reglerne, der arbejder sammen i konfigurationen. I nogle tilfælde er en værdi for attributten bidraget fra flere synkronisering regler til Destinationsattributten samme. I dette tilfælde bruges attributten rangorden for at finde ud af, hvilken attribut vinder. Se som et eksempel på attributten sourceAnchor. Denne attribut er en vigtig attribut skal kunne logge på Azure AD. Du kan finde en attribut flow for denne attribut i to forskellige synkronisering regler, **i fra AD – bruger AccountEnabled** og **i fra AD-bruger almindelige**. På grund af rangorden af synkronisering regler for er attributten sourceAnchor bidraget fra området med en aktiveret konto først når der er flere objekter, der er tilsluttet metaverse objektet. Hvis der er ingen aktiveret konti og derefter Synkroniser program bruger opbevaringsstedet synkronisering reglen **i fra AD-bruger almindelige**. Denne konfiguration sikrer, at selv for konti, der er deaktiveret, der stadig en sourceAnchor.

![Indgående regler for synkronisering](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Prioritet for synkronisering regler er angivet i grupper af installationsguiden. Alle regler i en gruppe har samme navn, men de har forbindelse til forskellige forbundne mapper. Installationsguiden giver reglen **i fra AD-brugere deltage i** højeste prioritet, og den gentages over alle forbindelse AD kataloger. Det derefter fortsætter med de næste grupper af regler i en foruddefineret rækkefølge. I en gruppe føjes reglerne i den rækkefølge forbindelserne blev tilføjet i guiden. Hvis en anden forbindelse er tilføjet i guiden, er genbestilles synkronisering regler og regler for den nye forbindelse der indsættes senest i hver gruppe.

### <a name="putting-it-all-together"></a>Lægge det hele sammen
Vi nu kendskab til synkronisering for at kunne forstå, hvordan fungerer konfigurationen med de forskellige synkronisering regler. Hvis du ser på en bruger, og de attributter, som er bidraget til metaverse, anvendes reglerne, i følgende rækkefølge:

Navn | Kommentar
:------------- | :-------------
I fra AD-bruger joinforbindelse | Regel for deltagelse i forbindelse mellemrum objekter med metaverse.
I fra AD-brugerkonto er aktiveret | Attributter, der kræves til logon på Azure AD og Office 365. Vi vil disse attributter fra kontoen aktiveret.
I fra AD-bruger fælles fra Exchange | Attributter, der findes i den globale adresseliste. Vi antager, at datakvaliteten af er bedste i det område, hvor vi har fundet brugerens postkasse.
I fra AD-bruger fælles | Attributter, der findes i den globale adresseliste. I tilfælde af, at vi fandt en postkasse, kan andre joinforbundne objekter bidrage attributværdien.
I fra AD-bruger Exchange | Findes kun, hvis Exchange er registreret. Teksten flyder alle infrastruktur Exchange attributter.
I fra AD-bruger Lync | Findes kun, hvis Lync er registreret. Teksten flyder alle infrastruktur Lync attributter.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om af konfigurationsmodellen i [Forstå deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Læs mere om udtrykssproget i [Forstå deklarativ klargøring udtryk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Fortsætte med at læse, hvordan out of box konfigurationen fungerer i [om brugere og kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md)
- Se, hvordan du ændrer praktiske ved hjælp af deklarativ klargøring i [Sådan foretager en ændring af standard-konfigurationen](active-directory-aadconnectsync-change-the-configuration.md).

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
