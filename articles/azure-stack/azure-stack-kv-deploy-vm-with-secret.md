<properties
    pageTitle="Installere en VM ved hjælp af en adgangskode, der er gemt i Azure stak nøgle samling | Microsoft Azure"
    description="Lær, hvordan du installerer en VM ved hjælp af en adgangskode, der er gemt i Azure stak nøgle samling"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Installere en VM ved at hente den adgangskode, der er gemt i nøgle samling

Når du vil overføre en sikker værdi (som en adgangskode) som en parameter under installationen, kan du gemme den pågældende værdi som et hemmeligt i en Azure stak vigtige samling og henviser til værdien i andre Azure ressourcestyring skabeloner. Du kan medtage kun en reference til hemmeligheden i skabelonen så hemmeligheden er aldrig vises. Du behøver ikke at manuelt at angive værdien for hemmeligheden, hver gang du installerer ressourcerne. Du angiver, hvilke brugere eller principper for tjenesten kan få adgang til hemmeligheden.

## <a name="reference-a-secret-with-static-id"></a>Henvise til et hemmeligt med statisk-ID

Du referere til hemmeligt fra i en fil, parametre, som overfører værdier til din skabelon. Du referere til Hemmeligheden bag ved at overføre ressource-id'et for den vigtige samling af legitimationsoplysninger og navnet på hemmeligt. Hemmeligheden bag vigtige samling skal allerede findes i dette eksempel. Du bruger en statisk værdi til dens ressource-ID.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]Den parameter, der accepterer hemmeligheden skal være en *securestring*.

## <a name="next-steps"></a>Næste trin
[Installere en eksempel-app med-tasten samling](azure-stack-kv-sample-app.md)

[Installere en VM med en nøgle samling certifikat](azure-stack-kv-push-secret-into-vm.md)

