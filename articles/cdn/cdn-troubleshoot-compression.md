<properties
    pageTitle="Fejlfinding i forbindelse med komprimering i Azure CDN | Microsoft Azure"
    description="Foretage fejlfinding af problemer med Azure CDN komprimering."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-file-compression"></a>Fejlfinding i forbindelse med CDN komprimering

I denne artikel hjælper dig med at foretage fejlfinding af problemer med [CDN komprimering](cdn-improve-performance.md).

Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, kan du kontakte de Azure eksperter på [MSDN Azure og forummerne stakoverløb](https://azure.microsoft.com/support/forums/). Alternativt kan du også sende en Azure support-hændelse. Gå til det [Azure supportwebsted](https://azure.microsoft.com/support/options/) og klikke på **Få Support**.

## <a name="symptom"></a>Symptom

Komprimering for din slutpunkt er aktiveret, men filer, der returneres dekomprimerede.

>[AZURE.TIP] For at kontrollere, om dine filer er blevet returneret komprimeret, du vil bruge et værktøj som [Fiddler](http://www.telerik.com/fiddler) eller din browser [udviklerværktøjer](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Kontrollér HTTP svar sidehoveder returneres med din cachelagrede CDN indhold.  Hvis der er et sidehoved med navnet `Content-Encoding` med en værdi af **gzip**, **bzip2**eller **deflate**, dit indhold er komprimeret.
>
>![Indhold-kodning sidehoved](./media/cdn-troubleshoot-compression/cdn-content-header.png)

## <a name="cause"></a>Årsag

Der er flere mulige årsager, herunder:

- Det ønskede indhold er ikke omfattet komprimering.
- Komprimering er ikke aktiveret for den ønskede filtype.
- HTTP-anmodningen omfatter ikke et sidehoved, der anmoder om en gyldig komprimeringstype.

## <a name="troubleshooting-steps"></a>Trin til fejlfinding

> [AZURE.TIP] Som med anvender nye slutpunkter, gå ændringer i konfigurationen CDN et stykke tid at overføre via netværket.  Ændringerne anvendes normalt inden for 90 minutter.  Hvis dette er første gang du har konfigureret komprimering for din CDN slutpunkt, bør du overveje, venter på 1-2 timer er vigtigt komprimeringen indstillinger har overføres til popper. 

### <a name="verify-the-request"></a>Bekræft din anmodning

Først skal vi gøre hurtig blive Tjek på anmodningen.  Du kan bruge zoomfunktionen i browseren [udviklerværktøjer](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) til at få vist de anmodninger.

- Bekræfte anmodningen skal sendes til din slutpunkt URL-adresse, `<endpointname>.azureedge.net`, og ikke dine origin.
- Bekræft anmodningen indeholder en **Acceptér-kodning** sidehoved, og værdien for sidehovedet indeholder **gzip**, **deflate**eller **bzip2**.

> [AZURE.NOTE] **Azure CDN fra Akamai** profiler understøtter kun **gzip** -kodning.

![LEVERANDØR(er) anmodning sidehoveder](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>Bekræfte komprimeringsindstillingerne (Standard CDN profil)

> [AZURE.NOTE] Dette trin gælder kun, hvis din CDN profil er en **Azure CDN Standard fra Verizon** eller **Azure CDN Standard fra Akamai** profil. 

Gå til din slutpunkt i [Azure-portalen](https://portal.azure.com) , og klik på knappen **Konfigurer** .

- Kontrollere komprimering er aktiveret.
- Kontrollér MIME-typen for indholdet, der skal komprimeres er inkluderet på listen over komprimeret formater.

![LEVERANDØR(er) komprimeringsindstillinger](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>Bekræfte komprimeringsindstillingerne (Premium CDN profil)

> [AZURE.NOTE] Dette trin gælder kun, hvis din CDN profil er en **Azure CDN Premium fra Verizon** profil.

Gå til din slutpunkt i [Azure-portalen](https://portal.azure.com) , og klik på knappen **Administrer** .  Portalen supplerende åbnes.  Holde markøren over fanen **HTTP store** , og derefter holde markøren over **Cacheindstillinger** pop op.  Klik på **komprimering**. 

- Kontrollere komprimering er aktiveret.
- Bekræft listen **Filtyper** indeholder en fil med semikolonseparerede-liste (uden mellemrum) af MIME-typer.
- Kontrollér MIME-typen for indholdet, der skal komprimeres er inkluderet på listen over komprimeret formater.

![LEVERANDØR(er) premium komprimeringsindstillinger](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>Bekræfte indholdet cachelagres

> [AZURE.NOTE] Dette trin gælder kun, hvis din CDN profil er en **Azure CDN fra Verizon** profil (Standard eller Premium).

Bruge browserens udviklerværktøjer, Markér svar overskrifterne for at sikre, at filen er cachelagret i det område, hvor det der anmodes.

- Se svarheaderen **Server** .  Sidehovedet har formatet **Platform (POP/Server-ID)**, som det fremgår i følgende eksempel.
- Se svarheaderen **X-Cache** .  Sidehovedet bør læse **ramme**.  

![LEVERANDØR(er) svar sidehoveder](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>Kontroller filen opfylder størrelse

> [AZURE.NOTE] Dette trin gælder kun, hvis din CDN profil er en **Azure CDN fra Verizon** profil (Standard eller Premium).

For at være berettiget til komprimering, skal en fil opfylde følgende størrelseskrav:

- Større end 128 byte.
- Mindre end 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrollere anmodningen, på den oprindelige server for en **Via** sidehoved

Overskriften **Via** HTTP angiver til webserveren, anmodningen sendes af en proxy-server.  Microsoft IIS-servere som standard Komprimer ikke svar, når anmodningen indeholder et **Via** sidehoved.  Hvis du vil ignorere problemet, skal du gøre følgende:

- **IIS 6**: [angive HcNoCompressionForProxies = "FALSE" i egenskaberne for IIS-metabasen](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 og op**: [angive både **noCompressionForHttp10** og **noCompressionForProxies** til False i server-konfiguration](http://www.iis.net/configreference/system.webserver/httpcompression)

