<properties
    pageTitle="Azure AD Connect: Ofte stillede spørgsmål om | Microsoft Azure"
    description="Denne side indeholder ofte stillede spørgsmål om Azure AD-forbindelse."
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

# <a name="azure-ad-connect-faq"></a>Azure AD Connect ofte stillede spørgsmål

## <a name="general-installation"></a>Generel installation
**Sp: installation fungerer, hvis Azure AD Global administrator har aktiveret 2FA?**  
Med builds fra februar 2016 understøttes dette.

**Sp: er der en metode til at installere Azure AD-forbindelse, der automatisk?**  
Det understøttes kun for at installere Azure AD-forbindelse ved hjælp af installationsguiden. En automatiseret og uovervåget installation understøttes ikke.

**Sp: Jeg har et område, hvor ét domæne ikke kan kontaktes. Hvordan installerer jeg Azure AD-forbindelse?**  
Med builds fra februar 2016 understøttes dette.

**Sp: AD DS sundhed agent fungerer på server core?**  
Ja. Når du har installeret agenten, kan du udføre registreringsprocessen ved hjælp af de følgende PowerShell cmdlet accepterer: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Netværk
**Sp: Jeg har en firewall, netværksenhed, eller noget andet, der begrænser de maksimale tid forbindelser kan forblive åbne på mit netværk. Hvor længe skal grænseværdi for timeout min klient side være når du bruger Azure AD-forbindelse?**  
Alle netværk software, fysiske enheder eller andet, der begrænser den maksimale tid forbindelser kan være åbne skal bruge en grænseværdi for mindst fem minutter (300 sekunder) for forbindelsen mellem den server, hvor Azure AD-forbindelse klienten er installeret og Azure Active Directory. Dette gælder også for alle tidligere udgivne Microsoft Identity synkronisering værktøjer.

**Sp: er SLD'er (enkelt etiket domæner) understøttes?**  
Nej, Azure AD Connect understøtter ikke lokalt områder/domæner ved hjælp af SLD'er.

**Sp: er "punkteret" NetBios med navnet understøttes?**  
Nej, Azure AD Connect understøtter ikke lokalt områder/domæner hvor NetBIOS-navnet indeholder et punktum "." i navnet.

## <a name="federation"></a>Sammenslutning
**Sp: Hvad gør jeg, hvis jeg modtager en mail, bedt om at forny mit Office 365-certifikat**  
Brug den vejledning, der er beskrevet i emnet [Forny certifikater](active-directory-aadconnect-o365-certs.md) til, hvordan du forny certifikatet.

**Sp: Jeg har "Automatisk at opdatere afhængige part" angive for O365 afhængige part. Skal jeg gøre noget, når min token signeringscertifikat automatisk løber?**  
Brug den vejledning, der er beskrevet i artiklen [Forny certifikater](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Miljø
**Sp: er den understøttet, hvis du vil omdøbe serveren efter Azure AD-forbindelse er blevet installeret?**  
Nej. Ændre navnet på den server, der forårsager Synkroniser programmet ikke skal kunne oprette forbindelse til SQL-databasen og tjenesten kan ikke starte.

## <a name="identity-data"></a>Identitetsdata
**Sp: UPN (userPrincipalName) attributten i Azure AD stemmer ikke overens lokalt UPN - Hvorfor?**  
Se følgende artikler:

- [Brugernavne i Office 365, Azure eller Intune svarer ikke til den lokale UPN eller alternative logon-ID](https://support.microsoft.com/en-us/kb/2523192)
- [Ændringer ikke er synkroniseret med værktøjet Azure Active Directory-synkronisering, når du ændrer UPN for en brugerkonto for at bruge et andet medlem af organisationsnetværket domæne](https://support.microsoft.com/en-us/kb/2669550)

Du kan også konfigurere Azure AD for at tillade Synkroniser program til at opdatere userPrincipalName, som beskrevet i [Azure AD-forbindelse Synkroniser service-funktioner](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Brugerdefineret konfiguration
**Sp: hvor er PowerShell-cmdlet'er til Azure AD-forbindelse dokumenteret?**  
Med undtagelse af cmdlet'erne dokumenteret på dette websted, understøttes ikke andre PowerShell-cmdlet'er, der findes i Azure AD-forbindelse for kundens brug.

* *SP: kan jeg bruge "Server eksport/server importere" er fundet i *synkronisering Service Manager* til at flytte konfiguration mellem servers? **  
Nej. Denne indstilling for at få vist alle indstillinger for søgekonfiguration og der ikke bør benyttes. Du skal i stedet bruge guiden til at oprette den grundlæggende konfiguration på den anden server og bruge synkronisering regeleditor til at generere PowerShell-scripts, hvis du vil flytte en brugerdefineret regel mellem servere. Se [flytte brugerdefineret konfiguration fra aktiv til arrangering server](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
**Sp: Hvordan kan jeg få hjælp til Azure AD-forbindelse?**

[Søge i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Søge i Microsoft Knowledge Base (KB) til tekniske løsninger på almindelige problemer med sideskift fix om understøttelse af Azure AD-forbindelse.

[Microsoft Azure Active Directory-fora](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Du kan søge og gå til tekniske spørgsmål og svar fra community'et eller stille dine egne spørgsmål ved at klikke på [her](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Azure AD-forbindelse kundesupport](https://manage.windowsazure.com/?getsupport=true)

- Brug dette link til at få support via Azure-portalen.
