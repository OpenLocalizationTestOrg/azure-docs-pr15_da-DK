<properties 
    pageTitle="Installer din Node.js WebApp til Azure i fem minutter | Microsoft Azure" 
    description="Få mere at vide, hvor nemt det er at køre webapps i App Service ved at installere en eksempel-app. Start hurtigt at gøre reelle udvikling, og få vist resultater med det samme." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-nodejs-web-app-to-azure-in-five-minutes"></a>Installer din første Node.js WebApp til Azure i fem minutter

Dette selvstudium hjælper dig med at installere din første Node.js WebApp til [Azure App-tjenesten](../app-service/app-service-value-prop-what-is.md).
Du kan bruge App Service til at oprette webapps, [mobilapp tilbage ender](/documentation/learning-paths/appservice-mobileapps/)og [API apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Du skal: 

- Oprette en web-app i Azure App Service.
- Installere eksempel Node.js kode.
- Se din kode, der kører direkte i fremstilling.
- Opdater din online på samme måde, du ville gøre [opslagsnål ciffer bekræfter](https://git-scm.com/docs/git-push).

## <a name="prerequisites"></a>Forudsætninger

- [Ciffer](http://www.git-scm.com/downloads).
- [Azure CLI](../xplat-cli-install.md).
- En Microsoft Azure-konto. Hvis du ikke har en konto, kan du [tilmelde dig en gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivere dine Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Du kan [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751) uden en Azure-konto. Oprette en starter app og lege med det til op til en time – ingen kreditkort er påkrævet, ingen forpligtelser.

## <a name="deploy-a-nodejs-web-app"></a>Installere en Node.js WebApp

1. Åbn en ny Windows-kommandoprompt, PowerShell-vinduet, Linux shell eller OS X terminal. Køre `git --version` og `azure --version` at bekræfte, at ciffer og Azure CLI er installeret på computeren.

    ![Test installationen af CLI værktøjer til din første online i Azure](./media/app-service-web-get-started/1-test-tools.png)

    Hvis du ikke har installeret værktøjerne, kan du finde [forudsætninger](#Prerequisites) Hent links.

3. Log på Azure således:

        azure login

    Følg Hjælp-meddelelsen for at fortsætte logon.

    ![Log på Azure til at oprette din første online](./media/app-service-web-get-started/3-azure-login.png)

4. Ændre Azure CLI til ASM tilstand, og derefter angive installation brugeren til App-tjenesten. Du kan installere kode ved hjælp af legitimationsoplysningerne senere.

        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

1. Ændre til en arbejdsmappe (`CD`) og klone eksempel app således:

        git clone https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git

2. Ændre til lager af din app eksempel.

        cd app-service-web-nodejs-get-started

4. Opret App Service app ressourcen i Azure med et entydigt appnavn og den installation bruger, du har konfigureret tidligere. Når du bliver bedt om det skal du angive antallet det ønskede område.

        azure site create <app_name> --git --gitusername <username>

    ![Oprette din første online Azure ressourcen i Azure](./media/app-service-web-get-started-languages/node-site-create.png)

    Din app er oprettet i Azure nu. Desuden er den aktuelle mappe ciffer-initialiseret og forbindelsen til den nye App Service-app som en remote ciffer.
    Du kan gå til app URL-adressen (http://&lt;app_name >. azurewebsites.net) får du vist siden flotte standard HTML, men lad os faktisk kan komme din kode der nu.

4. Installer din eksempelkode til Azure-app, som du vil skubbe en kode med ciffer. Når du bliver bedt om det, kan du bruge den adgangskode, du har konfigureret tidligere.

        git push azure master

    ![Push-kode til din første online i Azure](./media/app-service-web-get-started-languages/node-git-push.png)

    `git push`ikke kun indsætter kode i Azure, men også udløser installationsopgaver i programmet til installation. 
    Hvis du har en package.json i dit projekt (lager) rod, gendanner scriptet installation de nødvendige pakker for dig. 

Tillykke, du har installeret din app til Azure App-tjenesten.

## <a name="see-your-app-running-live"></a>Se din app, der kører live

For at få vist din kører direkte i Azure-app, du Kør denne kommando fra en anden mappe i din lager:

    azure site browse

## <a name="make-updates-to-your-app"></a>Foretage opdateringer til din app

Du kan nu bruge ciffer til at overføre din project (lager) rodwebstedet når som helst til at foretage en opdatering til webstedet. Du gøre det på samme måde som, når du har installeret din kode første gang. For eksempel hver gang du vil overføre en ny ændring, som du har afprøvet lokalt, skal kun køre følgende kommandoer fra dit projekt (lager) rod:

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Næste trin

[Oprette, konfigurere og installere en Node.js Express WebApp til Azure](app-service-web-nodejs-get-started.md). Ved at følge dette selvstudium, skal du lære de grundlæggende færdigheder, skal du køre en Node.js WebApp i Azure, f.eks.:

- Oprette og konfigurere apps i Azure fra PowerShell/fest.
- Angiv Node.js version.
- Bruge en startfil, der ikke er i programmet rodmappe.
- Automatisere med NPM.
- Få fejl og output logfiler.

Eller du kan få mere ud af din første online. Eksempel:

- Prøv [andre måder at implementere kode til Azure](../app-service-web/web-sites-deploy.md). For eksempel for at installere fra en af dine GitHub typer lagre, skal du blot markere **GitHub** i stedet for **Lokale ciffer lager** i **installationsindstillinger**.
- Tage dine Azure-app til et højere niveau. Godkende dine brugere. Skala det baseret på behov. Konfigurere påmindelser nogle ydeevne. Alle med nogle få klik. Se [tilføje funktioner til dit første WebApp](app-service-web-get-started-2.md).

