<properties
    pageTitle="Beskyttelse af Azure Active Directory din identitet | Microsoft Azure"
    description="Få mere at vide, hvordan Azure AD identitet beskyttelse gør det muligt at begrænse muligheden for en hacker at udnytte en kompromitteret identitet eller enhed og for at sikre en identitet eller en enhed, der tidligere blev mulig eller blevet konstateret, at være kompromitteret."
    services="active-directory"
    keywords="beskyttelse af Azure active directory din identitet, skyen app registrering, administration af programmer, sikkerhed, risikoen, risikoen niveau, sikkerhedsrisiko, sikkerhedspolitik"
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
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Beskyttelse af Azure Active Directory din identitet 

Azure Active Directory identitetsbeskyttelse er en funktion af Azure AD Premium P2 udgave, der giver dig en samlet visning i risikoen begivenheder og potentielle svagheder påvirker din organisations identiteter. Microsoft er sikring af skybaseret identiteter for over et årti, og med Azure AD identitet beskyttelse Microsoft foretager de samme beskyttelsessystemer tilgængelige for enterprise-kunder. Beskyttelse af din identitet bruger eksisterende Azure AD anomali registrering funktioner (tilgængelige via Azure AD uoverensstemmende aktivitetsrapporter), og introducerer nye risikoen begivenhed typer, der kan registrere afvigelser i realtid.



##<a name="getting-started"></a>Kom godt i gang

Størstedelen af sikkerhedsbrist foregå, når hackere få adgang til et miljø ved stjæle et bruger-id. Hackere er blevet mere og mere effektivt med udnytte fra tredjepart brud på, og ved hjælp af avancerede phishing-angreb. Når en hacker får adgang til endnu en lav privilegerede brugerkonto, er det relativt nemt at få adgang til vigtige virksomhed ressourcer via sideretningen bevægelse. Derfor er det vigtigt at beskytte alle identiteter og, når en identitet er afsløret, proaktiv forhindrer kompromitteret identitet i at blive misbrugt. 

Opdager kompromitteret identiteter er ikke nogen nem opgave. Heldigvis beskyttelse af din identitet kan hjælpe dig: beskyttelse af din identitet bruger algoritmer, der er tilpasset maskine læring og heuristik til at registrere afvigelser og offentliggjort hændelser, der kan betyde, at en identitet er blevet afsløret.
 
Beskyttelse af din identitet genererer ved hjælp af disse data rapporter og beskeder, der gør det muligt at undersøge disse risikoen begivenheder og tage relevante afhjælpning eller afhjælpning handling.
 
Men Azure Active Directory identitetsbeskyttelse er mere end et værktøj til overvågning og rapportering. Beskyttelse af din identitet beregner på baggrund af risikohændelser, en brugerniveau risikoen for hver bruger, så du kan konfigurere risikoen-baserede politikker for at beskytte automatisk identiteter i din organisation.  Disse risikoen-baserede politikker, ud over andre betinget access-kontrolelementer, der leveres af Azure Active Directory og EMS, kan automatisk blokere eller tilbyder tilpasset afhjælpning handlinger, der omfatter nulstilling af adgangskoder og multi-Factor authentication aktivering.  

####<a name="explore-identity-protections-capabilities"></a>Udforske identitetsbeskyttelse funktioner 

**Registrering af risikoen begivenheder og risikabel konti:**  

- Registrering af 6 risikoen begivenhed typer ved hjælp af maskine læring og heuristisk regler 

- Beregning af brugeren risikoen niveauer

- Give brugerdefinerede anbefalinger til at forbedre overordnede sikkerhed stilling ved at fremhæve svagheder



**Ved at undersøge risikohændelser:**

- Afsendelse af meddelelser om risikoen begivenheder

- Ved at undersøge risikohændelser ved hjælp af relevante og kontekstafhængige oplysninger

- Give grundlæggende arbejdsprocesser for at spore undersøgelser

- Give nem adgang til afhjælpning handlinger som nulstilling af adgangskode



**Risiko-baserede betinget access politikker:**

- Politik for at reducere risikabel logon ved at blokere logon eller kræve Multi-Factor authentication udfordringer.

- Politik om at blokere eller sikker risikabel brugerkonti

- Politik for brugerne at registrere til multi-Factor authentication


## <a name="detection-and-risk"></a>Registrering og risikoen

### <a name="risk-events"></a>Risiko begivenheder

Risikohændelser er hændelser, der er markeret som mistænkeligt af beskyttelse af din identitet, og angiver, at en identitet kan være blevet beskadiget. Du kan finde en komplet liste over risikoen begivenheder [typer risikohændelser, der er registreret af Azure Active Directory identitetsbeskyttelse](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Risiko niveau

Niveauet risikoen for en risikohændelse er angivelse (høj, mellem eller lav) af alvorlighed af risikohændelse. Risikoniveauet for hjælper brugerne med beskyttelse af din identitet prioritere de handlinger, de skal udføre for at reducere risikoen for deres organisation. Alvorlighed af hændelsen risikoen repræsenterer styrken af signalet som en indikator for stigning af identitet skade, kombineres med mængden støj, som den typisk introducerer. 

- **Høj**: høj konfidensinterval og høj alvorlighed risikohændelse. Disse hændelser er stærke indikatorer, som er blevet afsløret brugerens identitet, og alle brugerkonti, påvirkes skal afhjælpes med det samme.

- **Mediet**: høj alvor, men nederste konfidensinterval risikoen begivenhed, eller omvendt. Disse hændelser er potentielt risikabel, og alle brugerkonti, påvirkes skal afhjælpes.

- **Lav**: Lav KONFIDENSINTERVAL, og lav alvorlighed risikohændelse. Denne hændelse kræver muligvis ikke en øjeblikkelig handling, men når kombineres med andre risikoen begivenheder muligvis give en stærk angivelse, identitet er afsløret. 


![Risiko niveau] (./media/active-directory-identityprotection/01.png "Risiko niveau")

 

Risiko begivenheder identificeres enten i **realtid**, eller Placer i efter behandling efter hændelsen risikoen har truffet (offline). I øjeblikket de fleste risikoen begivenheder i beskyttelse af din identitet beregnes offline, og vises i identitetsbeskyttelse i 2-4 timer. Mens evalueres i realtid, realtid risikoen begivenheder, vises i konsollen identitet beskyttelse i 5-10 minutter.

Flere ældre klienter understøtter ikke i øjeblikket, realtid risikoen hændelsesregistrering og forebyggelse. Som et resultat, kan ikke logon fra disse klienter fundet eller forhindret i realtid.


## <a name="investigation"></a>Undersøgelse
Din rejse gennem beskyttelse af din identitet starter typisk med dashboardet for beskyttelse af din identitet. 

![Afhjælpning] (./media/active-directory-identityprotection/1000.png "Afhjælpning")

Dashboardet giver dig adgang til:
 
- Rapporter som **brugere markeret med flag til risikoen**, **risikoen begivenheder** og **svagheder**
- Indstillinger som konfigurationen af din **Sikkerhedspolitikker**, **beskeder** og **multi-Factor authentication registrering**
 

Det er normalt dit udgangspunkt for undersøgelse, som er processen med at gennemse aktiviteterne, logfiler og andre relevante oplysninger, der er relateret til en risikohændelse beslutte, om afhjælpning eller afhjælpning trin er nødvendige, og hvordan identitet er blevet afsløret, og forstå, hvordan den kompromitterede identitet blev brugt.

Du kan udnytte din undersøgelsesaktiviteter til [beskeder](active-directory-identityprotection-notifications.md) Azure Active Directory-beskyttelse sender per mail.

De følgende afsnit giver dig flere detaljer og de trin, som er relateret til en undersøgelse.  



## <a name="what-is-a-user-risk-level"></a>Hvad er en risiko brugerniveau?

En risiko brugerniveau er angivelse (høj, mellem eller lav) af sandsynligheden for, at det bruger-id er blevet afsløret. Det er beregnes ud fra de bruger risikohændelser, der er knyttet til brugerens identitet. 

Status for en risikohændelse er **aktiv** eller **lukket**. Kun risikohændelser, der er **aktive** bidrage til bruger risikoen beregningen. 

Risiko brugerniveau beregnes ved hjælp af de følgende input:

- Aktive risikoen begivenheder, som påvirker beskyttelsen brugeren
- Risiko niveau af disse hændelser 
- Om eventuelle afhjælpning handlinger er blevet ført 

![Bruger risici] (./media/active-directory-identityprotection/1001.png "Bruger risici")



Du kan bruge bruger risikoen niveauer til at oprette betingede access politikker at blokere risikabel brugere i at logge på eller Tving dem til at sikkert ændre deres adgangskode. 


## <a name="closing-risk-events-manually"></a>Lukke risikoen begivenheder manuelt

I de fleste tilfælde tager du afhjælpning handlinger som en sikker nulstilling af adgangskode for at lukke automatisk risikoen begivenheder. Men dette altid muligvis ikke.  
Dette gælder, f.eks., når:

- En bruger med aktive risikohændelser er blevet slettet
- En undersøgelse viser, at en anmeldt risikohændelse blevet udføre ved den retmæssige bruger

Fordi risikohændelser, der er **aktive** bidrage til bruger risikoen beregningen, kan du muligvis manuelt sænke et niveau for risikoen ved at lukke risikohændelser manuelt.  
I løbet af undersøgelsen, kan du vælge at udføre nogle af disse handlinger til at ændre status for en risikohændelse:

![Handlinger] (./media/active-directory-identityprotection/34.png "Handlinger")

- **Løse** - Hvis efter ved at undersøge en risikohændelse, du har udført en relevante afhjælpning handling uden for beskyttelse af din identitet, og du mener, risikohændelse skal anses markere lukket, begivenheden som løst. Løst hændelser indstiller hændelsen risikoen status til afsluttet og hændelsen risikoen ikke længere vil bidrage til bruger risikoen.

- **Markér som FALSK positiv** - i nogle tilfælde kan du undersøge en risikohændelse og opdager, at det forkert blev markeret som en ustabil. Du kan reducere antallet af sådanne forekomster ved at markere risikohændelse som FALSK positiv. Dette vil hjælpe maskinen læ algoritmer for at forbedre klassificeringen af lignende hændelser i fremtiden. Status for falsk-positive hændelser er til **lukket** , og de ikke længere vil bidrage til bruger risikoen.

- **Ignorer** - Hvis du ikke har foretaget dig noget for afhjælpning, men vil risikohændelse at blive fjernet fra den aktive liste, du kan markere en risikohændelse Ignorer og begivenhed status lukkes. Ignorerede begivenheder bidrager ikke til bruger risikoen. Denne indstilling skal kun bruges under usædvanlige omstændigheder. 

- **Genaktivere** - risikohændelser, der blev lukket manuelt (ved at vælge **løse**, **Falsk positiv**eller **Ignorer**) kan genaktiveres, angive begivenhed status til **aktiv**. Genaktiverede risikoen begivenheder bidrage til bruger risikoen niveau beregningen. Risiko begivenheder lukket gennem afhjælpning (som en sikker adgangskode nulstille) kan ikke aktiveres igen. 




**Åbne dialogboksen relaterede konfiguration**:

1. Klik på **risikoen begivenheder**under **Investigate**bladet **Azure AD identitet beskyttelse** .

    ![Nulstilling af adgangskode til manuel] (./media/active-directory-identityprotection/1002.png "Nulstilling af adgangskode til manuel")

2. Klik på en risiko i listen **risikoen begivenheder** .

    ![Nulstilling af adgangskode til manuel] (./media/active-directory-identityprotection/1003.png "Nulstilling af adgangskode til manuel")

2. Højreklik på en bruger på bladet risikoen.

    ![Nulstilling af adgangskode til manuel] (./media/active-directory-identityprotection/1004.png "Nulstilling af adgangskode til manuel")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Lukke alle risikohændelser for en bruger manuelt

I stedet for manuelt at lukke risikohændelser for en bruger enkeltvis, giver Azure Active Directory identitetsbeskyttelse dig også en metode til at lukke alle hændelser for en bruger med et enkelt klik.


![Handlinger] (./media/active-directory-identityprotection/2222.png "Handlinger")

Når du klikker på **Afvis alle begivenheder**, alle hændelser er lukket, og den pågældende bruger er ikke længere på ansvar.



## <a name="remediating-user-risk-events"></a>Afhjælper bruger risikohændelser

En afhjælpning er en handling til at sikre en identitet eller en enhed, der tidligere blev mulig eller blevet konstateret, at være kompromitteret. Handlingen afhjælpning gendanner den identitet eller enhed til en fejlsikret tilstand og løser tidligere risikohændelser, der er knyttet til den identitet eller enhed.

Hvis du vil afhjælpning bruger risikoen begivenheder, kan du:

- Udføre en sikker adgangskode Nulstil for at afhjælpning bruger risikoen begivenheder manuelt 

- Konfigurere en bruger risikoen sikkerhedspolitik for at reducere eller afhjælpning bruger risikoen begivenheder automatisk

- Igen billede inficerede enheden  


### <a name="manual-secure-password-reset"></a>Nulstilling af manuel sikker adgangskode

En nulstilling af sikker adgangskode er en effektiv afhjælpning af mange risikohændelser, og når udføres automatisk lukker disse risikoen begivenheder og genberegnes risikoen brugerniveau. Du kan bruge dashboardet for beskyttelse af din identitet for at starte en nulstilling af adgangskode for en risikabel bruger. 

Dialogboksen relaterede indeholder to forskellige metoder til at nulstille en adgangskode:

**Nulstil adgangskode** - Vælg **Kræv, at brugeren til at nulstille din adgangskode** til brugeren mulighed for at gendanne selv hvis brugeren har registreret til multi-Factor authentication. Under brugerens næste logon, bliver brugeren kræves for at løse en multi-Factor authentication udfordring korrekt og derefter nødt til at ændre adgangskoden. Denne indstilling er ikke tilgængelig, hvis brugerkontoen ikke allerede er registreret Multi-Factor authentication.

**Midlertidige adgangskode** - Vælg **Opret en midlertidig adgangskode** med det samme ødelægge den eksisterende adgangskode, og oprette en ny midlertidige adgangskode til brugeren. Sende den nye midlertidige adgangskode til en alternativ mailadresse for brugeren eller til brugerens overordnede. Da adgangskoden er midlertidige, brugeren bliver bedt om at ændre adgangskoden på logon.


![Politik] (./media/active-directory-identityprotection/1005.png "Politik")


**Åbne dialogboksen relaterede konfiguration**:

1. Klik på **brugere, der er afmærket til risikoen**bladet **Azure AD identitet beskyttelse** .

    ![Nulstilling af adgangskode til manuel] (./media/active-directory-identityprotection/1006.png "Nulstilling af adgangskode til manuel")


2. Vælg en bruger med mindst én risikoen begivenheder på listen over brugere.

    ![Nulstilling af adgangskode til manuel] (./media/active-directory-identityprotection/1007.png "Nulstilling af adgangskode til manuel")


2. Klik på **Nulstil adgangskode**på bladet bruger.

    ![Nulstilling af adgangskode til manuel] (./media/active-directory-identityprotection/1008.png "Nulstilling af adgangskode til manuel")





## <a name="user-risk-security-policy"></a>Bruger risikoen sikkerhedspolitik

En bruger risikoen sikkerhedspolitik er en betinget access-politik, der evaluerer risikoniveauet til en bestemt bruger og anvender afhjælpning og afhjælpning handlinger, der er baseret på foruddefinerede betingelser og regler.


![Bruger ridk politik] (./media/active-directory-identityprotection/1009.png "Bruger ridk politik")


Azure AD identitet beskyttelse hjælper dig med at administrere afhjælpning og afhjælpning af brugere, der er markeret med flag til risikoen ved at gøre det muligt at:

- Angive de brugere og grupper politikken gælder for: 

    ![Bruger ridk politik] (./media/active-directory-identityprotection/1010.png "Bruger ridk politik")


- Angiv den bruger risikoen niveau grænseværdi (lav, mellem eller højt), der udløser politikken: 

    ![Bruger ridk politik] (./media/active-directory-identityprotection/1011.png "Bruger ridk politik")


- Angive kontrolelementerne til er tvungne, når politikken udløser:

    ![Bruger ridk politik] (./media/active-directory-identityprotection/1012.png "Bruger ridk politik")


- Skift status for din politik:

    ![Bruger ridk politik] (./media/active-directory-identityprotection/403.png "MFA registrering")


- Gennemse og evaluere virkningen af en ændring, før du aktiverer den:

    ![Bruger ridk politik] (./media/active-directory-identityprotection/1013.png "Bruger ridk politik")


Vælge en **Høj** grænseværdi reducerer antallet af gange en politik udløses, og minimerer betydning for brugerne.
Men det udelukker **Lav** og **mellem** brugere, der er markeret med flag til risikoen fra politikken, som ikke kan sikre identiteter eller enheder, der tidligere mulig eller blevet konstateret, at være kompromitteret.

Når du angiver politikken

- Udelade brugere, der er sandsynlighed for at generere en masse falsk positive (udviklere, sikkerhed analytikere)

- Udelade brugere i landestandarder, hvor aktivere politikken ikke er praktiske (for eksempel ikke adgang til helpdesk)

- Brug en **Høj** grænseværdi under indledende politik udrulle, eller hvis du skal minimere udfordringer, ses af slutbrugere.

- Brug en **Lav** grænse, hvis din organisation kræver større sikkerhed. Hvis du vælger en grænseværdi for **Lav** introducerer flere bruger-logon udfordringer, men øget sikkerhed.

Anbefalede standarden for de fleste organisationer er at konfigurere en regel for en **mediet** grænseværdi at finde den rigtige balance mellem brugervenligheden og sikkerhed.

Finde en oversigt over relaterede brugeroplevelsen, i:

- [Compromised konto gendannelse forløb](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Compromised konto blokeret forløb](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Åbne dialogboksen relaterede konfiguration**:

1. Klik på **bruger risikoen politik**bladet **Azure AD identitet beskyttelse** i afsnittet **konfiguration** .

    ![Bruger ridk politik] (./media/active-directory-identityprotection/1009.png "Bruger ridk politik")






## <a name="mitigating-user-risk-events"></a>Mindske bruger risikoen begivenheder
Administratorer kan angive en bruger risikoen sikkerhedspolitik Bloker brugere på logon afhængigt af risikoniveauet. 

Blokering af en logon:
 
- Forhindrer oprettelse af nye bruger risikoen begivenheder til den pågældende bruger

- Giver administratorer mulighed for manuelt at afhjælpning risikoen begivenheder påvirker brugerens identitet og gendanne den til en sikker tilstand



## <a name="what-is-a-sign-in-risk-level"></a>Hvad er et logon risikoen niveau?

Et logon risikoen niveau er angivelse (høj, mellem eller lav) af sandsynligheden for, til en bestemt logon, en anden forsøger at godkende med det bruger-id. Risikoniveauet logon evalueres på tidspunktet for en logon og finder risikoen begivenheder og indikatorer, der er registreret i realtid for den specifikke logon. 

## <a name="mitigating-sign-in-risk-events"></a>Mindske risikoen logon begivenheder 
En afhjælpning er en handling til at begrænse muligheden for en hacker at udnytte en kompromitteret identitet eller enhed uden at gendanne den identitet eller enhed til en fejlsikret tilstand. En afhjælpning løst ikke tidligere logon risikohændelser, der er knyttet til den identitet eller enhed.

Du kan bruge betinget access i Azure AD identitet beskyttelse for at reducere automatisk logon risikoen begivenheder. Bruger disse politikker, skal du overveje at risikoniveauet for brugeren eller logonnavn til at blokere risikabel logon eller kræver, at brugeren til at udføre Multi-Factor authentication. Disse handlinger kan forhindre en hacker i at udnytte en stjålne identitet for at forårsage skade, og den kan give dig stykke tid til at sikre identitet. 


## <a name="sign-in-risk-security-policy"></a>Log på risikoen sikkerhedspolitik

En politik for logon risikoen er en betinget access-politik, der evaluerer risikoen til en bestemt logon og gælder afhjælpninger baseret på foruddefinerede betingelser og regler.

![Log på risikoen politik] (./media/active-directory-identityprotection/1014.png "Log på risikoen politik")


Azure AD identitet beskyttelse hjælper dig med at administrere afhjælpning af risikabel logon ved at gøre det muligt at:

- Angive de brugere og grupper politikken gælder for: 

    ![Log på risikoen politik] (./media/active-directory-identityprotection/1015.png "Log på risikoen politik")


- Angiv den logon risikoen niveau grænseværdi (lav, mellem eller højt), der udløser politikken: 

    ![Log på risikoen politik] (./media/active-directory-identityprotection/1016.png "Log på risikoen politik")


- Angive kontrolelementerne til er tvungne, når politikken udløser:  

    ![Log på risikoen politik] (./media/active-directory-identityprotection/1017.png "Log på risikoen politik")
    

- Skift status for din politik:

    ![MFA registrering] (./media/active-directory-identityprotection/403.png "MFA registrering")

- Gennemse og evaluere virkningen af en ændring, før du aktiverer den: 

    ![Log på risikoen politik] (./media/active-directory-identityprotection/1018.png "Log på risikoen politik")


### <a name="what-you-need-to-know"></a>Hvad du skal kende

Du kan konfigurere en logon risikoen sikkerhedspolitik for at kræve godkendelse i flere niveauer:

![Log på risikoen politik] (./media/active-directory-identityprotection/1017.png "Log på risikoen politik")

Dog af sikkerhedsmæssige årsager virker denne indstilling kun for brugere, der allerede er registreret til multi-Factor authentication. Hvis betingelsen til at kræve godkendelse i flere niveauer er opfyldt for en bruger, der endnu ikke er registreret til multi-Factor authentication, er brugeren blokeret. 

Som en bedste fremgangsmåde, hvis du vil kræve godkendelse i flere niveauer for risikabel logon, skal du:

1. Aktivere [multi-Factor authentication registreringspolitik](#multi-factor-authentication-registration-policy) for de pågældende brugere.
2. Kræv de pågældende brugere til logon i en ikke-risikabel sessioner lov til at udføre en MFA-registrering

Udføre disse trin sikrer, at Multi-Factor godkendelse er påkrævet til en risikabel logon. 


### <a name="best-practices"></a>Bedste fremgangsmåder

 
Vælge en **Høj** grænseværdi reducerer antallet af gange en politik udløses, og minimerer betydning for brugerne.  
 
Det udelader, **Lav** og **mellem** logon markeret med flag til risikoen fra politikken, som ikke kan forhindre en hacker i at udnytte en kompromitteret identitet. 

Når du angiver politikken 

- Udelade brugere, der ikke / kan ikke have Multi-Factor authentication

- Udelade brugere i landestandarder, hvor aktivere politikken ikke er praktiske (for eksempel ikke adgang til helpdesk)

- Udelade brugere, der er sandsynlighed for at generere en masse falsk positive (udviklere, sikkerhed analytikere)

- Brug en **Høj** grænseværdi under indledende politik udrulle, eller hvis du skal minimere udfordringer, ses af slutbrugere.

- Brug en **Lav** grænse, hvis din organisation kræver større sikkerhed. Hvis du vælger en grænseværdi for **Lav** introducerer flere bruger-logon udfordringer, men øget sikkerhed.

Anbefalede standarden for de fleste organisationer er at konfigurere en regel for en **mediet** grænseværdi at finde den rigtige balance mellem brugervenligheden og sikkerhed.

 
Politikken logon risikoen er:

- Anvendt på alle browsertrafik og logon ved hjælp af moderne godkendelse.
- Gælder ikke for programmer, der bruger ældre sikkerhedsprotokoller ved at deaktivere WS-Trust slutpunktet på det medlem af organisationsnetværket IDP som ADFS.

Siden **Risikoen begivenheder** i konsollen identitetsbeskyttelse viser alle hændelser:

- Denne politik er blevet anvendt
- Du kan gennemse aktiviteten og finde ud af, om handlingen, der var relevante eller ej 

Finde en oversigt over relaterede brugeroplevelsen, i:

- [Risikabel logon gendannelse](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Risikabel logon blokeret](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Multi-Factor authentication registrering under en risikabel logon](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Åbne dialogboksen relaterede konfiguration**:

1. Klik på **Log på risikoen politik**bladet **Azure AD identitet beskyttelse** i afsnittet **konfiguration** .

    ![Bruger ridk politik] (./media/active-directory-identityprotection/1014.png "Bruger ridk politik")





## <a name="multi-factor-authentication-registration-policy"></a>Multi-Factor authentication registreringspolitik

Azure Multi-Factor authentication er en metode til at bekræfte, hvem du er, der kræver brug af mere end bare et brugernavn og din adgangskode. Den indeholder en anden sikkerhedslag bruger logon og transaktioner.  
Vi anbefaler, at du kræver Azure Multi-Factor authentication for bruger logon, fordi det:

- Leverer stærke godkendelse med et udvalg af nemt bekræftelse

- Afspilles en vigtig rolle i forbindelse med forberedelsen organisationen til at beskytte og gendanne fra konto kompromiser

![Bruger ridk politik] (./media/active-directory-identityprotection/1019.png "Bruger ridk politik")



Yderligere oplysninger finder du [Hvad er Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)


Azure AD identitet beskyttelse hjælper dig med at administrere udrulning af Multi-Factor authentication registrering ved at konfigurere en politik, der gør det muligt at: 



- Angive de brugere og grupper politikken gælder for: 

    ![MFA politik] (./media/active-directory-identityprotection/1020.png "MFA politik")



- Angive kontrolelementerne til er tvungne, når politikken udløser::  

    ![MFA politik] (./media/active-directory-identityprotection/1021.png "MFA politik")


- Skift status for din politik:

    ![MFA politik] (./media/active-directory-identityprotection/403.png "MFA politik")

- Få vist den aktuelle registreringsstatus: 

    ![MFA politik] (./media/active-directory-identityprotection/1022.png "MFA politik")


Finde en oversigt over relaterede brugeroplevelsen, i:

- [Multi-Factor authentication registrering forløb](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Multi-Factor authentication registrering under en risikabel logon](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Åbne dialogboksen relaterede konfiguration**:

1. Klik på **multi-Factor authentication registrering**bladet **Azure AD identitet beskyttelse** i afsnittet **konfiguration** .

    ![MFA politik] (./media/active-directory-identityprotection/1019.png "MFA politik")





## <a name="next-steps"></a>Næste trin

 - [Kanal 9: Azure AD og identitet Vis: identitet beskyttelse Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Typer af risikohændelser, der er registreret af Azure Active Directory identitetsbeskyttelse](active-directory-identityprotection-risk-events-types.md)
 - [Svagheder, der er registreret af Azure Active Directory identitetsbeskyttelse](active-directory-identityprotection-vulnerabilities.md)
 - [Azure Active Directory identitetsbeskyttelse beskeder](active-directory-identityprotection-notifications.md)
 - [Azure Active Directory identitetsbeskyttelse playbook](active-directory-identityprotection-playbook.md)
 - [Azure Active Directory identitetsbeskyttelse ordliste](active-directory-identityprotection-glossary.md)

 - [Log på oplevelser med Azure AD identitet beskyttelse](active-directory-identityprotection-flows.md)

 - [Hvis du aktiverer Azure Active Directory identitetsbeskyttelse](active-directory-identityprotection-enable.md)
 - [Azure Active Directory identitetsbeskyttelse - hvordan du kan fjerne blokeringen af brugere](active-directory-identityprotection-unblock-howto.md)

 - [Introduktion til Azure Active Directory identitetsbeskyttelse og Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


