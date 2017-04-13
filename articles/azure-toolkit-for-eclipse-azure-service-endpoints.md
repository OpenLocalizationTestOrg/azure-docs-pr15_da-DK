<properties
    pageTitle="Azure Service slutpunkter"
    description="I denne artikel beskrives indstillingerne Azure Service slutpunkt i Azure-værktøjskassen til Eklipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Azure Service slutpunkter #

Azure service slutpunkter afgør, om dit program er installeret på og administreres af globale Azure-platformen, Azure drevet af 21Vianet i Kina eller en privat Azure-platformen. Dialogboksen **Slutpunkter for tjenesten** giver dig mulighed at angive, hvilken tjeneste slutpunkter, du vil bruge. Åbner dialogboksen **Service slutpunkter** i Eklipse, skal du klikke på **vinduet**, skal du klikke på **Indstillinger**, udvide **Azure**, og klik derefter på **Slutpunkter for tjenesten**. Angive **Aktive Angiv** felt bestemmer, hvilke Azure service slutpunkter der skal bruges til Azure projekter i din aktuelle arbejdsområde.

Følgende viser dialogboksen **Slutpunkter for tjenesten** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Angive slutpunkterne service ##

Benyt en af følgende fremgangsmåder i dialogboksen **Slutpunkter for tjenesten** :

* Hvis du vil bruge den globale Azure platform rullelisten **Aktive angive** Vælg **windowsazure.com** , og klik på **OK**.
* Hvis du vil bruge Azure drevet af 21Vianet i Kina, skal du på **Aktive angive** i rullelisten Vælg **windowsazure.cn (Kina)** , og klik på **OK**.
* Hvis du vil bruge en privat Azure platform:
    1. Klik på **Rediger**.
    2. Åbnes en dialogboks, informerer dig om, at dialogboksen **Slutpunkter for tjenesten** lukkes, og sæt indstillingsfil åbnes. Klik på **OK**.
    3. Oprette en ny i filen preferencesets.xml `preferenceset` element. Dette nyt element, oprette `name`, `blob`, `management`, `portalURL` og `publishsettings` attributter, og Føj værdier for dem, der svarer til din private Azure platform. Du kan bruge de værdier, der er knyttet til den eksisterende `preferenceset` elementer som skabeloner. **Bemærk**: den værdi, der bruges til den `blob` attribut skal indeholde tekst "blob" i URL-adressen.
    4. Gem og Luk preferencesets.xml.
    5. Åbne dialogboksen **Slutpunkter for tjenesten** .
    6. Vælg det aktive sæt, du har oprettet rulleliste **Aktive angive** og klik på **OK**.
    7. Når du har oprettet din private Azure platform `preferenceset` element, du kan ændre de værdier, der er tildelt til den ved at klikke på knappen **Rediger** i dialogboksen **Tjenester slutpunkt** . Du kan også oprette flere private Azure-platformen `preferenceset` elementer, hvis du ønsker.

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

[Oprette et Hej verden program til Azure i Eklipse][]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
