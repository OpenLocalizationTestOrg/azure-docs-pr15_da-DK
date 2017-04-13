<properties
    pageTitle="Log på erfaringer med Azure AD identitet beskyttelse | Microsoft Azure"
    description="Indeholder en oversigt over brugeroplevelsen, når beskyttelse af din identitet har afhjulpet eller remediated en bruger, eller når Multi-Factor godkendelse kræves af en politik."
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Log på oplevelser med Azure AD identitet beskyttelse

Med Azure Active Directory identitetsbeskyttelse kan du:

- kræve, at brugere til at registrere til multi-Factor authentication

- håndtere risikabel logon og kompromitteret brugere

Svar på systemet disse problemer har indflydelse på en brugers logonoplevelse, fordi lige direkte logge på ved at angive et brugernavn og en adgangskode er ikke muligt længere. Yderligere trin, der kræves for at få en bruger sikkert i business igen.

Dette emne får du en oversigt over en brugers logonoplevelse i alle tilfælde, der kan opstå.

**Multi-Factor authentication**

- Multi-Factor authentication registrering



**Logon på risiko**

- Risikabel logon gendannelse

- Risikabel logon blokeret

- Multi-Factor authentication registrering under en risikabel logon
 

**Bruger risiko**

- Kompromitteret kontogendannelse

- Kompromitteret kontoen er blokeret




## <a name="multi-factor-authentication-registration"></a>Multi-Factor authentication registrering

Den bedste brugeroplevelse for både, beskadiget konto gendannelse strømmen og risikabel logon strømmen, er, når brugeren kan selv gendanne. Hvis brugerne er registreret til multi-Factor authentication, har de allerede et telefonnummer, der er knyttet til deres konto, der kan bruges til at overføre sikkerhedsudfordringer. Nogen hjælp systemadministrator andel skal bruges til at gendanne fra konto sammensat. Det anbefales derfor meget for at få dine brugere, der er registreret til multi-Factor authentication. 

Administratorer kan:

- angive en politik, der kræver, at brugerne at konfigurere deres konti for ekstra sikkerhed bekræftelse. 
- Tillad springe over Multi-Factor authentication registrering i op til 30 dage, i tilfælde af, at de vil give brugerne en udvidet periode, før du registrerer.

**Multi-Factor authentication registreringen har tre trin:**

1. I det første trin bliver brugeren en meddelelse om kravet om at angive kontoen til multi-Factor authentication. 

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/140.png "Afhjælpning")


2. Hvis du vil konfigurere Multi-Factor godkendelse, skal du lade systemet vide, hvordan du ønsker at blive kontaktet.

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/141.png "Afhjælpning")
 
3. Systemet sender en udfordring at du, og du skal reagere.

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/142.png "Afhjælpning")

 



## <a name="risky-sign-in-recovery"></a>Risikabel logon gendannelse

Når en administrator har konfigureret en politik for logon risici, er de pågældende brugere besked, når de forsøger at logge på. 

**Risikabel logon-strømmen har to trin:** 

1. Brugeren besked om, at noget usædvanlige blev fundet om deres logon, såsom logge på fra en ny placering, enhed eller app. 

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/120.png "Afhjælpning")

2. Brugeren er kræves for at bevise deres identitet ved at lægge en sikkerhed udfordring. Hvis brugeren er registreret til multi-Factor authentication skal de kørsel en sikkerhedskode til deres telefonnummer. Da dette er bare en risikabel logge på og ikke en kompromitteret-konto, har brugeren ikke ændre adgangskoden i dette flow. 

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/121.png "Afhjælpning")



 
## <a name="risky-sign-in-blocked"></a>Risikabel logon blokeret
Administratorer kan også vælge at angive en politik for risikoen-logon til Bloker brugere på logon afhængigt af risikoniveauet. For at få blokeringen skal slutbrugere skal kontakte en administrator eller help desk, eller de kan prøve at logge på fra en velkendte placering eller enhed. Gendanne selv ved at lægge Multi-Factor authentication er ikke en indstilling i dette tilfælde.

![Afhjælpning] (./media/active-directory-identityprotection-flows/200.png "Afhjælpning")




## <a name="compromised-account-recovery"></a>Kompromitteret kontogendannelse

Når en bruger risikoen sikkerhedspolitik er konfigureret, brugere, der opfylder brugeren offentliggjort niveau, der er angivet i politikken (og dermed antages afsløret) skal gennemgå bruger sammensat gendannelse strømmen, før de kan logge på. 

**Bruger sammensat gendannelse strømmen består af tre trin:**

1. Brugeren besked om, at deres kontosikkerhed er på risiko på grund af mistænkelig aktivitet eller lækket legitimationsoplysninger.

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/101.png "Afhjælpning")

2.  Brugeren er kræves for at bevise deres identitet ved at lægge en sikkerhed udfordring. Hvis brugeren er registreret til multi-Factor authentication kan de selv gendanne fra bliver skadet. De skal kørsel en sikkerhedskode til deres telefonnummer. 

    ![Afhjælpning] (./media/active-directory-identityprotection-flows/110.png "Afhjælpning")


3.  Til sidst skal er brugeren nødt til at ændre deres adgangskode, fordi en anden har haft adgang til deres konto. Skærmbilleder af denne oplevelse er under.
 
    ![Afhjælpning] (./media/active-directory-identityprotection-flows/111.png "Afhjælpning")



## <a name="compromised-account-blocked"></a>Kompromitteret kontoen er blokeret 

For at få en bruger, der blev blokeret af en bruger risikoen sikkerhedspolitik ikke-blokerede, skal brugeren du kontakte en administrator eller helpdesk. Gendanne selv ved at lægge Multi-Factor authentication er ikke en indstilling i dette tilfælde.


![Afhjælpning] (./media/active-directory-identityprotection-flows/104.png "Afhjælpning")



 
## <a name="reset-password"></a>Nulstil adgangskode

Hvis der blokeres kompromitteret brugere i at logge på, kan en administrator generere en midlertidig adgangskode til dem. Brugerne nødt til at ændre deres adgangskode under en næste logon.

![Afhjælpning] (./media/active-directory-identityprotection-flows/160.png "Afhjælpning")


 




 

## <a name="see-also"></a>Se også

- [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md) 