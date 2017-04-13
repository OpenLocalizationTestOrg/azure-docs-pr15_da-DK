<properties
    pageTitle="Inden du implementerer Azure stak Konceptet | Microsoft Azure"
    description="Få vist miljø og hardware kravene til Azure stak Konceptet (tjenesteadministratoren)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Forudsætninger for installation af Azure stak

Før du installerer Azure stak Konceptet ([Bevis af konceptet](azure-stack-poc.md)), skal du kontrollere din computer opfylder følgende krav.
Technical Preview 2 installation kravene til Konceptet er den samme som dem, der kræves til Technical Preview-1. Derfor kan du bruge den samme hardware, du har brugt til den forrige enkelt preview.

## <a name="hardware"></a>Hardware

| Komponent | Minimum  | Anbefalede |
|---|---|---|
| Diskdrev: operativsystem | 1 OS disk med mindst 200 GB tilgængelig for systempartition (SSD eller harddisk) | 1 OS disk med mindst 200 GB tilgængelig for systempartition (SSD eller harddisk) |
| Diskdrev: generelle Azure stak Konceptet Data | 4 diske. Hver disk indeholder mindst 140 GB af kapacitet (SSD eller harddisk). Alle tilgængelige diske bruges. | 4 diske. Hver disk indeholder mindst 250 GB af kapacitet (SSD eller harddisk). Alle tilgængelige diske bruges.|
| Beregne: CPU | To-Socket: 12 fysiske kerner (i alt)  | To-Socket: 16 fysiske kerner (i alt) |
| Beregne: hukommelse | 96 GB RAM  | 128 GB RAM |
| Beregne: BIOS | Hyper-V aktiveret (med SLAT support)  | Hyper-V aktiveret (med SLAT support) |
| Netværk: NIC | Windows Server 2012 R2 certificering kræves til NIC; ingen specielle funktioner, der er påkrævet | Windows Server 2012 R2 certificering kræves til NIC; ingen specielle funktioner, der er påkrævet |
| Hardware logocertificering | [Certificeret til Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificeret til Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Data diskdrev konfiguration:** Alle datadrev skal være af samme type (alle Sikkerhedstilknytninger eller alle SATA) og kapacitet. Hvis SAS diskdrev har brugt, skal være knyttet diskdrev via en enkelt sti (ingen MPIO, understøttelse af flere sti leveres).

**HBA konfigurationsindstillinger**
 
- (Anbefales) Enkel HBA
- RAID HBA – Adapter skal konfigureres i "passerer gennem" tilstand
- RAID HBA – diske skal være konfigureret som enkelt Disk, RAID-0

**Understøttede bus og medier skrive kombinationer**

-   SATA HARDDISK

-   SAS HARDDISK

-   RAID HARDDISK

-   RAID SSD (hvis medietypen er uspecificeret/ukendt\*)

-   SATA SSD + SATA HARDDISK

-   SAS SSD + SAS HARDDISK

\*RAID enheder uden pass-through-funktioner ikke kan genkende medietypen. Disse enheder markere både harddisk og SSD som ikke-specificeret. Det er tilfældet, bruges SSD som fast lager i stedet for cachelagring enheder. Derfor kan du installere Microsoft Azure stak Konceptet på disse SSDs.

**Eksempel HBA'er**: LSI 9207 8i, LSI-9300-8i eller LSI-9265-8i i pass-through-tilstand

Eksempel OEM-konfigurationer er tilgængelige.

## <a name="operating-system"></a>Operativsystem

| | **Krav**  |
|---|---|
| **OS-Version** | Windows Server 2012 R2 eller nyere. Operativsystemversionen ikke kritiske, før installationen starter, som du vil starte host computeren i den virtuelle harddisk, der er inkluderet i Azure stak installation zip. Operativsystemet og alle nødvendige rettelser allerede er integreret i billedet. Brug ikke nogen taster til at aktivere alle forekomster af Windows Server bruges i Konceptet.|

## <a name="deployment-requirements-check-tool"></a>Værktøj til kontrol af installation krav

Når du har installeret operativsystemet, kan du bruge [Installation kompatibilitetskontrollen for Azure stak Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) for at bekræfte, at hardwaren, der opfylder alle krav.



## <a name="microsoft-azure-active-directory-accounts"></a>Microsoft Azure Active Directory-konti

Microsoft Azure stak Konceptet installationen skal have forbindelse til Azure. Du skal derfor forberede en Microsoft Azure Active Directory-konto, før du kører installationen PowerShell-script. Denne konto, bliver den globale administrator for lejeren Azure Active Directory. Det bruges til at klargøre og delegere programmer og tjenester principper for alle Azure stak tjenester, der arbejder sammen med Azure Active Directory og grafik API. Det kan også bruges som ejer af standard udbyder abonnementet (som du kan ændre senere). Du kan logge på systemet Azure stak admin-portalen ved hjælp af denne konto.

1. Oprette en Azure AD-konto, der er directory-administrator for mindst én Azure Active Directory. Hvis du allerede har en, kan du bruge. Ellers kan du oprette én gratis på [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (i Kina, skal du besøge <http://go.microsoft.com/fwlink/?LinkID=717821> i stedet.)

    Gemme disse legitimationsoplysninger til brug i trin 6 i [køres scriptet PowerShell-installation](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Denne *tjeneste* administratorkonto kan konfigurere og administrere ressource skyer, brugerkonti, lejer planer, kvoter og priser. I portalen, kan de oprette websted skyer, virtuelt privat skyer, oprette planer og administrere bruger abonnementer.

2. [Opret](azure-stack-add-new-user-aad.md) mindst én konto, så du kan logge på Azure stak Konceptet som en lejer.

  	| **Azure Active Directory-konto**  | **Understøttet?** |
  	|---|---| 
  	| Arbejds- eller skolekonto konto med gyldige offentlige Azure-abonnement  | Ja |
  	| Microsoft-Account med gyldige offentlige Azure-abonnement  | Nej |
  	| Arbejds- eller skolekonto konto med gyldige Kina Azure-abonnement  | Ja |
  	| Arbejds- eller skolekonto konto med gyldige OS Government Azure-abonnement  | Ja |


## <a name="network"></a>Netværk

### <a name="switch"></a>Skift

En tilgængelig port på en parameter for Konceptet maskinen.  

Azure stak Konceptet maskinen understøtter oprettelse af forbindelse til en Skift access eller trunk port. Der kræves ingen specielle funktioner på Skift. Hvis du bruger en trunk port, eller hvis du vil konfigurere et VLAN-ID, du skal angive det VLAN-ID, som en parameter for installation. Du kan se eksempler på [listen over installation parametre](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Undernet

Tilslut ikke Konceptet maskinen til de følgende undernet:
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Disse undernet er reserveret til de interne netværk i Microsoft Azure stak Konceptet-miljøet.

### <a name="ipv4ipv6"></a>IPv4/IPv6

Kun IPv4 understøttes. Du kan ikke oprette IPv6-netværk.

### <a name="dhcp"></a>DHCP

Kontrollér, at der findes en DHCP-server på netværket, NIC opretter forbindelse til. Hvis DHCP ikke er tilgængelig, skal du forberede et ekstra statisk IPv4-netværk ud over den, der bruges af vært. Du skal angive IP-adressen og gatewayen som en parameter for installation. Du kan se eksempler på [listen over installation parametre](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Adgang til internettet

Azure stak kræver adgang til internettet, skal du enten direkte eller via en gennemsigtig proxy. Azure stak understøtter ikke konfigurationen af en webtjenesteproxy til at aktivere adgang til internettet. Både host IP-adresse og den nye IP-tildelt MAS-BGPNAT01 (ved DHCP eller statiske IP-Adresser) skal have adgang til internettet. Portene 80 og 443 bruges under graph.windows.net og login.windows.net domæner.

### <a name="telemetry"></a>Telemetri

For at understøtte telemetri dataflow skal port 443 (HTTPS) være åben i dit netværk. Klient-slutpunkt er https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Næste trin

[Hent installationspakken Azure stak Konceptet](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Installere Azure stak Konceptet](azure-stack-run-powershell-script.md)
