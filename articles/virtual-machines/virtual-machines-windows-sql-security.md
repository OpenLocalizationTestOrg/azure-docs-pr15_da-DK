<properties
    pageTitle="Overvejelser om sikkerheden for SQL Server på Azure | Microsoft Azure"
    description="Dette emne refererer til ressourcer, der er oprettet med den klassiske implementeringsmodel og indeholder en generel vejledning til sikring af SQL Server, der kører på en Azure Virtual Machine."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Overvejelser om sikkerheden for SQL Server på Azure virtuelle computere
 
Dette emne indeholder generelle retningslinjer for sikkerhed, hjælpe for at oprette sikker adgang til forekomster af SQL Server i en Azure VM. Men for at sikre en bedre beskyttelse til din forekomster af SQL Server-database i Azure, anbefales det, at du implementerer traditionelle lokalt sikkerhedsfremgangsmåder ud over de bedste fremgangsmåder til sikkerhed for Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Kan finde flere oplysninger om SQL Server sikkerhedsfremgangsmåder i [SQL Server 2008 R2 bedste fremgangsmåder for sikkerhed - drift og administrationsopgaver](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure i overensstemmelse med flere brancheregler og standarder, der gør det muligt at oprette en løsning med kompatibel med SQL Server, der kører på en virtuel computer. Du kan finde oplysninger om overholdelse af lovgivningen med Azure [Azure Center for sikkerhed og rettighedsadministration](https://azure.microsoft.com/support/trust-center/).

Følgende er en liste over sikkerhedsanbefalinger, der skal overvejes, når du konfigurerer og oprette forbindelse til forekomsten af SQL Server på en Azure VM.

## <a name="considerations-for-managing-accounts"></a>Overvejelser i forbindelse med administration af konti:

- Oprette et entydigt lokale administratorkonto, som ikke hedder **Administrator**.

- Brug komplekse stærke adgangskoder til alle dine konti. Du kan finde flere oplysninger om at oprette en stærk adgangskode, kan du [Tip til oprettelse af en stærke adgangskoder](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) til artiklen.

- Som standard markerer Azure Windows-godkendelse under installationen af SQL Server Virtual Machine. Derfor **Systemadministratorens** logon er deaktiveret, og en adgangskode er tildelt af installationsprogrammet. Vi anbefaler, at **Systemadministratorens** logon skal være ikke brugt eller aktiveret. Følgende er alternativ strategier, hvis du ønsker en SQL-logon:
    - Oprette en SQL-konto, som er systemadministrator medlem.
    - Hvis du skal bruge et **Systemadministratorens** logon, aktivere logon og Omdøb den og tildele en ny adgangskode.
    - Begge de indstillinger, der er nævnt tidligere kræver en ændring godkendelsesmetode til **SQL Server og Windows-godkendelsestilstand**. Se [Ændre Server-godkendelsestilstand](https://msdn.microsoft.com/library/ms188670.aspx)kan finde flere oplysninger.

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Overvejelser i forbindelse med sikring af forbindelser til Azure virtuelt:

- Overvej at bruge [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) til at administrere virtuelle maskiner i stedet for offentlige RDP-porte.

- Brug en [Sikkerhedsgruppe netværk](../virtual-network/virtual-networks-nsg.md) (NSG) til at tillade eller nægte netværkstrafik til din virtuelle maskine. Hvis du vil bruge en NSG og allerede har et slutpunkt ACL på plads, skal du først fjerne slutpunktet ACL. Finde oplysninger om hvordan du gør dette, [Administration af adgangskontrollister (ACLs) for slutpunkter ved hjælp af PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Hvis du bruger slutpunkter, fjerne et slutpunkt på en virtuel maskine, hvis du ikke bruge dem. Du kan finde vejledning til brug af ACLs med slutpunkter, [Administrer ACL på et slutpunkt](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Aktivere en krypteret forbindelse indstilling for en forekomst af SQL Server-Database Engine i virtuelle Azure-computere. Konfigurere SQL server-forekomsten med en signeret certifikat. Se [Aktivere krypteret forbindelse til Database Engine](https://msdn.microsoft.com/library/ms191192.aspx) og [Forbindelse Strengsyntaksen](https://msdn.microsoft.com/library/ms254500.aspx)kan finde flere oplysninger.

- Hvis din virtuelle maskiner skal åbnes kun fra et bestemt netværk, kan du bruge Windows Firewall til at begrænse adgangen til bestemte IP-adresser eller netværksundernet.

## <a name="next-steps"></a>Næste trin

Hvis du er også interesseret i bedste fremgangsmåder omkring ydeevne, se [Ydeevne bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-performance.md).

Du kan finde andre emner, der er relateret til kører SQL Server i Azure FOS, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
