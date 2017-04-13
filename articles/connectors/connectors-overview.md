<properties
    pageTitle="Oversigt over logik Apps forbindelser | Microsoft Azure"
    description="Oversigt over forbindelser, der kan bruges i en logik app"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>Brug af forbindelser i en logik app

Forbindelser giver hurtig adgang til begivenheder, data og handlinger på tværs af services, protokoller og platforme.  Den komplette liste over forbindelser, der understøtter logik Apps kan [ikke findes her](apis-list.md).  Forbindelser kan bruges som en udløser eller en handling i en logik app og kan kræve en konfigureret *forbindelse* til brug af (for eksempel: godkendelse af en Twitter-konto for at få adgang til eller skrive på dine vegne).

## <a name="basics"></a>Grundlæggende funktioner

Forbindelserne er hostet tjenester, du kan få adgang til som en del af en logik app til at integrere med andre tjenester som Dynamics, Azure, Salesforce, [og mere](apis-list.md).  De er installeret og administreres af Microsoft, så du kan oprette dine integration af arbejdsprocesser med skala, overførselshastighed og sikkerhed, der er taget sig af.  Du kan føje en forbindelse til en logik app ved at søge og vælge en forbindelse handling eller udløser under **Vis Microsoft administrerede API'er**.

![Handlingsmenuen for at vælge udløser][1]

Hver forbindelse handling eller udløser har sit sæt af egenskaber for at konfigurere.  Du kan klikke på knapperne oplysninger for at få mere at vide om handling eller henviser til dens dokumentation [til at få mere at vide](apis-list.md).

Hvis du vil integrere med en tjeneste eller API, der ikke er endnu en forbindelse, du kan også udvide logik apps via en [brugerdefinerede forbindelse](../app-service-logic/app-service-logic-create-api-app.md) eller blot opkald direkte til tjenesten via en protokollen som HTTP.

## <a name="triggers"></a>Udløsere

Nogle forbindelser har en udløser, hvilket betyder, at en begivenhed fra forbindelsen, brand en logik app og overfører i en hvilken som helst data som en del af udløseren.  En udløser er altid det første trin i en logik app.  Populære udløsere omfatter handlinger som:
 
 * Gentagelse - køre hver time
 * Når der modtages en HTTP-anmodning
 * Når et element er føjet til en kø
 * Når der modtages en mail
 
Nogle udløsere vil gælde chatsamtale en begivenhed foregår via en meddelelse til appen logik, og andre skal et gentagelsesinterval, der er konfigureret på hvor ofte appen logik skal kontrollere tjenesten for en begivenhed (op til hver 15 sekunder).  

Når der modtages en begivenhed, bliver brand appen logik køre og handlingerne i arbejdsprocessen starter.  Du kan også få adgang til data fra udløser i hele arbejdsprocessen (for eksempel udløser 'på et nyt tweet' overfører tweet i feltet Kør).

## <a name="actions"></a>Handlinger

De fleste forbindelser har en eller flere handlinger, der kan udføres som en del af arbejdsprocessen.  Handlinger er de trin, der udføres, når afkrydsningsfeltet Kør har udløses fra en udløser.  Hvis du vil tilføje en handling klikke på knappen **Ny trin** og søge efter forbindelsen du vil bruge.  Du får vist kortet handling, du kan konfigurere, når markeret (og konfigurere efter en hvilken som helst [forbindelser](#connections) , kan det være nødvendigt).  Du kan vælge data fra de forrige trin ved at klikke på alle tokens for output, eller Angiv i en anden konfiguration, som det er nødvendigt.

![Konfigurere en forbindelse handling][2]

## <a name="connections"></a>Forbindelser

De fleste forbindelser skal du konfigurere en *forbindelse* , før du kan bruge forbindelsen.  En *forbindelse* er en hvilken som helst login eller forbindelse konfiguration, der er behov for at få adgang til forbindelsen.  For forbindelser, der bruger OAuth, oprette en forbindelse betyder, at logger på tjenesten (som Office 365, Salesforce eller GitHub) hvor din adgangstoken kan krypteres og sikkert gemmes i en Azure hemmeligt store.  Andre forbindelser (som FTP- og SQL) kræver en forbindelse, der indeholder konfiguration som serveradressen, brugernavn og adgangskode.  Disse oplysninger om konfiguration af forbindelse, også krypteres og sikkert gemmes.  Forbindelser vil kunne få adgang til tjenesten til, som giver mulighed for tjenesten.  Vi kan fortsætte med at opdatere adgangstoken på ubestemt tid til Azure Active Directory OAuth-forbindelser (som Office 365 og Dynamics).  Andre tjenester kan sætte begrænsninger på hvor længe kan vi bruge et token uden at blive opdateret.  Generelt bestemte handlinger som ændring af en adgangskode, bliver ugyldige alle access tokens.  

Forbindelser kan vises og administreres i Azure ved at klikke på **Gennemse** og vælge **API forbindelser**.  Du kan få vist, redigere, opdatere eller igen Godkend alle forbindelser, du har oprettet fra API forbindelser ressourcen.

## <a name="next-steps"></a>Næste trin

- [Oprette din første logik-app](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Få mere at vide almindelige anvendelsesområder og eksempler på logik apps](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Introduktion til enterprise-integration udløsere og handlinger](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png