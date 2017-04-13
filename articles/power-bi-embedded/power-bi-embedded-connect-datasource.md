<properties
   pageTitle="Microsoft Power BI integrerede - forbindelse til en datakilde"
   description="Power BI integreret, oprette forbindelse til datakilder"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Oprette forbindelse til en datakilde

Med **Power BI integreret**, kan du integrere rapporter i din egen app. Når du integrerer en Power BI-rapport i din app, rapporten opretter forbindelse til de underliggende data ved at **importere** en kopi af dataene eller **direkte forbindelse** til datakilden ved hjælp af **DirectQuery**.

Her er forskelle mellem at bruge **Import** og **DirectQuery**.

|Importér | DirectQuery
|---|---
|-Tabeller, kolonner *og data* er importeret eller kopieres til rapportens datasæt. For at se ændringer, der er indtruffet de underliggende data, skal du opdatere eller importere et komplet og aktuelle datasæt igen.|Kun *tabeller og kolonner* er importeret eller kopieres til rapportens datasæt. Du kan altid få vist de mest aktuelle data.
Med Power BI integreret, du kan bruge DirectQuery med skydatakilder, men ikke lokale datakilder på nuværende tidspunkt.

## <a name="benefits-of-using-directquery"></a>Fordelene ved at bruge DirectQuery

Der er to primære fordele, når du bruger **DirectQuery**:

   -    **DirectQuery** kan du oprette visualiseringer over meget store datasæt, hvor det ellers ville være unfeasible til første Importér data.
   -    Underliggende dataændringer kan kræver en opdatering af data, og for visse rapporter kan behovet for at få vist aktuelle data kræver store overførsler, foretage igen import af data unfeasible. **DirectQuery** rapporter derimod altid Brug aktuelle data.

## <a name="limitations-of-directquery"></a>Begrænsninger i forbindelse med DirectQuery

   Der er nogle begrænsninger til at bruge **DirectQuery**:

   -    Alle tabeller skal komme fra en enkelt database.
   -    Hvis forespørgslen er alt for kompleks, opstår der en fejl. For at løse fejlen skal du refactor forespørgslen, så det er mindre kompleks. Hvis quuery skal være kompleks, skal du importere data i stedet for ved hjælp af **DirectQuery**.
   -    Filtrering af relation er begrænset til en enkelt retning i stedet for begge retninger.
   -    Du kan ikke ændre datatypen for en kolonne.
   -    Som standard placeres begrænsninger i DAX-udtryk, der er tilladt i mål. Se [DirectQuery og mål](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>DirectQuery og mål

For at sikre forespørgsler, der sendes til den underliggende datakilde har tilstrækkelig ydeevne, er begrænsninger som på mål. Når ved hjælp af **Power BI Desktop**, erfarne brugere kan vælge at ignorere denne begrænsning ved at vælge **Filer > Indstillinger > Indstillinger for**. Vælg **DirectQuery**i dialogboksen **Indstillinger** , og vælg indstillingen, der **tillader ubegrænset målinger i DirectQuery-tilstand**. Når denne indstilling er markeret, kan bruges et DAX-udtryk, der er gyldige for en måling. Brugere skal være opmærksom på; dog, at nogle udtryk, der udfører meget vel når dataene er importeret, kan det medføre i meget langsom forespørgsler til back end-kilde i **DirectQuery** -tilstand. 

## <a name="see-also"></a>Se også
- [Introduktion til Microsoft Power BI integreret](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
