<properties
    pageTitle="Azure CDN alarmer i realtid | Microsoft Azure"
    description="Alarmer i Microsoft Azure CDN i realtid. Alarmer i realtid giver beskeder om ydeevne på slutpunkterne i din CDN profil."
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
    ms.date="07/12/2016"
    ms.author="casoper"/>

# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alarmer i realtid i Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]


## <a name="overview"></a>Oversigt

I dette dokument forklares alarmer i realtid i Microsoft Azure CDN. Denne funktionalitet leverer realtid beskeder om ydeevne på slutpunkterne i din CDN profil.  Du kan konfigurere mail eller HTTP-meddelelser, der er baseret på:

* Båndbredde
* Status-koder
* Statusser, som cache
* Forbindelser

## <a name="creating-a-real-time-alert"></a>Oprette en besked i realtid

1. Gå til din CDN profil i [Azure-portalen](https://portal.azure.com).

    ![LEVERANDØR(er) profil blade](./media/cdn-real-time-alerts/cdn-profile-blade.png)

2. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-real-time-alerts/cdn-manage-btn.png)

    Portalen CDN management åbnes.

3. Peg på fanen **analyser** og derefter holde markøren over **Realtid statistik** pop op.  Klik på **alarmer i realtid**.

    ![LEVERANDØR(er) management portal](./media/cdn-real-time-alerts/cdn-premium-portal.png)

    Der vises på listen over eksisterende beskeder om konfigurationer (hvis relevant).

4. Klik på knappen **Tilføj besked** .

    ![Beskeder om knappen Tilføj](./media/cdn-real-time-alerts/cdn-add-alert.png)

    Der vises en formular til at oprette en ny besked.

    ![Ny besked formular](./media/cdn-real-time-alerts/cdn-new-alert.png)

5. Hvis denne besked skal være aktiv, når du klikker på **Gem**skal du, markere afkrydsningsfeltet **Påmindelse aktiveret** .

6. Angiv et beskrivende navn til din besked i feltet **navn** .

7. Vælg **HTTP stort objekt**i rullemenuen **Medietype** .

    ![Medietype med HTTP markeret store objekt](./media/cdn-real-time-alerts/cdn-http-large.png)

    > [AZURE.IMPORTANT] Du skal vælge **HTTP stort objekt** som den **Medietype**.  De andre muligheder er ikke bruges af **Azure CDN fra Verizon**.  Fejl ved at markere **HTTP store objekt** medfører beskeden vil aldrig blive udløst.

8. Oprette et **udtryk** til at overvåge ved at vælge en **metrikværdi**, **Operator**og **udløser værdi**.

    - Vælg typen af betingelse, du vil overvåget for **metrisk**.  **Båndbredde Mbps** er mængden af båndbredden i høj båndbredde sekundet.  **Samlet forbindelser** er antallet af samtidige HTTP-forbindelser til vores edge-servere.  Definitioner af de forskellige cache statusser og status-koder under [Azure CDN Cache statuskoder](https://msdn.microsoft.com/library/mt759237.aspx) og [Azure CDN HTTP Status-koder](https://msdn.microsoft.com/library/mt759238.aspx)
    - **Operatoren** er den matematiske operator, der opretter relationen mellem metriske og værdien udløser.
    - **Udløser værdi** er den tærskelværdi, som skal være opfyldt, før en meddelelse sendes ud.

    I den nedenstående eksempel angiver det expression, som jeg har oprettet, jeg gerne vil have besked, når antallet 404 statuskoder er større end 25.

    ![Realtid besked eksempel på et udtryk](./media/cdn-real-time-alerts/cdn-expression.png)

9. Angiv for **Interval**, hvor ofte du vil have udtrykket evalueres.

10. Vælg, når du gerne vil have besked, når udtrykket er sandt i rullemenuen **Informer på** .
    
    - **Betingelse Start** angiver, at der sendes en meddelelse, når den angivne betingelse registreres først.
    - **Betingelse slutningen** angiver, at der sendes en meddelelse, når den angivne betingelse findes ikke længere. Denne meddelelse kan kun udløses, når registreret vores netværk, overvågning system, at den angivne betingelse opstod.
    - **Fortløbende** angiver, at en meddelelse sendes hver gang, at netværket overvågning system registrerer den angivne betingelse. Husk på, netværket overvågning system kan kun tjekke én gang i intervallet for de angivne betingelser.
    - **Betingelse Start og Stop** angiver, at en meddelelse sendes første gang, at den angivne betingelse registreres og igen når betingelsen ikke længere er fundet.

11. Hvis du vil modtage meddelelser via mail, skal du markere afkrydsningsfeltet **Giv besked via mail** .  

    ![Giv besked ved e-mail-formular](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Angiv den mailadresse, hvor du vil have meddelelser sendes, i feltet **til** . Du kan lade være standard for **emne** og **brødteksten**, eller du kan tilpasse meddelelsen ved hjælp af listen over **tilgængelige nøgleord** til at indsætte dynamisk beskeder om data, når meddelelsen er sendt.

    > [AZURE.NOTE] Du kan teste mailmeddelelse ved at klikke på knappen **Test meddelelse** , men kun efter konfigurationen af beskeder er blevet gemt.

12. Hvis meddelelser sendes til en webserver skal du, markere afkrydsningsfeltet **Giv besked ved HTTP Post** .

    ![Giv besked ved HTTP Post formular](./media/cdn-real-time-alerts/cdn-notify-http.png)

    Angiv den URL-adresse, hvor du vil have HTTP meddelelsen skrevet i feltet **URL-adresse** . Angiv HTTP-headere skal sendes i anmodningen, i tekstfeltet **sidehoveder** .  Du kan tilpasse meddelelsen ved hjælp af listen over **tilgængelige nøgleord** til at indsætte dynamisk beskeder om data, når meddelelsen er sendt til **brødteksten** .  **Overskrifter** og **brødtekst** standard til en XML-data, der ligner den nedenstående eksempel.

    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```

    > [AZURE.NOTE] Du kan teste HTTP Post meddelelsen ved at klikke på knappen **Test meddelelse** , men kun efter konfigurationen af beskeder er blevet gemt.

13. Klik på knappen **Gem** for at gemme din besked konfiguration.  Hvis du har markeret **Besked aktiveret** i trin 5, er din besked er nu aktiv.

## <a name="next-steps"></a>Næste trin

- Analysere [realtid statistik i Azure CDN](cdn-real-time-stats.md)
- Gå dybere med [Avancerede HTTP-rapporter](cdn-advanced-http-reports.md)
- Analysere [brugsmønstre](cdn-analyze-usage-patterns.md)

