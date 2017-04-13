<properties
   pageTitle="Aktivere eller deaktivere Azure VM overvågning"
   description="Beskriver, hvordan du aktiverer eller deaktiverer Azure VM overvågning"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Aktivere eller deaktivere Azure VM overvågning

Dette afsnit beskrives, hvordan du aktiverer eller deaktiverer overvågning på virtuelle maskiner, der kører på Azure. Som standard overvågning er aktiveret på Azure virtuelle maskiner Hvis installeres fra [Azure-portalen](https://portal.azure.com) og overvågning grafer er angivet som standard sammen med en 1-minutters periode. Du kan aktivere eller deaktivere overvågning ved hjælp af portalen eller Azure kommandolinjen til Mac, Linux og Windows (Azure-CLI). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Aktivere / deaktivere overvågning via Azure-portalen
 
Du kan aktivere overvågning af dine Azure VM, som indeholder dataene om dine forekomst i 1-minutters perioder. (lagerplads ændringer anvender). Detaljeret diagnosticering data bliver tilgængelige for VM i portalen grafer eller via API. Azure portalen kan overvåge som standard, men du kan slå den fra, som beskrevet nedenfor. Du kan aktivere overvågning, mens VM kører eller i stoppede tilstand.

- Åbn den Azure portalen på ** [https://portal.azure.com](https://portal.azure.com)**

- I venstre navigationsrude skal du klikke på virtuelle maskiner.

- Vælg en kører eller er stoppet forekomst på listen virtuelle computere. Virtuelt blad åbnes.

- Klik på "Alle indstillinger".

- Klik på "Diagnosticering".

- Ændre status til eller fra. Du kan også vælge kontrolniveau detaljer, du vil aktivere for din virtuelle maskine, i denne blade.

[Azure.Note] Skift diagnosticering på er standard, når du opretter en ny virtuel maskine

![Aktivere / deaktivere overvågning via Azure-portalen.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Aktivere / deaktivere overvågning med Azure CLI
 
For at aktivere overvågning for en Azure VM.

- Oprette en fil med navnet som PrivateConfig.json med følgende indhold.
        {"storageAccountName": "lagerplads kontoen til at modtage data", "storageAccountKey": "tast til kontoen"}
- Køre følgende kommando for Azure CLI.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Du kan skifte fra version 2.0 til en nyere version, når de er tilgængelige. 

Få flere oplysninger om at konfigurere overvågning målepunkter og eksempler, dokumentet - **[Ved hjælp af Linux diagnosticering udvidelse til skærm Linux VM ydeevne og diagnosticering data](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

