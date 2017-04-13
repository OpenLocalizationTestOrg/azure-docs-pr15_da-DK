
<properties
    pageTitle="Bruge Office med Azure RemoteApp | Microsoft Azure" 
    description="Lære, hvordan Office og Azure RemoteApp fungerer sammen"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-office-with-azure-remoteapp"></a>Bruge Office med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Har du to valgmuligheder for vært for Office-programmer i Azure RemoteApp: Office 365 ProPlus eller Office 2013 Professional Plus prøveabonnement.

**Hey, vidste du, vi har et nyt, bedre artikel, der skal snart erstatte dette? Se, [hvordan du kan bruge din Office 365-abonnement med Azure RemoteApp](remoteapp-officesubscription.md). Det dækker alle de oplysninger, du skal bruge til brug af Office 365 + Azure RemoteApp.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
Du kan oprette en RemoteApp af websteder ved hjælp af Office 365 ProPlus skabelon billedet. Denne indstilling giver dig mulighed at udvide din Office 365-tjenesten til RemoteApp. Du skal have et eksisterende abonnementsplan og dine brugere skal have licens til Office 365 ProPlus-tjenesten, enten separat eller via Office 365 vedligeholde planer.

RemoteApp understøtter Office 365 delt computeraktivering. Når du aktiverer delt computeraktivering og bruge [Office udrulningsværktøj](http://www.microsoft.com/download/details.aspx?id=36778) til installation, installeres Office 365 ProPlus uden at være aktiveret. Når en bruger underskriver til en samling, der indeholder Office 365, kontrollerer Office, hvis brugeren har klargjort til Office 365 ProPlus. Hvis så Office midlertidigt aktiveres Office 365 ProPlus - fortsætter denne aktivering indtil fortegn, brugere af tjenesten.

Hvis du vil bruge Office 365 delt computeraktivering, skal du oprette en [brugerdefineret skabelon](remoteapp-create-custom-image.md) og installere Office 365 ProPlus der skal følge [disse instruktioner](https://technet.microsoft.com/library/dn782858.aspx).

Du kan administrere dine brugeres Office 365-licenser i [Office 365-administrationsportalen](https://portal.office365.com/). Læs mere at vide om [Office 365-tjenesten planer](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Prøveversion af Office 2013 Professional Plus
Under en 30-dages prøveversion af RemoteApp, kan du bruge Office 2013 Professional Plus (prøveversion) skabelon billedet til at oprette en RemoteApp af websteder. Du kan tildele brugere til denne prøveversion af websteder ved hjælp af deres Azure Active Directory arbejde firma eller en Microsoft-konti. Ingen yderligere abonnement er påkrævet.

Dette er en god mulighed for at starte til dækkene og få en god følelse til Office i RemoteApp. Denne indstilling er dog tilsigtede til evaluering og test kun. RemoteApp samlinger, der er oprettet ved hjælp af Office 2013 Professional Plus (prøveversion) skabelon billedet ikke være transitioned til fremstilling tilstand og deaktiveres i slutningen af prøveperioden.

## <a name="switching-from-trial-to-production"></a>Skifte fra prøveversion til fremstilling
Når du starter din 30-dages gratis prøveversion, fortæller en note i afsnittet RemoteApp i portalen dig, hvor længe du har tilbage i prøveversionen, før du har brug for til at skifte til et betalt konto. Du kan aktivere din konto, og Skift til fremstilling tilstand ved hjælp af linket i denne note.

Når du aktiverer din konto, kan det påvirke alle RemoteApp af websteder på din konto.

- Samlinger, der kører med Windows Server 2012 R2 eller Office 365 ProPlus skabelonbillederne vil problemfri overgang til fremstilling. Alle brugerdata og indstillinger, herunder igangværende sessioner, forbliver intakt.
- Hvis du har overført brugerdefineret skabelon billeder, overdragelse af websteder ved hjælp af disse billeder også problemfrit.
- Office 2013 Professional Plus (prøveversion) skabelon billedet er beregnet til kun evaluering. Samlinger, der kører med denne skabelonbillede kan ikke transitioned til fremstilling. De vil blive lagt i "deaktiveret" tilstand.


Hvis du ikke overgang til fremstilling tilstand ved udløbet af prøveperioden, deaktiveres dit RemoteApp af websteder. Bare rolig – dine indstillinger og brugernes data gemmes til en anden 90 dage, så du kan stadig aktivere din tjeneste, og Skift til fremstilling tilstand uden at miste data.
