<properties
    pageTitle="Ændre vigtige samling lejer-ID'ET, når et abonnement flytter | Microsoft Azure"
    description="Lær, hvordan du skifter lejer-ID'ET til et vigtige samling, når et abonnement flyttes til en anden lejer"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Ændre et vigtige samling lejer ID, når et abonnement flytter
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Sp: mit abonnement blev flyttet fra lejer A til lejer B. Hvordan ændre lejer-ID'ET for min eksisterende vigtige samling af legitimationsoplysninger og angive korrekte ACLs for principper i B-lejer?

Når du opretter en ny nøgle samling i et abonnement, er den automatisk knyttet til standard Azure Active Directory-lejer-ID'ET for dette abonnement. Alle access politik poster er også knyttet til denne lejer-ID. Når du flytter abonnementet Azure fra lejer A til lejer B, er din eksisterende vigtige vaults ikke tilgængelig ved principper (brugere og programmer) i lejer B. Hvis du vil løse dette problem, skal du:

- Ændre lejer-ID, der er knyttet til alle eksisterende vigtige vaults i dette abonnement til lejer B.
- Fjerne alle eksisterende poster access-politik.
- Tilføje nye access politik poster, der er knyttet til lejer B.

Eksempelvis hvis du har vigtige samling 'myvault' i et abonnement, der er blevet flyttet fra lejer A til lejerens B, her hvordan du kan ændre lejer ID for denne vigtige samling af legitimationsoplysninger og fjerne gamle access politikker.

<pre>
$vaultResourceId = (get-AzureRmKeyVault - VaultName myvault). ResourceId $vault = Get-AzureRmResource – ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() sæt AzureRmResource - ResourceId $vaultResourceId-egenskaber $vault. Egenskaber
</pre>

Da denne samling var i lejer A før flytningen, **$vault oprindelige værdi. Properties.TenantId** er lejer A, mens du **(Get-AzureRmContext). Tenant.TenantId** er lejer B.

Nu, hvor din samling af legitimationsoplysninger er knyttet til den korrekte lejer-ID og gamle access politik poster er fjernet, kan du angive nye adgang politik posterne med [Sæt AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Næste trin

Hvis du har spørgsmål om Azure-tasten samling, gå til [Azure nøgle samling fora](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
