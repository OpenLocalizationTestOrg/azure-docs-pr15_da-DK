<properties
    pageTitle="Azure Active Directory identitet beskyttelse ordliste | Microsoft Azure"
    description="Azure Active Directory identitet beskyttelse ordliste"
    services="active-directory"
    keywords="beskyttelse af Azure active directory din identitet, skyen app registrering, administration af programmer, sikkerhed, risikoen, risikoen niveau, sikkerhedsrisiko, sikkerhedspolitik, ordliste"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory identitet beskyttelse ordliste 


### <a name="at-risk-user"></a>Risiko (bruger)  
En bruger med en eller flere active risikoen begivenheder. 

### <a name="atypical-sign-in-location"></a>Atypiske logonadresse   
En logon fra en geografisk placering, der ikke er typisk for den specifikke bruger, lignende brugere eller lejeren.

### <a name="azure-ad-identity-protection"></a>Azure AD-identitet beskyttelse    
Et sikkerhedsmodul af Azure Active Directory, der indeholder en samlet visning i risikoen begivenheder og potentielle svagheder påvirker en organisations identiteter.

### <a name="conditional-access"></a>Betinget adgang  
En politik for at sikre adgang til ressourcer. Betinget adgangsregler gemmes i Azure Active Directory og evalueres af Azure AD før give adgang til ressourcen.  Eksempler på regler omfatter begrænsning af adgang, der er baseret på brugerplacering skal enhed sundhed eller bruger godkendelsesmetode.

### <a name="credentials"></a>Legitimationsoplysninger     
Oplysninger, der omfatter identifikation og bevis for id, der bruges til at få adgang til lokale og netværksressourcer. Eksempler på legitimationsoplysninger er brugernavne og adgangskoder, chipkort og certifikater.

### <a name="event"></a>Begivenhed   
En post med en aktivitet i Azure Active Directory.

### <a name="false-positive-risk-event"></a>FALSK positiv (risikohændelse) 
En risiko begivenhed status angivet manuelt af en beskyttelse af din identitet brugere, der angiver, at risikoen hændelsen blev undersøges og forkert blev markeret som en risikohændelse.

### <a name="identity"></a>Identitet    
En person eller enhed, der skal kontrolleres ved hjælp af godkendelse, baseret på flere kriterier som adgangskode eller et certifikat.

### <a name="identity-risk-event"></a>Identitet risikohændelse     
AAD hændelse, der er afmærket som uoverensstemmende ved beskyttelse af din identitet og kan betyde, at en identitet er blevet afsløret.

### <a name="ignored-risk-event"></a>Ignoreres (risikohændelse)    
En risiko begivenhed status angivet manuelt af en beskyttelse af din identitet bruger, der angiver, at hændelsen risikoen er lukket uden at foretage dig en afhjælpning handling.

### <a name="impossible-travel-from-atypical-locations"></a>Umuligt rejse fra atypiske placeringer   
En risikohændelse udløses, når to logon for samme bruger registreres, hvor mindst én af dem er fra en atypiske logonadresse, og hvor tiden mellem logon er mindre end den mindste tid det tager at fysisk rejser mellem disse placeringer.  

###<a name="investigation"></a>Undersøgelse    
Processen med at gennemse aktiviteterne, logfiler og andre relevante oplysninger, der er relateret til en risikohændelse beslutte, om afhjælpning eller afhjælpning trin er nødvendigt forstå, hvis og hvordan identitet blev skadet og forstå, hvordan den kompromitterede identitet blev brugt.

### <a name="leaked-credentials"></a>Lækket legitimationsoplysninger  

En risikohændelse udløses, når aktuelle brugerlegitimationsoplysninger (brugernavn og adgangskode) findes sendt offentligt i webdelen mørke af vores forskere.

### <a name="mitigation"></a>Afhjælpning  
En handling til at begrænse eller eliminere mulighed for en hacker at udnytte en kompromitteret identitet eller enhed uden at gendanne den identitet eller enhed til en fejlsikret tilstand. En afhjælpning løst ikke tidligere risikohændelser, der er knyttet til den identitet eller enhed.

### <a name="multi-factor-authentication"></a>Multi-Factor authentication 
En godkendelsesmetode, der kræver to eller flere godkendelsesmetoder, hvilket kan inkludere noget, at brugeren har et certifikat; noget den brugeren kender, som brugernavne, adgangskoder eller gang sætninger; fysiske attributter, som en miniature og personlige attributter, som et personligt brevpapir.

### <a name="offline-detection"></a>Offline registrering   
Påvisning af afvigelser og evalueringen af risikoen for en begivenhed som logon forsøg efter fakultet for en begivenhed, der har allerede er der sket.

### <a name="policy-condition"></a>Politik betingelse    
En del af en sikkerhedspolitik som definerer enheder (grupper, brugere, apps, Enhedsplatforme, enhed stater, IP-intervaller, klienttyper) medtages i politikken eller udelades fra den.

### <a name="policy-rule"></a>For politikregel     
Del af en sikkerhedspolitik, som beskriver de omstændigheder, der vil udløse politikken, og de handlinger, der udføres, når politikken udløses.

### <a name="prevention"></a>Forebyggelse  
En handling for at forhindre beskadigelse organisationen gennem misbrug af en identitet eller enhed mulig eller ved at være kompromitteret. Handlingen forebyggelse beskytter ikke enheden eller identitet, og løst ikke tidligere risikohændelser.

### <a name="privileged-user"></a>Rettigheder (bruger)
En bruger, samtidig med en risikohændelse havde permanente eller midlertidige administratortilladelser til en eller flere ressource i Azure Active Directory, som en Global Administrator faktureringsadministrator, tjenesteadministratoren, Brugeradministrator og Adgangskodeadministrator. 

###<a name="real-time"></a>Realtid    
Se realtid registrering.

###<a name="real-time-detection"></a>Realtid registrering  
Påvisning af afvigelser og evalueringen af risikoen for en begivenhed som logon forsøg før begivenheden har tilladelse til at fortsætte.

### <a name="remediated-risk-event"></a>Remediated (risikohændelse)     
En risiko begivenhed status, der angives automatisk ved identitetsbeskyttelse, der angiver, at risikoen hændelsen blev remediated ved hjælp af handlingen standard afhjælpning for denne type risikohændelse. For eksempel, når du brugeradgangskode er nulstillet, er mange risikohændelser, der angiver, at tidligere adgangskoden er blevet afsløret automatisk remediated.

### <a name="remediation"></a>Afhjælpning     
En handling til at sikre en identitet eller en enhed, der tidligere mulig eller blevet konstateret, at være kompromitteret. Handlingen afhjælpning gendanner den identitet eller enhed til en fejlsikret tilstand og løser tidligere risikohændelser, der er knyttet til den identitet eller enhed.

### <a name="resolved-risk-event"></a>Løst (risikohændelse)   
Risiko begivenhed status angivet manuelt af en beskyttelse af din identitet bruger, der angiver, at brugeren har udført en relevante afhjælpning handling uden for beskyttelse af din identitet og, risikoen begivenheden skal anses lukket.

###<a name="risk-event-status"></a>Risiko begivenhed status    
En egenskab for en risikohændelse, der angiver, om begivenheden er aktiv, og hvis lukket, grunden til, lukkes den.

###<a name="risk-event-type"></a>Risiko hændelsestype  
En kategori for hændelsen risikoen, angiver typen af anomali, der forårsagede hændelsen skal betragtes som risikabel.

###<a name="risk-level-risk-event"></a>Risiko niveau (risikohændelse)  
Angivelse (høj, mellem eller lav) af alvorlighed af risikohændelse kan hjælpe brugerne med beskyttelse af din identitet prioritere handlingerne de tage for at reducere risikoen for deres organisation. 

###<a name="risk-level-sign-in"></a>Risiko niveau (logon) 
Angivelse (høj, mellem eller lav) af sandsynligheden for, til en bestemt logon, en anden forsøger at bruge det bruger-id.

###<a name="risk-level-user-compromise"></a>Risiko niveau (bruger sammensat) 
Angivelse (høj, mellem eller lav) af sandsynligheden for, at en identitet er blevet afsløret.

### <a name="risk-level-vulnerability"></a>Risiko niveau (sikkerhedsrisiko)  
Angivelse (høj, mellem eller lav) af alvorlighed af sikkerhedsrisikoen kan hjælpe brugerne med beskyttelse af din identitet prioritere handlingerne de tage for at reducere risikoen for deres organisation.

### <a name="secure-identity"></a>Secure (identitet)   
Tage afhjælpning handling som en ændring af adgangskode eller maskine reimaging for at gendanne en potentielt kompromitteret identitet til en kompromisløs tilstand.

### <a name="security-policy"></a>Sikkerhedspolitik
En samling af regler og betingelse. En politik kan anvendes for enheder, som brugere, grupper, apps, enheder, Enhedsplatforme, enhed stater, IP-intervaller og Auth2.0 klienttyper. Når en politik er aktiveret, evalueres det, når et objekt, der er inkluderet i politikken er udstedt et token for en ressource.

### <a name="sign-in-v"></a>Log på (v) 
Til at godkende en identitet i Azure Active Directory.

### <a name="sign-in-n"></a>Logon (n) 
Processen eller handlingen for godkendelse af en identitet i Azure Active Directory, og den begivenhed, registrerer denne handling.

###<a name="sign-in-from-anonymous-ip-address"></a>Log på fra anonyme IP-adresse    
En risikohændelse udløses efter en vellykket logon fra IP-adresse, der er identificeret som en anonyme proxy IP-adresse.

###<a name="sign-in-from-infected-device"></a>Log på fra inficerede enhed 
En risikohændelse udløses, når en logon stammer fra en IP-adresse, som kendes der skal bruges i en eller flere kompromitteret enheder, som aktivt prøver at kommunikere med en bot server.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Logge på fra IP-adresse med mistænkelig aktivitet 
En risikohændelse udløses, når en vellykket logon fra en IP-adresse adresse med et stort antal mislykkede logonforsøg på tværs af flere brugerkonti over en kort tidsperiode.

###<a name="sign-in-from-unfamiliar-location"></a>Log på fra fremmede placering 
En risikohændelse udløses, når en bruger logger korrekt fra en ny placering (IP, længde-/ breddegrader og ASN).

###<a name="sign-in-risk"></a>Log på risikoen     
Se risikoen niveau (logon)

###<a name="sign-in-risk-policy"></a>Log på risikoen politik  
Et betinget access-politik, der evaluerer risikoen til en bestemt logon og gælder afhjælpninger baseret på foruddefinerede betingelser og regler.

###<a name="user-compromise-risk"></a>Bruger sammensat risikoen     
Se risikoen niveau (bruger sammensat)

###<a name="user-risk"></a>Bruger risikoen    
Se risikoen niveau (bruger sammensat).

###<a name="user-risk-policy"></a>Bruger risikoen politik     
Et betinget access-politik, finder logonnavn og gælder afhjælpninger baseret på foruddefinerede betingelser og regler.

###<a name="users-flagged-for-risk"></a>Brugere, der er markeret med flag til risikoen   
Brugere, der har risikohændelser, der er aktiv eller remediated

###<a name="vulnerability"></a>Sikkerhedsrisiko    
En konfiguration eller betingelse i Azure Active Directory, hvilket gør mappen udsatte for udnytter eller trusler.


## <a name="see-also"></a>Se også

- [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md)
