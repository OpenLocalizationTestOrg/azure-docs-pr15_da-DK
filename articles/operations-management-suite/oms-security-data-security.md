<properties
   pageTitle="Handlinger Management pakke sikkerhed og Overvåg løsning datasikkerhed | Microsoft Azure"
   description="Dette dokument forklares, hvordan data administreres og sikres i handlinger Management pakke sikkerhed og Overvåg løsning."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Handlinger Management pakke sikkerhed og Overvåg løsning datasikkerhed

For at hjælpe kunder med at forhindre, at registrere og besvare trusler, [Handlinger Management Suite (OMS) sikkerhed og Overvåg løsning](operations-management-suite-overview.md) indsamler og behandler data om dine ressourcer, som omfatter:

- Sikkerhed hændelseslog
- Begivenhed sporing for Windows (ETW) begivenheder
- AppLocker overvå begivenheder
- Windows Firewall-loggen
- Avancerede truslen Analytics-begivenheder
- Resultaterne af oprindelige vurdering
- Resultaterne af Antimalwareprogram vurdering
- Resultaterne af opdatering/programrettelse vurdering
- Syslogs streams, der eksplicit er aktiveret på agenten

Vi yder stærke forpligtelser til at beskytte fortroligheden og sikkerheden for disse data. Microsoft overholder faste retningslinjerne for overholdelse af regler og sikkerhed for – fra koder til operativsystem en tjeneste.
Denne artikel forklares, hvordan data administreres og sikres i OMS sikkerhed og Overvåg løsning.

## <a name="data-sources"></a>Datakilder

OMS sikkerhed og Overvåg løsning analyserer data fra din virtuelle maskiner og fysiske computere, hvor OMS Agent er installeret. OMS sikkerhed og overvåge løsning kan indsamler konfigurationsoplysninger om sikkerhed begivenheder, som Windows begivenhed, overvågningslogge, IIS-logfiler og syslog meddelelser. Eksempler på sådanne data er: operativsystemtype og version, kører processer, computernavn, IP-adresser, der er logget på bruger og lejer-ID.  

## <a name="data-protection"></a>Beskytte data

**Data opdeling**: Data bevares logisk separat på hver komponent i hele tjenesten. Alle data, der er mærket i organisationen. Denne mærkning fortsætter i hele livscyklus data, og den håndhæves hvert enkelt lag til tjenesten. 

**Dataadgang**: for at give anbefalinger til sikkerhed, og Undersøg potentielle sikkerhedsrisici, Microsoft personale kan få adgang til oplysninger der indsamles eller analysere i tjenester. Vi overholde [Microsoft Online Services betingelser](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) og [Erklæring om](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), hvilke tilstand, at Microsoft ikke bruge kundedata eller udlede oplysninger fra den til reklame eller lignende kommerciel brug. Hvis du vil give anbefalinger til sikkerhed, og Undersøg mulige trusler, Microsoft personale adgang til oplysninger der indsamles eller analysere i tjenester. Vi kun bruge kundedata efter behov for at give dig Azure tjenesterne, herunder formål, der er kompatible med give disse tjenester. Du bevarer alle rettigheder til dine egne data.

**Brug af data**: Microsoft bruger mønstre og truslen intelligence set på tværs af flere lejere for at forbedre vores at forebygge og opdage funktionalitet Vi kan gøre det i overensstemmelse med beskrevet i vores [Erklæring om](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)beskyttelse af personlige oplysninger forpligtelser.

> [AZURE.NOTE] Placering af er konfigureret på niveauet OMS arbejdsområde under arbejdsområde oprettelse, som er en del af den indledende konfigurationsproces OMS sikkerhed og overvågning.

## <a name="see-also"></a>Se også

I dette dokument, du har lært hvordan data administreres og sikres i OMS. Hvis du vil vide mere om OMS sikkerhed og Overvåg løsning, i:

- [Oversigt over operationer Management pakke (OMS)](operations-management-suite-overview.md)
- [Overvåge og reagere på sikkerhedsadvarsler i handlinger Management pakke sikkerhed og overvågningsloggen løsning](oms-security-responding-alerts.md)
- [Overvåge ressourcer i handlinger Management pakke sikkerhed og overvågningsloggen løsning](oms-security-monitoring-resources.md)

