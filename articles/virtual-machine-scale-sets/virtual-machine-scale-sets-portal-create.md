<properties
    pageTitle="Oprette et virtuelt skala angive ved hjælp af portalen Azure | Microsoft Azure"
    description="Installer skala sæt ved hjælp af Azure-portalen."
    keywords="virtuelt skala sæt" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Oprette et virtuelt skala angive ved hjælp af portalen Azure

Dette selvstudium viser, hvor nemt det er at oprette et virtuelt skala angive i et par minutter, ved hjælp af portalen Azure. Hvis du ikke har et Azure-abonnement, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) , inden du går i gang.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Vælg VM billedet fra marketplace

Fra portalen, kan du let at implementere en skala, der er konfigureret med CentOS, CoreOS, Debian, Åbn Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu Server eller Windows Server billeder.

Gå først til [Azure-portalen](https://portal.azure.com) i en webbrowser. Klik på `New`, søge efter `scale set`, og vælg derefter den `Virtual machine scale set` post:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Oprette virtuelt Linux

Nu kan du bruge standardindstillingerne og hurtigt at oprette den virtuelle maskine.

* På den `Basics` blade, Angiv et navn til sættet skala. Dette navn bliver til bunden af det fuldstændige Domænenavn for justering af belastning foran sættet skala, så skal du kontrollere, at navnet er entydigt på tværs af alle Azure.

* Vælg dit ønskede OS Skriv, Angiv dit ønskede brugernavn, og vælg, hvilke godkendelse Skriv du foretrækker. Hvis du vælger en adgangskode, den skal være mindst 12 tegn lang og opfylder tre ud af fire følgende kompleksitet krav: en lille bogstav, et stort bogstav, et tal og ét specialtegn. Se mere om [kravene til brugernavn og din adgangskode](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Hvis du vælger `SSH public key`, være sikker på, at kun sæt ind i din offentlige nøgle, ikke din private nøgle:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Angiv dit ønskede ressource gruppenavn og placering, og klik derefter på `OK`.

* På den `Virtual machine scale set service settings` blade: Angiv ønskede domæne navn etiketten (på basis af det fuldstændige Domænenavn for justering af belastning foran sættet skala). Denne etiket skal være entydig på tværs af alle Azure.

* Vælg dit operativsystem disk billede, forekomst Tæl og maskine størrelse.

* Aktivere eller deaktivere Autoskalering og konfigurere, hvis det er aktiveret:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* På den `Summary` blade, når validering er færdig, skal du klikke på `OK`.

* Til sidst på den `Purchase` blade, skal du klikke på `Purchase` til at starte skalaen angive installation.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Oprette forbindelse til en VM i sættet skala

Når dit skala sæt er installeret, kan du gå til den `Inbound NAT Rules` fanen for justering af belastning for sættet skala:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Du kan oprette forbindelse til hver VM i skalaen sæt med disse NAT regler. For eksempel et Windows skala sæt, hvis der er en NAT regel på indgående port 50000, du kunne oprette forbindelse til denne maskine via RDP på `<load-balancer-ip-address>:50000`. Et Linux skala sæt, skal du oprette forbindelse ved hjælp af kommandoen `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Næste trin

Du kan finde dokumentation om, hvordan du installerer skala sæt fra CLI [denne dokumentation](./virtual-machine-scale-sets-cli-quick-create.md).

Du kan finde dokumentation om, hvordan du installerer skala sæt fra PowerShell [denne dokumentation](./virtual-machine-scale-sets-windows-create.md).

Du kan finde dokumentation om, hvordan du installerer skala sæt fra Visual Studio, [denne dokumentation](./virtual-machine-scale-sets-vs-create.md).

Se den [dokumentation oversigtssiden for skalaen angiver](./virtual-machine-scale-sets-overview.md)generelle dokumentation.

Generelle oplysninger, skal du se den [primære landingssiden for skala sæt](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

