<properties
    pageTitle="Gendanne nøgle samling nøgle og hemmeligt for krypterede FOS Azure sikkerhedskopiering | Microsoft Azure"
    description="Lær, hvordan du kan gendanne nøgle samling nøgle og hemmeligt i Azure sikkerhedskopi ved hjælp af PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Gendanne nøgle samling nøgle og hemmeligt for krypterede FOS Azure sikkerhedskopiering
I denne artikel taler om sikkerhedskopiering Azure VM til at udføre gendannelse af krypterede Azure FOS, hvis din nøgle og hemmeligt ikke findes i den vigtige samling af legitimationsoplysninger. Disse trin kan også bruges, hvis du vil bevare en separat kopi af nøgle (tasten krypteringsnøgle) og hemmeligt (BitLocker krypteringsnøgle) af gendannede VM.

## <a name="pre-requisites"></a>Forudsætninger

1. **Sikkerhedskopi krypteret FOS** - krypteret Azure FOS er blevet sikkerhedskopieret Azure sikkerhedskopiering. Se artiklen [administrere sikkerhedskopiering og gendannelse af Azure FOS ved hjælp af PowerShell](backup-azure-vms-automation.md) få mere at vide om, hvordan du sikkerhedskopiere krypterede Azure FOS.

2. Der findes allerede **Konfigurere Azure nøgle samling** – Kontroller, at de primære samling, nøgler og hemmeligheder skal gendannes. Se i artiklen [Introduktion til Azure nøgle samling](../key-vault/key-vault-get-started.md) for oplysninger om administration af vigtige samling.

## <a name="setup-recovery-services-vault"></a>Konfiguration af gendannelse services samling 
Brug følgende trin til at logge på PowerShell og angive gendannelse services samling kontekst

### <a name="log-in-to-azure-powershell"></a>Log på Azure PowerShell 

Log på Azure-konto ved hjælp af følgende cmdlet

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Angive gendannelse services samling kontekst

Når logget på, kan du bruge følgende cmdlet til at hente en liste over dine tilgængelige abonnementer

```
PS C:\> Get-AzureRmSubscription
```

Vælg det abonnement, som ressourcer er tilgængelige

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Angiv den samling kontekst ved hjælp af gendannelse Services samling, hvor sikkerhedskopi blev aktiveret krypterede FOS

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Få gendannelsespunkt 

Vælg objektbeholder i den samling af legitimationsoplysninger, der repræsenterer krypterede Azure virtuelt

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Ved hjælp af denne objektbeholder kom tilbage i element til den tilsvarende virtuelle maskine

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Få en matrix med gendannelse punkter til det valgte sikkerhedskopiering element i variable rp

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Gendanne krypteret virtuelt
Brug følgende trin til at gendanne krypteret VM, dens nøgle og hemmeligt.

### <a name="restore-key"></a>Gendanne nøgle

Matrixen $rp ovenfor, er sorteret i omvendt rækkefølge af tid med den seneste gendannelse midterpunkt Index 0. For eksempel: $rp [0] markerer det seneste gendannelsespunkt.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Når denne cmdlet kører korrekt, bliver der genereres en blob-fil i den angivne mappe på computeren, hvor det skal køres. Denne blob-fil repræsenterer nøgle krypteret nøgle i krypteret form.

Gendanne vigtige tilbage til den vigtige samling af legitimationsoplysninger ved hjælp af følgende cmdlet. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Gendanne hemmeligt

Gendanne hemmeligt data fra gendannelsespunkt der fås ovenfor

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
Teksten før vault.azure.net repræsenterer oprindelige vigtige samling navn. Tekst efter hemmeligheder / repræsenterer hemmeligt navn. 

Få hemmeligt navnet og værdien fra output fra Cmdletten kører ovenfor, hvis du vil bruge det samme hemmelige navn. I andre tilfælde ajourføres $secretname nedenfor for at bruge det nye hemmelige navn. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Angive mærker for hemmeligt, i tilfælde af VM skal gendannes samt. Mærket DiskEncryptionKeyFileName skal værdi indeholde navnet på det hemmeligt, du vil bruge. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Værdi for DiskEncryptionKeyFileName er den samme som hemmeligt navn, der hentes ovenfor. Værdi for DiskEncryptionKeyEncryptionKeyURL kan fås fra vigtige samling efter gendannelse af tasterne tilbage og bruge [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet   

Angiv den hemmeligt tilbage til den vigtige samling af legitimationsoplysninger

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Gendanne virtuelt
Ovenstående PowerShell-cmdletter hjælpe dig med at gendanne nøgle og hemmeligt tilbage til den vigtige samling af legitimationsoplysninger, hvis du har sikkerhedskopieret krypterede VM Azure VM sikkerhedskopiering. Se artiklen [administrere sikkerhedskopiering og gendannelse af Azure FOS ved hjælp af PowerShell](backup-azure-vms-automation.md) til at gendanne krypteret FOS efter gendannelse af dem.
