<properties
   pageTitle="Azure Active Directory Graph API | Microsoft Azure"
   description="En oversigt og hurtig start vejledning til Graph API som giver mulighed for programmeringsmæssig adgang til Azure AD gennem REST-API slutpunkter."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [AZURE.IMPORTANT] Azure AD Graph API funktionalitet er også tilgængelig via [Microsoft Graph](https://graph.microsoft.io/), et samlet API, der indeholder API'er fra andre Microsoft-tjenester som Outlook, OneDrive, OneNote, teamplanlægning og Office Graph, der er tilgængelige via et enkelt slutpunkt og med en enkelt adgangstoken.

Azure Active Directory Graph API leverer programmeringsmæssig adgang til Azure AD gennem REST-API slutpunkter. Programmer kan bruge Graph API til at udføre oprette, læse, opdatere og slette (CRUD) handlinger på directory-data og objekter. For eksempel understøtter Graph API følgende almindelige handlinger til et brugerobjekt:

- Oprette en ny bruger i en mappe

- Få en brugers detaljerede egenskaber, som deres grupper

- Opdatere en brugers egenskaber, som deres placering og telefonnummer, eller ændre deres adgangskode

- Kontrollere en brugers gruppemedlemskab for rollebaseret adgang

- Deaktivere en brugerkonto eller slette den helt

Ud over brugerobjekter, kan du udføre lignende handlinger på andre objekter som grupper og programmer. For at ringe Graph API på en mappe, programmet skal være registreret hos Azure AD og konfigureres til at give adgang til mappen. Dette opnås normalt via en bruger eller administrator samtykke forløb.

Se [Graph API Hurtig start vejledning](active-directory-graph-api-quickstart.md)for at begynde at bruge Azure Active Directory Graph API, eller få vist [interaktive Graph API referencedokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Funktioner

Graph API indeholder følgende funktioner:

- **RESTEN API slutpunkter**: Graph API'EN er en RESTful tjeneste består af slutpunkter, der åbnes ved hjælp af standard HTTP-anmodninger. Graph API understøtter XML eller Javascript Object Notation (JSON) indholdstyper for anmodninger om og svar. Du kan finde yderligere oplysninger finder [Azure AD Graph RESTEN API Reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Godkendelse med Azure AD**: alle anmodninger til Graph API skal godkendes, ved at tilføje en JSON Web Token (JWT) i overskriften godkendelse for din anmodning. Dette token er hentet ved at oprette en anmodning til Azure AD token slutpunkt og give gyldige legitimationsoplysninger. Du kan bruge OAuth 2.0-klienten legitimationsoplysninger strømmen eller godkendelseskoden give flow til at hente en token for at ringe til diagrammet. Yderligere oplysninger finder [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Rollebaseret godkendelse (RBAC)**: sikkerhedsgrupper bruges til at udføre RBAC i Graph API. Eksempelvis hvis du vil finde ud af, om en bruger har adgang til en bestemt ressource, kan programmet ringe til handlingen [Kontrollere gruppemedlemskab (transitive)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , der returnerer true eller false.

- **En forespørgsel**: Hvis du vil kontrollere for ændringer i en mappe mellem to perioder uden at skulle foretage hyppige forespørgsler til Graph API, kan du gøre en ændret forespørgsel. Denne type af anmodning returnerer ændringer mellem forrige forskelle forespørgsel anmodningen og den aktuelle anmodning. Få mere at vide under [Azure AD Graph API forskelle forespørgsel](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Directory filtypenavne**: Hvis du udvikler et program, der skal læse eller skrive entydige egenskaber for directory-objekter, du kan registrere og bruge lokalnummer værdier ved hjælp af Graph API. Eksempelvis hvis programmet kræver en Skype-id-egenskab for hver bruger, kan du registrere den nye egenskab i kataloget og det vil være tilgængelige på hvert brugerobjekt. Du kan finde yderligere oplysninger finder [Azure AD Graph API Directory skema filtypenavne](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Sikret ved tilladelse områder**: AAD Graph API Fremviser tilladelse områder, der aktiverer secure/accepteret adgang til AAD data og understøtte flere forskellige typer klient app, herunder:
 - dem med en brugergrænseflade, der er givet uddelegeret adgang til data ved hjælp af godkendelse af logget på brugerens (delegerede)
  - dem, der bruger Definer program – rollebaseret adgangskontrol som service/daemon klienter (app roller)

    Både delegerede og app rolle tilladelse områder repræsenterer en rettighed, der vises af Graph API og kan være blevet anmodet af klientprogrammer via programmet registrering tilladelser [funktioner i portalen Azure klassisk](https://manage.windowsazure.com). Klienter kan bekræfte tilladelse områder ydes til dem ved at undersøge omfang ("scp") kravet modtaget i adgangstoken delegeret tilladelser og roller ("roller") gøre krav på app rolle tilladelser. Lær mere om [Azure AD Graph API tilladelse områder](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Scenarier

Graph API gør det muligt for mange programmet scenarier. De følgende scenarier er de mest almindelige:

- **Line of Business (enkelt lejer) programmet**: I dette scenarie skal en enterprise udvikler fungerer for en organisation, der har et Office 365-abonnement. Udvikleren opretter et webprogram, der skal arbejde sammen med Azure AD til at udføre opgaver sådanne tildeler en licens til en bruger. Denne opgave kræver adgang til API Graph, så de udvikler registre enkelt lejer programmet i Azure AD og konfigurerer læse og skrive tilladelser for Graph API. Derefter er programmet konfigureret til at bruge sin egen legitimationsoplysninger, eller dem på den bruger, der i øjeblikket-logon til at hente et token for at ringe Graph API.

- **Software som et tjenesteprogram (med flere lejer)**: I dette scenario en uafhængig softwareproducent () udvikling af hostet med flere lejer webprogram, der indeholder funktioner til administration af brugeren til andre organisationer, der bruger Azure AD. Disse funktioner kræver adgang til directory-objekter, og så programmet skal ringe Graph API. Udvikleren registrerer programmet i Azure AD, konfigurerer det til at kræve læst og skrivetilladelser til Graph API og derefter aktiverer ekstern adgang, så andre organisationer kan samtykke til at bruge programmet i deres mappe. Når en bruger i en anden organisation godkendes til programmet for første gang, vises de en samtykke dialogboks med de tilladelser, der anmoder om programmet.  Give samtykke derefter giver programmet dem, der kræves tilladelser til Graph API i brugerens mappe. Se [Oversigt over den samtykke, som](active-directory-integrating-applications.md)kan finde flere oplysninger om samtykke framework.

## <a name="see-also"></a>Se også

[Azure AD Graph API Hurtig start vejledning](active-directory-graph-api-quickstart.md)

[AD Graph RESTEN dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory developer's vejledning](active-directory-developers-guide.md)
