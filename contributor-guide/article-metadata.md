

#<a name="metadata-for-azure-technical-articles"></a>Metadataene for Azure tekniske artikler

Alle Azure tekniske artikler indeholder to metadata afsnit - en egenskabssektion og en mærker sektion. Sektionen egenskaber giver nogle websted automatisering og SEO ting, når sektionen mærker gør det muligt for en masse interne indhold rapportering. Begge sektioner er påkrævet.

- [Syntaksen for]
- [Brugen]
- [Attributter og værdier for sektionen Egenskaber]
- [Attributter og værdier for sektionen mærker]

##<a name="syntax"></a>Syntaksen for

Sektionen Egenskaber bruger følgende syntaks:

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

Sektionen mærker bruger følgende syntaks:

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Brugen

- Der skelnes mellem elementnavn og navne på attributter.
- Den <properties> sektionen skal være den første linje i filen.
- Forlade en tom linje efter hvert afsnit af metadata, før din sidetitel at sikre, at siden titel er korrekt konverteres til HTML under publiceringsprocessen.

## <a name="attributes-and-values-for-the-properties-section"></a>Attributter og værdier for sektionen Egenskaber

![](./media/article-metadata/checkmark-small.png)**pageTitle**: obligatorisk. vigtigt at SEO. Teksten for denne attribut vises på fanen browser og som titel i et søgeresultat. Brug 55 60 tegn, herunder mellemrum samt sted-id *| Microsoft Azure* (skrevet som: plads pipe mellemrum Microsoft Azure).  PageTitle skal være forskellige fra H1.

![](./media/article-metadata/checkmark-small.png)**Beskrivelse**: kræves. vigtigt for SEO (relevans) og -webstedsfunktionen. Beskrivelsen skal være mindst 125 tegn lang tid at 155 tegn maksimale inklusive mellemrum. Beskriv formålet med dit indhold, så kunder ved, om du vil vælge det på en liste over søgeresultater. Værdien er:

- Denne tekst vises muligvis som beskrivelse af eller abstrakte afsnit i søgeresultaterne på Google.
- Denne tekst vises på [listen artikel indeks resultaterne](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**tjenester**: kræves til artikler, der drejer sig om en tjeneste. Denne værdi kaldes ofter "servce sats". Liste over alle relevante tjenesterne, adskilt af kommaer. Den første tjeneste, som du vil øge navigationsmæssige brødkrummer til siden og den venstre navigationsrude, der vises med siden.

I artikler, der angiver både værdien tjenester og værdien documentationCenter, vil værdien services drive brødkrummen. Flere værdier, som du liste vises som mærker i den publicerede artikel. Værdier:

- Active directory
- Active-directory-b2c
- Active-directory-katalogtjeneste
- App-service\api
- API-administration
- App-tjenesten
- App-servic\mobile
- App-service\web
- App-service\logic
- gateway-programmet på computeren
- programmet indsigt
- automatisering
- Azure-portal
- Azure ressourcestyring
- Azure-stak
- sikkerhedskopi
- batchen
- bedste fremgangsmåder
- BizTalk-tjenester
- cache
- leverandør(er)
- skytjenester
- datakatalog
- data-factory
- data-sø-analyser
- data-sø-store
- devtest øvelser
- DNS
- documentdb
- expressroute
- begivenhed hubs
- hdinsight
- iot-hub
- nøgle-samling
- justering af belastning
- Machine learning
- Marketplace
- Media-tjenester
- Mobile-aftale
- Mobile-tjenester
- MFA authentication
- meddelelse om hubs
- funktionsdygtige indsigt
- handlinger-management-pakke
- powerapps
- gendannelse manager
- redis-cache
- RemoteApp
- Rights management
- Opgavestyring
- søgning
- Sikkerhedscenter
- Service-bus
- Service-struktur
- gendannelse af websteder
- SQL-database
- SQL-datalager
- SQL-rapportering
- lagerplads
- store
- storsimple
- stream-analyser
- trafik manager
- virtuelle maskiner
- virtuelt netværk
- Visual-studio online
- VPN-gateway
- websteder

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: kræves til Udviklingscenter centreret artikler bedste udvalgt gennem en udviklercenter. Angive enkelt udviklercenter eller sprog, der gælder for i artiklen. Den værdi, du vil øge navigationsmæssige brødkrummer for siden. I artikler, der angiver både værdien tjenester og værdien documentationCenter, vil værdien services drive brødkrummen. Værdier:

- **.NET**
- **nodejs**
- **Java**
- **php**
- **Python**
- **Fonetisk**
- **mobil**: forældet. Erstat med bestemte mobil platform.
- **IOS**: Verifing den nye værdi
- **Android**: ved at bekræfte den nye værdi
- **Windows**: ved at bekræfte den nye værdi
- **xamarin**: ved at bekræfte den nye værdi

![](./media/article-metadata/checkmark-small.png)**forfattere**: påkrævet, kun én værdi. Liste over GitHub kontoen for den primære forfatter eller artikel små og mellemstore Virksomheders. Denne attribut drev på forfatterlinje på publicerede artiklen. Liste over kun én, på trods af flertalsnavnet på attributten.

![](./media/article-metadata/checkmark-small.png)**Manager**: påkrævet, hvis du er en Microsoft-bidragyder. Liste over mail-alias af indhold publicering manager for området, teknologi. Hvis du er en community bidragyder, indeholder attributten, men det er tomt, så vi kan udfylde den.

![](./media/article-metadata/checkmark-small.png)**Editor**: ikke bruges. Ikke bruge det til andre formål.

![](./media/article-metadata/checkmark-small.png)**mærker**: valgfrit. Medtag kun, hvis du vil aktivere et link under artikel brødkrummen til siden artikel indeks (http://azure.microsoft.com/documentation/articles/) til en prefiltered liste over artikler, der svarer til en af de godkendte værdier. Disse værdier er beregnet til at give en metode til at gruppere indhold sammen, når indhold gruppering ikke er tjenestens. Disse mærker kan også give etiketter, der angiver artiklen gælder for teknologi stablen. Denne værdi **ikke** understøtter ledig formular mærker eller hashtags; mærkerne, skal være aktiveret på webstedet. Du kan angive flere mærker værdier til en artikel, adskilt af kommaer. De godkendte værdier er:

  - arkitektur
  - Azure ressourcestyring
  - Azure-service-administration
  - fakturering
  - MySQL

![](./media/article-metadata/checkmark-small.png)**nøgleord**: valgfrit. Til brug af SEO champs kun. Separat ord med kommaer. **Spørg din SEO champ, før du ændre eller slette indhold i denne artikel, der indeholder ordene.** Denne attribut poster nøgleord SEO champ har rettet og registrerer for at forbedre søgning rang. Nøgleordene gengives ikke i den publicerede HTML-kode. Datavalidering er ikke nødvendigt denne attribut.

## <a name="attributes-and-values-for-the-tags-section"></a>Attributter og værdier for sektionen mærker

![](./media/article-metadata/checkmark-small.png)**MS.Service**: påkrævet. Angiver Azure service, værktøj eller funktion, som artiklen gælder for. Én værdi hver side.

 Hvis en side, der gælder for flere tjenester, skal du vælge tjenesten, det mest direkte gælder; for eksempel har en artikel, der bruger en app hostet på websteder til at vise Service Bus funktionalitet den **service-bus** værdi i stedet for **websteder**. Hvis en side gælder for flere tjenester lige, skal du vælge **flere**. Hvis en side ikke gælder for alle tjenester (dette er sjældne), kan du vælge **ikke.tilgængelig**.

 - **Active directory**
 - **Active-directory-b2c**
 - **Active-directory-katalogtjeneste**
 - **API-administration**
 - **App-tjenesten**: gælder kun for generelle grundlæggende materiale på App Service
 - **App-service-api**
 - **App-service-logik**
 - **App-service-mobil**
 - **App-service-web**
 - **programmet indsigt**
 - **gateway-programmet på computeren**
 - **automatisering**
 - **Azure ressourcestyring**
 - **Azure-sikkerhed**
 - **Azure-stak**
 - **sikkerhedskopi**
 - **batchen**
 - **bedste fremgangsmåder**
 - **BizTalk-tjenester**
 - **fakturering**
 - **cache**
 - **leverandør(er)**
 - **skytjenester**
 - **datakatalog**
 - **data-sø-store**
 - **data-sø-analyser**
 - **devtest øvelser**
 - **expressroute**
 - **hdinsight**
 - **Internet af ting**
 - **iot-hub**
 - **nøgle-samling**
 - **Machine learning**
 - **Marketplace**: artikler om Azure marketplace
 - **Media-tjenester**
 - **Mobile-aftale**
 - **Mobile-tjenester**
 - **MFA authentication**
 - **flere**: siden gælder for flere tjenester lige
 - **ikke.tilgængelig**: siden gælder ikke for alle tjenester (sjældne)
 - **meddelelse om hubs**
 - **funktionsdygtige indsigt**
 - **powerapps**
 - **gendannelse manager**
 - **redis-cache**
 - **RemoteApp**
 - **Rights management**
 - **Opgavestyring**
 - **Sikkerhedscenter**
 - **Service-bus**
 - **Service-struktur**
 - **gendannelse af websteder**: tidligere gendannelse-tjenester
 - **SQL-database**
 - **SQL-datalager**
 - **SQL-rapportering**
 - **lagerplads**
 - **gemme**: artikler om tjenester, der er tilgængelige via Azure Store
 - **storsimple**
 - **trafik manager**
 - **virtuelle maskiner**
 - **virtuelt netværk**
 - **Visual-studio online**
 - **VPN-gateway**
 - **websteder**

![](./media/article-metadata/checkmark-small.png)**MS.devlang**: påkrævet. Angiver det programmeringssprog, i artiklen gælder for. Enkelt værdi hver side.

 Hvis en side gælder for to programmeringssprog lige, skal du vælge **flere**. Hvis en side er primært grundlæggende og dens indhold er generelt gælder for flere programmeringssprog, skal du vælge **flere**. Hvis en side ikke er rettet mod udviklere og programmering sprog kan anvendes er ikke relevant, skal du vælge **ikke.tilgængelig**. Bruge **rest-api** til at identificere REST-API referenceemner.

 - **cpp**
 - **DotNet**
 - **Java**
 - **JavaScript**
 - **flere**: siden gælder for flere programmeringssprog lige.
 - **ikke.tilgængelig**: siden ikke målretning af udviklere og er ikke specifik for en hvilken som helst programmeringssprog.
 - **nodejs**
 - **mål-c**
 - **php**
 - **Python**
 - **Rest-api**
 - **Fonetisk**


![](./media/article-metadata/checkmark-small.png)**MS.topic**: påkrævet. Specifikke oplysninger i emnet Skriv. De fleste nye sider, der er oprettet af bidragydere bliver artikel eller reference.

 - **artikel**: et grundlæggende emne, selvstudium, funktion vejledning eller andre ikke-referenceartikel

 - **kampagnens tosidet**: Azure.com kun.  En side, som er udviklet specielt som en landingssiden for eksterne kampagner, og er ikke inkluderet som en del af det primære websted i a.  Der ikke bør benyttes til dokumentation artikler og almindelige doc lander sider.  Eksempler: azure.microsoft.com/develop/net/aspnet/; Azure.Microsoft.com/develop/Mobile/IOS/

 - **Udviklingscenter-center-hjemmeside**: Azure.com kun.  En Udviklingscenter centrere startsiden fx/udvikle/nettooversigt /

 - **Kom i gang artikel**: tildele til artikler, der er udvalgt i afsnittet Introduktion eller oversigt i venstre navigationsrude til en tjeneste.

 - **helt-artikel**: et "helt" selvstudium, der er udviklet til at levere en introduktion til en tjeneste eller funktion, som får besøgende ved hjælp af tjenesten hurtigt i gang og drev gratis prøveversion-sig og MSDN aktiveringer. Tildele denne værdi kun til artikler, der er udvalgt øverst i landingssiden dokumentation til din tjeneste.

 - **hjemmeside**: øverste niveau dokumentation startsiden. Vi har to kun: azure.microsoft.com/documentation/ og msdn.microsoft.com/library/azure/

 - **indeks-side**: andet niveau lander sider til programming sprog, tjenester eller funktioner. Disse er udbrede Azure.com og biblioteket, og der anvendes som indgangspunkter relateret mere specifikke oplysninger. Eksempler: http://azure.microsoft.com/develop/mobile/resources-wp8/, http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **infographic-side**: Azure.com kun. En side, der indeholder et kan vises infographic eller en plakat, for eksempel http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **reference**: en API reference side (herunder REST-API) eller PowerShell-cmdlet reference side

 - **tjenesten startsiden**: Azure.com kun.  Et dokument hjemmesiden for tjenesten, f.eks. /documentation/services/virtual-machines /

 - **sektion--startsiden for webstedet**: Azure.com kun. En "startside" for en bestemt type af indhold på azure.com eksempler: http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **video-side**: Azure.com kun.  En side, der indeholder en video, for eksempel http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**MS.tgt_pltfrm**: påkrævet. Angiver mål platformen, for eksempel Windows, Linux, Windows Phone, iOS, Android eller speciel cache platforme. Én værdi hver side. Denne værdi være **ikke.tilgængelig** for de fleste emner undtagen mobile og virtuelle computere.

 - **cachen i rolle**
 - **cache-flere**
 - **cache-redis**
 - **cache-tjenesten**
 - **cache-delte**
 - **Command-line-grænseflade**
 - **Ibiza**: indhold, der bruger portalen Ibiza. Brug denne kun i tilfælde, hvor den funktion, der diskuteres er tilgængelig på tværs af både portalen Ibiza og den aktuelle portal.
 - **mobile-android**: Azure.com kun lige nu
 - **mobile-html**: Azure.com kun lige nu
 - **mobile-ios**: Azure.com kun lige nu
 - **mobile-kindle**: Azure.com kun lige nu
 - **Mobile-flere**
 - **mobile-nokia-x**: Azure.com kun lige nu
 - **mobile-phonegap**: Azure.com kun lige nu
 - **mobile-sencha**: Azure.com kun lige nu
 - **mobile-windows**: Azure.com kun lige nu. Windows Universal
 - **Mobile-windows-telefon**
 - **Mobile-windows-store**
 - **mobile-xamarin**: Azure.com kun lige nu. Xamarin alle platforme
 - **mobile-xamarin-android**: Azure.com kun lige nu
 - **mobile-xamarin-ios**: Azure.com kun lige nu
 - **flere**: siden gælder for flere platforme lige
 - **ikke.tilgængelig**: en angivelse for platform er ikke tilgængelig for denne side
 - **PowerShell**
 - **VM linux**
 - **VM multiplum**
 - **VM-windows**
 - **VM-windows-sharepoint**
 - **VM-windows-sql-server**
 - **vs--Introduktion**: identificerer sidegruppe VS Kom godt i gang. Mærke tilføjet 12/1/14.
 - **vs-Hvad-er der sket**: identificerer siden VS komme i gang Hvad skete der med. Mærke tilføjet 12/1/14.

![](./media/article-metadata/checkmark-small.png)**MS.workload**: påkrævet. Angiver Azure arbejdsbelastningen, siden gælder for. Én værdi kun per artikel.

**opdatere 6/8/15** Værdien ms.workload tilknyttes ved en xls, ikke værdien i filen .md. Værdien ms.workload er stadig kræves til validering, indtil funktionen, der kan opdateres. Der fungerer planlægges nu.  Brug **"ikke.tilgængelig"** som værdien for nu.

![](./media/article-metadata/checkmark-small.png)**MS.Date**: påkrævet. Angiver den dato, i artiklen sidst blev gennemgået til relevans, nøjagtigheden, korrekte skærmbilleder og arbejde links. Angiv datoen i formatet mm-dd-åååå. Denne dato vises også på publicerede artiklen som den sidste opdaterede dato.

![](./media/article-metadata/checkmark-small.png)**MS.author**: påkrævet. Angiver den forfatternavne, der er knyttet til emnet. Interne rapporter (såsom friskhed) bruge denne værdi skal tilknyttes i artiklen højre forfatternavne. Hvis du vil angive flere værdier skal du adskille dem med semikolon. Microsoft aliasser eller fuldført mailadresser accepteres. Længden kan ikke være længere end 200 tegn.


###<a name="contributors-guide-links"></a>Bidragydere vejledning Links

- [Oversigt over artikel](./../README.md)
- [Indeks over vejledning artikler](./contributor-guide-index.md)


<!--Anchors-->
[Syntaksen for]: #syntax
[Brugen]: #usage
[Attributter og værdier for sektionen Egenskaber]: #attributes-and-values-for-the-properties-section
[Attributter og værdier for sektionen mærker]: #attributes-and-values-for-the-tags-section
