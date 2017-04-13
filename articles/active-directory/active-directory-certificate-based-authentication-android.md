<properties 
    pageTitle="Komme i gang med baseret certifikatgodkendelse på Android | Microsoft Azure" 
    description="Lær, hvordan du kan konfigurere baseret certifikatgodkendelse i løsninger med Android-enheder" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-android---public-preview"></a>Komme i gang med baseret certifikatgodkendelse på Android - prøveversionen  

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Dette emne viser, hvordan du konfigurere og bruge baseret certifikatgodkendelse (CBA) på en Android-enhed for brugere af lejere i Office 365 Enterprise, Business og Education-planer. 

CBA gør det muligt at være bekræftet af Azure Active Directory med et klientcertifikat på en Android- eller iOS-enhed, når du opretter forbindelse til din Exchange online-konto til at: 

- Office mobile programmer som Microsoft Outlook og Microsoft Word   
- Exchange ActiveSync (EAS)-klienter 

Konfiguration af funktionen behovet for at angive en kombination af brugernavn og din adgangskode til bestemte mail- og Microsoft Office-programmer på din mobilenhed. 
 

## <a name="supported-scenarios-and-requirements"></a>Understøttede scenarier og krav  



### <a name="general-requirements"></a>Generelle krav 


For alle scenarier i dette emne der følgende opgaver kræves:  

- Adgang til certifikat authority(s) til at udstede klient certifikater.  

- Certifikater authority(s) skal konfigureres i Azure Active Directory. Du kan finde detaljerede trin til at fuldføre konfigurationen i afsnittet [Introduktion](#getting-started) .  

- Rodnøglecenter og en mellemliggende nøglecentre skal konfigureres i Azure Active Directory.  

- Hver nøglecenter skal have en liste over tilbagekaldte (liste over tilbagekaldte certifikater), der skal refereres til via internettet modstående URL-adresse.  

- Klientcertifikat skal være udstedt til klientgodkendelse.  


- Kun Exchange ActiveSync-klienter, skal klientcertifikat have brugerens distribueret mailadresse i Exchange online i den hovednavn eller værdien RFC822 navn i feltet emne alternativ navn. Azure Active Directory knytter værdien RFC822 til attributten proxyadresse i kataloget.  



### <a name="office-mobile-applications-support"></a>Support til Office mobile-programmer 

| Apps                      | Support      |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![Afkrydsningsfelt][1]  |
| OneNote                   | Kommer snart  |
| OneDrive                  | ![Afkrydsningsfelt][1]  |
| Outlook                   | ![Afkrydsningsfelt][1]  |
| Yammer                    | ![Afkrydsningsfelt][1]  |
| Skype for Business        | ![Afkrydsningsfelt][1]  |


### <a name="requirements"></a>Krav  

Enhed OS versionen skal være Android 5.0 (Slikkepind) og derover. 

En sammenslutningsserver skal være konfigureret.  


Azure Active Directory ophæve et klientcertifikat, skal ADFS tokenet have følgende krav:  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(Serienummeret for klientcertifikatet) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(Streng til klienten certifikatets udsteder) 

Azure Active Directory tilføjer disse krav til token opdatering, hvis de er tilgængelige i ADFS tokenet (eller andre SAML-token). Når Opdater tokenet skal være valideret, bruges disse oplysninger til at kontrollere tilbagekaldelsen. 

Som en bedste fremgangsmåde, skal du opdatere fejlsider ADFS med oplysninger om, hvordan du kan få et brugercertifikat. 

Få mere at vide under [tilpasse siderne AD FS-logon](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Understøtter Exchange ActiveSync-klienter 


Visse Exchange ActiveSync-programmer på Android 5.0 (Slikkepind) eller nyere, understøttes. Hvis du vil finde ud af, hvis dit mailprogram understøtter denne funktion, skal du kontakte dit program udvikler. 



## <a name="getting-started"></a>Kom godt i gang 


For at komme i gang, skal du konfigurere nøglecentre i Azure Active Directory. For hver nøglecenter overføre følgende: 

- Den offentlige del af certifikatet, i *.cer* -format 

- Internettet modstående URL-adresser, hvor de lister over tilbagekaldte (lister over tilbagekaldte certifikater) er placeret
 

Nedenfor vises skemaet for et nøglecenter: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Hvis du vil overføre oplysninger, kan du bruge Azure AD-modulet til Windows PowerShell.  
Nedenfor finder du eksempler for at tilføje, fjerne eller ændre et nøglecenter. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Konfigurere din Azure AD-lejer for certifikat baseret på godkendelse 

1. Start Windows PowerShell med administratorrettigheder. 

2. Installer Azure AD-modulet. Du skal installere Version [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) eller nyere.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Oprette forbindelse til din mållejeren: 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Tilføje en ny nøglecenter

1. Angive forskellige egenskaber for nøglecenteret og tildele den til Azure Active Directory: 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Få nøglecentre: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Hente nøglecentre liste

Hente de nøglecentre, der aktuelt er gemt i Azure Active Directory for din lejer: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Fjerne et nøglecenter

1.  Hente nøglecentre: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Fjerne certifikatet for nøglecenteret: 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying et nøglecenter 

1.  Hente nøglecentre: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Ændre egenskaber på nøglecenteret: 

        $c[0].AuthorityType=1 

3. Angiv den **nøglecenter**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Test af Office mobile-programmer  

Teste certifikatgodkendelse på din mobile Office-program: 

1.  Installere et mobile Office-program (f.eks. OneDrive) fra Google Play Store på enheden test.

2.  Kontrollér, at brugercertifikatet er klargjort til enheden test. 

3.  Start app'en. 

4.  Angiv dit brugernavn, og vælg derefter det brugercertifikat, du vil bruge. 

Du skal være logget på. 





## <a name="testing-exchange-activesync-client-applications"></a>Test af Exchange ActiveSync-klientprogrammer

For at få adgang til Exchange ActiveSync via baseret certifikatgodkendelse, skal en EAS-profil, der indeholder klientcertifikatet være tilgængelig for program. EAS profilen skal indeholde følgende oplysninger:

- Brugercertifikatet, der skal bruges til godkendelse 

- EAS-slutpunkt skal være outlook.office365.com (som denne funktion understøttes i øjeblikket kun i Exchange online med flere lejer miljø)

En EAS-profil kan være konfigureret og, som anbringes på enheden via anvendelsen af en MDM som Intune eller ved at placere manuelt certifikatet i profilen EAS på enheden.  


### <a name="testing-eas-client-applications-on-android"></a>Test EAS-klientprogrammer på Android-enhed 

Udfør nedenstående trin for at teste certifikatgodkendelse med et program under Android 5.0 (Slikkepind) eller nyere:  

1. Konfigurere en EAS-profil i det program, der opfylder kravene ovenfor.  


2. Når profilen, der er konfigureret korrekt, Åbn programmet, og Bekræft, at synkronisering af mail. 




## <a name="revocation"></a>Tilbagekaldte certifikater

Hvis du vil ophæve et klientcertifikat, Azure Active Directory henter listen over tilbagekaldte certifikater (liste over tilbagekaldte certifikater) fra de URL-adresser, der er overført som en del af oplysninger om nøglecenter og gemmer den. Sidst publicere tidsstempel (**Ikrafttrædelsesdato** egenskaben) i den liste over tilbagekaldte certifikater bruges til at sikre, at den liste over tilbagekaldte certifikater er stadig er gyldig. LISTEN med jævne mellemrum refereres til for at tilbagekalde adgang til certifikater, der er en del af listen.

Hvis flere Chat tilbagekaldelsen er påkrævet (for eksempel, hvis en bruger mister en enhed), kan være ugyldiggjorte tokenet godkendelse af brugeren. For at ødelægge godkendelse tokenet, angive feltet **StsRefreshTokenValidFrom** for denne bestemt bruger, der bruger Windows PowerShell. Du skal opdatere feltet **StsRefreshTokenValidFrom** for hver bruger, du vil tilbagekalde adgang til.
 
For at sikre, at en tilbagekaldelse fortsætter, skal du angive den **Effektive dato** i den liste over tilbagekaldte certifikater til en dato, efter værdien, der er angivet af **StsRefreshTokenValidFrom** og sikre certifikatet pågældende er i den liste over tilbagekaldte certifikater.
 
De følgende trin skitserer processen for at opdatere, og gør ugyldig godkendelse tokenet ved at angive feltet **StsRefreshTokenValidFrom** . 

1. Oprette forbindelse til legitimationsoplysninger til tjenesten MSOL: 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Hente den aktuelle StsRefreshTokensValidFrom værdi for en bruger: 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Konfigurere en ny StsRefreshTokensValidFrom værdi for bruger, der svarer til det aktuelle tidsstempel: 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


Den dato, du angiver skal være i fremtiden. Hvis datoen, der ikke er i fremtiden, er egenskaben **StsRefreshTokensValidFrom** ikke angivet. Hvis datoen, der er på et senere tidspunkt, er **StsRefreshTokensValidFrom** angivet til det aktuelle klokkeslæt (ikke den angivne dato ved at angive-MsolUser kommandoen). 


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png