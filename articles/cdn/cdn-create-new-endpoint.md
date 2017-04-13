<properties
     pageTitle="Brug af Azure CDN | Microsoft Azure"
     description="Dette emne viser, hvordan du aktiverer indhold levering netværk (CDN) til Azure. Selvstudiet vejleder gennem oprettelse af en ny CDN profil og slutpunkt."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>Brug af Azure CDN  

Dette emne indeholder en gennemgang aktivere Azure CDN ved at oprette en ny CDN profil og slutpunkt.

>[AZURE.IMPORTANT] Du kan finde en introduktion til, hvordan CDN virker, samt en liste over funktioner, [CDN oversigt](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Oprette en ny CDN profil

En CDN profil er en samling af CDN slutpunkter.  Hver profil indeholder en eller flere CDN slutpunkter.  Du vil bruge flere profiler til at organisere CDN slutpunkterne af internet-domæne, webprogram eller nogle andre kriterier.

> [AZURE.NOTE] Et enkelt Azure abonnement er begrænset til otte CDN profiler som standard. Hver CDN profil er begrænset til 10 CDN slutpunkter.
>
> LEVERANDØR(er) priser anvendes på niveauet for CDN profil. Hvis du vil bruge en blanding af Azure CDN priser lag, skal du flere CDN profiler.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Oprette et nyt CDN slutpunkt

**Oprette et nyt CDN slutpunkt**

1. Gå til din CDN profil i [Azure-portalen](https://portal.azure.com).  Du kan have fastgjort den til dashboardet i det forrige trin.  Hvis du ikke, kan du finde det ved at klikke på **Gennemse**og derefter **CDN profiler**og klikke på den profil, du vil tilføje dine slutpunkt til.

    Bladet CDN profil vises.

    ![LEVERANDØR(er) profil][cdn-profile-settings]

2. Klik på knappen **Tilføj slutpunkt** .

    ![Slutpunkt knappen Tilføj][cdn-new-endpoint-button]

    **Tilføj et slutpunkt** blade vises.

    ![Tilføje slutpunkt blade][cdn-add-endpoint]

3. Angiv et **navn** for dette CDN slutpunkt.  Dette navn bruges til at få adgang til dine cachelagrede ressourcer på domænet `<endpointname>.azureedge.net`.

4. Vælg din origin i rullemenuen **Origin type** .  Du kan vælge **lagerplads** på en Azure-lager-konto, **skybaseret tjeneste** for en Azure skybaseret tjeneste, **Web App** for et Azure Web App eller en **brugerdefineret origin** for eventuelle andre offentligt tilgængelige web server origin (vært i Azure eller et andet sted).

    ![LEVERANDØR(er) origin type](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Vælg eller Angiv domænet origin i rullemenuen **Origin hostname** .  På rullelisten vises alle tilgængelige baggrunden for den type, du angav i trin 4.  Hvis du har valgt *brugerdefineret origin* som din **Skriv Origin**, skriver du i domænet for dit brugerdefinerede origin.

6. Angiv stien til de ressourcer, du vil cachen, eller lad være tomt for at tillade cache en ressource på det domæne, du angav i trin 5 i tekstboksen **Origin sti** .

7. Angiv den ønskede CDN sende med hver enkelt anmodning vært overskrift i **Origin host sidehoved**, eller lad standard.

    > [AZURE.WARNING] Nogle typer baggrunden som Azure-lager og Web Apps, kræver host-overskriften til at matche domænet for origin. Medmindre du har en origin, der kræver et host sidehoved, der er anderledes end domænet, skal du lade standardværdien.

8. Angiv de protokoller og porte, der bruges til at få adgang til dine ressourcer ved nulpunktet til **protokol** og **Origin port**.  Mindst én protocol (HTTP eller HTTPS) skal være markeret.
    
    > [AZURE.NOTE] **Origin port** påvirker kun hvad port slutpunkt bruger til at hente oplysninger fra startpunkt.  Slutpunktet selve fås kun til slutningen klienter på standard HTTP og HTTPS-porte (80 og 443), uanset **Origin port**.  
    >
    > **Azure CDN fra Akamai** slutpunkter tillader ikke det hele TCP portområde til baggrunden.  Du kan finde en liste over origin porte, som ikke er tilladt, [Azure CDN fra Akamai tilladte Origin porte](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > Få adgang til CDN har indhold ved hjælp af HTTPS følgende begrænsninger:
    > 
    > - Du skal bruge det SSL-certifikat, der leveres af CDN. Fra tredjepart certifikater understøttes ikke.
    > - Du skal bruge udleveret CDN domænet (`<endpointname>.azureedge.net`) til at få adgang til HTTPS indhold. HTTPS-understøttelse er ikke tilgængelig for brugerdefinerede domænenavne (CNAMEs), da CDN ikke understøtter brugerdefinerede certifikater på nuværende tidspunkt.

9. Klik på knappen **Tilføj** for at oprette nyt slutpunkt.

10. Når slutpunktet er oprettet, vises det på en liste med slutpunkter til profilen. Listevisningen viser URL-adressen skal bruges til at få adgang til cachelagret indhold samt origin domænet.

    ![LEVERANDØR(er) slutpunkt][cdn-endpoint-success]

    > [AZURE.IMPORTANT] Slutpunktet kan umiddelbart ikke bruges, som det tager tid om at overføre via CDN registrering.  For <b>Azure CDN fra Akamai</b> profiler fuldfører overførsel normalt inden for et minut.  For <b>Azure CDN fra Verizon</b> profiler overførsel fuldfører normalt inden for 90 minutter, men i nogle tilfælde kan tage længere tid.
    >    
    > Brugere, der forsøger at bruge domænenavnet CDN, før konfigurationen af slutpunktet har overføres til popper modtager HTTP 404 Svarkoder.  Hvis der er gået flere timer, fordi du har oprettet din første eller sidste ark, og du stadig modtager 404 svar, skal du se [fejlfinding CDN slutpunkter returnere 404 statusser](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Se også
- [Styre cachelagring funktionsmåden for anmodninger om med forespørgselsstrenge](cdn-query-string.md)
- [Hvordan du knytter CDN indhold til et brugerdefineret domæne](cdn-map-content-to-custom-domain.md)
- [Foreløbig Indlæs Aktiver på Azure CDN ark](cdn-preload-endpoint.md)
- [Fjern Azure CDN ark](cdn-purge-endpoint.md)
- [Fejlfinding i forbindelse med CDN slutpunkter returnere 404 statusser](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
