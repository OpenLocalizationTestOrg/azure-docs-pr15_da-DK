<properties 
   pageTitle="Accelereret netværk for en virtuel maskine - Portal | Microsoft Azure"
   description="Lær, hvordan du konfigurerer accelereret netværk til en Azure virtuelt ved hjælp af portalen Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Accelereret netværk for en virtuel maskine

> [AZURE.SELECTOR]
- [Azure-portalen](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Accelereret netværk gør det muligt for enkelt roden i/o-Virtualization (SR-IOV) til en virtuel maskine (VM) gør det meget forbedring af ydeevnen netværk. Denne høj ydeevne sti tilsidesætte værten fra datapath reducere ventetid, formindskelse og CPU-forbrug til brug sammen med tunge netværk arbejdsbelastningen på understøttede VM datatyper. I denne artikel forklares, hvordan du bruger portalen Azure til at konfigurere accelereret netværk i implementeringsmodel Azure ressourcestyring. Du kan også oprette en VM med accelereret netværk ved hjælp af Azure PowerShell. Til at få mere at vide hvordan, klik på feltet PowerShell øverst i denne artikel.

Følgende billede viser kommunikation mellem to virtuelle maskiner (VM) med og uden accelereret netværk:

![Sammenligning](./media/virtual-network-accelerated-networking-portal/image1.png)

Alle netværk trafik og VM skal gennemgang af værten og parameteren virtuelle uden accelereret netværk. Virtuel Skift indeholder alle politikhåndhævelse, som netværk sikkerhedsgrupper, adgangskontrollister, isolationsniveauet og andre virtualiseret netværkstjenester til netværkstrafik. Hvis du vil vide mere, du læse artiklen [Hyper-V netværk Virtualization og Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Med accelereret netværk, netværkstrafik ankommer til netværkskort (NIC) og derefter videresendes til VM. Alle netværkspolitikker, der gælder parameteren virtuelle uden accelereret netværk er lagt over og anvendt i hardware. Anvende politik i hardware, der gør det muligt for NIC for at videresende netværkstrafik direkte til VM, springe værten og parameteren virtuelle, samtidig med at alle politikken anvendes den i værten.

Fordelene ved accelereret netværk gælder kun for den VM, som det er aktiveret på. De bedste resultater er den perfekt at aktivere denne funktion på mindst to FOS forbindelse til den samme VNet. Når du kommunikerer på tværs af VNets eller forbindende lokalt, har denne funktion en minimale betydning for ventetid på overordnet.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Fordele

- **Kortere ventetid / højere pakker hver anden (pps):** Fjerne parameteren virtuelle fra datapath fjerner den tid, pakker bruger i at være vært for behandling af politik og øge antallet af pakker, der kan behandles i VM.
- **Reduceret formindskelse:** Virtuel Skift behandling afhænger af mængden politik, der skal anvendes og arbejdsbelastningen af CPU'EN, der gør behandlingen. Overføre politik håndhævelsen til hardware fjerner denne variation ved at levere pakker direkte til VM, fjerne værten VM kommunikation og alle software afbrydelser og kontekst parametre.
- **Mindre CPU-forbrug:** Springe parameteren virtuelle på værten fører til mindre CPU-forbrug til at behandle netværkstrafik.

## <a name="limitations"></a>Begrænsninger

Der findes følgende begrænsninger, når du bruger denne egenskab:
 
- **Netværk oprettelse af brugergrænsefladen:** Accelereret netværk kan kun aktiveres for en ny netværksgrænseflade.  Det kan ikke aktiveres på en eksisterende netværksgrænseflade.
- **VM oprettelse:** Grænsefladen et netværk med accelereret netværk aktiveret kan kun knyttes til en VM, når VM oprettes. Grænsefladen for det netværk, kan ikke knyttes til en eksisterende VM.
- **Områder:** Tilbydes i Vest Central os og Vest Europe Azure områder kun. Sættet af områder udvides i fremtiden.
- **Understøttede operativsystem:** Microsoft Windows Server 2012 R2 og Windows Server 2016 Technical Preview 5. Understøttelse af Linux og Windows Server 2012, føjes snart.
- **VM størrelse:** Standard_D15_v2 og Standard_DS15_v2 er den eneste understøttede VM forekomst størrelser. Få mere at vide artiklen [Windows VM størrelser](../virtual-machines/virtual-machines-windows-sizes.md) . Sæt af understøttede VM forekomst størrelser udvides i fremtiden.

Ændringer af disse begrænsninger bliver offentliggjort via siden [Azure Virtual Networking opdateringer](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Oprette en Windows VM med accelereret netværk

1. Registrere for eksempel ved at sende en mail til [Accelereret netværk abonnementer](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) med dit abonnement-ID og tilsigtede brug. Ikke fuldført de resterende trin indtil, når du modtager en mail med besked om, at du har antaget i eksemplet.
2. Log på portalen Azure på http://portal.azure.com.
3. Oprette en VM ved at udføre trinnene i artiklen [oprette en Windows-VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vælge følgende indstillinger:
    - Vælg et operativsystem, der er angivet i afsnittet begrænsninger i denne artikel.
    - Vælg en placering (område), der er angivet i afsnittet begrænsninger i denne artikel.
    - Vælg en VM størrelse, der er angivet i afsnittet begrænsninger i denne artikel. Hvis en af de understøttede størrelser ikke vises, skal du klikke på **Vis alle** i bladet **Vælg en størrelse** til at vælge en størrelse fra en udvidet liste.
    - I bladet **Indstillinger** , klik på *aktiveret* for **accelereret netværk**, som vist i billedet nedenfor:

        ![Indstillinger](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] Indstillingen accelereret netværk kan kun ses, hvis du har:
    >
    >- Antaget i eksemplet
    >- Markeret understøttet operativsystem, placering og VM størrelser, der er nævnt i afsnittet begrænsninger i denne artikel.

5. Når VM er oprettet, kan du hente [accelereret netværk driver](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), oprette forbindelse og logge på VM, og kør driver installer i VM.
6. Højreklik på Windows-knappen, og klik på **Enhedshåndtering**. Kontrollere, at **Mellanox ConnectX-3 virtuelle funktionen Ethernet-netværkskort** vises under indstillingen **netværk** , når det er udvidet, som vist i billedet nedenfor:

    ![Enhedshåndtering](./media/virtual-network-accelerated-networking-portal/image2.png)