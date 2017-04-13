<properties
    pageTitle="Oprette forbindelse til Azure stak med CLI | Microsoft Azure"
    description="Lær at bruge kommandolinjen på tværs af platforme (CLI) til at administrere og installere ressourcer på Azure stak"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Installere og konfigurere Azure stak CLI

I dette dokument fører vi dig gennem processen med at bruge Azure kommandolinjen (CLI) til at administrere Azure stak ressourcer på Linux og Mac-klient-platforme.  

## <a name="install-azure-stack-cli"></a>Installer Azure stak CLI

Hvis du er på Mac eller Linux, kan du få CLI ved hjælp af følgende kommando:
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Oprette forbindelse til Azure stak
I de følgende trin, kan du konfigurere Azure CLI til at oprette forbindelse til Azure stak. Derefter skal du logge på og hente abonnementsoplysninger.

1.  Hente værdien for aktiv-directory-ressource-id ved at udføre denne PowerShell:
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  Brug følgende CLI kommando til at tilføje Azure stak miljø, og Sørg for at opdatere *– aktiv-directory-ressource-id* med dataene, hentes URL-adresse i det forrige trin:

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Log på ved hjælp af kommandoen følgende (Erstat *brugernavn* med dit brugernavn):

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Hvis du får certifikat valideringsproblemer, deaktiveres certifikat validering ved at køre kommandoen `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Angive konfigurationstilstanden Azure til Azure ressourcestyring ved hjælp af følgende kommando:

        azure config mode arm

5.  Hente en liste over abonnementer.

        azure account list     

## <a name="next-steps"></a>Næste trin

[Installere skabeloner med Azure CLI](azure-stack-deploy-template-command-line.md)

[Oprette forbindelse med PowerShell](azure-stack-connect-powershell.md)

[Administrere brugertilladelser](azure-stack-manage-permissions.md)
