<properties 
   pageTitle="Informere brugere om data modtaget fra sensorer eller andre systemer | Microsoft Azure"
   description="Beskriver, hvordan du bruger begivenhed Hubs til at informere brugere om føler data."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Informere brugere om data modtaget fra sensorer eller andre systemer

Antag, at du har et program, der overvåger data i realtid eller giver rapporter på en tidsplan. Hvis du ser på det websted, hvor disse realtid diagrammer eller rapporter vises, kan du muligvis vist noget, der kræver handling. Hvad nu, hvis du skal være påmindelsen vedrører til disse situationer i stedet for afhængige på skulle huske at kontrollere det offentlige websted? Forestil dig, at du har Tilpas lys i en drivhusgasser, og du skal kende med det samme, hvis lys, der går. En metode til at gøre det ville være med en light føler i væksthus, arrangere skal sendes en mail, hvis lys er slået fra.

![][1]

Forestil dig, at du kører en selskabsdyr boardingafvisning facilitet, og du skal have besked om lav lager levering niveauer i et andet scenarie. For eksempel kan du arrangere skal sendes en SMS-besked fra ERP-system, hvis din lagerbeholdning af hund fødevarer er faldet til et kritiske niveau. 

![][2]

Problemet er, hvordan du får vigtige oplysninger, når bestemte betingelser er opfyldt, ikke, når du kommer til udtjekning af en statisk rapport. Hvis du bruger en [Azure begivenhed Hub][] eller [Azure IoT Hub][] til at hente data fra enheder eller enterprise-programmer som [Dynamics AX][], har du forskellige valgmuligheder til at behandle dem. Du kan se dem på et websted, kan du analysere dem, kan du gemme dem, og du kan bruge dem til at udløse kommandoer til at gøre noget. For at gøre dette, kan du bruge effektive værktøjer som [Azure websteder][], [SQL Azure][], [HDInsight][], [Cortana Intelligence pakke][], [IoT pakke][], [Logik Apps][]eller [Azure meddelelse Hubs][]. Men nogle gange alt, du vil gøre, er at sende dataene til en person med et minimum af omkostninger. Hvis du vil viser, hvordan du gør dette med et lille stykke af kode, har vi angivet en ny stikprøve, [AppToNotifyUsers][]. Indstillinger, der er inkluderet er mail (SMTP), SMS og telefon.

## <a name="application-structure"></a>Programmet struktur

Programmet er skrevet i C#, og filen med vigtige oplysninger i stikprøven indeholder alle de oplysninger, du vil redigere, oprette og publicere programmet. De følgende afsnit indeholder en overordnet oversigt over, hvad betyder det programmet.

Vi begynder med den antagelse, at du har vigtige hændelser, der overføres til en Azure begivenhed Hub eller IoT Hub. En hvilken som helst hub gør, så længe du har adgang til den og ved forbindelsesstrengen.

Hvis du ikke allerede har en begivenhed Hub eller IoT hub, kan du nemt konfigurere en afprøvningen med en Arduino lampeskålens og en hindbær Pi, følge vejledningen i [Oprette forbindelse til prikker](https://github.com/Azure/connectthedots) projektet. Light føler på skjoldet Arduino sender light niveauerne gennem Pi til en [Azure begivenhed Hub][] (**ehdevices**), og et [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -job flytter beskeder til en anden begivenhed hub (**ehalerts**), hvis de light niveauer, der er modtaget falder under et bestemt niveau.

Når **AppToNotify** starter, læser en konfigurationsfil (App.config) for at få den URL-adresse og legitimationsoplysninger for begivenhed hubben modtage beskeder. Lægge æg derefter en proces at overvåge tiden, begivenhed Hub til en hvilken som helst meddelelse, der kommer gennem – så længe du har kan få adgang til URL-adressen for begivenhed Hub eller IoT hub og gyldige legitimationsoplysninger, denne begivenhed Hubs læser kode løbende læser hvad der kommer i. Under start læser programmet også URL-adresse og legitimationsoplysninger for messaging-tjenesten (mail, SMS, telefon) du vil bruge, og navnet/adressen på afsenderen og en liste over modtagere.

Når begivenhed Hub skærmen registrerer en meddelelse, udløser en proces, der sender den meddelelse, ved hjælp af metoden angivet i konfigurationsfilen. Bemærk, at de får alle meddelelser, der registreres. Hvis du angiver skærmen til at pege til en begivenhed-Hub, der modtager ti meddelelser sekundet, afsenderen sender ti meddelelser sekundet – ti mails sekundet, ti SMS-beskeder sekundet, ti telefonopkald sekundet. Sørg for, at du overvåger en begivenhed-Hub, der kun modtager de vigtige beskeder, der skal sendes, du ikke en begivenhed Hub, modtager alle rækkedata fra din sensorer eller programmer ud for derfor.

## <a name="applicability"></a>Anvendelse

Koden i dette eksempel viser kun Sådan overvåge begivenhed Hubs og til at ringe eksterne messaging-tjenesterne, hvis du vil tilføje denne funktionalitet i dit program. Bemærk, at denne løsning er en DIY, henvender sig til udviklere eksempel kun. Den omhandler ikke enterprise krav, som redundans, fejl over, genstart ved fejl osv. Se følgende for mere omfattende og fremstilling løsninger:

- Brug af forbindelser eller pushmeddelelser ved hjælp af tjenesten [Azure logik Apps](../app-service-logic/app-service-logic-connectors-list.md) .
- Brug af [Azure meddelelse Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx), som beskrevet på bloggen [Udsend pushmeddelelser for millioner af mobile enheder, der bruger Azure meddelelse Hubs](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## <a name="next-steps"></a>Næste trin

Det er nemt at oprette en simpel Beskedtjeneste, der sender mails eller SMS-beskeder til modtagere eller kalder dem til relay-data, der modtages af en begivenhed Hub eller IoT Hub. For at installere løsningen for at give brugere, der er baseret på data, der modtages af disse hubs besked, skal du besøge [AppToNotifyUsers][].

Se følgende artikler kan finde flere oplysninger om disse hubs:

- [Azure begivenhed Hubs]
- [Azure IoT-Hub]
- Introduktion til en [begivenhed Hubs selvstudium].
- En komplet [Northwind, der bruger begivenhed Hubs].

For at installere løsningen for at give brugere, der er baseret på data, der modtages af disse hubs besked, skal du besøge:

- [AppToNotifyUsers][]

[Begivenhed Hubs selvstudium]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT-Hub]: https://azure.microsoft.com/services/iot-hub/
[Azure begivenhed Hubs]: https://azure.microsoft.com/services/event-hubs/
[Azure begivenhed Hub]: https://azure.microsoft.com/services/event-hubs/
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Azure websteder]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence pakke]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT pakke]: https://azure.microsoft.com/solutions/iot-suite/
[Logik Apps]: https://azure.microsoft.com/services/app-service/logic/
[Azure meddelelse Hubs]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png