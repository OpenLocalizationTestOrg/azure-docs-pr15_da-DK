<properties
    pageTitle="Azure AD-domænetjenester: Aktivere synkronisering af adgangskoder | Microsoft Azure"
    description="Introduktion til Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Aktivere synkronisering af adgangskoder til Azure AD-domænetjenester
I foregående opgaver, du har aktiveret Azure AD-domænetjenester for din Azure AD-lejer. Den næste opgave er at aktivere synkronisering af adgangskoder til Azure AD-domænetjenester. Når legitimationsoplysninger synkronisering er konfigureret, kan brugerne logge på til administrerede domænet ved hjælp af deres virksomhedens legitimationsoplysninger.

Trinnene er forskellige afhængigt af om din organisation har et kun skyen Azure AD lejer eller er indstillet til at synkronisere med din lokale mappe ved hjælp af Azure AD-forbindelse.

<br>

> [AZURE.SELECTOR]
- [Kun skyen Azure AD-lejer](active-directory-ds-getting-started-password-sync.md)
- [Synkroniseret Azure AD-lejer](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Opgave 5: Aktivere synkronisering af adgangskoder til AAD Domain Services til en synkroniseret Azure AD-lejer
Et synkroniseret Azure AD-lejer er indstillet til at synkronisere med din organisations lokale directory ved hjælp af Azure AD-forbindelse. Azure AD-forbindelse ikke synkronisere NTLM og Kerberos legitimationsoplysninger hashes til Azure AD som standard. Hvis du vil bruge Azure AD-domænetjenester, skal du konfigurere Azure AD-forbindelse for at synkronisere legitimationsoplysninger hashes kræves til NTLM og Kerberos-godkendelse. Følgende trin aktivere synkronisering af de nødvendige legitimationsoplysninger hashes til din Azure AD-lejer.


### <a name="install-or-update-azure-ad-connect"></a>Installere eller opdatere Azure AD-forbindelse
Installer den seneste anbefalede version af Azure AD-forbindelse på et domæne sammenføjet computer. Hvis du har en eksisterende forekomst af Azure AD-forbindelse konfigurationen, skal du opdatere den for at bruge den nyeste version af Azure AD-forbindelse. For at undgå kendte problemer/fejl, kan der allerede er blevet rettet, sikre, at du altid bruge den nyeste version af Azure AD-forbindelse.

**[Hent Azure AD-forbindelse](http://www.microsoft.com/download/details.aspx?id=47594)**

Anbefalede version: **1.1.281.0** - udgives på September 7 2016.

  > [AZURE.WARNING] Du skal installere den nyeste anbefalede version af Azure AD-forbindelse til at aktivere de ældre adgangskode legitimationsoplysninger (påkrævet til NTLM og Kerberos-godkendelse) til at synkronisere til din Azure AD-lejer. Denne funktionalitet er ikke tilgængelig i tidligere versioner af Azure AD-forbindelse eller med værktøjet ældre DirSync.

Installationsvejledning til Azure AD-forbindelse findes i følgende artikel - [Kom godt i gang med Azure AD-forbindelse](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Aktivér synkronisering af NTLM og Kerberos legitimationsoplysninger hashes til Azure AD
Udføre følgende PowerShell-script på hvert enkelt AD område fuld adgangskode synkroniseres, og Aktivér alle lokale-brugernes legitimationsoplysninger hashes synkronisere til din Azure AD-lejer. Dette script gør det muligt for de legitimationsoplysninger hashes, der kræves til NTLM/Kerberos-godkendelse til at blive synkroniseret til din Azure AD-lejer.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Afhængigt af størrelsen af mappen (antal brugere, grupper osv.), synkronisering af legitimationsoplysninger hashes til Azure AD tager tid. Adgangskoder kan bruges på Azure AD-domænetjenester administrerede domænet umiddelbart efter legitimationsoplysninger hashene er blevet synkroniseret til Azure AD.


<br>

## <a name="related-content"></a>Relateret indhold

- [Aktivere synkronisering af adgangskoder til AAD Domain Services til en skybaseret kun Azure AD-mappe](active-directory-ds-getting-started-password-sync.md)

- [Administrere en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-administer-domain.md)

- [Deltage i en Windows virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-windows-vm.md)

- [Deltage i en Red Hat Enterprise Linux virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
