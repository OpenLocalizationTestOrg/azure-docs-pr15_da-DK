<properties 
    pageTitle="Windows Phone Silverlight SDK oversigt" 
    description="Oversigt over Windows Phone Silverlight SDK til Azure Mobile aftale"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone Silverlight SDK oversigt til Azure Mobile aftale

Start her for at få oplysninger om, hvordan du integrerer Azure Mobile aftale i en Windows Phone Silverlight-App. Hvis du vil gerne Prøv det første, Sørg for, at du har fuldført vores [15 minutter selvstudium](mobile-engagement-windows-phone-get-started.md).

Klik for at se de [SDK indhold](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Integration af procedurer

1. Start her: [hvordan du integrerer Mobile aftale i din Windows Phone Silverlight-app](mobile-engagement-windows-phone-integrate-engagement.md)

2. Til meddelelser: [hvordan du integrerer rækkevidde (meddelelser om) i din Windows Phone Silverlight-app](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Mærke plan implementering: [Sådan bruges de avancerede Mobile aftale mærkning API i din Windows Phone Silverlight-app](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Produktbemærkninger

###<a name="330-04192016"></a>3.3.0 (04-19/2016)
En del af *MicrosoftAzure.MobileEngagement* nuget pakke **v3.4.0**

-   Tilføjede "TestLogLevel" API til at aktivere/deaktivere/filter console logfiler, der udsendes af SDK.

Skal du se den [komplette produktbemærkninger](mobile-engagement-windows-phone-release-notes.md) til tidligere version

##<a name="upgrade-procedures"></a>Opgradere procedurer

Hvis du har allerede integreret en ældre version af vores SDK i dit program, har du overveje følgende punkter, når du opgraderer SDK.

Du kan være nødvendigt at følge flere procedurer, hvis du ikke har set flere versioner af SDK. Se den komplette [Opgradere procedurer](mobile-engagement-windows-phone-upgrade-procedure.md). Eksempelvis hvis du overfører fra 0.10.1 til 0.11.0, du skal først følge fremgangsmåden "fra 0.9.0 til 0.10.1" derefter fremgangsmåden "fra 0.10.1 til 0.11.0".

###<a name="from-200-to-330"></a>Fra 2.0.0 til 3,3,0

####<a name="test-logs"></a>Test logfiler

Console logfiler, der er oprettet med SDK kan nu være aktiveret/deaktiveret/filtreret. Hvis du vil tilpasse, skal du opdatere egenskaben `EngagementAgent.Instance.TestLogEnabled` til en af den værdi, der er tilgængelige fra den `EngagementTestLogLevel` optælling, for eksempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Opgradere fra ældre versioner

Se [opgradere procedurer](mobile-engagement-windows-phone-upgrade-procedure.md)
 
