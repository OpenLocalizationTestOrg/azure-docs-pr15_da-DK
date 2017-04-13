<properties 
    pageTitle="Opgradere PhoneFactor Agent til Azure Multi-Factor Authentication Server"
    description="Dette dokument beskriver, hvordan du kommer i gang med Azure MFA-serveren og Sådan opgraderer du fra ældre phonefactor en agent."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Opgradere PhoneFactor Agent til Azure Multi-Factor Authentication Server

Opgradering fra PhoneFactor Agent v5.x eller ældre til Azure Multi-Factor Authentication serveren kræver, at de PhoneFactor Agent og tilknyttede komponenter fjernes før Multi-Factor Authentication serveren og dens tilknyttede komponenter kan installeres.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Sådan opgraderes PhoneFactor Agent til Azure Multi-Factor Authentication Server
<ol>
<li>Først skal du sikkerhedskopiere PhoneFactor-datafil. Standardplaceringen er c:\Programmer\Microsoft Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Hvis User Portal er installeret:</li>
<ol>
<li>Gå til installationsmappen og sikkerhedskopiere filen web.config. Standardplaceringen er C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Hvis du har tilføjet brugerdefinerede temaer til portalen, skal du sikkerhedskopiere den brugerdefinerede mappe under mappen C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Fjern bruger portalen via PhoneFactor-Agent (kun tilgængelig, hvis installeret på den samme server som PhoneFactor Agent) eller via Windows-programmer og funktioner.</li></ol>




<li>Hvis webtjenesten Mobile-Appen er installeret:
<ol>
<li>Gå til installationsmappen og sikkerhedskopiere filen web.config. Standardplaceringen er C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Fjerne Mobile App webtjenesten gennem Windows programmer og funktioner.</li></ol>

<li>Hvis Web Service SDK er installeret, skal du fjerne det, enten via PhoneFactor Agent eller via Windows-programmer og funktioner.

<li>Fjerne PhoneFactor Agent gennem Windows programmer og funktioner.

<li>Installere Multi-Factor Authentication Server. Bemærk, at installationsstien er valgte fra registreringsdatabasen fra den tidligere PhoneFactor Agent-installation, så det skal installere på samme placering (fx C:\Program Files\PhoneFactor). Nye installationer har forskellige standard installere sti (fx C:\Program Files\Multi-faktor-godkendelsesserver). Den datafil, der er tilbage efter den forrige PhoneFactor Agent skal opgraderes under installationen, så dine brugere og indstillinger skal stadig være der når du har installeret den nye multi-Factor Authentication Server.

<li>Hvis du bliver bedt om det, kan du aktivere Multi-Factor Authentication Server og sikre, at den er tildelt til gruppen korrekte gentagelse.

<li>Hvis Web Service SDK var tidligere installeret, skal du installere det nye Web Service SDK via brugergrænsefladen Multi-Factor Authentication Server. Bemærk, at navnet på den standard virtuelle mappe er nu "MultiFactorAuthWebServiceSdk" i stedet for "PhoneFactorWebServiceSdk". Hvis du vil bruge det tidligere navn, skal du ændre navnet på den virtuelle mappe under installationen. Ellers, hvis du tillader Installer for at bruge det nye standardnavn, har du ændre URL-adresse i alle programmer, som henviser til Web Service SDK som User Portal og Mobile App-webtjeneste til at pege på den korrekte placering.

<li>Hvis portalen bruger blev tidligere installeret på PhoneFactor Agent Server, skal du installere den nye multi-Factor Authentication bruger Portal via brugergrænsefladen Multi-Factor Authentication Server. Bemærk, at navnet på den standard virtuelle mappe er nu "MultiFactorAuth" i stedet for "PhoneFactor". Hvis du vil bruge det tidligere navn, skal du ændre navnet på den virtuelle mappe under installationen. Ellers, hvis du tillader Installer for at bruge det nye standardnavn, skal du klikke på ikonet User Portal i Multi-Factor Authentication Server og opdatere bruger Portal URL-adressen på fanen Indstillinger.

<li>Hvis User Portal og/eller Mobile App-webtjeneste tidligere installeret på en anden server fra en Agent for PhoneFactor:
<ol>
<li>Gå til installationsplacering (fx C:\Program Files\PhoneFactor), og Kopiér den relevante installer(s) til anden serveren. Der er 32-bit og 64-bit Installer til både feltet User Portal og Mobile App-webtjeneste. De kaldes MultiFactorAuthenticationUserPortalSetupXX.msi og MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi henholdsvis.</li>
<li>Åbn en kommandoprompt som administrator for at installere User Portal på webserveren, og kør MultiFactorAuthenticationUserPortalSetupXX.msi. Bemærk, at navnet på den standard virtuelle mappe er nu "MultiFactorAuth" i stedet for "PhoneFactor". Hvis du vil bruge det tidligere navn, skal du ændre navnet på den virtuelle mappe under installationen. Ellers, hvis du tillader Installer for at bruge det nye standardnavn, skal du klikke på ikonet User Portal i Multi-Factor Authentication Server og opdatere bruger Portal URL-adressen på fanen Indstillinger. Eksisterende brugere skal informeres om den nye URL-adresse.</li>
<li>Gå til portalen bruger installationsplacering (fx C:\inetpub\wwwroot\MultiFactorAuth) og redigere filen web.config. Kopiér værdierne i sektionerne appSettings og applicationSettings fra den oprindelige web.config-fil, der blev sikkerhedskopieret før opgraderingen til den nye web.config-fil. Hvis navnet på den nye standard virtuelle mappe blev bevares, når du installerer Web Service SDK, kan du ændre URL-adressen i sektionen applicationSettings til at pege på den korrekte placering. Hvis andre standardindstillinger er blevet ændret i den forrige web.config-fil, kan du anvende de samme ændringer til den nye web.config-fil.</li>
<li>Åbn en kommandoprompt som administrator for at installere den Mobile App webtjeneste på webserveren, og kør MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Bemærk, at navnet på den standard virtuelle mappe er nu "MultiFactorAuthMobileAppWebService" i stedet for "PhoneFactorPhoneAppWebService". Hvis du vil bruge det tidligere navn, skal du ændre navnet på den virtuelle mappe under installationen. Du vil vælge et kortere navn, der gør det nemt for slutbrugere at skrive på på deres mobilenheder. Ellers, hvis du tillader Installer for at bruge det nye standardnavn, skal du klikke på ikonet Mobile-App i Multi-Factor Authentication Server og opdatere den Mobile App Service URL-adresse.</li>
<li>Gå til Mobile-App-webtjenesten installationsplacering (fx C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) og redigere filen web.config. Kopiér værdierne i sektionerne appSettings og applicationSettings fra den oprindelige web.config-fil, der blev sikkerhedskopieret før opgraderingen til den nye web.config-fil. Hvis navnet på den nye standard virtuelle mappe blev bevares, når du installerer Web Service SDK, kan du ændre URL-adressen i sektionen applicationSettings til at pege på den korrekte placering. Hvis andre standardindstillinger er blevet ændret i den forrige web.config-fil, kan du anvende de samme ændringer til den nye web.config-fil.</li></ol>
