<properties
    pageTitle="Sådan overføres fra en RemoteApp VNET til en Azure VNET | Microsoft Azure"
    description="Lær, hvordan du overfører fra en RemoteApp VNET til en Azure VNET"
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



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Sådan overføres en hybrid af websteder fra en RemoteApp VNET til en Azure VNET

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Gode nyheder! Vi har aktiveret du kan installere hybrid RemoteApp samlinger direkte i dine eksisterende Azure virtuelle netværk (VNETs) i stedet for at oprette RemoteApp-specifikke VNETs. Her kan du drage fordel af de nyeste VNET funktioner (såsom ExpressRoute) og give dine hybrid samlinger direkte netværksadgang til andre Azure tjenester og virtuelle maskiner, der er installeret på VNET.  (Dette får du bedre ydeevne og nemmere installation end VNET-VNET konfigurationer).


Lad os sige, at du allerede har oprettet en hybrid RemoteApp samling kaldet *OriginalCollection* med et RemoteApp VNET kaldet *RemoteAppVNET*. Her er trinnene til at overføre den til en ny Azure VNET kaldet *AzureVNET*.

1.  Oprette en VNET kaldet *AzureVNET*, bruger den samme placering, DNS-konfiguration og adresseområde (for mindst én af *AzureVNET* undernet) som dig bruges til *RemoteAppVNET*under fanen **netværk** i [management portal](http://manage.windowsazure.com/).
2.  Konfigurere *AzureVNET* enten vært eller har forbindelse til den Active Directory-installation, *OriginalCollection* er domæne, der er tilsluttet netværket.
3.  Oprette en ny RemoteApp samling kaldet *Ny samling*under fanen **RemoteApps** . (Brug indstillingen **Opret med VNET** , ikke **Hurtig oprettelse**).
3.  Konfigurere *NewCollection* skal installeres til et undernet i *AzureVNET*.
4.  Konfigurere *NewCollection* for at bruge det samme billede og Deltag i domæneoplysninger som du brugte til *OriginalCollection*.
5.  Efter et par timer vises *NewCollection* på listen af websteder med tilstanden aktiv.

Nu, hvis du ikke behøver at overføre en hvilken som helst brugeroplysninger fra den oprindelige af websteder til den nye websteder, gøre disse trin:

6.  Slette *OriginalCollection*.
7.  Slette *RemoteAppVNET*.

Og du er færdig!

Alternativt, hvis du har brug at overføre brugeroplysninger fra den oprindelige af websteder til den nye websteder, gøre disse trin:

6.  Sende en mail til [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) med dit Azure abonnement-ID, navnet på den oprindelige af websteder og navnet på den nye samling, og bed dem om at overføre dine brugeroplysninger.
7.  Inden for 2 arbejdsdage flytter RemoteApp teamet adgangslisten bruger og alle brugerdokumenter, der og brugerindstillinger fra samlingen oprindelige til den nye websteder.
8.  Slette *OriginalCollection*.
9.  Slette *RemoteAppVNET*.

Og nu er du færdig!

Hvis du har spørgsmål eller har brug for hjælp til særlige, bedes du sende [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
