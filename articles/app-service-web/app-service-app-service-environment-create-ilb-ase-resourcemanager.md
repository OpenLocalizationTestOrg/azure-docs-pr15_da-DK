<properties 
    pageTitle="Sådan oprettes en ILB ASE ved hjælp af Azure ressourcestyring skabeloner | Microsoft Azure" 
    description="Få mere at vide, hvordan du opretter en intern Indlæs belastningsjusteringstjenesten ASE, ved hjælp af Azure ressourcestyring skabeloner." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Sådan oprettes en ILB ASE ved hjælp af Azure ressourcestyring skabeloner

## <a name="overview"></a>Oversigt ##
App Service miljøer kan oprettes med et virtuelt netværk interne adresse i stedet for en offentlig VIP.  Denne interne adresse er angivet af en Azure komponent kaldet den interne justering af belastning (ILB).  En ILB ASE kan oprettes ved hjælp af portalen Azure.  Det kan også oprettes med automation ved hjælp af Azure ressourcestyring skabeloner.  I denne artikel vejledes gennem de trin og syntaks, der bruges til at oprette en ILB ASE med Azure ressourcestyring skabeloner.

Der er tre trin involveret i automatisere oprettelse af en ILB ASE:
1. Den grundlæggende ASE oprettes først i et virtuelt netværk ved hjælp af en intern Indlæs belastningsjusteringstjenesten adresse i stedet for en offentlig VIP.  Som en del af dette trin skal tildeles en rod domænenavn til ILB ASE.
2. Når ILB ASE er oprettet, er det et SSL-certifikat, der overføres.  
3. Det overførte SSL-certifikat tildeles eksplicit ILB ASE som dens "standard" SSL-certifikat.  Dette SSL-certifikat, der skal bruges til SSL-trafik til apps på ILB ASE, når apps er rettet ved hjælp af almindelige roddomænet tildelt ASE (fx https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Oprette Base ILB ASE ##
Et eksempel Azure ressourcestyring skabelon og dens tilknyttede parametre filer er tilgængelige på GitHub [her][quickstartilbasecreate].

De fleste af parametrene i filen *azuredeploy.parameters.json* er fælles for oprettelse af både ILB ASEs samt ASEs, der er bundet til en offentlig VIP.  På listen herunder opkald ud-parametre i speciel note, eller som er entydige, når du opretter en ILB ASE:


- *interalLoadBalancingMode*: I de fleste tilfælde vil konfigurere det til 3, hvilket betyder både HTTP/HTTPS trafik på porte 80/443 og kontrolelementet/data kanal porte lyttet til FTP-tjenesten på ASE, være bundet til en ILB, der er allokeret virtuelt netværk interne adresse.  Hvis denne egenskab i stedet er indstillet til 2, derefter relateret kun sek porte (både kontrolelementet og data kanaler) er bundet til en adresse, ILB, mens HTTP/HTTPS-trafik forbliver på det offentlige VIP.
-  *dnsSuffix*: denne parameter definerer roddomænet standard, der skal tildeles til ASE.  I den offentlige variation Azure App-tjenesten er rod standarddomænet for alle Onlines *azurewebsites.net*.  Men da en ILB ASE er interne til en kundes virtuelt netværk, det ikke mening at bruge tjenesten offentlige standard roddomænet.  I stedet for har en ILB ASE en standard-roddomæne, der giver mening til brug i virksomhedens interne virtuelt netværk.  En hypotetiske Contoso Corporation kan for eksempel bruge et standard roddomæne af *interne contoso.com* til apps, der er beregnet til kun at være løsning og tilgængelige i Contosos virtuelt netværk. 
-  *ipSslAddressCount*: denne parameter hentes automatisk til en værdi på 0 i filen *azuredeploy.json* , fordi ILB ASEs kun har en enkelt ILB adresse.  Der er ingen eksplicitte IP-SSL-adresser for en ILB ASE, og derfor IP-SSL adresse puljen for en ILB ASE skal være angivet til nul, ellers fejlen klargøring skal forekomme. 

Når filen *azuredeploy.parameters.json* er blevet udfyldt til en ILB ASE, kan ILB ASE derefter oprettes med følgende Powershell kodestykke.  Ændre filen stier til at matche hvor skabelonfiler Azure ressourcestyring er placeret på din computer.  Husk også at angive dine egne værdier for Azure ressourcestyring installation navn og ressource gruppenavn.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Efter Azure ressourcestyring skabelon sendes det kan tage et par timer for ILB ASE skal oprettes.  Når oprettelsen er fuldført, vises ILB ASE i portalen UX på listen over App Service miljøer for det abonnement, som udløste installationen.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Overføre og konfiguration af "Standard" SSL-certifikat ##

Når ILB ASE er oprettet, skal et SSL-certifikat være knyttet til ASE, som bruger "standard" SSL-certifikat til at oprette SSL-forbindelser til apps.  Fortsætter med hypotetiske Contoso Corporation eksempel, hvis ASE standard DNS suffiks er *interne contoso.com*, og klik derefter en forbindelse til *https://some-random-app.internal-contoso.com* kræver et SSL-certifikat, der er gyldige for **.internal contoso.com*. 

Der er flere forskellige måder at få et gyldigt SSL-certifikat, herunder interne nøglecentre, købe et certifikat fra en ekstern udsteder, og brug af et selvsigneret certifikat.  Uanset kilden til SSL-certifikatet skal følgende attributter certifikat være konfigureret korrekt:

- *Emne*: denne attribut skal være indstillet til **.your-rod-domæne-here.com*
- *Alternativt emnenavn*: denne attribut skal indeholde begge * *.your-rod-domæne-here.com*og * *.scm.your-rod-domæne-here.com*.  Årsagen til den anden post er, at SSL-forbindelser til webstedet SCM/Kudu, der er knyttet til hver app foretages via en adresse på formular *your-app-name.scm.your-root-domain-here.com*.

Med et gyldigt SSL-certifikat i hånd, der er nødvendige for to ekstra forberedende trin.  SSL-certifikat skal være konverteret/gemmes som en .pfx-fil.  Husk, at .pfx-filen skal medtage alle mellemliggende og root certifikater, og også skal være fastgjort med en adgangskode.

Den resulterende .pfx-fil skal konverteres til en base64-streng, fordi SSL-certifikatet overføres ved hjælp af en skabelon til Azure ressourcestyring.  Fordi Azure ressourcestyring skabeloner er tekstfiler, skal konverteres til en base64-streng, så det kan være angivet som en parameter af skabelonen .pfx-fil.

Kodestykke Powershell nedenfor viser et eksempel på oprettelse af et selvsigneret certifikat, eksporterer certifikatet som en .pfx-fil, konvertering .pfx-fil til en base64-kodet streng, og derefter gemme base64-kodet streng i en separat fil.  Powershell-koden for base64 kodning er baseret på [Powershell-Scripts, Blog][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Når SSL-certifikatet er blevet oprettet og konverteret til en base64-kodet streng, skabelonen eksempel Azure Ressourcestyring på GitHub til [konfiguration af standard-SSL-certifikat] [ configuringDefaultSSLCertificate] kan bruges.

Parametre i filen *azuredeploy.parameters.json* vises nedenfor:

- *appServiceEnvironmentName*: navnet på den ILB ASE konfigureres.
- *existingAseLocation*: tekststreng, der indeholder det Azure område, hvor ILB ASE blev installeret.  For eksempel: "Syd Central os".
- *pfxBlobString*: based64 kodet strengrepræsentation af .pfx-fil.  Ved hjælp af kodestykket vises tidligere, skal du kopiere den streng, der er indeholdt i "exportedcert.pfx.b64" og indsætte det i denne værdi for attributten *pfxBlobString* .
- *adgangskode*: adgangskoden bruges til at sikre .pfx-fil.
- *certificateThumbprint*: certifikatets miniature.  Hvis du henter denne værdi fra Powershell (fx *$certificate. Miniature* fra den tidligere kodestykke), kan du bruge værdien som-er.  Men hvis du kopierer værdien fra dialogboksen Windows certifikat, du huske at udelade overflødige mellemrum.  *CertificateThumbprint* skal ligne: AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: et brugervenligt streng id efter eget valg, der bruges til at identitet certifikatet.  Navnet bruges som en del af den entydige identifikator Azure Ressourcestyring for objektet *Microsoft.Web/certificates* , der repræsenterer SSL-certifikatet.  Navnet **skal** afsluttes med følgende suffikset: \_yourASENameHere_InternalLoadBalancingASE.  Denne suffiks bruges af portalen som en indikator, at certifikatet, der bruges til sikring af en ILB aktiverede ASE.


Et forkortet eksempel på *azuredeploy.parameters.json* er vist nedenfor:


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Når filen *azuredeploy.parameters.json* er blevet udfyldt, kan standard-SSL-certifikatet konfigureres ved hjælp af følgende Powershell kodestykke.  Ændre filen stier til at matche hvor skabelonfiler Azure ressourcestyring er placeret på din computer.  Husk også at angive dine egne værdier for Azure ressourcestyring installation navn og ressource gruppenavn.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Når skabelonen Azure ressourceleder, der er sendt tager det omkring 40 minutter minutter per ASE front end at anvende ændringen.  For eksempel med en standard tilpasset ASE ved hjælp af to forside-ender, tager skabelonen omkring en time og 20 minutter for at gennemføre.  Mens der kører skabelonen kan ASE ikke skaleret.  

Når skabelonen er fuldført, kan få adgang til apps på ILB ASE over HTTPS og forbindelserne er sikret ved hjælp af standard-SSL-certifikat.  Standard SSL-certifikat, der skal bruges, når apps på ILB ASE er rettet ved hjælp af en kombination af navnet på programmet plus standard hostname.  For eksempel *https://mycustomapp.internal-contoso.com* ville bruge standard-SSL-certifikat til **.internal contoso.com*.

Men ligesom apps, der kører på tjenesten offentlige med flere arkitekturer, udviklere kan også konfigurere brugerdefinerede host navne for individuelle apps, og derefter konfigurere entydige SNI SSL-certifikat bindinger for individuelle apps.  


## <a name="getting-started"></a>Kom godt i gang

For at komme i gang med App-tjenesten miljøer skal du se [Introduktion til App Service-miljø](app-service-app-service-environment-intro.md)

Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
