<properties
    pageTitle="Azure Mobile aftale Web SDK oversigt | Microsoft Azure"
    description="De seneste opdateringer og procedurer for Web SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk"></a>Azure Mobile aftale Web SDK

Start her for alle oplysninger om, hvordan du integrerer Azure Mobile aftale i en WebApp. Hvis du gerne vil give det en til at prøve at komme i gang med din egen WebApp, kan du se vores [15-minutters selvstudium](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Integration af procedurer
1. Lær, [hvordan du integrerer Mobile aftale i din online](mobile-engagement-web-integrate-engagement.md).

2. For mærke plan implementering Lær [at bruge de avancerede Mobile aftale mærkning API i din online](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Produktbemærkninger

### <a name="202-10182016"></a>2.0.2 (10-18/2016)

-   Fast nedbrud på private browsing (Safari).
-   Fast nedbrud i browsere med deaktiveret cookies.

For alle versioner, skal du se den [komplette produktbemærkninger](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Opgradere procedurer

### <a name="upgrade-from-121-to-200"></a>Opgradere fra 1.2.1 til 2.0.0

I nedenstående afsnit beskrives, hvordan du kan overføre en Mobile aftale Web SDK-integration fra tjenesten Capptain, som Capptain SAS tilbyder en Azure Mobile aftale app. Hvis du overfører fra en version tidligere end 1.2.1, skal du se webstedet Capptain for at overføre til 1.2.1 først og derefter anvende følgende procedurer.

Denne version af Mobile aftale Web SDK understøtter ikke Samsung Smart TV, Opera TV, webOS eller funktionen rækkevidde.

>[AZURE.IMPORTANT] Capptain og Azure Mobile aftale er ikke den samme tjeneste, og følgende procedurer fremhæve kun Sådan overføres klient-app. Overføre Mobile aftale Web SDK i appen overføres ikke dine data fra en Capptain server til en server, Mobile aftale.

#### <a name="javascript-files"></a>JavaScript-filer

Erstatte fil capptain-sdk.js med filen azure-engagement.js, og derefter opdatere din script indførsel i overensstemmelse hermed.

#### <a name="remove-capptain-reach"></a>Fjerne Capptain rækkevidde

Denne version af Mobile aftale Web SDK understøtter ikke funktionen rækkevidde. Hvis du har integreret Capptain rækkevidde i dit program, skal du fjerne den.

Fjerne når CSS importen fra din side, og Slet relaterede CSS-fil (capptain-reach.css, som standard).

Slet følgende rækkevidde ressourcerne: Luk billedet (capptain-close.png, som standard) og ikonet brand (capptain--meddelelsesikon, som standard).

Fjerne når Brugergrænsefladen til i app'en meddelelser. Standardlayoutet ser sådan ud:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Fjerne når Brugergrænsefladen for tekst og web meddelelser og afstemninger. Standardlayoutet ser sådan ud:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Fjerne den `reach` objekt fra din konfiguration, hvis den findes. Det ser sådan ud:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Fjern eventuelle andre rækkevidde opsætning, som kategorier.

#### <a name="remove-deprecated-apis"></a>Fjerne frarådede API'er

Nogle API'er fra Capptain frarådes i Mobile aftale Web SDK.

Fjerne et opkald til følgende API'er: `agent.connect`, `agent.disconnect`, `agent.pause`, og `agent.sendMessageToDevice`.

Fjerne en af følgende tilbagekald fra konfigurationen af Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, og `onPushMessageReceived`.

#### <a name="configuration"></a>Konfiguration

Mobile aftale bruger en forbindelsesstreng til at konfigurere SDK-id'er, for eksempel program-id.

Erstat program-ID med din forbindelsesstreng. Bemærk, at det globale objekt til SDK konfigurationen ændringer fra `capptain` til `azureEngagement`.

Før overførsel:

    window.capptain = {
      appId: ...,
      [...]
    };

Efter overflytningen:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

Forbindelsesstrengen for dit program vises i portalen Azure.

#### <a name="javascript-apis"></a>JavaScript API'er

Objektet globale JavaScript `window.capptain` er blevet omdøbt `window.azureEngagement`, men du kan bruge den `window.engagement` alias til API-opkald. Du kan ikke bruge dette alias for at definere SDK konfigurationen.

For eksempel `capptain.deviceId` bliver `engagement.deviceId`, `capptain.agent.startActivity` bliver `engagement.agent.startActivity`, og så videre.

Hvis du har allerede integreret en tidligere version af Azure Mobile aftale Web SDK i dit program, skal du læse om [opgradere procedurer](mobile-engagement-web-upgrade-procedure.md).
