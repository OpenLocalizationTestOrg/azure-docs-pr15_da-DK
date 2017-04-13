<properties 
    pageTitle="Installere QuickBooks i Azure RemoteApp | Microsoft Azure" 
    description="Lær at dele QuickBooks med Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Hvordan kan du installere QuickBooks i Azure RemoteApp?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Brug følgende oplysninger til at dele QuickBooks som en app i Azure RemoteApp.


Du kan dele QuickBooks 2015 Enterprise med Azure RemoteApp i en hybrid eller skyen af websteder. Virksomhed-filen skal placeres på en VM, der kører QuickBooks-databaseserver, der er adskilt fra Azure RemoteApp-servere. Gemme aldrig filen virksomhed på Azure RemoteApp billedet – der forventes tab af data, hvis du gør dette. Kun QuickBooks Enterprise understøtter vært QuickBooks-fil på en ekstern del med QuickBooks databaseserver, der er tilgængelige via standard Windows-netværk.   

> [AZURE.IMPORTANT] Den QuickBooks-databaseserver, der er vært for virksomhed filen skal placeres på en separat VM i den samme VNET som samlingen Azure RemoteApp.  

## <a name="steps-to-deploy-quickbooks"></a>Trin til at udrulle QuickBooks

1. Opret en Azure VM og installere QuickBooks, QuickBooks-databaseserver, og Placer virksomhed filen på en Azure VM.  Sørg for at konfigurere firewallregler korrekt.
2. Installere QuickBooks på et [brugerdefineret billede](remoteapp-imageoptions.md) , og Opret en [Azure RemoteApp af websteder](remoteapp-collections.md), enten skyen eller hybrid inden for den nøjagtige samme VNET, hvor VM vært databaseserveren QuickBooks med virksomhedens filer er placeret. 
3.  [Publicere](remoteapp-publish.md) QuickBooks app til brugere
4.  Start af Azure RemoteApp-hostet QuickBooks-klienten, navigere ved hjælp af standard Windows-netværk til VM vært QuickBooks-databaseserver, og Åbn filen virksomhed. 

## <a name="documentation-references"></a>Dokumentation referencer

- QuickBooks [Understøttede konfigurationer](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- QuickBooks [installationsindstillinger](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Du kan også se min Ignite præsentationen, [grundlæggende af Microsoft Azure RemoteApp styring og Administration](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - hurtig frem til 1:02:45 for at få adgang til delen QuickBooks.

## <a name="deployment-architecture"></a>Installationsarkitektur

![QuickBooks + Azure RemoteApp installation](./media/remoteapp-quickbooks/ra-quickbooks.png)