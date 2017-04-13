<properties
    pageTitle="Identitet synkronisering og Dupliker attribut fleksibilitet | Microsoft Azure"
    description="Ny funktionsmåde for Sådan håndteres objekter med UPN eller ProxyAddress konflikter under katalogsynkronisering ved hjælp af Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitet synkronisering og Dupliker attribut fleksibilitet
Dublerede attribut fleksibilitet er en funktion i Azure Active Directory, der fjerner gnidning forårsaget af **UserPrincipalName** og **ProxyAddress** konflikter, når du kører en af Microsofts synkronisering værktøjer.

Disse to attributter er normalt behøver at være entydige på tværs af alle **bruger**, **gruppe**eller **Kontakt** objekter i en given Azure Active Directory-lejer.

> [AZURE.NOTE] Kun brugere kan have UPN.

Den nye funktionalitet, der gør det muligt for denne funktion er i skyen del af Synkroniser rørledningen, det er derfor klient agnostic og relevante for en hvilken som helst Microsoft synkronisering produkt, herunder Azure AD-forbindelse, DirSync og MIM + forbindelse. Generisk ordet "synkroniseringsklient" bruges i dette dokument til at repræsentere en af disse produkter.

## <a name="current-behavior"></a>Aktuelle funktionsmåde
Hvis der er et forsøg på at klargøre et nyt objekt med en UPN eller ProxyAddress værdi, som krænker denne separate begrænsning, blokerer Azure Active Directory objektet i at blive oprettet. På samme måde, hvis et objekt er opdateret med en ikke-unik UPN eller ProxyAddress, mislykkes opdateringen. Klargøring forsøg eller update skal gentages ved synkroniseringsklient på hver Eksportér cyklus og fortsætter med at mislykkes, indtil konflikten er løst. En fejl rapport mail genereres af hvert forsøg og en fejl logføres synkroniseringsklienten.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Funktionsmåde i forbindelse med dublerede attribut fleksibilitet
I stedet for helt ned til at klargøre eller opdatere et objekt med en dublet af attribut, karantæne Azure Active Directory"" attributten dublerede som ville overskride den separate begrænsning. Hvis denne attribut er påkrævet til klargøring som UserPrincipalName, tildeler tjenesten en pladsholderværdi. Formatet af disse midlertidige værdier er  
"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***".  
Hvis attributten ikke er obligatorisk, som en **ProxyAddress**, Azure Active Directory blot karantæne attributten konflikt og skrider frem til oprettelse af objekt eller opdatering.

Oplysninger om konflikten sendes på quarantining attributten, i den samme fejl rapport mail, der er brugt i den gamle funktionsmåde. Dog denne oplysninger vises kun i fejlrapporten én gang, når der sker karantænen, det fortsætter ikke være logget på fremtidige mails. Også, da eksporten for dette objekt er lykkedes, synkroniseringsklienten registrerer ikke en fejl og ikke igen Opret / opdateringshandling på efterfølgende Synkroniser skifter.

En ny attribut er blevet føjet til de brugere, grupper og Kontakt objekt klasser, for at understøtter denne funktionsmåde:  
**DirSyncProvisioningErrors**

Dette er en opslagskolonne med flere valgmuligheder attribut, der bruges til at gemme de modstridende attributter, der ville overskride den separate begrænsning de lægges normalt. En baggrund timer opgave er blevet aktiveret i Azure Active Directory, der kører, hver time til at søge efter dublerede attribut konflikter, der er blevet løst, og fjerner automatisk de pågældende attributter fra karantæne.

### <a name="enabling-duplicate-attribute-resiliency"></a>Aktivere dublerede attribut fleksibilitet
Dublerede attribut fleksibilitet bliver den nye standardfunktionsmåde på tværs af alle Azure Active Directory-lejere. Det vil være til som standard for alle lejere, aktiveret synkronisering for første gang på 22. August 2016 eller nyere. Lejere, aktiveret synkronisering før denne dato har funktionen er aktiveret i batches. Denne udrulning af påbegyndes i September 2016 og besked via mail sendes til hver lejer tekniske meddelelse kontakt med en bestemt dato, hvor funktionen skal aktiveres.

Når duplikere attribut fleksibilitet er blevet aktiveret, kan den ikke deaktiveres.

Sådan kontrollerer du, hvis funktionen er aktiveret for din lejer, kan du gøre det ved at downloade den seneste version af Azure Active Directory PowerShell-modulet og kører:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Hvis du vil aktivere funktionen proaktiv, før den er aktiveret for din lejer, kan du gøre det ved at downloade den seneste version af Azure Active Directory PowerShell-modulet og kører:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificere objekter med DirSyncProvisioningErrors
Der findes i øjeblikket to metoder til at identificere objekter, der har disse fejl på grund af dublerede egenskabskonflikter, Azure Active Directory PowerShell og Office 365-administrationsportalen. Der er planer om at udvide til yderligere portalen baseret rapportering i fremtiden.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Til PowerShell-cmdletter i dette emne er følgende sandt:

- Alle følgende cmdletter er store og små bogstaver.
- **– ErrorCategory PropertyConflict** skal altid medtages. Der er i øjeblikket ingen andre typer **ErrorCategory**, men dette kan forlænges i fremtiden.

Først skal komme i gang ved at køre **Forbinde MsolService** og angive legitimationsoplysninger for lejeradministrator.

Brug derefter følgende cmdletter og operatorer til at få vist fejl på forskellige måder:

1. [Se alle](#see-all)

2. [Efter egenskabstype](#by-property-type)

3. [Ved uoverensstemmende værdi](#by-conflicting-value)

4. [Ved hjælp af en streng søgning](#using-a-string-search)

5. [Sorteret](#sorted)

6. [I en begrænset mængde eller alle](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Se alle
Når forbindelse, hvis du vil se en overordnet oversigt over klargøring af attribut køre fejl i lejeren:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Dette giver et resultat som følger:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>Efter egenskabstype
For at se fejl efter egenskabstype skal du tilføje flaget **- Egenskabsnavn** med argumentet **UserPrincipalName** eller **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Eller

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Ved uoverensstemmende værdi
Hvis du vil se fejl i forbindelse med tilføjer en bestemt egenskab flaget **- PropertyValue** (**- Egenskabsnavn** skal bruges også når du tilføjer dette flag):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>Ved hjælp af en streng søgning
Brug flaget **- SearchString** til at udføre en bred streng søgning. Dette kan bruges uafhængigt af hinanden fra alle de ovenstående flag, med undtagelse af **-ErrorCategory PropertyConflict**, som altid er påkrævet:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>I en begrænset mængde eller alle
1. **MaxResults <Int> ** kan bruges til at begrænse forespørgslen til et bestemt antal værdier.

2. **Alle** kan bruges til at sikre, at alle resultater er hentet i tilfælde, der findes et stort antal fejl.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Office 365 admin-portalen

Du kan få vist directory synkroniseringsfejl i Office 365 administration. Rapport i Office 365-portalen viser kun **bruger** objekter, der indeholder disse fejl. Det viser ikke oplysninger om konflikter mellem **grupper** og **kontakter**.


![Aktive brugere] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Aktive brugere")

Du kan finde oplysninger om, hvordan du får vist directory synkroniseringsfejl i Office 365 administration, [identificere directory synkroniseringsfejl i Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Identitet synkronisering fejlrapport
Når et objekt med en dublet af attribut konflikt håndteres med denne nye funktionsmåde for en meddelelse er medtaget i standard identitet synkronisering fejlrapport mails, der sendes til tekniske meddelelsen kontakte for lejeren. Der er dog en vigtig ændring i dette problem. Oplysninger om en dublet af attribut konflikt skal medtages i hver efterfølgende fejlrapport tidligere, indtil konflikten er blevet løst. Med denne nye funktionsmåde vises fejl meddelelsen til en given konflikt kun én gang - samtidig attributten uoverensstemmende er sat i karantæne.

Her er et eksempel på hvordan e-mail-meddelelsen ser ud til en ProxyAddress konflikt:  
    ![Aktive brugere](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Løse konflikter
Fejlfinding i forbindelse med strategi og opløsning taktikker for disse fejl skal ikke være forskellige fra den måde, dublerede attribut fejl blev håndteret i fortiden. Den eneste forskel er, at opgaven timer har vundet gennem lejeren service-side til automatisk at føje den pågældende attribut til objektet stort, når konflikten er løst.

I følgende artikel beskrives forskellige fejlfinding og løsning strategier: [dublerede værdier eller ugyldige attributter forhindrer katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Kendte problemer
Ingen af disse kendte problemer får data tab eller tjenesten er forringet. Flere af dem er æstetiske, andre medføre standard "*foreløbig fleksibilitet*" dublerede attribut fejl for at være udløst i stedet for quarantining attributten konflikt, og en anden medfører, at visse fejl til at kræve ekstra manuel rettelse af.

**Core funktionsmåde:**

1. Objekter med bestemt attribut konfigurationer fortsat modtage Eksportér fejl i modsætning til de dublerede attribut(ter) blive sat i karantæne.  
Eksempel:

    en. Ny bruger er oprettet i AD med et UPN af **Joe@contoso.com** og ProxyAddress**smtp:Joe@contoso.com**

    b. Egenskaberne for dette objekt er i konflikt med en eksisterende gruppe, hvor ProxyAddress er **SMTP:Joe@contoso.com**.

    c. Ved eksport udløst fejlen **ProxyAddress konflikt** i stedet for få attributterne konflikt sat i karantæne. Handlingen skal gentages på hver efterfølgende Synkroniser cyklus, som den ville have været før funktionen fleksibilitet er aktiveret.

2. Hvis to grupper er oprettet i det lokale miljø med den samme SMTP-adresse, ikke en klargøring i første forsøg med fejlen dublerede **ProxyAddress** standard. Dog den duplikerede værdi er korrekt sat i karantæne på den næste synkronisering cyklus.

**Office portalen rapport**:

1. Den detaljerede fejlmeddelelse for to objekter i et UPN konflikten sæt er det samme. Dette angiver, at de har begge havde deres UPN ændret / sat i karantæne, når faktisk kun en af dem havde data ændres.

2. Den detaljerede fejlmeddelelse for en UPN konflikt viser den forkerte vist navn for en bruger, der har haft deres UPN ændret/sat i karantæne. Eksempel:

    en. **Bruger A** synkroniserer først med **UPN = User@contoso.com **.

    b. **Bruger B** er forsøgte at blive synkroniseret op næste med **UPN = User@contoso.com **.

    c. **Bruger BS** UPN ændres til **User1234@contoso.onmicrosoft.com** og **User@contoso.com** føjes til **DirSyncProvisioningErrors**.

    d. Fejlmeddelelsen for **Bruger B** skal angive, at **brugeren A** allerede har **User@contoso.com** som en UPN, men den viser **Bruger B** egen vist navn.



**Identitet synkronisering fejlrapport**:

Linket for *trin til at løse dette problem* er forkert:  
    ![Aktive brugere](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

Det skal du pege på [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Se også

- [Synkronisering af Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md)

- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)

- [Identificere directory synkroniseringsfejl i Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
