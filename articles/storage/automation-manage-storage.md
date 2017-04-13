<properties
    pageTitle="Administrere Azure-lager, med Automation Azure"
    description="Få mere at vide om, hvordan Azure Automation service kan bruges til at administrere Azure-lager på skala."
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-storage-using-azure-automation"></a>Administrere Azure-lager, med Automation Azure

Denne vejledning introducerer dig til Azure Automation service, og hvordan det kan bruges til at forenkle administration af BLOB Azure-lager, tabeller og køer.


## <a name="what-is-azure-automation"></a>Hvad er Azure automatisering?

[Azure automatisering](https://azure.microsoft.com/services/automation/) er en Azure service for at forenkle skyen administration ved hjælp af automatisering af områdeprocesser for. Ved hjælp af Azure automatisering længerevarende, manuel, fejlbehæftede og ofte gentagne opgaver kan automatisk for at øge pålideligheden og effektiviteten, og reducere tiden til værdi for din organisation.

Azure automatisering giver en meget pålidelig og meget tilgængelig arbejdsproces udførelse af program, der skaleres til at opfylde dine behov, som organisationen vokser. I Azure Automation kan processer sættes manuelt ved at 3 tredjepart systemer eller med planlagte intervaller så opgaver sker nøjagtigt, når det er nødvendigt.

Sænke funktionsdygtige omkostninger og frigøre IT / DevOps personale til at fokusere på arbejde, der tilføjer business værdien ved at flytte dine skyen administrationsopgaver skal køres automatisk med Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Hvordan kan Azure automatisering med at administrere Azure-lager?

Azure-lager kan administreres i Azure Automation ved hjælp af PowerShell-cmdlet'er, der er tilgængelige i [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure automatisering har disse lagerplads PowerShell-cmdletter tilgængelige af feltet, så du kan udføre alle dine blob, tabel og kø administrationsopgaver i tjenesten. Du kan også par disse cmdlet'er i Azure Automation med cmdletter for andre Azure tjenester til at automatisere komplekse opgaver på tværs af Azure tjenester og -3 part systemer.

[Azure automatisering runbook galleriet](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) indeholder en række forskellige produkt team og community runbooks at komme i gang automatisere styring af Azure-lager, andre Azure tjenester og 3 part systemer. Galleriet runbooks omfatter:

 * [Fjerne Azure BLOB-lager, der er visse dage gamle ved hjælp af Automation Service](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Hente en Blob fra Azure-lager](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Sikkerhedskopiér alle for en enkelt Azure VM eller for alle FOS i en skybaseret tjeneste](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Azure automatisering, og hvordan det kan bruges til at administrere Azure-lager blob, tabeller og køer, skal du følge disse links til yderligere oplysninger om Azure automatisering.

Se Azure automatisering selvstudiet, [oprette eller importere en runbook i Azure Automation](../automation/automation-creating-importing-runbook.md).
 
