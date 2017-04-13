<properties
    pageTitle="SSL-certifikater binding ved hjælp af PowerShell"
    description="Lær at binde SSL-certifikater til din online ved hjælp af PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure App Service SSL-certifikat Binding ved hjælp af PowerShell #

Med version af Microsoft Azure PowerShell version 1.1.0 er blevet tilføjet en ny-cmdlet, der giver brugeren mulighed for at binde eksisterende eller nye SSL-certifikater til en eksisterende Web App.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

For at få mere for at vide om brug af Azure ressourcestyring baseret Azure PowerShell kontrollere-cmdlet'er til at administrere din Onlines [Azure ressourcestyring baseret PowerShell-kommandoer til Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Overføre og binder et nyt SSL-certifikat ##

Scenarie: Brugeren vil binde et SSL-certifikat til en af sin webapps.

Viden om ressource gruppenavn, der indeholder WebApp, web app-navn, certifikat .pfx filstien på bruger maskinen, adgangskoden for certifikatet, og de brugerdefinerede hostname, kan vi bruge følgende PowerShell-kommando til at oprette SSL-bindingen:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Bemærk, at du før du tilføjer en SSL-binding til en WebApp, har et værtsnavn (brugerdefinerede domæne) allerede konfigureret. Hvis værtsnavnet ikke er konfigureret, så du får en fejl 'hostname' findes ikke, mens du kører ny AzureRmWebAppSSLBinding. Du kan tilføje et værtsnavn direkte fra portalen eller via Azure PowerShell. Den følgende PowerShell-kodestykke kan være til at konfigurere værtsnavnet før du kører ny AzureRmWebAppSSLBinding.   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
Det er vigtigt at forstå, cmdlet'en Set-AzureRmWebApp til overskriver værtsnavne til WebApp. Den ovenstående PowerShell kodestykke derfor føjes til den eksisterende liste over navnene host for WebApp.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Overføre og binder et eksisterende SSL-certifikat ##

Scenarie: Brugeren vil binde et tidligere overførte SSL-certifikat til en af sin webapps.

Vi kan få vist listen over certifikater, der allerede er overført til en bestemt ressourcegruppe ved hjælp af følgende kommando

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Bemærk, at certifikater er lokale for en bestemt placering og ressourcegruppe, brugeren skal overføre certifikatet igen, hvis der er konfigureret WebApp på en anden placering og ressourcegruppe andre, der nødvendige certifikatet 

Viden om ressource gruppenavn, der indeholder WebApp, web app-navn, certifikat miniature og brugerdefinerede hostname, kan vi bruge følgende PowerShell-kommando til at oprette SSL-bindingen:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Slette en eksisterende SSL-binding  ##

Scenarie: Brugeren vil slette en eksisterende SSL-binding.

Viden om ressource gruppenavn, der indeholder WebApp, web app-navn og den brugerdefinerede hostname, kan vi bruge følgende PowerShell-kommando til at fjerne SSL-bindingen:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Bemærk, at hvis den fjernede SSL-binding blev den sidste binding via det pågældende certifikat i den valgte placering, som standard certifikatet, slettes, hvis brugeren vil beholde det certifikat, han kan bruge indstillingen DeleteCertificate til at holde certifikatet

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Referencer ###
- [Azure ressourcestyring baseret PowerShell-kommandoer til Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Introduktion til App Service-miljø](app-service-app-service-environment-intro.md)
- [Brug af Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)
