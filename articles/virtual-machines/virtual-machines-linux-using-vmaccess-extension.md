<properties
    pageTitle="Nulstille access på Azure Linux FOS bruger filtypenavnet VMAccess | Microsoft Azure"
    description="Nulstil access på Azure Linux FOS bruger filtypenavnet VMAccess."
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
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Administrere brugere, SSH, og markér eller reparere diske på Azure Linux FOS bruger filtypenavnet VMAccess

Denne artikel beskrives, hvordan du bruger filtypenavnet Azure VMAcesss til at kontrollere eller reparere en disk, nulstille brugeradgang, administrere brugerkonti eller nulstille SSHD konfiguration på Linux. I artiklen kræver:

- en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) logget på med `azure login`.

- Azure CLI _skal være i_ Azure ressourcestyring tilstand `azure config mode arm`.

## <a name="quick-commands"></a>Hurtig kommandoer

Der er to måder at bruge VMAccess på din Linux FOS:

- Brug af Azure CLI og de krævede parametre.
- Brug af raw JSON-filer, der VMAccess behandler og derefter handle på.

For sektionen hurtig kommando skal vi bruge Azure CLI `azure vm reset-access` metode. Erstatte de værdier, der indeholder "eksempel" med værdierne fra dit eget miljø i eksemplerne nedenfor kommandoen.

## <a name="create-a-resource-group-and-linux-vm"></a>Oprette en ressourcegruppe og Linux VM

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Oprette en Debian VM

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Nulstil rod adgangskode

Nulstille adgangskoden rod:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>SSH vigtige Nulstil

Sådan nulstilles tasten SSH for en bruger, ikke rod:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Oprette en bruger

Sådan opretter du en bruger:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Fjerne en bruger

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Nulstil SSHD

Sådan nulstilles SSHD konfigurationen:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Detaljeret gennemgang

### <a name="vmaccess-defined"></a>VMAccess defineret:

Disken på din Linux VM viser fejl. Du har en eller anden måde at nulstille adgangskoden rodwebstedet for din Linux VM eller slettes ved et uheld din SSH privat nøgle. Hvis det er tilfældet tilbage i dage i datacenteret, skal du drive der og derefter åbne KVM til at få i serverkonsollen. Tænk på filtypenavnet Azure VMAccess som den KVM-switch, hvor du kan få adgang til konsollen for at nulstille adgang til Linux eller udføre disk niveau vedligeholdelse.

Detaljeret gennemgang skal vi bruge lang form af VMAccess, som bruger raw JSON-filer.  Disse VMAccess JSON-filer kan også kaldes fra Azure skabeloner.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Brug af VMAccess til at kontrollere eller reparere disken af en Linux VM

Brug af VMAccess kan du gøre et fsck køre på disken under din Linux VM.  Du kan også gøre en kontrol af disk og en disk reparation ved hjælp af en VMAccess.

For at kontrollere, og derefter reparere disken, skal du bruge dette VMAccess-script:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Udføre VMAccess scriptet med:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Brug af VMAccess til at nulstille brugeradgang til Linux

Hvis du har mistet adgang til rod på din Linux VM, kan du starte et VMAccess script for at nulstille adgangskoden rod.

Brug denne VMAccess script for at nulstille adgangskoden rod:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Udføre VMAccess scriptet med:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Brug denne VMAccess script for at nulstille SSH nøglen for en bruger, ikke rod:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Udføre VMAccess scriptet med:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Bruge VMAccess til at administrere brugerkonti på Linux

VMAccess er et Python script, der kan bruges til at administrere brugere på din Linux VM uden at logge på og bruge sudo eller kontoen rod.

Brug dette VMAccess script til at oprette en bruger:

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Udføre VMAccess scriptet med:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

For at slette en bruger, skal du bruge dette VMAccess script:

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Udføre VMAccess scriptet med:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Brug af VMAccess til at nulstille SSHD konfigurationen

Hvis du foretager ændringer af Linux FOS SSHD konfigurationen og lukke SSH forbindelsen, før du bekræfte ændringerne, du kan være forhindret i SSH'ing igen.  VMAccess kan bruges til at nulstille SSHD konfigurationen tilbage til en kendte gode konfiguration uden at der logges over SSH.

For at nulstille SSHD konfigurationen, skal du bruge dette VMAccess-script:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Udføre VMAccess scriptet med:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Næste trin

Opdatere Linux er ved hjælp af Azure VMAccess udvidelser en metode til at foretage ændringer på en igangværende Linux VM.  Du kan også bruge funktioner som skyen initialisering og Azure skabeloner til at ændre din Linux VM ved start.

[Om virtuelt filtypenavne og funktioner](virtual-machines-linux-extensions-features.md)

[Redigering Azure ressourcestyring skabeloner med Linux VM filtypenavne](virtual-machines-linux-extensions-authoring-templates.md)

[Bruge skyen initialisering til at tilpasse en Linux VM under oprettelse af](virtual-machines-linux-using-cloud-init.md)
