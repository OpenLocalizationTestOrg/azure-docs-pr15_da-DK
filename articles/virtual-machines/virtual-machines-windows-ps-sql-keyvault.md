<properties
    pageTitle="Konfigurere Azure vigtige samling Integration til SQL Server på Azure FOS (ressourcestyring)"
    description="Lær at automatisere konfigurationen af SQL Server-kryptering til brug sammen med Azure-tasten samling. Dette emne forklares det, hvordan du bruger Azure-tasten samling Integration med SQL Server virtuelle maskiner, der er oprettet med ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Konfigurere Azure vigtige samling Integration til SQL Server på Azure FOS (ressourcestyring)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-ps-sql-keyvault.md)
- [Klassisk](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Oversigt
Der findes flere SQL Server-krypteringsfunktioner, som [gennemsigtig datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [kolonne niveau kryptering (KEL)](https://msdn.microsoft.com/library/ms173744.aspx)og [Sikkerhedskopiering kryptering](https://msdn.microsoft.com/library/dn449489.aspx). Disse former for kryptering kræver, at du til at administrere og gemme de cryptographic taster, der bruges til kryptering. Tjenesten Azure nøgle samling (AKV) er udviklet til at forbedre sikkerheden og administration af disse taster på en sikker og fleksibelt placering. [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server til at bruge disse taster fra Azure-tasten samling.

Hvis du kører SQL Server med lokale maskiner, der er [trin, du kan følge for at få adgang til Azure nøgle samling fra computeren lokal SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Men til SQL Server på Azure FOS, kan du spare tid ved hjælp af funktionen *Azure nøgle samling Integration* .

Når denne funktion er aktiveret, den automatisk installeres SQL Server-forbindelsen, konfigurerer EKM udbyderen for at få adgang til Azure-tasten samling og opretter de legitimationsoplysninger, der gør det muligt at få adgang til din samling af legitimationsoplysninger. Hvis du har set trinnene i den tidligere nævnte lokalt dokumentation, kan du se, at denne funktion automatisere trin 2 og 3. Det eneste du stadig har brug for at gøre det manuelt er at oprette de vigtigste samling og -taster. Hele konfigurationen af din SQL VM er automatiseret derfra. Du kan udføre T-SQL-sætninger for at begynde at kryptere dine databaser eller sikkerhedskopier, som du normalt ville gøre, når denne funktion er afsluttet denne konfiguration.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Aktivere og konfigurere AKV-integration
Du kan aktivere AKV integration under klargøringen eller konfigurere det til eksisterende FOS.

### <a name="new-vms"></a>Ny FOS
Hvis du klargøring af en ny SQL Server virtuel maskine med Ressourcestyring, indeholder portalen Azure et trin for at aktivere Azure-tasten samling integration. Funktionen Azure-tasten samling er kun tilgængelig for Enterprise, udvikler og evaluering udgaver af SQL Server.

![Integration af SQL Azure vigtige samling](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Du kan finde en detaljeret gennemgang af klargøring [klargøring en SQL Server virtuel maskine i portalen Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Eksisterende FOS
Vælg din SQL Server virtuelle maskine til eksisterende SQL Server virtuelle maskiner. Vælg derefter **SQL Server-konfiguration** del af bladet **Indstillinger** .

![SQL AKV Integration for eksisterende FOS](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Klik på knappen **Rediger** i sektionen automatisk nøgle samling integration i bladet **konfiguration af SQL Server** .

![Konfigurere SQL AKV Integration til eksisterende FOS](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Klik på knappen **OK** nederst på bladet **SQL Server-konfiguration** for at gemme ændringerne, når du er færdig.

>[AZURE.NOTE] Du kan også konfigurere AKV integration ved hjælp af en skabelon. Du kan finde yderligere oplysninger finder [Azure Hurtig start skabelon til integration af Azure nøgle samling](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
