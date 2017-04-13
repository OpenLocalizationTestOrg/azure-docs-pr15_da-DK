<properties
    pageTitle="Cloud Services ofte stillede spørgsmål om | Microsoft Azure"
    description="Ofte stillede spørgsmål om Cloud Services."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>Cloud Services ofte stillede spørgsmål
I denne artikel finder du svar på nogle ofte stillede spørgsmål om Microsoft Azure Cloud Services. Du kan også gå [Azure understøtter ofte stillede spørgsmål om](http://go.microsoft.com/fwlink/?LinkID=185083) generelle Azure priser og understøttelse af oplysninger. Du kan også se den [side i skyen Services VM størrelse](cloud-services-sizes-specs.md) for størrelsesoplysninger.

## <a name="certificates"></a>Certifikater

### <a name="where-should-i-install-my-certificate"></a>Hvor skal jeg installere mit certifikat?

- **Min**  
Programmet certifikat med privat nøgle (\*.pfx, \*.p12).

- **NØGLECENTER**  
Alle dine mellemliggende certifikater gå i denne store (politik og Sub nøglecentre).

- **ROD**  
Rodnøglecenteret gemme, så din primære rod CA cert skal du gå hertil.

### <a name="i-cant-remove-expired-certificate"></a>Jeg kan ikke fjerne udløbet certifikat

Azure forhindrer dig i at fjerne et certifikat, mens det er i brug. Du skal enten slette den installation, der bruger certifikatet, eller opdatere installationen med et andet eller fornyet certifikat.

### <a name="delete-an-expired-certificate"></a>Slette et udløbet certifikat

Som certifikatet, der ikke er i brug, kan du bruge [Fjern AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell-cmdlet til at fjerne et certifikat.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Jeg har udløbne certifikater navnet Windows Azure Service Management for filtypenavne

Certifikaterne oprettes, når et filtypenavn føjes til skytjenesten som filtypenavnet Fjernskrivebord. Certifikaterne bruges kun til at kryptere og dekryptere privat konfigurationen af filtypenavnet. Det betyder ikke noget, hvis certifikaterne udløber. Udløbsdatoen er ikke markeret.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Certifikater, som jeg har slettet fortsat

Disse fortsat sandsynligvis på grund af et værktøj, du bruger, som Visual Studio. Når du opretter forbindelse igen med et værktøj, der bruger et certifikat, overføres det igen til Azure.

### <a name="my-certificates-keep-disappearing"></a>Holde at forsvinde mine certifikater

Alle lokale ændringer går tabt, når den virtuelle maskine forekomst genbruger. Bruge en [Start opgave](cloud-services-startup-tasks.md) til at installere certifikater til den virtuelle maskine, hver gang rollen starter.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Jeg kan ikke finde mine management certifikater i portalen

[Administration af certifikater](..\azure-api-management-certs.md) er kun tilgængelige i portalen Azure klassisk. Den aktuelle Azure portal bruger ikke management certifikater. 

### <a name="how-can-i-disable-a-management-certificate"></a>Hvordan kan jeg deaktivere et management certifikat?

[Administration af certifikater](..\azure-api-management-certs.md) kan ikke deaktiveres. Du slette dem via Azure klassisk administrationsportalen, når du ikke vil kunne bruges længere.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Hvordan opretter jeg et SSL-certifikat for en bestemt IP-adresse?

Følg vejledningen i [oprette et certifikat selvstudium](cloud-services-certs-create.md). Du kan bruge IP-adressen som DNS-navn.

## <a name="security"></a>Sikkerhed

### <a name="disable-ssl-30"></a>Deaktivere SSL 3.0

For at deaktivere SSL 3.0 og bruge TLS sikkerhed, oprette en Start-opgave, som er beskrevet i dette blogindlæg: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Tilpasse en skybaseret tjeneste

### <a name="i-cannot-scale-beyond-x-instances"></a>Jeg kan ikke tilpasse ud over X forekomster

Abonnementet Azure har en begrænsning på antallet kerner, du kan bruge. Skalering fungerer ikke, hvis du har brugt alle tilgængelige borekerner. Hvis du har en begrænsning på 100 kerner, betyder det f.eks, du kan have 100 A1 tilpasset virtuelt forekomster til skybaseret tjeneste, eller 50 A2 tilpasset virtuelt forekomster.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Jeg kan ikke reservere en IP-adresse i en multi-VIP skybaseret tjeneste

Først skal du sørge for, at den virtuelle maskine forekomst, du forsøger at reservere IP-adresse for er slået til. Andet, Sørg for, at du bruger reserveret IP'er til bekymre dig test- og installationer. **Send ikke** ændre indstillingerne, mens installationen opgradering.

