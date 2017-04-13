<properties
    pageTitle="Ordliste til Azure - Azure ordbog | Microsoft Azure"
    description="Brug Azure-Ordliste til at forstå skyen terminologi på Azure-platformen. Denne korte Azure ordbog indeholder definitioner almindelige skyen vilkår til Azure."
    keywords="Azure ordbog skyen terminologi Azure ordliste, terminologi definitioner, og skyen vilkår"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure-ordliste: en ordbog med skyen terminologi på Azure-platformen

Microsoft Azure-ordliste er en kort ordbog med skyen terminologi til Azure-platformen.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Finde tjenestedefinitioner og andre skyen begreber

* Definitioner af Azure tjenester og deres AWS i versionerne [Microsoft Azure og Amazon webtjenester](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Generelle branche finde skyen vilkår [Cloud computing udtryk](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Azure-ordliste med de ovenstående to referencer giver en til slut taksonomi til Azure og skyen branche.  

## <a name="azure-glossary-list"></a>Azure-ordliste liste


### <a name="account"></a>konto  
Arbejde eller skole eller personlig Microsoft-konto, der bruges til at få adgang til og administrere et Azure-abonnement.  
Se også, [hvordan Azure abonnementer er knyttet til Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>tilgængelighed sæt  
En samling af virtuelle maskiner, der administreres sammen til at levere programmet redundans og pålidelighed. Brug af et sæt tilgængelighed sikrer, at under hændelsen planlagt eller ikke-planlagt vedligeholdelse mindst én virtuelt er tilgængeligt.  
Se også [administrere tilgængeligheden af Windows virtuelle maskiner](./virtual-machines/virtual-machines-windows-manage-availability.md) eller [Administrer tilgængeligheden af Linux virtuelle maskiner](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Azure klassisk implementeringsmodel  
En af to [installation modeller](resource-manager-deployment-model.md) bruges til at implementere ressourcer i Azure (den nye model er Azure ressourcestyring). Nogle Azure ressourcer kan installeres i en model eller et andet, mens andre kan installeres i begge modeller. Vejledning til individuelle Azure ressourcer detaljer, som model(s) en ressource kan installeres med.


### <a name="cli"></a>Azure kommandolinjen (CLI)  
En [kommandolinjen](xplat-cli-install.md) , der kan bruges til at administrere Azure-tjenester fra Windows, OSX og Linux pc'er.


### <a name="powershell"></a>Azure PowerShell  
En [kommandolinjen](powershell-install-configure.md) til at administrere Azure tjenester via en kommandolinje fra Windows-computere. Nogle tjenester eller service-funktioner kan administreres kun via PowerShell eller CLI. Vejledning til hver enkelt Azure ressourcedetaljer som model(s) en ressource kan installeres med.   
Se også, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md)


### <a name="arm-model"></a>Azure ressourcestyring implementeringsmodel  
En af to [installation modeller](resource-manager-deployment-model.md) bruges til at implementere ressourcer i Microsoft Azure (den anden er den klassiske implementeringsmodel). Nogle Azure ressourcer kan installeres i en model eller et andet, mens andre kan installeres i begge modeller. Vejledning til individuelle Azure ressourcer detaljer, som model(s) en ressource kan installeres med.


### <a name="fault-domain"></a>et domæne  
Samling af virtuelle maskiner med en tilgængelighed, der muligvis kan mislykkes på samme tid. Et eksempel er en gruppe af computere i et rack, der deler en fælles power kilde- og netværk parameter. I Azure adskilles automatisk virtuelle maskiner i et sæt tilgængelighed på tværs af flere fejl domæner.  
Se også [administrere tilgængeligheden af Windows virtuelle maskiner](./virtual-machines/virtual-machines-windows-manage-availability.md) eller [Administrer tilgængeligheden af Linux virtuelle maskiner](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>geografisk  
Et defineret grænse for data residency, der typisk indeholder to eller flere områder. Grænserne kan være inden for eller ud over nationale grænser og er påvirket af skat bestemmelser. Hver geografisk har mindst ét område. Eksempler på geos er Asien/Stillehavsområdet og Japan. Også kaldet *Geografi*.  
Se også [Azure områder](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>geografisk gentagelse  
Processen til automatisk replikering indhold som blob, tabeller og køer i et internationale par.  
Se også [Aktive geografisk-gentagelse til Azure SQL-Database](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>Billede af  
En fil, der indeholder det operativsystem og programkonfiguration, der kan bruges til at oprette et vilkårligt antal virtuelle maskiner. Der findes to typer billeder i Azure: VM billede og OS billede. Billede af en VM indeholder et operativsystem og alle diske, der er knyttet til en virtuel maskine, når billedet er blevet oprettet. Billede af en OS indeholder kun en generalized operativsystem med ingen data Diskkonfigurationer.  
Se også [Naviger og vælge Windows virtuelt billeder i Azure med PowerShell eller CLI](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>begrænsninger  
Antallet af ressourcer, der kan oprettes eller benchmark-ydeevne, der kan nås. Begrænsninger for er typisk knyttet til abonnementer, tjenester og tilbud.  
Se også [Azure-abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>justering af belastning  
En ressource, der distribuerer indgående trafik mellem computere i et netværk. I Azure distribuerer belastningsjustering trafik til den virtuelle maskiner, der er defineret i et sæt justering af belastning. En [justering af belastning](./load-balancer/load-balancer-overview.md) kan være forbindelse til internettet, eller det kan være interne.  


### <a name="offer"></a>tilbud  
De priser, kredit og relaterede udtryk, der gælder for et Azure-abonnement.  
Se [Azure tilbyder oplysningssiden](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>Portal  
Sikker webportalen bruges til at installere og administrere Azure tjenester.  Der er to portaler: [Azure portal](http://portal.azure.com/) og [Klassisk portal](http://manage.windowsazure.com/). Nogle af tjenesterne er tilgængelige i begge portaler, mens andre er kun tilgængelige i ene eller den anden. [Azure portalen tilgængelighed diagram](https://azure.microsoft.com/features/azure-portal/availability/) viser, hvilke tjenester der er tilgængelige i hvilke portal.  


### <a name="region"></a>område  
Et område i en geografisk, som ikke på tværs af nationale grænser og indeholder en eller flere datacentre. Priser, internationale tjenester og tilbud typer vises på områdeniveauet for. Et område er typisk parvis med et andet område, som kan være op til flere hundrede sømil ikke til stede, til at danne en internationale par. Internationale par kan bruges som en metode til nedbrud og høj tilgængelighed scenarier. Også kaldes Generelt *placering*.  
Se også [Azure områder](best-practices-availability-paired-regions.md)


### <a name="resource"></a>ressource  
Et element, der er en del af din Azure løsning. Hver Azure service gør det muligt at installere forskellige typer ressourcer, som databaser eller virtuelle computere.   
Se også [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>ressourcegruppe  
En objektbeholder i ressourcestyring, der indeholder relaterede ressourcer for et program. Ressourcegruppen kan medtage alle ressourcerne til et program eller kun de ressourcer, der er logisk grupperet sammen. Du kan beslutte, hvordan du vil tildele ressourcer til ressourcegrupper, der er baseret på hvad giver mest mening for din organisation.  
Se også [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Ressourcestyring skabelon  
En JSON-fil, der definerer en eller flere Azure ressourcer med en erklæring og, der definerer afhængigheder mellem de ressourcer, der er installeret. Skabelonen kan bruges til at installere ressourcerne, ensartet måde og flere gange.  
Se også [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>ressource-udbyder  
En tjeneste, der leverer de ressourcer, du kan installere og administrere gennem ressourcestyring. Hver ressource-udbyder tilbyder handlinger til at arbejde med de ressourcer, der er installeret. Udbydere af ressource kan åbnes via portalen Azure, Azure PowerShell og flere programming SDK'er.  
Se også [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>rolle  
En metode til at kontrollere adgangen, der kan være tildelt til brugere, grupper og tjenester. Roller er i stand til at udføre handlinger, sådan som oprette, administrere, og Læs på Azure ressourcer.  
Se også [RBAC: indbyggede roller](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>serviceniveauaftale (SLA)  
Den aftale, der beskriver Microsofts forpligtelser til oppetid og forbindelse til. Hver Azure service har en bestemt SERVICENIVEAUAFTALE.  
Se også [serviceaftaler](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>lagerplads konto  
En lagerplads-konto, som giver dig adgang til Azure Blob, kø, tabel og fil tjenesterne på Azure-lager. Kontoen lagerplads indeholder det entydige navneområde til dataobjekter din Azure-lager.  
Se også [om Azure lagerplads konti](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>abonnement  
En kundes aftale med Microsoft, der gør det muligt for til at hente Azure tjenester. Abonnement priser og relaterede vilkår er underlagt de tilbud, der er valgt til abonnementet. Se [Microsoft Online-abonnement aftale](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Se også, [hvordan Azure abonnementer er knyttet til Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>mærke  
En indeksering ord, der gør det muligt at kategorisere ressourcer efter behov for administration af eller fakturering. Du kan bruge mærker, når du har en kompleks samling af grupper og ressourcer, og du har brug for til visualisering af disse aktiver på den måde, der giver mest mening. Du kan for eksempel mærke ressourcer, udskrive og bruge en lignende rolle i din organisation eller tilhører samme afdeling.  
Se også [bruge mærker til at organisere dine Azure ressourcer](resource-group-using-tags.md)


### <a name="update-domain"></a>opdatere domæne  
Samling af virtuelle maskiner i et sæt tilgængelighed, der opdateres på samme tid. Virtuelle maskiner i det samme domæne opdatering genstartet sammen under planlagt vedligeholdelse. Azure genstarter aldrig mere end ét opdatering domæne ad gangen. Også kaldes en opgradering domæne.  
Se også [administrere tilgængeligheden af Windows virtuelle maskiner](./virtual-machines/virtual-machines-windows-manage-availability.md) eller [Administrer tilgængeligheden af Linux virtuelle maskiner](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>virtuelt  
Softwareimplementeringen af en fysisk computer, der kører et operativsystem. Flere virtuelle maskiner kan køre samtidigt på den samme hardware. I Azure er virtuelle maskiner tilgængelige i forskellige størrelser.  
Se også [virtuelle maskiner dokumentation](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>virtuelt lokalnummer  
En ressource, der implementerer funktionsmåder eller funktioner, der enten nemmere for andre programmer arbejds- eller gør muligheden for dig at interagere med en kører på computeren. Du kan for eksempel bruge filtypenavnet VM Access til at nulstille eller ændre fjernadgang værdier på en Azure virtuel maskine.  
Se også [om virtuelt extensions og funktioner (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) eller [virtuelt extensions og funktioner (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>virtuelt netværk  
Et netværk, hvor du kan oprette forbindelse mellem din Azure ressourcer, der er adskilt fra alle andre Azure lejere. Det kan være forbundet til andre Azure virtuelle netværk via en [VPN-Gateway, Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) og til dit lokale netværk ved hjælp af [flere indstillinger](./vpn-gateway/vpn-gateway-cross-premises-options.md). Du kan fuldt styre IP-adresseblokke, DNS-indstillinger, sikkerhedspolitikker og distribuere tabeller inden for dette netværk.  
Se også [virtuelt netværk oversigt](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Se også**  
- [Introduktion til Azure](https://azure.microsoft.com/get-started/)
- [Ressourcecenter til skyen](https://azure.microsoft.com/resources/)  
- [Azure for din forretningsprogram](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure i dit datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/) 





