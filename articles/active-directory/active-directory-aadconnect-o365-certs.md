<properties
    pageTitle="Fornyelse vejledning til Office 365 og Azure Active Directory-brugere af certifikat | Microsoft Azure"
    description="Denne artikel forklares til Office 365-brugere at løse problemer med de mails, minde dem om at forny et certifikat."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Forny sammenslutning certifikater til Office 365 og Azure Active Directory

##<a name="overview"></a>Oversigt

Du opnår vellykket sammenslutning mellem Azure Active Directory (Azure AD) og Active Directory Federation Services (AD FS) stemmer certifikater bruges af AD FS logge sikkerhedstokens til Azure AD overens Hvad er konfigureret i Azure AD. En hvilken som helst uoverensstemmelse kan føre til brudt sikkerhed og rettighedsadministration. Azure AD sikrer, at oplysningerne synkroniseres når du installerer ADFS- og Web proxyen (for ekstranet adgang).

Denne artikel finder du yderligere oplysninger for at administrere din token logge certifikater og holde dem synkroniseret med Azure AD i følgende tilfælde:

* Du installerer ikke webproxyen, og derfor sammenslutning metadata er ikke tilgængelig i den ekstranet.
* Du bruger ikke standardkonfigurationen af AD FS for at logge certifikater-id.
* Du bruger en tredjeparts-identitetsudbyder.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standardkonfiguration af AD FS for at logge certifikater-id

Token logge og token dekryptere certifikater er normalt selvsignerede certifikater, og er velegnet til et år. AD FS omfatter som standard en automatisk fornyelse proces, der kaldes **AutoCertificateRollover**. Hvis du bruger AD FS 2.0 eller nyere, Office 365 og Azure AD automatisk opdatere certifikatet, før det udløber.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Meddelelse om fornyelse fra Office 365-portalen eller en mail

>[AZURE.NOTE] Hvis du har modtaget en mail eller en portalen meddelelse beder dig om at forny dit certifikat til Office, skal du se [administrere ændres til token logge certifikater](#managecerts) til at kontrollere, hvis du vil foretage dig noget. Microsoft er bekendt med en mulig problem, der kan medføre beskeder om Certifikatfornyelse blive sendt, selvom ingen handling er påkrævet.

Azure AD forsøger at overvåge sammenslutning metadata, og Opdater tokenet logge certifikater, som angivet af disse metadata. 30 dage inden udløb af tokenet logge certifikater, kontrollerer Azure AD, om nye certifikater er tilgængelige ved at forespørge sammenslutning metadata.

* Hvis det kan korrekt afstemning sammenslutning metadataene og hente de nye certifikater, er ingen mailmeddelelse eller advarsel i Office 365-portalen udstedt til brugeren.
* Hvis den ikke kan hente det nye token logge certifikater, udsteder enten fordi sammenslutning metadata kan ikke nås eller automatisk certifikat overgang ikke er aktiveret, Azure AD besked via mail og en advarsel i Office 365-portalen.

![Office 365-portalen meddelelse](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Hvis du bruger AD FS, til at sikre Forretningskontinuitet, skal du kontrollere, at serverne har følgende opdateringer, så mislykkede forsøg på godkendelse til kendte problemer ikke vises. Dette afhjælper kendte AD FS-proxy serverproblemer for denne fornyelse og fremtidige fornyelse perioder:
>
>Server 2012 R2 - [Windows Server opsummering af maj 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 og 2012 - [godkendelse gennem proxy mislykkes i Windows Server 2012 eller Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## Kontroller om certifikater skal opdateres<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Trin 1: Kontrollér tilstanden AutoCertificateRollover

Åbn PowerShell på din ADFS-server. Kontrollér, at værdien AutoCertificateRollover er indstillet til sand.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Hvis du bruger AD FS 2.0 skal køre først Tilføj Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Trin 2: Bekræfte, at AD FS og Azure AD er synkroniseret

Åbn Azure AD PowerShell-prompt, og oprette forbindelse til Azure AD på din ADFS-server.

>[AZURE.NOTE] Du kan hente Azure AD PowerShell [her](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Markér de certifikater, der er konfigureret i AD FS og Azure AD, der er tillid til egenskaberne for det angivne domæne.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Hvis thumbprints i begge lagrer stemmer overens, er certifikaterne synkroniseret med Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Trin 3: Kontrollér, om dit certifikat vises ved at udløbe

Output fra Get-MsolFederationProperty eller Get-AdfsCertificate, Markér for dato under "Ikke efter." Hvis datoen, der er mindre end 30 dage ikke til stede, skal du tage handling.

| AutoCertificateRollover | Certifikater, der er synkroniseret med Azure AD | Sammenslutning metadata er offentligt tilgængelig | Gyldighed | Handling |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Ja | Ja | Ja | - | Ingen handling påkrævet. Se [Forny token signeringscertifikat automatisk](#autorenew). |
| Ja | Nej  | - | Mindre end 15 dage | Forny med det samme. Se [Forny token signeringscertifikat manuelt](#manualrenew). |
| Nej | - | - | Mindre end 30 dage | Forny med det samme. Se [Forny token signeringscertifikat manuelt](#manualrenew). |

\[-] Betyder ikke noget

## Forny Tokenet signeringscertifikat automatisk (anbefales)<a name="autorenew"></a>

Du behøver ikke at udføre en hvilken som helst manuelle trin, hvis begge af følgende er sande:
- Du har installeret Web proxyen, som kan aktivere adgang til sammenslutning metadata fra den ekstranet.
- Du bruger AD FS standardkonfigurationen (AutoCertificateRollover er aktiveret).

Kontrollér følgende for at bekræfte, at certifikatet, der kan opdateres automatisk.

**1. AD FS egenskaben AutoCertificateRollover skal være indstillet til sand.** Dette angiver, at AD FS genereres automatisk nye token logge og token dekryptering certifikater, før gammelt dem udløbe.

**2. AD FS sammenslutning metadataene er offentligt tilgængelig.** Kontrollér, at metadata for din sammenslutning er offentligt tilgængelig ved at navigere til følgende URL-adressen fra en computer på offentlige internettet (fra virksomhedens netværk):


https:// (your_FS_name) /federationmetadata/2007-06/federationmetadata.xml

hvor `(your_FS_name) `erstattes med din organisation bruger, som fs.contoso.com sammenslutning service host name.  Hvis du er i stand til at kontrollere begge af disse indstillinger korrekt, du ikke behøver at gøre noget andet.  

Eksempel: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Forny Tokenet signeringscertifikat manuelt<a name="manualrenew"></a>

Du kan vælge at forny tokenet logge certifikater manuelt. For eksempel kan følgende scenarier fungere bedre til manuel fornyelse:
* Token logge certifikater er ikke selvsignerede certifikater. Den mest almindelige årsag til dette er, at din organisation administrerer AD FS-certifikater, der er registreret fra en organisatoriske nøglecenter.
* Netværkssikkerhed tillader ikke metadata, sammenslutning skal være offentligt tilgængelige.

I disse scenarier, hver gang du opdaterer tokenet logge certifikater, skal du også opdatere din Office 365-domæne ved hjælp af PowerShell-kommandoen Opdater MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Trin 1: Sørg for, at AD FS har nye token logge certifikater

**Ikke-standard-konfiguration**

Hvis du bruger en ikke-standard-konfiguration af AD FS (hvor **AutoCertificateRollover** er indstillet til **Falsk**), bruger du sandsynligvis brugerdefinerede certifikater (ikke selvsigneret). Du kan finde flere oplysninger om, hvordan du kan forny AD FS tokenet logge certifikater, kan du se [vejledning til kunder, der ikke bruger AD FS selvsigneret certifikater](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Sammenslutning metadata er ikke offentligt tilgængeligt**

På den anden side, hvis **AutoCertificateRollover** er indstillet til **Sand**, men metadata for din sammenslutning ikke er offentligt tilgængelig, først sørge for, at nye token signerende certifikater har dannet af AD FS. Bekræfte, at du har nye token logge certifikater ved at gøre følgende:

1. Kontrollér, at du er logget den primære AD FS-server.
2. Du kan kontrollere de aktuelle signerende certifikater i AD FS ved at åbne en PowerShell-kommandovindue og køre følgende kommando:

    PS C:\>Get-ADFSCertificate – CertificateType signering med token

    >[AZURE.NOTE] Hvis du bruger AD FS 2.0, skal du først køre Tilføj Pssnapin Microsoft.Adfs.Powershell.

3. Se på kommando-output på en hvilken som helst certifikater, der er angivet. Hvis AD FS har oprettet et nyt certifikat, skal du se to certifikater i output: en hvor værdien **IsPrimary** er **Sand** og **NotAfter** datoen er inden for 5 dage, og en hvor **IsPrimary** er **Falsk** , og **NotAfter** er om året i fremtiden.

4. Hvis du kun vist et certifikat, og **NotAfter** datoen er inden for 5 dage, skal du oprette et nyt certifikat.

5. For at oprette et nyt certifikat, skal du udføre følgende kommando kommandoprompt PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Bekræfte opdateringen ved at køre følgende kommando igen: PS C:\>Get-ADFSCertificate – CertificateType signering med token

To certifikater, der skal vises nu, hvoraf den ene har en **NotAfter** dato af cirka ét år på et senere tidspunkt, og hvor værdien **IsPrimary** er **Falsk**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Trin 2: Opdatere nye tokenet logge certifikater til Office 365 trust

Opdatere Office 365 med nye tokenet logge certifikater, der bruges til Hav tillid til, på følgende måde.

1.  Åbn Microsoft Azure Active Directory-modul til Windows PowerShell.
2.  Køre $cred = Get-legitimationsoplysninger. Når denne cmdlet beder dig om legitimationsoplysninger, skal du indtaste dine skyen tjenesten administrator kontolegitimationsoplysninger.
3.  Køre forbinde MsolService – $cred af legitimationsoplysninger. Denne cmdlet opretter forbindelse til skytjenesten. Oprette en kontekst, der tilsluttes skytjenesten er påkrævet, før du kører en af de ekstra cmdletter, der er installeret af værktøjet.
4.  Hvis du kører disse kommandoer på en computer, der ikke er den primære AD FS-sammenslutningsserver, skal du køre sæt MSOLAdfscontext-Computer <AD FS primary server>, hvor <AD FS primary server> er det interne fulde navn på den primære AD FS-server. Denne cmdlet opretter en kontekst, der tilsluttes AD FS.
5.  Køre opdatering MSOLFederatedDomain – DomainName <domain>. Denne cmdlet opdaterer indstillingerne fra AD FS i skytjenesten og konfigurerer tillidsforhold mellem to.


>[AZURE.NOTE] Hvis du har brug at understøtte flere topdomæner, som contoso.com og fabrikam.com, skal du bruge parameteren **SupportMultipleDomain** med en hvilken som helst cmdletter. Du kan finde yderligere oplysninger finder [understøttelse af flere domæner for øverste niveau](active-directory-aadconnect-multiple-domains.md).

## Reparere Azure AD tillid ved hjælp af Azure AD-forbindelse<a name="connectrenew"></a>

Hvis du har konfigureret din AD FS-farm og Azure AD tillid ved hjælp af Azure AD-forbindelse, kan du bruge Azure AD-forbindelse til at registrere, hvis du vil foretage dig noget for din token logge certifikater. Hvis du har brug at forny certifikater, kan du bruge Azure AD-forbindelse til at gøre dette.

Se [reparere tilliden](./active-directory-aadconnect-federation-management.md#repairing-the-trust)kan finde flere oplysninger.
