<properties 
    pageTitle="Azure Government Udviklervejledning" 
    description="Dette giver en sammenligning af funktioner og vejledning om udvikling af programmer til Azure Government" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Microsoft Azure Government Developer Guide 

<p> Microsoft Azure Government er en fysisk og netværk isolerede forekomst af Microsoft Azure.  Denne udviklervejledning, der indeholder oplysninger om forskellene, udviklere og administratorer nødt til at interagere og arbejde med disse separate områder af Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>I dette emne


+ [Oversigt](#Overview)
+ [Vejledning til udviklere](#Guidance)
+ [Funktioner, der er tilgængelige i Microsoft Azure Government](#Features)
+ [Slutpunkt tilknytning](#Endpoint)
+ [Næste trin](#next)


## <a name="Overview"></a>Oversigt

Microsoft Azure Government er en separat forekomst af tjenesten Microsoft Azure, vælge en adresse til behovet sikkerhed og overholdelse af US national myndigheder, stat og lokale stater og deres løsningsudbydere. Azure Government tilbyder fysiske og netværk isolation fra ikke-amerikanske regering installationer og giver afskærmede USA personale. 

Microsoft tilbyder en række værktøjer til at oprette og implementere skyen programmer til Microsofts globale Microsoft Azure service ("globale tjeneste") og Microsoft Azure Government tjenester.

Når du opretter og implementerer programmer til Azure Government tjenesterne, i modsætning til tjenesten globale skal udviklere kende vigtige forskelle mellem de to tjenester.  Konfiguration af slutpunkter, skrive programmer og implementere dem som tjenester til Azure Government specifikt rundt om konfiguration og konfigurere deres programming miljø.

Oplysningerne i dette dokument kan du se disse forskelle og supplerer de oplysninger, der er tilgængelige på webstedet [Azure Government](http://www.azure.com/gov "Azure Government") og [Microsoft Azure Technical Library](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") på MSDN. Officielle oplysninger måske også er tilgængelig på mange andre steder såsom den [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center" /), [Azure dokumentation Center](https://azure.microsoft.com/documentation/) og i [Azure blogge] (https://azure.microsoft.com/blog/ "Azure blogge" /). 

Dette indhold er beregnet til partnere og udviklere, der implementerer til Microsoft Azure Government.



## <a name="Guidance"></a>Vejledning til udviklere
Da de fleste af det tekniske indhold, der er tilgængelig i øjeblikket antager, at programmer der udvikles for tjenesten globale i stedet for til Microsoft Azure Government, er det vigtigt for dig at sikre, at udviklere opmærksom på vigtige forskelle for programmer, der er udviklet til at være vært Azure Government.

- Først, der er tjenester og forskel i funktioner, det betyder, at bestemte funktioner, der er i bestemte områder af den globale tjeneste ikke muligvis tilgængelig i Azure Government.

- Der findes, konfigurationsforskelle fra den globale tjeneste til funktioner, der tilbydes i Azure Government.  Du skal derfor kontrollere din kodeeksempler, konfigurationer og trin for at sikre, at du udvikler og afholde Azure Government Cloud Services-miljø.


## <a name="Features"></a>Funktioner, der er tilgængelige i Microsoft Azure Government
Azure Government har aktuelt følgende tjenester tilgængelige i både os GOV krigsskibe i IOWA og OS GOV VIRGINIA områder:

- Virtuelle maskiner
- Cloud Services
- Lagerplads
- Active Directory
- Opgavestyring
- Virtuelt netværk
- SQL-Database
- Azure-filer
- Media Services
- Trafik Manager
- Service Bus
- StorSimple
- Redis Cache
- Azure sikkerhedskopi
- Automatisering
- ExpressRoute
- osv.

Andre tjenester er tilgængelige, og flere tjenester, føjes løbende.  De mest aktuelle liste over tjenester, skal du se [områder side](https://azure.microsoft.com/regions/#services) , der fremhæver hver tilgængelige område og deres tjenester.  

OS GOV krigsskibe i Iowa og OS GOV Virginia er i øjeblikket, de understøttende Azure Government datacentre.  Se siden områder over for aktuelle datacentre og -tjenester, der er tilgængelige.

## <a name="Endpoint"></a>Slutpunkt tilknytning

Brug tabellen nedenfor til at hjælpe dig, når du knytter offentlige slutpunkter for Microsoft Azure og SQL-Database til Azure Government bestemte slutpunkter.


Tjenestetype|Azure offentlige|Azure Government
---|---|---
Administrationsportalen|Manage.windowsazure.com|Manage.windowsazure.us
Generelt|*. windows.net|*. usgovcloudapi.net
Core|*. core.windows.net|*. core.usgovcloudapi.net
Beregne|*. cloudapp.net|*. usgovcloudapp.net
BLOB-lager|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Kø lagerplads|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Table Storage|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Service Management|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
SQL-Database|*. database.windows.net|*. database.usgovcloudapi.net
ARM Indlæs afstemmes slutpunkt|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* For ARM godkendelse via Azure AD, skal du henvise til [Godkendelse af Azure ressourcestyring anmodninger](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Næste trin

Hvis du er interesseret i at vide mere, og om Azure Government kan du udnytte nogle af linkene nedenfor.

- **[Tilmelde dig en prøveversion](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Sådan finder og får adgang til Azure Government](http://azure.com/gov)**

- **[Azure Government oversigt](/azure-government-overview)**

- **[Azure Government Blog](http://blogs.msdn.com/b/azuregov/)**

- **[Azure overholdelse](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
