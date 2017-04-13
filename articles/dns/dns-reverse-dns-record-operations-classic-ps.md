<properties
   pageTitle="Administrere modsat DNS-poster for tjenester Azure (klassisk) ved hjælp af PowerShell | Microsoft Azure"
   description="Sådan administreres modsat DNS-poster eller PTR poster til Azure-tjenester ved hjælp af PowerShell i modellen Klassisk installation. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Sådan administreres modsat DNS-posterne for dine Azure tjenester (klassisk) ved hjælp af Azure PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validering af modsat DNS-poster
For at sikre en tredjepart ikke kan oprette modsat DNS-poster, der er tilknytning til dine DNS-domæner, tillader Azure kun oprettelse af en modsat DNS-post, hvor et af følgende er sandt:

- Omvendt DNS FQDN er navnet på den skybaseret tjeneste, hvor det er angivet, eller en skybaseret tjeneste navn i det samme abonnement f.eks., modsat DNS "contosoapp1.cloudapp.net.".
- Modsat DNS FQDN fremad oversættes til navnet eller IP-adresse til skyen-tjenesten, som det er angivet, eller til en skybaseret tjeneste navn eller IP-inden for samme abonnement f.eks., modsat DNS er "app1.contoso.com." som er et CName alias for contosoapp1.cloudapp.net.

Validering kontrol udføres kun, når egenskaben modsat DNS til en skybaseret tjeneste er angivet eller ændret. Periodiske fornyet validering udføres ikke.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Føje modsat DNS til eksisterende Cloud Services
Du kan føje en modsat DNS-post til en eksisterende skytjeneste ved hjælp af "Sæt-AzureService" cmdlet:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Oprette en skybaseret tjeneste med modsat DNS
Du kan tilføje en ny tjeneste i skyen med modsat DNS-egenskaben angivet ved hjælp af "Sæt-AzureService" cmdlet:

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Vis modsat DNS for eksisterende Cloud Services
Du kan få vist den værdi, der er konfigureret til en eksisterende skytjeneste ved hjælp af "Get-AzureService" cmdlet:

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Fjerne modsat DNS fra eksisterende Cloud Services
Du kan fjerne en modsat DNS-egenskab fra en eksisterende skytjeneste ved hjælp af Cmdletten "Sæt-AzureService". Det gøres ved at angive den modsatte DNS-egenskabsværdi til tom:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
