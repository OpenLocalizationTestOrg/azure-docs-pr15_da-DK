<properties 
    pageTitle="Cloud Services og administration af certifikater | Microsoft Azure" 
    description="Lær at oprette og bruge certifikater med Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Oversigt over certifikater til Azure Cloud Services
Certifikater bruges i Azure skytjenester ([service certifikater](#what-are-service-certificates)) og til godkendelse af med management API ([management certifikater](#what-are-management-certificates) når du bruger Azure klassisk portalen og ikke ARM). Dette emne giver et overblik over begge certifikattyper af, hvordan du kan [oprette](#create) og [installere](#deploy) dem til Azure.

Certifikater, der bruges i Azure er x.509 v3 certifikater og kan være logget af en anden certifikat, der er tillid til, eller de kan være selvsigneret. Et selvsigneret certifikat er signeret af sin egen har oprettet, og på grund af dette, er du ikke har tillid til som standard. De fleste browsere kan ignorere dette. Selvsignerede certifikater skal kun bruges af dig selv ved udvikling og test din skytjenester. 

Certifikater, der bruges af Azure kan indeholde et privat eller en offentlig nøgle. Certifikater, der har en miniature, der giver mulighed for at identificere dem i en utvetydige måde. Denne miniature bruges i Azure [konfigurationsfil](cloud-services-configure-ssl-certificate.md) til at identificere, hvilke certifikat, der skal bruge en skybaseret tjeneste. 

## <a name="what-are-service-certificates"></a>Hvad er tjenesten certifikater?
Tjenesten certifikater er knyttet til skyen tjenester og aktivere sikker kommunikation til og fra tjenesten. Eksempelvis hvis du har installeret en web rolle, vil du vil angive et certifikat, der kan godkende udsatte HTTPS ark. Tjenesten certifikater, som er defineret i tjenestedefinitionen for din, installeres automatisk til den virtuelle maskine, der kører en forekomst af din rolle. 

Du kan overføre service certifikater til Azure klassisk portal, enten ved hjælp af portalen Azure klassisk eller ved at bruge Service Management API. Tjenesten certifikater der er knyttet til en bestemt skybaseret tjeneste og tildelt til en installation i definitionsfil tjeneste.

Tjenesten certifikater kan administreres separat fra dine tjenester og kan administreres af forskellige personer. For eksempel kan en udvikler overføre en tjeneste-pakke, som refererer til et certifikat, som en IT-chef har tidligere blev overført til Azure. En IT-chef kan administrere og forny certifikatet ændre konfigurationen af tjenesten uden at skulle overføre en ny tjeneste-pakke. Dette er muligt, da det logiske navn til certifikatet og dens store navn og placering er angivet i definitionsfil tjenesten, mens certifikat miniature er angivet i filen service konfiguration. Hvis du vil opdatere certifikatet, er det kun nødvendigt at overføre et nyt certifikat og ændre miniatureværdien i tjenesten konfigurationsfil.

## <a name="what-are-management-certificates"></a>Hvad er styring certifikater?
Administration af certifikater gør det muligt at godkende med Service Management API fra Azure klassisk. Mange programmer og værktøjer (såsom Visual Studio eller Azure SDK) bruger disse certifikater til at automatisere konfiguration og installation af forskellige Azure tjenester. Disse er ikke virkelig relateret til skyen tjenester. 

>[AZURE.WARNING] Vær forsigtig! Disse typer certifikater Tillad alle, der godkender med dem til at administrere de er knyttet til abonnementet. 

### <a name="limitations"></a>Begrænsninger
Er der en grænse på 100 management certifikater per abonnement. Der er også en begrænsning på 100 management certifikater for alle abonnementer under en bestemt tjenesteadministratoren bruger-ID. Hvis bruger-ID for kontoadministratoren er allerede blevet brugt til at tilføje 100 management certifikater, og der er behov for flere certifikater, kan du tilføje en samtidig administrator for at tilføje yderligere certifikater. 

Før du tilføjer mere end 100 certifikater, se Hvis kan du genbruge et eksisterende certifikat. Brug af flere administratorer føjer potentielt unødvendige kompleksitet til din proces til styring af certifikat.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Oprette et nyt selvsigneret certifikat
Du kan bruge et værktøj, der er tilgængelige til at oprette et selvsigneret certifikat, som de overholder disse indstillinger:

* Et x.509-certifikat.
* Indeholder en privat nøgle.
* Oprettet for vigtige exchange (.pfx-fil).
* Emnenavn skal svare til det domæne, der bruges til at få adgang til tjenesten skyen. 
    > Du kan ikke hente et SSL-certifikat til cloudapp.net (eller til en hvilken som helst Azure relateret) domæne certifikatets emnenavn skal svare til det brugerdefinerede domænenavn, der bruges til at få adgang til dit program. For eksempel, **contoso.net**, ikke **contoso.cloudapp.net**.
* Minimum af 2048-bit kryptering.
* **Tjenesten certifikat kun**: klientsiden certifikat skal være placeret i den *personlige* lager.

To måder at oprette et certifikat i Windows, med den `makecert.exe` utility eller IIS.

### <a name="makecertexe"></a>MakeCert.exe

Denne funktion frarådes og er ikke længere er beskrevet her. Se [Denne MSDN-artikel](https://msdn.microsoft.com/library/windows/desktop/aa386968) for at få flere oplysninger.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Hvis du vil bruge certifikatet med en IP-adresse i stedet for et domæne, kan du bruge IP-adressen i parameteren - DnsName.


Hvis du vil bruge dette [certifikat med portalen management](../azure-api-management-certs.md), skal du eksportere den til en **.cer** -fil:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Der findes mange sider på internettet, der dækker hvordan du gør dette med IIS. [Her](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) er en god jeg fundet, som jeg tror forklares godt. 

### <a name="java"></a>Java
Du kan bruge Java til at [oprette et certifikat](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[I denne](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) artikel beskrives, hvordan du opretter certifikater med SSH.

## <a name="next-steps"></a>Næste trin

[Overføre din tjenestecertifikat til portalen Azure klassisk](cloud-services-configure-ssl-certificate.md) (eller [Azure portal](cloud-services-configure-ssl-certificate-portal.md)).

Overføre et [management API certifikat](../azure-api-management-certs.md) til portalen Azure klassisk.

>[AZURE.NOTE] Portalen Azure bruger ikke management certifikater til at få adgang til API, men i stedet bruger brugerkonti.
