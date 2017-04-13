<properties
   pageTitle="Detaljeret gennemgang af ved hjælp af Azure Active Directory B2B samarbejde preview | Microsoft Azure"
   description="Samarbejde med Azure Active Directory-B2B understøtter din tværs af firmaer relationer ved at aktivere forretningsforbindelser selektivt adgang til din virksomhedens programmer"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Azure AD B2B samarbejde preview: detaljeret gennemgang

Denne gennemgang beskriver, hvordan du bruger Azure AD B2B-samarbejde. Som IT-administratoren af Contoso ønsker vi at dele programmer med medarbejdere fra tre partnervirksomheder. Ingen af partnervirksomheder skal have Azure AD.

- Bent fra enkel Partner organisationsdiagram
- BOB fra mediet Partner organisationsdiagram, skal have adgang til et sæt af apps
- Carols fra komplekse Partner organisationsdiagram, skal have adgang til et sæt af apps og medlemskab af grupper på Contoso

Når invitationer sendt ud til partner brugere, kan vi konfigurere dem i Azure AD til at give adgang til apps, og medlemskab til grupper via Azure-portalen. Lad os starte med at tilføje Bent.

## <a name="adding-alice-to-the-contoso-directory"></a>Tilføje Bent til mappen Contoso
1. Opret en .csv-fil med overskrifterne, som vist, udfylde kun Alices **mail**, **vist navn**og **InviteContactUsUrl**. **Vist navn** er det navn, der vises, i invitationen, og det navn, der vises i mappen Contoso Azure AD. **InviteContactUsUrl** er en måde til Bent kontakte Contoso. I følgende eksempel angiver InviteContactUsUrl LinkedIn profilen for Contoso. Det er vigtigt at skrive navnene i den første række i .csv-filen præcis som angivet i [reference til CSV-fil-format](active-directory-b2b-references-csv-file-format.md).  
![Eksempel csv-fil til Bent](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Tilføje en bruger i portalen Azure ind i Contoso directory (Active Directory > Contoso > brugere > Tilføj bruger). Vælg "Brugere i partnervirksomheder" i "Type af bruger" rullemenuen. Overfør .csv-fil. Sørg for, at .csv-fil er lukket, før du overfører.  
![Overførsel af csv-filen til Bent](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Bent er nu repræsenteret som eksterne brugere i mappen Contoso Azure AD.  
![Bent er angivet i Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Bent modtager følgende mailen.  
![Invitation via mail til Bent](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Bent klikker på linket, og hun er bedt om at acceptere invitationen og logge på med sine arbejde legitimationsoplysninger. Hvis Bent ikke er i mappen Azure AD, Bent bedt om at tilmelde dig.  
![Tilmelde sig efter invitation til Bent](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Bent omdirigeres til App Access panelet tom, indtil hun har fået tildelt adgang til apps.  
![Access Panel for Bent](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Denne fremgangsmåde gør det muligt for den nemmeste måde B2B samarbejde. Som en bruger i mappen Contoso Azure AD kan Bent få adgang til programmer og grupper via Azure-portalen. Nu Lad os tilføje Bob, der skal have adgang til programmerne, Moodle og Salesforce.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>Tilføje Bob til mappen Contoso og give adgang til apps
1. Brug af Windows PowerShell med Azure AD-modulet installeret for at finde de programmet id'er af Moodle og Salesforce. Id'erne kan hentes ved hjælp af cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` dette viser en liste over alle tilgængelige programmer i Contoso og deres AppPrincialIds.  
![Hent id'er for Bob](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Oprette en .csv-fil, der indeholder Bobs mail og vist navn, **InviteAppID**, **InviteAppResources**og InviteContactUsUrl. Udfylde **InviteAppResources** med AppPrincipalIds Moodle og Salesforce fundet via PowerShell, adskilt af et mellemrum. Udfylde **InviteAppId** med samme AppPrincipalId af Moodle brande e-mailen, og log på sider med et Moodle logo.  
![Eksempel csv-fil til Bob](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Overfør .csv-fil via Azure-portalen, ligesom det blev gjort til Bent. BOB er nu en ekstern bruger i Contoso Azure AD-kataloget.

4. BOB får følgende mailen.  
![Invitation via mail til Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. BOB klikker på linket og bliver bedt om at acceptere invitationen. Når hun er logget på, skal han omdirigeres til panelet adgang og kan allerede bruge Moodle og Salesforce.  
![Access Panel for Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Vi tilføjer Carols næste, der kræver adgang til programmer samt medlemskab til grupper i mappen Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Tilføje Carols til mappen Contoso, give adgang til apps og give gruppemedlemskab

1. Brug af Windows PowerShell med Azure AD-modulet installeret for at finde programmet id'er og gruppe-id'er i Contoso.
 - Hente AppPrincipalId ved hjælp af cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, det samme som for Bob
 - Hente ObjectId til grupper ved hjælp af cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Dette viser en liste over alle grupper i Contoso og deres ObjectIds. Gruppe-id'er kan også hentes som objekt-ID på fanen Egenskaber i gruppen på portalen Azure.  
![Hente id'er og grupper til Carols](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Oprette .csv-fil, udfylde Carols mail, vist navn, InviteAppID, InviteAppResources, **InviteGroupResources**og InviteContactUsUrl. **InviteGroupResources** udfyldes af ObjectIds af grupperne MyGroup1 og eksterne, adskilt af et mellemrum.  
![Eksempel csv-fil til Carols](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Overfør .csv-fil via Azure-portalen.

4. Carols er en bruger i mappen Contoso og er medlem af grupperne MyGroup1 og eksterne, også, som det fremgår Azure portalen.  
![Carols er angivet i en gruppe i Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carols modtager en mail, der indeholder et link for at acceptere invitationen. Når hun logger på, omdirigeres hun til panelet App adgang skal have adgang til Moodle og Salesforce.  

Det er alt, der er at tilføje brugere fra partner virksomheder i Azure AD B2B-samarbejde. Denne gennemgang viste, hvordan du tilføjer brugere Bent, Bob og Carols til mappen Contoso ved hjælp af tre separate .csv-filer. Denne proces kan gøres nemmere ved kondensering separat .csv-filerne til en enkelt fil.  
![Eksempel csv-fil til Bent, Bob og Carols](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Relaterede artikler
Gennemse vores andre artikler på Azure AD B2B samarbejde:

- [Hvad er Azure AD B2B samarbejde?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Sådan fungerer det](active-directory-b2b-how-it-works.md)
- [Reference til CSV-fil-format](active-directory-b2b-references-csv-file-format.md)
- [Eksterne brugere token format](active-directory-b2b-references-external-user-token-format.md)
- [Eksterne brugere objekt attributændringer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle preview-begrænsninger](active-directory-b2b-current-preview-limitations.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
