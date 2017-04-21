<properties
pageTitle="Installere og konfigurere værktøjer til redigering i GitHub"
description="Værktøjer og trin for at få konfigureret til redigering Azure indhold i GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Installere og konfigurere værktøjer til redigering i GitHub

Følg trinnene i denne artikel for at konfigurere værktøjer til at bidrage til den Azure tekniske dokumentation. Tilfældige og lejlighedsvise bidragydere kan sandsynligvis bruge GitHub brugergrænseflade, der er beskrevet i trin 2.

Hvis du ikke kender ciffer, skal du muligvis vil gennemgå nogle ciffer terminologi: [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). Desuden denne StackOverflow tråd indeholder en ordliste ciffer støder du i denne række trin: [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Indholdsfortegnelse

- [Oprette en GitHub konto og konfigurere din profil](#create-a-github-account-and-set-up-your-profile)
- [Tilmelde sig Disqus](#sign-up-for-disqus)
- [Finde ud af, om du virkelig skal følge resten af disse trin](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Tilladelser i GitHub](#permissions-in-github)
- [Installere ciffer til Windows](#install-git-for-windows)
- [Aktivere to-faktor-godkendelse](#enable-two-factor-authentication)
- [Installere en tabsbeløb editor](#install-a-markdown-editor)
- [Konfigurere Atom](#configure-atom)
- [Deler sig lageret og kopiere det til din computer](#fork-the-repository-and-copy-it-to-your-computer)
- [Konfigurere dit brugernavn og din mail lokalt](#configure-your-user-name-and-email-locally)
- [Næste trin](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Oprette en GitHub konto og konfigurere din profil

Hvis du vil bidrage til Azure tekniske indholdet, skal du en [GitHub](http://www.github.com) -konto.

Hvis du er en Microsoft-bidragyder, skal du konfigurere kontoen GitHub, så du er klart defineret som en Microsoft-medarbejder. Konfigurere din profil på følgende måde:

- **Profilbillede**: et billede af du (påkrævet)
- **Navn**: dit første og sidste navn (påkrævet)
- **Mail**: din Microsoft-mailadresse (valgfrit)
- **Virksomhed**: Microsoft Corporation (påkrævet)
- **Placering**: liste over din placering (valgfrit)

Din profil skal ligne denne profil:

<p align="center">
 ![Eksempel på GitHub profil](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>Tilmelde sig Disqus

Alle publicerede Azure tekniske artiklen har en kommentar stream Disqus tjenesten.

 ![Discus-logo](./media/tools-and-setup/discus.png)

Hvis du er en Microsoft-medarbejder, og hvis du er en dokumentforfatter eller en bidragyder til en artikel, skal du tilmelde sig Disqus, så du kan deltage i kommentar strømmen til artiklen.

1. Tilmelde sig en konto på [http://www.disqus.com/](http://www.disqus.com/)
2. Udfyld din profil på følgende måde:

 - **Fulde navn**: dit fulde navn som vises i din Microsoft address book oversigt plus de kantede parenteser oplysninger, som er dit alias plus @MSFT. Format: *først sidste [alias@MSFT] *
 - **Placering**: din placering
 - **Kort Bio**: din titel

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Finde ud af, om du virkelig skal følge resten af disse trin

Du kan ikke nødvendigt at følge trinnene i denne artikel. Det afhænger af type indhold bidrag, du vil have eller har brug at gøre.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Sende en kun tekst-ændring af en eksisterende artikel

Hvis du kun har brug for, eller du vil gøre tekstbaseret opdateringer til en eksisterende artikel, skal du sandsynligvis ikke følge resten af trinnene. Du kan bruge Githubs webbaseret tabsbeløb editor til at sende dine ændringer. Klik på GitHub link i artiklen, du vil ændre:

 ![Eksempel på GitHub profil](./media/tools-and-setup/contributetogit.png)

 Klik derefter på redigeringsikonet GitHub-versionen af artiklen

 ![Eksempel på GitHub profil](./media/tools-and-setup/editicon.PNG)

 Der åbner nemt bruge webeditor, der gør det nemt at sende ændringer. Du behøver ikke at følge trinnene i denne artikel.

###<a name="all-other-changes"></a>Alle andre ændringer
GitHub UI understøtter oprettelse af nye filer og trække og slippe billeder. Når du arbejder i Brugergrænsefladen, kan administrere forgreninger dog være forvirrende så anbefales typisk du installere værktøjerne og lære kommandoer til oprettelse og administration af artikler. Hvis du vil bruge Brugergrænsefladen, skal du se:

- [Oprette filer på Github](https://github.com/blog/1327-creating-files-on-github)
- [Overføre filer til din typer lagre](https://github.com/blog/2105-upload-files-to-your-repositories)

For følgende typer arbejde anbefales det du installerer og Lær at bruge værktøjerne:

 - Foretage større ændringer til en artikel
 - Oprette og publicere en ny artikel
 - Tilføje nye billeder eller opdatere billeder
 - Opdatere en artikel over en periode på dage uden publicering ændringer af disse dage
 - Oprettelse af indhold til en version, der skal gå på en bestemt dag på et bestemt tidspunkt

##<a name="permissions-in-github"></a>Tilladelser i GitHub

Alle med en GitHub konto kan bidrage til Azure tekniske indhold via vores offentlige lager på [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). Der kræves ingen særlige tilladelser.

Hvis du er en Microsoft-PM eller writer, der arbejder på Azure indhold, skal du arbejder i vores privat indhold lager, azure-indhold-kurs. Besøg [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) for at anmode om læsetilladelser, skal du gøre bidrag til den private repo - Log på ved hjælp af knappen GitHub > Klik på Azure > Klik på **Deltag i et team** eller **deltage i et andet team**, og Søg efter og deltage i gruppen **azure indhold læse** .

## <a name="install-git-for-windows"></a>Installere ciffer til Windows

Du kan installere ciffer til Windows fra [http://git-scm.com/download/win](http://git-scm.com/download/win). Dette program installeres ciffer version control system, og den installeres ciffer fest, kommandolinjen app, du vil bruge til at interagere med din lokale ciffer lager.

Du kan acceptere standardindstillingerne; Hvis du vil kommandoerne skal være tilgængeligt i kommandolinjen Windows, Vælg den indstilling, der gør det muligt.

<p align="center">
 ![Eksempel på GitHub profil](./media/tools-and-setup/gitbashinstall.png)

(Bemærk: Dette er ikke den samme som "Github til Windows". "Github til Windows" er et andet grafiske brugergrænseflade-baserede værktøj, der fungerer også, hvis du vil Læs mere om dig selv. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Aktivere to-faktor-godkendelse

Du skal aktivere to faktor-godkendelse (2FA) på kontoen GitHub Hvis du arbejder i den private indhold lager. Det er påkrævet i den private lager.

Følg vejledningen i begge følgende GitHub Hjælp-emnerne for at aktivere skal:

- [Om to faktor-godkendelse](https://help.github.com/articles/about-two-factor-authentication/)
- [Oprette et adgangstoken til kommandolinjen brug](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Når du opretter tokenet, Vælg de områder, der er tilgængelige i oprettelse af token Brugergrænsefladen ([oplysninger på hvert område](https://developer.github.com/v3/oauth/#scopes))

Når du aktiverer 2FA, er det nødvendigt at angive adgangstoken i stedet for adgangskoden GitHub ved kommandoprompten, når du forsøger at få adgang til et GitHub lager fra kommandolinjen. Adgangstoken er ikke den godkendelse kode, du får i en SMS-besked, når du konfigurerer 2FA. Det er en lang tekststreng, der ser nogenlunde sådan ud: fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Nogle noter om dette:

- Når du opretter din adgangstoken, kan du gemme det i en tekstfil at gøre det let tilgængelige, når du har brug for.

- Senere, hvis du har brug at indsætte tokenet, ved at indsætte i kommandolinjen på to måder:

 - Klik på ikonet i øverste venstre hjørne af vinduet kommandolinjen > Rediger > Sæt ind.
 - Højreklik på ikonet i øverste venstre hjørne af vinduet, og klik på Egenskaber > Indstillinger > Ekspresredigering tilstand. Dette konfigurerer kommandolinjen, så du kan indsætte ved at højreklikke i vinduet kommandolinjen.

## <a name="install-a-markdown-editor"></a>Installere en tabsbeløb editor

Vi opretter indhold ved hjælp af enkle "tabsbeløb" notation i filerne hellere end komplekst "markering" (HTML, XML- osv.). Tilfældet, skal du installere en tabsbeløb editor.

- **Atom**: de fleste af os Brug Github's Atom tabsbeløb editor: [http://atom.io](http://atom.io). Det kræver ikke en licens til erhvervsbrug. Den har stavekontrol.

- **Notesblok**: Du kan bruge Notesblok til en meget lette indstilling.

- **Prose**: Dette er en lette, elegante, online og åbne tabsbeløb Kildeeditor, der indeholder et eksempel. Besøg [http://prose.io](http://prose.io) og Godkend Prose i dit lager.

- **[Visual Studio-kode](https://www.visualstudio.com/products/code-vs.aspx)** - Microsofts post i dette område.

## <a name="configure-atom"></a>Konfigurere Atom

Hvis du bruger Atom, skal du konfigurere et par ting.

- Atom standard til at bruge 2 mellemrum til faner, men tabsbeløb forventer 4 mellemrum. Hvis du lader standardindstillingen for to, din artikel ser godt ud i lokale preview, men ikke når den er importeret til Azure. Så konfigurere Atom for at bruge 4 mellemrum – du kan finde denne indstilling under filer > Indstillinger > Indstillinger for Editor > fanen længde.
- Du vil sikkert også vil aktivere bløde ombryde i dette afsnit, som indeholder den samme som "tekstombrydning" i Notesblok.
- Hvis du vil slå tabsbeløb eksemplet, skal du klikke på pakker > tabsbeløb Preview > slå Preview. Du kan bruge Ctrl-Skift-M for at skifte preview HTML-visning.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Deler sig lageret og kopiere det til din computer

1. Oprette en forgrening lageret i GitHub - gå til øverst til højre på siden, og klik på knappen forgreningsovergang. Hvis du bliver bedt om det, kan du vælge din konto som den placering, hvor forgreningsovergang skal oprettes. Dette opretter en kopi af lager i kontoen ciffer Hub. Generelt skal tekniske forfattere og program ledere forgreningsovergang azure-indhold-kurs, private repo. Community bidragydere skal forgreningsovergang azure-indhold, offentlige repo. Du skal kun deler sig én gang efter din første installation, hvis du vil kopiere din forgreningsovergang til en anden computer, behøver du kun til at køre de kommandoer, der følges i dette afsnit for at kopiere repo til din computer.  Hvis du vælger at oprette forgreninger af begge typer lagre, skal du oprette en forgrening til hver lager.

2. Kopiér den personlige Access Token, du fik fra [https://github.com/settings/tokens](https://github.com/settings/tokens). Du kan acceptere standardtilladelser for tokenet.  Gemme personlige Access Token i en tekstfil til senere brug.

3. Dernæst skal du kopiere lageret til din computer med dine legitimationsoplysninger, der er integreret i kommandostrengen.  Åbn ciffer fest for at gøre dette, og Kør som administrator. Skriv følgende kommando i kommandoprompten.  Denne kommando opretter en azure-content(-pr) mappe på computeren.  Hvis du bruger standardplaceringen, vil det være på c:\users<your Windows user name>\azure-content(-pr).

Offentlige repo:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Privat repo:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Denne kommando Klon kan for eksempel se nogenlunde sådan ud:

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Angive remote lager forbindelse og konfigurere legitimationsoplysninger

Oprette en reference til rod lager ved at angive disse kommandoer. Dette indstiller forbindelser til lager i GitHub, så du kan få de seneste ændringer på din lokale computer, og Overfør dine ændringer tilbage til GitHub. Denne kommando konfigurerer også et token lokalt, så du ikke behøver at angive dit brugernavn og adgangskode, hver gang du forsøger at få adgang til den foregående repo og din forgreningsovergang på GitHub.

Offentlige repo:

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Privat repo:

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Det tager normalt et stykke tid. Når du gør dette, behøver du ikke vil deler sig igen, eller angive dine legitimationsoplysninger igen. Du skal kun nødvendigt at kopiere forgreninger til en lokal computer igen, hvis du konfigurerer værktøjerne på en anden computer.


## <a name="configure-your-user-name-and-email-locally"></a>Konfigurere dit brugernavn og din mail lokalt

For at sikre, at du er angivet korrekt som en bidragyder, skal du konfigurere dit brugernavn og din mail lokalt i ciffer.

1. Start ciffer fest, og Skift til azure-indhold eller azure-indhold-kurs:

   ````
   cd azure-content
   ````

 eller

   ````
   cd azure-content-pr
   ````

2. Konfigurere dit brugernavn, så det svarer til dit navn, som du konfigurere det i din GitHub profil:

    ````
    git config --global user.name "John Doe"
    ````
3. Konfigurere din mail, så det svarer til den primære mailadresse, der er angivet i din profil GitHub; Hvis du er en MSFT medarbejder, skal den være din MSFT e-mail-adresse:

    ````
    git config --global user.email "alias@example.com"
    ````
4. Skriv `git config -l` og gennemse dine lokale indstillinger for at sikre brugernavnet og mail i konfigurationen er korrekte.

##<a name="next-steps"></a>Næste trin

- Forstå typen indhold, der hører til i det tekniske indhold repo, og ved, hvad der ikke hører. Se [indhold kanal vejledning](./content-channel-guidance.md)!
- Følg [disse trin for at oprette eller ændre en artikel og derefter sende det til publicering](./git-commands-for-master.md).
- Kopiere [skabelonen tabsbeløb](../markdown templates/markdown-template-for-new-articles.md) som grundlag for en ny artikel.
- Brug [denne tjekliste for at bekræfte din anmodning om hente opfylder kriteriet kvalitet](./contributor-guide-pr-criteria.md) til fletning.


###<a name="contributors-guide-navigation"></a>Bidragydere vejledning navigation

- [Oversigt over artikel](./../README.md)
- [Indeks over vejledning artikler](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
