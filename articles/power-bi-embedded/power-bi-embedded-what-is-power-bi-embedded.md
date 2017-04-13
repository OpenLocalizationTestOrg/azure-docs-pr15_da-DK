<properties
   pageTitle="Hvad er Microsoft Power BI integrerede?"
   description="Power BI integrerede gør det muligt at integrere Power BI-rapporter i webstedet eller mobilprogrammer, så du ikke behøver at opbygge tilpassede løsninger at visualisere data for dine brugere"
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

# <a name="what-is-microsoft-power-bi-embedded"></a>Hvad er Microsoft Power BI integrerede?

Du kan integrere Power BI-rapporter med **Power BI integreret**, højre til webstedet eller mobilprogrammer.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI integrerede er en **Azure service** , som gør det muligt for softwareudviklere og app udviklere til surface Power BI data oplevelser i deres programmer. Du har oprettet programmer som en udvikler, og disse programmer har deres egne brugere og forskellige sæt af funktioner. Disse apps kan også opstå have nogle indbyggede dataelementer som diagrammer og rapporter, der kan nu være udstyret med Microsoft Power BI integreret. Brugere ikke har brug for en Power BI-konto for at bruge din app. De kan fortsætte med at logge på dit program ligesom før, og få vist og interagere med Power BI rapportering oplevelse uden at kræve ekstra licenser.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Licenser til Microsoft Power BI integreret

I **Microsoft Power BI integrerede** brugen modellen er licenser til Power BI ikke ansvarlig for slutbrugeren.  I stedet **gengiver** købes af udvikleren af den app, der bruger de visuelle elementer og betaler til det abonnement, der ejer disse ressourcer.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI integreret konceptbaseret Model

![](media\powerbi-embedded-whats-is\model.png)

Som en hvilken som helst anden tjeneste i Azure, er ressourcer til Power BI integrerede klargjort via [Azure ARM API'er](https://msdn.microsoft.com/library/mt712306.aspx). I dette tilfælde er den ressource, du klargøre en **Power BI arbejdsområde af websteder**.

## <a name="workspace-collection"></a>Arbejdsområde af websteder

En **Samling af arbejdsområdet** er på øverste niveau Azure beholderen til ressourcer, der indeholder 0 eller flere **arbejdsområder**.  Et **arbejdsområde** **af websteder** har alle Azure standardegenskaberne, såvel som følgende:

-   **Access-taster** – nøgler, der bruges, når du ringer sikkert Power BI API'er (beskrevet i en senere afsnit).
-   **Brugere** – Azure Active Directory (AAD) brugere, der har administratorrettigheder for at administrere samlingen Power BI arbejdsområde via Azure administrationsportalen eller ARM API.
-   **Område** – som en del af klargøring af en **Arbejdsområde af websteder**, kan du vælge et område, der skal være klargjort i. Du kan finde yderligere oplysninger finder [Azure områder](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Arbejdsområde

Et **arbejdsområde** er en beholder af Power BI-indhold, som kan indeholde datasæt, rapporter og dashboards. Et **arbejdsområde** er tom, når du opretter. Du kan redigere alt indhold ved hjælp af Power BI Desktop under eksempelvisning, og du overføre den til en af dine arbejdsområder ved hjælp af [Power BI REST API'er](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>Brug af arbejdsområdet af websteder og arbejdsområder
**Arbejdsområde af websteder** og **arbejdsområder** er beholdere med indhold, der bruges organiseret i uanset hvilken metode, der bedst passer designet af det program, du opretter. Der vil være, at du kan arrangere indholdet i dem på mange forskellige måder. Du kan vælge at placere alt indhold i et arbejdsområde, og brug derefter senere app tokens til yderligere underopdele indholdet mellem dine kunder. Du kan også vælge at indsætte alle dine kunder i separate arbejdsområder, så der er nogle adskilt. Eller du kan vælge at organisere brugere via område i stedet for kunde. Dette fleksible design giver dig mulighed at vælge den bedste måde at organisere indhold.

## <a name="cached-datasets"></a>Cachelagrede datasæt

Cachelagrede datasæt kan bruges i Vis udskrift.  Du opdatere ikke cachelagrede data, når den er blevet indlæst i **Microsoft Power BI integreret**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Godkendelse og autorisation med app tokens

**Microsoft Power BI integrerede** udskydes til dit program til at udføre alle de nødvendige brugergodkendelse og godkendelse. Der er ingen eksplicitte krav, at dine slutbrugere være kunder af Azure Active Directory (Azure AD).  I stedet udtrykker dit program til **Microsoft Power BI integrerede** tilladelse til at gengive en Power BI-rapport ved hjælp af **Programmet godkendelsestokens (App Tokens)**.  Disse **App Tokens** oprettes efter behov, når din app vil til at gengive en rapport.  Se [App Tokens](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Programmet godkendelsestokens (App Tokens)** bruges til godkendelse mod **Microsoft Power BI integreret**.  Der findes tre typer **App Tokens**:

1.  Klargøring af Tokens - bruges, når et nyt **arbejdsområde** til et **Arbejdsområde af websteder**
2.  Udvikling Tokens - bruges, når du foretager opkald direkte til **Power BI REST API'er**
3.  Integrere Tokens - bruges, når du ringer op til at gengive en rapport i den integrerede iframe

Disse tokens anvendes for de forskellige faser af din interaktion med **Microsoft Power BI integreret**.  Tokens er designet, så du kan uddelegere tilladelser fra din app til Power BI. Få mere at vide ved at se [App Token dataflow](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Se også
- [Almindelige Microsoft Power BI integrerede scenarier](power-bi-embedded-scenarios.md)
- [Introduktion til Microsoft Power BI integreret](power-bi-embedded-get-started.md)
