<properties
    pageTitle="Oprette en Linux VM ved hjælp af portalen Azure | Microsoft Azure"
    description="Oprette en Linux VM ved hjælp af portalen Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Oprette en Linux VM på Azure ved hjælp af portalen


I denne artikel beskrives, hvordan bruge [Azure portal](https://portal.azure.com/) til at oprette en Linux virtuel maskine.

Kravene er:

- [en Azure-konto](https://azure.microsoft.com/pricing/free-trial/)

- [SSH offentlige og private nøgler filer](virtual-machines-linux-mac-create-ssh-keys.md)


1. Logget på portalen Azure med din identitet Azure-konto, skal du klikke på **+ Ny** i øverste venstre hjørne:

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Klik på **virtuelle maskiner** på **Marketplace** , og derefter **Ubuntu Server 14.04 LTS** fra **Udvalgte Apps** billeder liste.  Kontrollere nederst, er implementeringsmodel `Resource Manager` og klik derefter på **Opret**.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Angiv på siden **grundlæggende** :
    - et navn til VM
    - et brugernavn for administrator
    - den godkendelsestype, der er angivet til **SSH offentlig nøgle**
    - din SSH offentlig nøgle som en streng (fra din `~/.ssh/` directory)
    - en ressource gruppere navn, eller Vælg en eksisterende gruppe

    og klikke på **OK** for at fortsætte og vælge VM størrelsen. det skal se ud som følger:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Vælg **DS1** størrelse, der installeres Ubuntu på en Premium SSD, og klik på **Vælg** for at konfigurere indstillinger.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. Beholde standardindstillingerne for lager og netværk værdier i **Indstillinger**, og klik på **OK** for at få vist oversigten.  Meddelelse om disktypen er blevet indstillet til Premium SSD ved at vælge DS1, **S** notates SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Bekræft indstillingerne for din nye Ubuntu VM, og klik på **OK**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Åbn dashboardet Portal og i **netværksgrænseflader** vælge din NIC

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Åbn den offentlige IP-adresser menu under indstillingerne for NIC

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH til den offentlige IP-adresse ved hjælp af din SSH offentlig nøgle

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Næste trin

Du har nu oprettet en Linux VM hurtigt skal bruges til test eller demonstrationer formål. Hvis du vil oprette en Linux VM tilpasset til infrastrukturen, kan du følge en af følgende artikler.

- [Oprette en Linux VM på Azure ved hjælp af skabeloner](virtual-machines-linux-cli-deploy-templates.md)
- [Oprette en SSH sikret Linux VM på Azure ved hjælp af skabeloner](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Oprette en Linux VM ved hjælp af Azure CLI](virtual-machines-linux-create-cli-complete.md)
