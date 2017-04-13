<properties
    pageTitle="Lokale ciffer installation til Azure App-tjenesten"
    description="Få mere at vide, hvordan du aktiverer lokale ciffer installation til Azure App-tjenesten."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale ciffer installation til Azure App-tjenesten

Dette selvstudium viser, hvordan du installerer din app til [Azure App-tjenesten] fra et ciffer lager på din lokale computer. App Service understøtter denne metode med indstillingen **Lokale ciffer** installation i [Azure-portalen].  
Mange af de ciffer kommandoer, der er beskrevet i denne artikel udføres automatisk, når du opretter en App Service-app ved hjælp af [Azure kommandolinjen] som beskrevet [her](app-service-web-get-started.md).

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du:

- Ciffer. Du kan hente den installation binære [her](http://www.git-scm.com/downloads).  
- Grundlæggende kendskab til ciffer.
- En Microsoft Azure-konto. Hvis du ikke har en konto, kan du [tilmelde dig en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial) eller [aktivere dine Visual Studio abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.  

## <a name="Step1"></a>Trin 1: Oprette et lokalt lager

Du kan udføre følgende opgaver for at oprette et nyt ciffer lager.

1. Start et værktøj på kommandolinjen, som **GitBash** (Windows) eller **Bash** (Unix Shell). På OS X-systemer kan du åbne kommandolinjen via programmet **Terminal** .

2. Gå til den mappe, hvor indholdet til at udrulle vil være placeret.

3. Brug følgende kommando initialiseret en ny ciffer lager:

        git init

## <a name="Step2"></a>Trin 2: Bekræfte dit indhold

App Service understøtter programmer, der er oprettet i en række programmeringssprog. 

1. Hvis din lager allerede indeholder indhold Spring dette peg og flytte til at pege 2 nedenfor. Hvis din lager ikke allerede indeholder indhold blot udfylde som følger med en statisk .html-fil: 

    - Med et tekstredigeringsprogram kan du oprette en ny fil med navnet **index.html** i roden af ciffer lager
    - Tilføj følgende tekst, som oplysningerne for index.html fil og Gem den: *Hej ciffer!*
        
2. Kontrollér, at du er i roden af din ciffer lager fra kommandolinjen. Brug derefter følgende kommando for at føje filer til din lager:

        git add -A 

4. Næste, foretag ændringerne i lageret ved hjælp af følgende kommando:

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Trin 3: Aktivere App Service app lager

Udfør følgende trin for at aktivere et ciffer lager til din App Service-app.

1. Log på [Azure-portalen].

2. Klik på i din App Service app blade **Indstillinger > installation kilde**. Klik på **Vælg datakilde**, og derefter klikke på **Lokale ciffer lager**, og klik derefter på **OK**.  

    ![Lokale ciffer lager](./media/app-service-deploy-local-git/local_git_selection.png)

3. Hvis dette er første gang konfigurerer et lager i Azure, skal du oprette logonoplysninger til den. Du kan bruge dem til at logge på Azure lager og opslagsnål ændringerne fra din lokale ciffer lager. Klik på fra din app blade **Indstillinger > installation legitimationsoplysninger**, skal du konfigurere din installation brugernavn og din adgangskode. Klik på **Gem**, når du er færdig.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Trin 4: Installere dit projekt

Brug følgende trin til at publicere din app til App-tjenesten ved hjælp af lokale ciffer.

1. Klik på i din app blade i portalen Azure, **Indstillinger > egenskaber** **Ciffer URL-adressen**til.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Ciffer URL-adressen** er remote referencen skal installeres til fra din lokale lager. Du skal bruge denne URL-adresse i følgende trin.

2. Brug af kommandolinjen, Kontrollér, at du er i roden af din lokale ciffer lager.

3. Brug `git remote` tilføje remote referencen vises i **Ciffer URL-adressen** fra trin 1. Din kommando vil se ud som følger:

        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] Kommandoen **remote** føjer en navngivet reference til en ekstern lager. I dette eksempel oprettes en reference, navnet 'azure' for din online-lager.

4. Push dit indhold til App-tjenesten ved hjælp af den nye **azure** remote du lige har oprettet.

        git push azure master

    Du bliver bedt om for den adgangskode, du oprettede tidligere, når du nulstiller din installation legitimationsoplysninger i portalen Azure. Skriv adgangskoden (Bemærk, at Gitbash ikke ekko stjerner til konsollen, mens du skriver adgangskoden). 
       
5. Gå tilbage til din app i portalen Azure. Post i en logfil over dine seneste opslagsnål skal vises i bladet **installationer** . 

    ![](./media/app-service-deploy-local-git/deployment_history.png)

6. Klik på knappen **Gennemse** øverst på den app blade til at kontrollere indholdet er blevet installeret. 
    
## <a name="Step5"></a>Fejlfinding i forbindelse med

Følgende er fejl eller problemer, der ofte er stødt på når du bruger ciffer publicere til en App Service-app i Azure:

****

**Symptom**: det er ikke muligt at få adgang til '[siteURL]': kunne oprette forbindelse til [scmAddress]

**Årsag**: Denne fejl kan opstå, hvis app ikke er oppe at køre.

**Opløsning**: starte appen i portalen Azure. Ciffer installation fungerer ikke, medmindre appen kører. 


****

**Symptom**: ikke kunne løse host 'hostname'

**Årsag**: Denne fejl kan opstå, hvis de adresseoplysninger, der er angivet, når du opretter azure remote var forkerte.

**Løsning**: Brug den `git remote -v` kommandoen til at få vist alle fjernbetjeninger sammen med den tilknyttede URL-adresse. Bekræft, at URL-adressen til den 'azure' remote er korrekte. Hvis det er nødvendigt, fjerne og gendanne denne remote ved hjælp af den korrekte URL-adresse.

****

**Symptom**: ingen referencer i fælles og ingen angivet; undlade at gøre noget. Måske skal du angive en gren som 'master'.

**Årsag**: Denne fejl kan opstå, hvis du ikke angiver en gren, når udføre en ciffer push handlingen, og har ikke indstiller værdien push.default bruges af ciffer.

**Opløsning**: udføre push-handlingen igen, der angiver den overordnede gren. Eksempel:

    git push azure master

****

**Symptom**: src refspec [branchname] svarer ikke til nogen.

**Årsag**: Denne fejl kan opstå, hvis du forsøger at overføre til en gren end master på azure remote.

**Opløsning**: udføre push-handlingen igen, der angiver den overordnede gren. Eksempel:

    git push azure master

****

**Symptom**: fejl - ændringer, der er anvendt til remote lager, men din online, ikke opdateret.

**Årsag**: Denne fejl kan opstå, hvis du installerer en Node.js-app, der indeholder en package.json-fil, der angiver yderligere nødvendige moduler.

**Opløsning**: flere meddelelser, der indeholder "npm fejl!" skal være logget forud for denne fejl, og kan angive yderligere kontekst ved fejl. Følgende er blevet konstateret årsager til denne fejl og de tilsvarende 'npm fejl!" meddelelse:

* **Forkert udformet package.json fil**: npm fejl! Kunne ikke læse afhængigheder.

* **Oprindelig modul, der ikke har en binær fordeling til Windows**:

    * npm fejl! \`cmd "/ c" "node gyp Genopbyg"\` mislykkedes med 1

        ELLER

    * npm fejl! [modulename@version]forudinstallere: \`foretage || gmake\`


## <a name="additional-resources"></a>Yderligere ressourcer

* [Ciffer dokumentation](http://git-scm.com/documentation)
* [Project Kudu dokumentation](https://github.com/projectkudu/kudu/wiki)
* [Uafbrudt installation til Azure App-tjenesten](app-service-continuous-deployment.md)
* [Sådan bruger du PowerShell til Azure](../powershell-install-configure.md)
* [Hvordan du kan bruge kommandolinjen Azure](../xplat-cli-install.md)

[Azure App Service]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure-portalen]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure kommandolinjen]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
