<properties 
    pageTitle="Låse ressourcer med ressourcestyring | Microsoft Azure" 
    description="Forhindre brugere i at opdatering eller sletning af bestemte ressourcer ved at anvende en begrænsning på alle brugere og roller." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Låse ressourcer med Azure ressourcestyring

Som administrator skal du låse et abonnement, ressourcegruppe eller ressource for at forhindre andre brugere i organisationen fra kommer til at slette eller ændre kritiske ressourcer. Du kan angive niveauet Lås til **CanNotDelete** eller **skrivebeskyttet**. 

- **CanNotDelete** betyder autoriserede brugere kan læse og redigere en ressource stadig, men de kan ikke slette den. 
- **Skrivebeskyttet** betyder autoriserede brugere kan læse fra en ressource, men de kan ikke slette den eller udføre handlinger på den. Tilladelsen for ressourcen er begrænset til rollen **læser** . 

Anvende **skrivebeskyttet** kan medføre uventede resultater, da nogle handlinger, som ser ud som læst operationer faktisk kræver brug af flere handlinger. For eksempel forhindrer placere en **skrivebeskyttet** Lås på en lagerplads konto alle brugere i listen over tasterne. Listen nøgler håndteres via en POST-anmodning, fordi de returnerede taster er tilgængelige for skrive handlinger. Placere en **skrivebeskyttet** Lås på en App Service ressource forhindrer til et andet eksempel Visual Studio Server Explorer i viser filer til ressourcen, fordi interaktionen kræver skrive-adgang.

I modsætning til rollebaseret adgangskontrol, kan du bruge management låse til at anvende en begrænsning på tværs af alle brugere og roller. Se [Azure rollebaseret adgangskontrol](./active-directory/role-based-access-control-configure.md)for at få mere for at vide om at angive tilladelser for brugere og roller.

Når du anvender en lås på et overordnet omfang, nedarver alle underordnede ressourcer samme låsen. Lige ressourcer, du tilføjer senere nedarver låsen fra overordnet. Mest restriktiv låsen i nedarvningen har højere prioritet.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Hvem kan oprette eller slette låse i din organisation

Hvis du vil oprette eller slette management låse, skal du have adgang til **Microsoft.Authorization/\* ** eller **Microsoft.Authorization/locks/\* ** handlinger. Af de indbyggede roller tildeles kun **ejeren** og **Access Brugeradministrator** disse handlinger.

## <a name="creating-a-lock-through-the-portal"></a>Oprette en lås via portalen

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Oprette en lås i en skabelon

I følgende eksempel viser en skabelon, der opretter en lås på en lagerplads konto. Kontoen lagerplads, du vil anvende låsen er angivet en parameter. Navnet på låsen oprettes ved at sammenkæde ressourcenavnet med **/Microsoft.Authorization/** og navnet på låsen, i dette tilfælde **myLock**.

Den type, der leveres er specifikke for ressourcetypen. Angiv typen til "Microsoft.Storage/storageaccounts/providers/locks" til lagring.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Oprette en lås med REST-API

Du kan låse udløst ressourcer med [REST-API til administration af låse](https://msdn.microsoft.com/library/azure/mt204563.aspx). REST-API gør det muligt at oprette og slette låse og hente oplysninger om eksisterende låse.

Hvis du vil oprette en lås, skal du køre:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Omfanget kan være et abonnement, ressourcegruppe eller ressource. Lås-navnet er, hvad du vil ringe låsen. Brug **2015-01-01**-api-version.

Medtage et JSON-objekt, der angiver egenskaber for låsen i anmodningen.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Du kan finde eksempler, [REST-API til administration af låse](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Oprette en lås med Azure PowerShell

Du kan låse udløst ressourcer med Azure PowerShell ved hjælp af **Ny AzureRmResourceLock** , som vist i følgende eksempel.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell indeholder andre kommandoer til at arbejde låse, som **Sæt AzureRmResourceLock** til at opdatere en lås og **Fjern AzureRmResourceLock** til at slette en lås.

## <a name="next-steps"></a>Næste trin

- Du kan finde flere oplysninger om at arbejde med ressourcelåse se [Lås ned dine Azure ressourcer](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- For at få mere for at vide om at organisere dine ressourcer logisk, se [Brug af mærker til at organisere dine ressourcer](resource-group-using-tags.md)
- For at ændre hvilke ressourcegruppe, en ressource er placeret i skal du se [flytte ressourcer til ny ressourcegruppe](resource-group-move-resources.md)
- Du kan anvende begrænsninger og konventioner på tværs af dit abonnement med tilpassede politikker. Se [Politik om brug af til at administrere ressourcer og kontrollere adgangen](resource-manager-policy.md)kan finde flere oplysninger.
