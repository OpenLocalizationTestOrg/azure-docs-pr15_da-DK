<properties
    pageTitle="Skalere forekomst Tæl manuelt eller automatisk | Microsoft Azure"
    description="Lær at tilpasse dine tjenester Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Skalere forekomst Tæl manuelt eller automatisk

[Azure-portalen](https://portal.azure.com/), kan du manuelt angive forekomst antallet af din tjeneste, eller du kan angive parametre, der har den automatisk skala, der er baseret på behov. Dette kaldes typisk eller *skala ud* *i*.

Før skalering baseret på forekomst Tæl, skal du overveje, skalering påvirkes af **priser niveau** ud over forekomst Tæl. Andre priser niveauer kan have forskellige tal kerner og hukommelse, og så de får bedre ydeevne for det samme antal forekomster (som er eller *skala op* *ned*). Denne artikel omhandler specifikt *skala i* og *ud*.

Du kan skalere i portalen, og du kan også bruge [REST-API](https://msdn.microsoft.com/library/azure/dn931953.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) til at justere skala manuelt eller automatisk.

> [AZURE.NOTE] I denne artikel beskrives, hvordan du opretter en Autoskalering indstilling på portalen på [http://portal.azure.com](http://portal.azure.com). Autoskalering indstillinger, der er oprettet i denne portal kan ikke redigeres den portalen klassisk ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Skalering manuelt

1. Klik på **Gennemse**i [Azure-portalen](https://portal.azure.com/), og derefter gå til den ressource, du vil skalere, som en **App-serviceaftale**.

2. Feltet **skala** i **Handlinger, der** fortæller dig, at status for skalering: **fra** for når du skaleringen manuelt **på** til, når du skalering med et eller flere målepunkter for ydeevnen.
    ![Feltet skala](./media/insights-how-to-scale/Insights_UsageLens.png)

3. At klikke på feltet, kommer du til bladet **skala** . På øverst del af bladet skala kan du se en oversigt over Autoskalering handlinger tjenesten.  
    ![Skala blade](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Kun de handlinger, der er udført af Autoskalering vises i dette diagram. Hvis du manuelt justerer forekomst antallet, afspejles ikke ændringen i dette diagram.

4. Du kan justere tal **forekomster** med skyderen manuelt.
5. Klik på kommandoen **Gem** og du får skaleres til dette antal forekomster næsten med det samme.

## <a name="scaling-based-on-a-pre-set-metric"></a>Skalering baseret på en forudindstillet metrikværdi

Hvis du vil antallet af forekomster for automatisk at justere baseret på en metrikværdi, kan du vælge den ønskede metrikværdi i rullemenuen **skala ved** . For en **App-serviceaftale** kan du f.eks skalere med **CPU procentdel**.

1. Når du vælger en metrikværdi får du en skyder og/eller, tekstfelter for at angive det antal forekomster, du vil skalere mellem:

    ![Skala blade med CPU procent](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Autoskalering tager aldrig din tjeneste under eller over grænserne, som du fastsætter, uanset din Indlæs.

2. Andet, du vælger målområdet for en metrikværdi. Eksempelvis hvis du vælger **CPU procentdel**, kan du angive et mål for den gennemsnitlige CPU på tværs af alle forekomster i din tjeneste. En skala ud af, sker der, når den gennemsnitlige CPU overskrider maksimumgrænsen du definerer, på samme måde en skala i der sker, når gennemsnitlige CPU udelader under minimum.

3. Klik på kommandoen **Gem** . Autoskalering kontrollerer hvert par minutter til at sikre, at du er i den forekomst område- og destinationswebsteder for din metrikværdi. Når din tjeneste modtager ekstra trafik, får du flere forekomster uden at gøre noget.

## <a name="scale-based-on-other-metrics"></a>Skala på andre basis måleenheder

Du kan skalere baseret på målepunkter end de forudindstillinger, der vises i rullemenuen **skalere med** og kan selv har et komplekst sæt af Skaler ud og skalere i regler.

### <a name="adding-or-changing-a-rule"></a>Tilføje eller ændre en regel

1. Vælg **regler for tidsplan og ydeevnen** i rullemenuen **skala ved** : ![ydeevne regler](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Hvis du tidligere havde Autoskalering, skal du se en oversigt over de nøjagtige regler, der fandtes på.

3. Hvis du vil skalere på basis af en anden metriske Klik på rækken **Tilføj regel** . Du kan også klikke på en af de eksisterende rækker fra den metrikværdi, du tidligere havde til den metrisk, du vil skalere ved at ændre.
![Tilføj regel](./media/insights-how-to-scale/Insights_AddRule.png)

4. Nu skal du vælge hvilke metrisk, du vil skalere med. Når du vælger en metrikværdi, der er et par ting, du bør overveje:
    * *Ressource* metrikværdien kommer fra. Dette vil typisk være den samme som den ressource, du skalering. Men hvis du vil skalere med en lagerplads kø dybde, er den kø, du vil skalere ved ressourcen.
    * *Metriske navn* sig selv.
    * *Tid sammenlægning* af metrikværdien. Dette er, hvordan dataene er kombination over *varigheden*.

5. Når du har valgt dine metrisk vælge du grænsen for metriske og operatoren. Du kan f.eks, at **større end** **80%**.

6. Vælg derefter den handling, du vil tage. Der er et par forskellige typer handlinger:
    * Øge eller mindske med – dette vil tilføje eller fjerne **værdi** antallet af forekomster, du definerer
    * Øge eller mindske percent – dette ændrer antallet forekomst med en procent. For eksempel kan du lægge 25 i feltet **værdi** , og hvis du havde aktuelt 8 forekomster, 2 ville blive tilføjet.
    * Øge eller mindske – Dette indstiller forekomst antallet til den **værdi** , du definerer.

7. Til sidst, kan du vælge fantastiske ned - hvor længe denne regel skal vente efter den forrige handling skala skalere igen.

8. Når du har konfigureret din regel Klik på **OK**.

9. Når du har konfigureret alle de regler, du vil, skal du sørge for at ramme kommandoen **Gem** .

### <a name="scaling-with-multiple-steps"></a>Skalering med flere trin

Ovenstående eksempler er ret grundlæggende. Hvis du vil være mere tilstanden om skalering op (eller ned), kan du også tilføje flere skala regler for den samme metrikværdi. Du kan for eksempel definere to skala regler på CPU procent:

1. Skalere ud af 1 forekomst, hvis CPU procentdel er over 60%
2. Skalere ud af 3 forekomster, hvis CPU procentdel er højere end 85%

![Flere skala regler](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Med denne ekstra regel, hvis din Indlæs overstiger 85% før handlingen skala, får du to flere forekomster i stedet for en.

## <a name="scale-based-on-a-schedule"></a>Skala, der er baseret på en tidsplan


Som standard, når du opretter en regel for skala standardtypografierne altid. Du kan se, når du klikker på overskriften profil:

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Du kan dog vil have mere tilstanden skalering under dag eller uge, end på weekenden. Du kan også lukke din tjeneste helt væk fra arbejdstid.

1. Vælg **Gentagelse** i stedet for **altid** for at gøre dette, på den profil, du har, og vælg de tidspunkter, hvor du vil anvende profilen.

2. For eksempel for at få en profil, der gælder inden for uge, i rullemenuen **dage** Fjern markeringen i **lørdag** og **søndag**.

3. Angive **starttidspunktet** til det tidspunkt, du vil starte på for at få en profil, der gælder de lygtetændingstiden.
    ![Standard gentagelse](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Klik på **OK**.

5. Derefter skal du tilføje den profil, som du vil anvende på andre tidspunkter. Klik på rækken **Tilføj profil** .
    ![Ikke på arbejde](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Navngiv din nye, sekund, profil, for eksempel kan du ringe til det **ikke på arbejde**.

7. Derefter vælge **Gentagelse** igen, og vælg det ønskede i dette tidsrum forekomst Tæl område.

8. Som med standardprofil, Vælg de **dage** vil du denne profil skal gælde for, og den **starttidspunktet** om dagen.

>[AZURE.NOTE] Autoskalering bruger sommertid sparer regler for den **tidszone** , du vælger. Dog under sommertid UTC-forskydning, vises den grundlæggende tidszone forskydning, ikke sommertid sparer UTC forskydning.

9. Klik på **OK**.

10. Nu skal du tilføje netop de regler, du vil anvende under din anden profil. Klik på **Tilføj regel**, og derefter kan du oprette den samme regel, du har under standardprofilen.
    ![Tilføj regel for at slå arbejde](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Sørg for at oprette både en regel for Skaler ud og skalering i eller under profilen forekomst antallet vil kun vokse (eller Formindsk).

12. Til sidst skal du klikke på **Gem**.

## <a name="next-steps"></a>Næste trin

* [Overvåge tjenesten målepunkter](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
* [Aktivere overvågning og diagnosticering](insights-how-to-use-diagnostics.md) til at indsamle detaljerede hyppigt målepunkter på din tjeneste.
* [Modtag beskeder om meddelelser](insights-receive-alert-notifications.md) , når funktionsdygtige begivenheder sker eller målepunkter på tværs af en tærskelværdi.
* [Overvåg programmet ydeevne](../application-insights/app-insights-azure-web-apps.md) , hvis du ønsker at forstå præcis hvordan din kode klarer sig i skyen.
* [Se begivenheder og overvågningslogge for](insights-debugging-with-events.md) at lære alt det, der er sket i din tjeneste.
* [Overvåge tilgængelighed og tilgængeligheden af enhver webside](../application-insights/app-insights-monitor-web-app-availability.md) med programmet viden, så du kan finde ud af, om din side er nede.
