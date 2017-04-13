<properties
   pageTitle="Azure AD-forbindelse: Designe begreber | Microsoft Azure"
   description="Dette emne indeholder oplysninger om bestemte implementering design områder"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure AD-forbindelse: Designe begreber
Formålet med dette emne er at beskrive forskellige områder, der skal betragtes under implementering designet af Azure AD-forbindelse. Dette emne er en undersøgelse på visse områder og disse begreber som er beskrevet i andre emner samt.

## <a name="sourceanchor"></a>sourceAnchor
Attributten sourceAnchor er defineret som *en fast i et objekt levetid attribut*. Den de identificerer entydigt et objekt som værende de samme objekt lokalt og i Azure AD. Attributten kaldes også **immutableId** og de to navne bruges erstatte hinanden.

Fast, word, der er "kan ikke ændres", er det vigtigt at i dette emne. Da denne attributværdien ikke kan ændres, når der er angivet, er det vigtigt at vælge et design, der understøtter scenariet.

Egenskaben bruges til følgende scenarier:

- Når en ny synkronisering program server er bygget eller genopbygges efter et nedbrud, sammenkæder denne attribut eksisterende objekter i Azure AD med lokale objekter.
- Hvis du flytter fra en kun skyen identitet til en synkroniseret identitet datamodel, kan denne attribut objekter til "hårde match" eksisterende objekter i Azure AD med lokale objekter.
- Hvis du bruger sammenslutning, bruges denne attribut sammen med **userPrincipalName** i kravet til entydigt identificerer en bruger.

Dette emne kan kun taler om sourceAnchor i forhold til brugere. De samme regler gælder for alle objekttyper, men det er kun for brugere dette problem er som regel et problem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Vælge en god sourceAnchor attribut
Attributværdien skal udføre følgende regler:

- Være mindre end 60 tegn
    - Tegn, der ikke er en z, A-Å eller 0-9-kodet og tælles som 3 tegn.
- Ikke indeholder et specialtegn: & #92;! # $ % & * + / = ? ^ & #96; { } | ~ sammen () '; : , [ ] " @ _
- Skal være globalt entydige
- Skal være enten en streng, heltal eller binært tal
- Bør ikke være baseret på brugerens navn disse ændringer
- Ikke skal være store og små bogstaver og undgå værdier, der kan variere efter store og små bogstaver
- Skal tildeles, når objektet, der er oprettet

Hvis den valgte sourceAnchor ikke er af typen streng, Azure AD forbinde Base64Encode attributværdien at sikre, at ingen særlige tegn, der vises. Hvis du bruger en anden sammenslutningsserveren end ADFS, skal du kontrollere din server, kan også Base64Encode attributten.

Attributten sourceAnchor er store og små bogstaver. En værdi på "JohnDoe" er ikke den samme som "johndoe". Men du bør ikke have to forskellige objekter med kun forskel på store og små bogstaver.

Hvis du har et enkelt område er lokalt, derefter den attribut, skal du bruge **objectGUID**. Dette er også attributten bruges, når du bruger udtrykkelig indstillinger i Azure AD-forbindelse og også bruges af DirSync-attribut.

Hvis du har flere områder og Flyt ikke brugere mellem områder og domæner, er **objectGUID** en god attribut bruge selv i dette tilfælde.

Hvis du flytter brugere mellem områder og domæner, skal du finde en attribut, der ikke ændres eller kan flyttes med brugerne under flytningen. Den anbefalede tilgang er at indføre attributten korte. En attribut, der kan indeholde noget, der ligner en GUID ville være passende. Under oprettelse af objektet skal oprettes et nyt GUID og tidsstemplet på brugeren. En brugerdefineret Synkroniser regel kan oprettes i Synkroniser program serveren for at oprette denne værdi, der er baseret på **objectGUID** og opdatere den valgte attribut i tilføjer. Når du flytter objektet, skal du kontrollere også kopiere indholdet af denne værdi.

En anden løsning er at vælge en eksisterende attribut, du kender ikke ændres. Ofte anvendte attributter omfatter **Medarbejder-id**. Hvis du finder en attribut, der indeholder bogstaver, skal du kontrollere, at der er ingen mulighed for bogstaver (store bogstaver eller små bogstaver) kan ændre for den attributværdien. Forkert attributter, der ikke bør benyttes omfatter disse attributter med navnet på brugeren. I et ægteskab eller skilsmisse forventes navnet ændres, hvilket ikke er tilladt for denne attribut. Dette er også en grund hvorfor attributter som **userPrincipalName**, **mail**og **targetAddress** ikke er er også muligt at markere i installationsguiden Azure AD-forbindelse. Disse attributter også indeholder den @-character, , er ikke tilladt i sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Ændre attributten sourceAnchor
Attributværdien sourceAnchor kan ikke ændres, når objektet, der er oprettet i Azure AD og identitet synkroniseres.

Derfor gælder følgende begrænsninger for Azure AD-forbindelse:

- Attributten sourceAnchor kan kun angive under den indledende installation. Hvis du køre installationsguiden, vil denne indstilling er skrivebeskyttet. Hvis du vil ændre denne indstilling, skal du fjerne og geninstallere.
- Hvis du installerer en anden server Azure AD-forbindelse, skal du vælge den samme sourceAnchor-attribut, som tidligere har brugt. Hvis du har tidligere brugt DirSync og flytte til Azure AD-forbindelse, skal du bruge **objectGUID** , da det er den attribut, der bruges af DirSync.
- Hvis værdien for sourceAnchor er ændret efter er objektet, der eksporteret til Azure AD, derefter Azure AD-forbindelse synkronisering opstår en fejl og tillader ikke flere ændringer på, at objekt, før problemet er løst og sourceAnchor ændres tilbage i kildemappen.

## <a name="azure-ad-sign-in"></a>Azure AD-logon
Mens integreres din lokale mappe med Azure AD, er det vigtigt at forstå, hvordan indstillingerne for synkronisering af kan påvirke den måde, som bruger godkender. Azure AD bruger userPrincipalName (UPN) til at godkende brugeren. Når du synkroniserer dine brugere, skal du vælge attributten, der skal bruges for værdi af userPrincipalName omhyggeligt.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Vælge attributten for userPrincipalName
Når du vælger attributten for at give skal værdien af UPN skal bruges i Azure én sikre

- Attributværdier er i overensstemmelse med den UPN-syntaks (RFC 822), som de skal være i formatetusername@domain
- Suffikset i værdierne, der svarer til en af de bekræftede brugerdefinerede domæner i Azure AD

I indstillinger for hurtig er det formodet valg for attributten userPrincipalName. Hvis userPrincipalName-attributten ikke indeholder værdien du vil dine brugere til at logge på Azure, og derefter skal du vælge **Brugerdefineret Installation**.

### <a name="custom-domain-state-and-upn"></a>Brugerdefineret domæne stat og UPN
Det er vigtigt at sikre, at der er et bekræftet domænet for UPN-suffikset.

John er en bruger i contoso.com. Du vil John at bruge det lokale UPN john@contoso.com til at logge på Azure, når du har synkroniseret brugere til din Azure AD directory contoso.onmicrosoft.com. Hvis du vil gøre det, skal du tilføje og bekræfte contoso.com som et brugerdefineret domæne i Azure AD, før du kan starte synkronisering af brugerne. Hvis UPN-suffikset af Jens, for eksempel contoso.com ikke svarer til en bekræftet domæne i Azure AD, erstatter Azure AD UPN-suffiks med contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Ikke-distribueret lokalt domæner og UPN til Azure AD
Nogle organisationer har ikke understøtter routing domæner, som contoso.local eller enkel enkelt etiket domæner som contoso. Du kan ikke bekræfte et domæne, der ikke understøtter routing i Azure AD. Azure AD-forbindelse kan synkronisere til et bekræftet domæne i Azure AD. Når du opretter en Azure AD-mappe, opretter en distribueret domæne, bliver standarddomænet for din Azure AD for eksempel contoso.onmicrosoft.com. Det bliver nødvendigt at kontrollere andre distribueret domæner i et scenarie med sådanne i tilfælde af, at du ikke vil synkronisere til standard onmicrosoft.com-domæne.

Læst [føje dit eget domænenavn til Azure Active Directory](active-directory-add-domain.md) til flere oplysninger om at tilføje og bekræfte domæner.

Azure AD-forbindelse registrerer, hvis du kører i et ikke-distribueret domænemiljø og vil korrekt advare dig fra at fortsætte med udtrykkelig indstillinger. Hvis du arbejder i et ikke-distribueret domæne, er det sandsynligvis, UPN for brugerne, er ikke-distribueret suffikser for. Eksempelvis hvis du kører under contoso.local, foreslår Azure AD-forbindelse dig til at bruge brugerdefinerede indstillinger i stedet for at bruge udtrykkelig indstillinger. Bruge brugerdefinerede indstillinger, er det muligt at angive den attribut, der skal bruges som UPN til at logge på Azure, når brugerne er synkroniseret til Azure AD.

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
