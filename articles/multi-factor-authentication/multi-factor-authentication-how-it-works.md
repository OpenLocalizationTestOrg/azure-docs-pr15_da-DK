<properties 
    pageTitle="Azure Multi-Factor Authentication - hvordan det fungerer"
    description="Azure Multi-Factor Authentication hjælper med at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen. Det indeholder ekstra sikkerhed ved at kræve en anden form for godkendelse og leverer stærke godkendelse via et udvalg af nemt bekræftelse."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Hvordan fungerer Azure Multi-Factor Authentication

Sikkerheden for multi-Factor authentication ligger i sin lagdelte fremgangsmåde. En stor udfordring præsenteres bringe flere godkendelse faktorer for hackere. Selvom en hacker administrerer for at lære brugerens adgangskode, er det ikke bruges også uden besiddelse af den enhed, der er tillid til. Skal brugeren mister enheden, kan den person, som finder den ikke bruge den, medmindre han eller hun også kender brugerens adgangskode.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication hjælper med at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen.  Den indeholder ekstra sikkerhed ved at kræve en anden form for godkendelse og leverer stærke godkendelse via et udvalg af nemt bekræftelse:

- telefonopkald
- SMS-besked
- meddelelse om mobilapp – så brugerne kan vælge metoden de foretrækker
- mobilapp verifikationskoden
- 3 part ed tokens

Finde flere oplysninger Hold da hvordan det fungerer i den følgende video.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Metoder til multi-Factor authentication
Når en bruger logger på, er det en yderligere kontrol, der sendes til brugeren.  Følgende er en liste over metoder, der kan bruges til denne anden bekræftelse.

Metode til bekræftelse  | Beskrivelse
------------- | ------------- |
Telefonopkald | Et opkald er placeret på en brugers Smartphone bede dem om at bekræfte, at de logge på ved at trykke på tegnet #.  Dette fuldfører bekræftelsesprocessen.  Denne indstilling kan konfigureres og kan ændres til en kode, du angiver.
SMS-besked | En SMS-besked sendes til en brugers Smartphone med et 6-cifret kode.  Angiv denne kode i at fuldføre bekræftelsesprocessen.
Meddelelse om-Mobilappen | En anmodning om bekræftelse sendes til en brugers Smartphone, hvor du beder dem fuldført bekræftelsen ved at vælge Bekræft fra mobilappen. Dette sker, hvis du har valgt appmeddelelse som din primære bekræftelsesmetode.  Hvis de modtager ud, når de ikke logge på, kan de vælge at rapportere det som svindel.
Verifikationskoden med Mobile-App | En bekræftelseskode sendes til mobilappen, der kører på en brugers Smartphone.  Dette sker, hvis du har valgt en bekræftelseskode som din primære bekræftelsesmetode.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Tilgængelige versioner af Azure Multi-Factor Authentication
Azure Multi-Factor Authentication er tilgængelig i tre forskellige versioner.  Tabellen nedenfor beskrives hver af disse mere detaljeret.

Version  | Beskrivelse
------------- | ------------- |
Multi-Factor Authentication til Office 365 | Denne version virker udelukkende med Office 365-programmer og administreres fra Office 365-portalen. Så kan administratorer nu beskytte deres Office 365-ressourcer ved hjælp af godkendelse i flere niveauer.  I denne version leveres med et Office 365-abonnement.
Multi-Factor Authentication til Azure-administratorer | Det samme undersæt af Multi-Factor Authentication funktioner til Office 365 fås gratis til alle Azure administratorer. Hver administratorkonto på et Azure abonnement kan nu få yderligere beskyttelse ved at aktivere denne Multi-Factor authentication kernefunktioner. En administrator, som ønsker at få adgang til Azure portal for at oprette en VM, et websted, Administrer lagerplads, kan mobile-tjenester eller andre Azure Service tilføje så Multi-Factor authentication sin administratorkonto.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication indeholder det bedst sæt af egenskaber. <br><br>Det giver yderligere konfigurationsindstillinger via den Azure Management-portalen, avancerede rapportering og support for et celleområde i det lokale miljø og skyen programmer. Azure Multi-Factor Authentication kan købes som en separat licens og følger i Azure Active Directory Premium og Enterprise mobilitet pakke. <br><br>Det kan også købes på grundlag af forbrug ved at oprette en udbyder af Azure Multi-Factor godkendelse i et Azure-abonnement.
##<a name="feature-comparison-of-versions"></a>Sammenligning af versioner
Følgende tabellen nedenfor indeholder en liste over de funktioner, der er tilgængelige i forskellige versioner af Azure Multi-Factor Authentication.


Funktion  | Multi-Factor Authentication til Office 365 (inkluderet i Office 365 lagerenheder)|Multi-Factor Authentication til Azure administratorer (følger med Azure abonnement) | Azure Multi-Factor Authentication (inkluderet i Azure AD Premium og Enterprise mobilitet pakke)
------------- | :-------------: |:-------------: |:-------------: |
Administratorer kan beskytte konti med MFA| * | * (Kun tilgængelig for Azure administratorkonti)|*
Mobilapp som en anden faktor|* | * | *
Telefonopkald som en anden faktor|* | * | *
SMS som en anden faktor|* | * | *
Appadgangskoder for klienter, som ikke understøtter MFA|* | * | *
Administrator kontrol over godkendelsesmetoder| *| *| *
FASTGØR tilstand| | | *
Svindel besked| | | *
MFA-rapporter| | | *
Enkeltstående Spring| | | *
Brugerdefineret hilsener for telefonopkald| | | *
Tilpasning af opkalds-ID for telefonopkald| | | *
Begivenhed bekræftelse| | | *
Der er tillid til IP'er| | | *
Suspender MFA til husket enheder (Public Preview)| | | *
MFA SDK| | | *
MFA for lokale programmer, der bruger MFA server| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Sådan henter du Azure Multi-Factor Authentication

Hvis du vil have fuld funktionalitet, som Azure Multi-Factor Authentication i stedet for blot dem, der er knyttet til Office 365-brugere og Azure administratorer, er der flere muligheder for at få det:

1.  Købe Azure Multi-Factor Authentication licenser og tildele dem til dine brugere.
2.  Købe licenser, der har Azure Multi-Factor Authentication samles i dem som Azure Active Directory Premium eller Enterprise mobilitet pakke og tildele dem til dine brugere.
3.  Oprette en Azure Multi-Factor udbyder af godkendelse i et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du tilmelde dig en Azure prøveabonnement. Prøveabonnementer skal konverteres til almindelig abonnementer før udløb af prøveversion.

Når du bruger en Azure Multi-Factor godkendelse-udbyder, der er to brugen modeller tilgængelige, der er faktureret via din Azure abonnement:


- **Hver bruger**. Generelt til store virksomheder, som vil aktivere Multi-Factor godkendelse for et fast antal medarbejdere, der jævnligt har brug for godkendelse.
- **Per godkendelse**. Generelt til store virksomheder, der vil aktivere Multi-Factor godkendelse for en stor gruppe af eksterne brugere, der sjældent har brug for godkendelse.

Finde priser detaljer under [Azure MFA priser.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Vælg den per plads eller forbrug-baserede model, der passer bedst til din organisation.   Derefter for at få Introduktion se [Komme i gang](multi-factor-authentication-get-started.md)
