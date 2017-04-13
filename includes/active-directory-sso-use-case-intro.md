Organisationer bruger flere [som en tjeneste (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) programmer til produktivitet fordi skyen hjælpemidler og værktøjer bliver mere tilgængelige. Som antallet SaaS apps bliver større, bliver det udfordring for administratorer at administrere konti og adgangsrettigheder og for brugerne at huske deres forskellige adgangskoder. Administration af disse programmer individuelt opretter ekstra arbejde og er mindre sikker.


- Medarbejdere, der har at holde styr på mange adgangskoder har tendens til at bruge mindre sikre metoder til at huske dem, enten skrive adgangskoder ned eller med de samme adgangskoder på tværs af mange konti.

- Når der modtages en ny medarbejder eller en forlader, skal alle deres konti være individuelt klargjort eller klargøringen nulstilles.

- Desuden medarbejdere kan begynde med at bruge SaaS apps til deres arbejde uden at gå IT, hvilket betyder, at de opretter deres egne konti på systemer, IT-administratorer ikke godkendt og ikke overvågning.  

En løsning for alle disse udfordringer er enkeltlogon (SSO). Det er den nemmeste måde at administrere flere apps og give brugerne en ensartet sign-on – oplevelse. Azure Active Directory (Azure AD) indeholder en robust SSO-løsning og har mange tilgængelige allerede integrerede programmer, med selvstudier til administratorer til hurtigt at konfigurere en ny app og starter klargøring af brugere.


## <a name="how-does-azure-active-directory-integrate-apps"></a>Hvordan integreres Azure Active Directory apps?  

Azure AD gør det muligt at integrere dine apps og klargjort konti. Dette kan gøres ved at benytte en af to måder.

- Hvis appen er allerede integreret i galleriet-appen, kan du gå igennem portalen for at indstille apps og konfigurere indstillinger for at tillade SSO. For en hvilken som helst Gallery-app, du kan komme i gang ved Følg de simple trinvise instruktioner præsenteres i galleriet app og i portalen Azure til at aktivere enkeltlogon.

- Hvis appen ikke er i galleriet, kan du stadig konfigurere de fleste apps i Azure AD som en brugerdefineret app. Dette kræver lidt mere teknisk ekspertise til at konfigurere. Du kan tilføje et program, der understøtter SAML 2.0 som en samlet app eller et program, der indeholder en HTML-baserede logon side som en adgangskode SSO-app.

I de tilfælde, hvor brugere har oprettet deres egne konti til SaaS apps, der ikke administreres af IT, værktøjet [Skyen App registrering](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) indeholder en løsning. Dette værktøj overvåger webtrafik for at identificere, hvilke apps bruges i hele organisationen og antallet af personer, der bruger hver af dem. IT kan bruge disse oplysninger til at se, hvilke apps til brugere foretrækker og beslutter, at integrere i Azure AD for SSO.  

Når du integrerer en app i Azure AD, kan du tilknytte brugernes eksisterende programmet identiteter til deres respektive Azure AD identiteter.  
