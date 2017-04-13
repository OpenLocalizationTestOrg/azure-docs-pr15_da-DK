<properties
    pageTitle="Kontinuert integration i VS Team Services ved hjælp af Azure ressourcegruppe projekter | Microsoft Azure"
    description="Beskriver, hvordan du konfigurerer fortløbende integration i Visual Studio Team Services ved hjælp af Azure ressourcegruppe installation projekter i Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Kontinuert integration i Visual Studio Team Services ved hjælp af Azure ressourcegruppe installation projekter

Du skal installere en Azure skabelon, du skal udføre opgaver for at gå gennem de forskellige faser: Build, Test, Kopiér til Azure (også kaldet "Midlertidige"), og installere skabelon.  Der findes to forskellige måder at gøre dette i Visual Studio Team Services (VS Team Services). Begge metoder giver de samme resultater, så Vælg det, der passer bedst tilnærmer arbejdsprocessen.

-   Tilføje en enkelt trin i definitionen for din build, der kører på PowerShell-script, der er inkluderet i Azure ressourcegruppe installation projektet (Implementer-AzureResourceGroup.ps1). Scriptet kopierer elementer og derefter installerer skabelonen.
-   Tilføje flere VS Team Services opbygge vejledning, skal hver enkelt udføre en fase opgave.

Denne artikel beskrives, hvordan du bruger den første indstilling (Brug en build definition til at køre PowerShell-scriptet). En af fordelene ved denne indstilling er, at script, der bruges af udviklere i Visual Studio er det samme script, der bruges af VS Team Services. Denne procedure antages det, du allerede har en Visual Studio installation projektet være tjekket ind eller-Team Services.

## <a name="copy-artifacts-to-azure"></a>Kopiere elementer til Azure 

Hvis du har en hvilken som helst elementer, der skal bruges til installation af skabelonen, uanset dette scenario skal du give Azure ressourcestyring adgang til dem. Disse elementer kan indeholde filer f.eks.:

-   Indlejrede skabeloner
-   Konfigurationsscripts til og DTK
-   Programmet binære filer

### <a name="nested-templates-and-configuration-scripts"></a>Indlejrede skabeloner og konfigurationsscripts
Når du bruger de skabeloner, der leveres af Visual Studio (eller oprettet med Visual Studio kodestykker), PowerShell-script faser ikke kun elementer, det også parameterizes URI for ressourcerne i forskellige installationer. Scriptet og derefter kopierer elementer til en sikker objektbeholder i Azure, opretter et SaS token for objektbeholderen, og overfører derefter disse oplysninger til skabelon installationen. Se [oprette en skabelon installation](https://msdn.microsoft.com/library/azure/dn790564.aspx) til at få mere at vide om indlejrede skabeloner.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Konfigurere fortløbende installation i VS Team Services

Hvis du vil ringe PowerShell-script i VS Team Services, skal du opdatere definitionen for din build. Kort sagt er trinnene: 

1.  Redigere definitionen for build.
1.  Konfigurere Azure godkendelse i VS Team Services.
1.  Tilføje et Azure PowerShell build trin, der refererer til PowerShell-script i Azure ressourcegruppe installation projektet.
1.  Angive værdien af parameteren *- ArtifactsStagingDirectory* til at arbejde med et projekt, der er indbygget i VS Team Services.

### <a name="detailed-walkthrough"></a>Detaljeret gennemgang

Følgende trin fører dig gennem de trin, der er nødvendige for at konfigurere fortløbende installation i VS Team Services 

1.  Redigere definitionen for din VS Team Services build og tilføje en Azure PowerShell build trin. Vælg Opret definitionen under kategorien **opbygge definitioner** , og vælg derefter **Rediger** link.

    ![][0]

1.  Tilføje et nyt **Azure PowerShell** build trin til build definitionen og vælg derefter **Tilføj opbygge trin...** knappen.

    ![][1]

1.  Vælg kategorien **Implementer opgave** , Vælg opgaven, **Azure PowerShell** , og vælg derefter knappen **Tilføj** .

    ![][2]

1.  Vælg **Azure PowerShell** build trin, og udfyld derefter dens værdier.

    1.  Hvis du allerede har føjet til VS Team Services Azure service ark, Vælg abonnementet, i **Azure abonnement** rullelisten og derefter gå videre til næste afsnit. 

        Hvis du ikke har et Azure service slutpunkt i VS Team Services, skal du tilføje en. Dette afsnit fører dig gennem processen. Hvis kontoen Azure bruger en Microsoft-konto (såsom Hotmail), skal du udføre følgende trin for at få en tjeneste hovedstolen godkendelse.

    1.  Vælg linket **Administrer** ud for **Azure abonnement** rulleliste.

        ![][3]

    1. Vælg **Azure** i **Nye tjenesteslutpunkt** rullelisten.

        ![][4]

    1.  Vælg indstillingen **Service hovedstolen** i dialogboksen **Tilføj Azure-abonnement** .

        ![][5]

    1.  Tilføj dine Azure abonnementsoplysninger til dialogboksen **Tilføj Azure-abonnement** . Skal du angive følgende elementer:
        -   Abonnement-Id
        -   Navn på abonnement
        -   Tjenesten hovedstolen-Id
        -   Tjenesten hovedstolen nøgle
        -   Lejer-Id

    1.  Føje et navn på dit valg til feltet Navn på **abonnement** . Denne værdi vises senere i **Azure abonnement** rullelisten i VS Team Services. 

    1.  Hvis du ikke kender dit Azure abonnement-ID, kan du bruge en af følgende kommandoer til at få den.
        
        Bruge PowerShell-scripts:

        `Get-AzureRmSubscription`

        Bruge Azure CLI:

        `azure account show`
    

    1.  For at få en tjeneste hovedstolen-ID, Service hovedstolen nøgle og lejer-ID, ved at følge fremgangsmåden i [portalen til oprette Active Directory-program og service hovedstolen](resource-group-create-service-principal-portal.md) eller [godkendelse af en tjeneste sikkerhedskonto med Azure ressourcestyring](resource-group-authenticate-service-principal.md).

    1.  Addere værdierne Service hovedstolen-ID, Service hovedstolen nøgle og lejer-ID til dialogboksen **Tilføj Azure-abonnement** , og vælg knappen **OK** .

        Du har nu en gyldig Service sikkerhedskonto bruge til at køre scriptet Azure PowerShell.

1.  Redigere definitionen for build, og vælg trinnet **Azure PowerShell** build. Vælg abonnementet i **Azure abonnement** rullelisten. (Hvis abonnementet ikke vises, Vælg knappen **Opdater** i næste linket **Administrer** .) 

    ![][8]

1.  Angiv en sti til Implementer AzureResourceGroup.ps1 PowerShell-script. Vælg knappen ellipsen (...) ud for feltet **Scriptsti** for at gøre dette, gå til Implementer AzureResourceGroup.ps1 PowerShell-script i mappen **Scripts** på dit projekt, markere den, og vælg knappen **OK** . 

    ![][9]

1. Når du har valgt scriptet, skal du opdatere stien til scriptet, så den kører fra Build.StagingDirectory (den samme mappe, der *ArtifactsLocation* er indstillet til). Du kan gøre dette ved at tilføje "$(Build.StagingDirectory)/"til begyndelsen af script stien.

    ![][10]

1.  Angiv følgende parametre (i en enkelt linje) i feltet **Scriptargumenter** . Når du kører scriptet i Visual Studio, kan du se, hvordan VS bruger parametrene i vinduet **Output** . Du kan bruge det som udgangspunkt for at indstille parameterværdierne i din build trin.

  	| Parameter | Beskrivelse|
  	|---|---|
  	| -ResourceGroupLocation           | Hvor ressourcegruppen er placeret, som **eastus** eller **' af US '**værdien geografisk placering. (Tilføj enkelte anførselstegn, hvis der er et mellemrum i navnet.) Du kan få flere oplysninger i [Azure områder](https://azure.microsoft.com/en-us/regions/) .|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | Navnet på den ressourcegruppe, der bruges til denne installation.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Denne parameter, angiver når der forekommer, at elementer skal overføres til Azure fra det lokale system. Du skal kun angive denne parameter, hvis din installation af skabelon kræver ekstra elementer, du vil fase ved hjælp af PowerShell-script (såsom konfigurationsscripts eller indlejrede skabeloner).                                                                                                                                                                 |
  	| -StorageAccountName              | Navnet på lagerplads-konto, der bruges til at fase elementer til denne installation. Denne parameter er påkrævet, kun, hvis du kopierer elementer til Azure. Denne lagerplads konto oprettes ikke der automatisk i forbindelse med installationen, den skal allerede findes.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | Navnet på den ressourcegruppe, der er knyttet til kontoen lagerplads. Denne parameter er påkrævet, kun, hvis du kopierer elementer til Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | Stien til skabelonfilen i Azure ressourcegruppe installation projektet. For at forbedre fleksibilitet, kan du bruge en sti til denne parameter, der er i forhold til placeringen af PowerShell-script i stedet for en absolut sti.|
  	| -TemplateParametersFile          | Stien til parameterfilen i Azure ressourcegruppe installation projektet. For at forbedre fleksibilitet, kan du bruge en sti til denne parameter, der er i forhold til placeringen af PowerShell-script i stedet for en absolut sti.|
  	| -ArtifactStagingDirectory        | Denne parameter kan PowerShell script kender mappen fra hvor binære filer i projektets skal kopieres. Denne værdi tilsidesætter standardværdien bruges af PowerShell-script. For VS Team Services bruger, skal du angive værdien: - ArtifactStagingDirectory $(Build.StagingDirectory)                                                                                                                                                                                              |

    Her er et script argumenter eksempel (linje ødelagt for læsbarhed):

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Feltet **Scriptargumenter** skal ligne følgende, når du er færdig.

    ![][11]

1.  Når du har tilføjet alle de nødvendige elementer til Azure PowerShell opbygge trin, kan du vælge **kø** opbygge knappen til at oprette projektet. Skærmbilledet **opbygge** viser output fra PowerShell-script.

## <a name="next-steps"></a>Næste trin

Læs [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md) for at få flere oplysninger om Azure ressourcestyring og Azure ressourcegrupper.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
