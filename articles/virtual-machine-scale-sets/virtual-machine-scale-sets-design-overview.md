<properties
    pageTitle="Designe virtuelt skala indstiller For skala | Microsoft Azure"
    description="Få mere at vide om, hvordan du designsæt din virtuelt skala til skala"
    keywords="Linux virtuelt virtuelt skala angiver" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Designe VM skala indstiller For skala

Dette emne beskrives Designovervejelser virtuelt skala sæt. For oplysninger om, hvad virtuelt skala sæt er, at referere til [Virtuelt skala sæt oversigt](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Lagerplads

Nogle skala anvender lagerplads konti til lagring OS diske af FOS i dialogboksen Angiv. Vi anbefaler, at en forholdet mellem 20 FOS lagerplads konto- eller mindre. Vi anbefaler også, at du flyder på tværs af alfabetet de første tegn i kontonavne lagerplads. Gør det hjælper med at sprede belastning på tværs af forskellige interne systemer. For eksempel i skabelonen følgende vi bruge uniqueString ressourcestyring skabelon funktionen til at generere præfiks hashes, der er foran lagerplads kontonavne: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Overprovisioning

Starter med "2016-03-30" API-version, standard VM skala sæt "overprovisioning" FOS. Med overprovisioning slået til, skalaen angive faktisk drejer rundt op flere FOS, end du bedt om og derefter sletter de ekstra VM'er, der af af op sidste. Overprovisioning forbedrer klargøring succes satser. Du ikke er faktureret til disse ekstra FOS, og de tæller ikke mod din kvote begrænsninger.

Mens overprovisioning forbedre klargøring succes satser, kan det medføre forvirrende funktionsmåde for et program, der ikke er udviklet til at håndtere FOS at forsvinde uanmeldt. For at slå overprovisioning fra skal sikre, at du har følgende streng i din skabelon: "overprovision": "falsk". Yderligere oplysninger finder du i [VM skala angive REST-API dokumentation](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Hvis du deaktiverer overprovisioning, du kan få ikke til stede med en større forholdet mellem FOS per lagerplads konto, men vi anbefaler ikke besøge over 40.


## <a name="limits"></a>Begrænsninger
Et skala sæt baseret på et brugerdefineret billede (én oprettet af dig) skal oprette alle OS disk virtuelle harddiske i én lagerplads konto. Den største anbefalede antal FOS i et skala sæt baseret på et brugerdefineret billede, som et resultat, er 20. Hvis du deaktiverer overprovisioning, kan du gå op til 40.

Nogle skala bygget på en platform billede er i øjeblikket er begrænset til 100 FOS (Vi anbefaler 5 lagerplads konti til denne skala).

For flere FOS end disse begrænsninger, der er tilladt, skal du installere flere skala sæt, som vist i [denne skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).