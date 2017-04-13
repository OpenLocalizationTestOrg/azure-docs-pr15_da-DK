<properties
    pageTitle="Azure Mobile aftale Web SDK opgradering procedurer | Microsoft Azure"
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
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure Mobile aftale Web SDK opgradering procedurer

Hvis du har allerede integreret en tidligere version af Azure Mobile aftale Web SDK i dit webprogram, skal du overveje følgende punkter, når du opgraderer SDK.

Hvis du har ignoreret flere versioner af Mobile aftale Web SDK, skal du kan udføre flere procedurer under opgraderingen. Eksempelvis hvis du overfører fra 1.4.0 til 1.6.0, først følge fremgangsmåden til at opgradere fra 1.4.0 til 1.5.0. Følg derefter procedurer til at opgradere fra 1.5.0 til 1.6.0.

Uanset hvilken version du opgraderer fra, erstatte en tidligere version af filen azure-engagement.js med den seneste version af filen.

## <a name="upgrade-from-121-to-200"></a>Opgradere fra 1.2.1 til 2.0.0

I dette afsnit beskrives, hvordan du kan overføre en Mobile aftale Web SDK-integration fra tjenesten Capptain, som Capptain SAS tilbyder en Azure Mobile aftale app. Hvis du overfører fra en tidligere version, skal du se webstedet Capptain for at overføre først til 1.2.1, og derefter anvende følgende procedurer.

Denne version af Mobile aftale Web SDK understøtter ikke Samsung Smart TV, Opera TV, webOS eller funktionen rækkevidde.

>[AZURE.IMPORTANT] Capptain og Azure Mobile aftale er ikke den samme tjeneste. Følgende procedure fremhæver kun Sådan overføres klient-app. Overføre Mobile aftale Web SDK i appen overføres ikke dine data fra en Capptain server til en server, Mobile aftale.

### <a name="javascript-files"></a>JavaScript-filer

Erstatte fil capptain-sdk.js med filen azure-engagement.js, og derefter opdatere din script indførsel i overensstemmelse hermed.

### <a name="remove-capptain-reach"></a>Fjerne Capptain rækkevidde

Denne version af Mobile aftale Web SDK understøtter ikke funktionen rækkevidde. Hvis du integreret Capptain rækkevidde i dit program, skal du fjerne den.

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

### <a name="remove-deprecated-apis"></a>Fjerne frarådede API'er

Nogle API'er fra Capptain frarådes i Mobile aftale Web SDK.

Fjerne et opkald til følgende API'er: `agent.connect`, `agent.disconnect`, `agent.pause`, og `agent.sendMessageToDevice`.

Fjerne alle forekomster af de følgende tilbagekald fra konfigurationen af Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, og `onPushMessageReceived`.

### <a name="configuration"></a>Konfiguration

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

### <a name="javascript-apis"></a>JavaScript API'er

Objektet globale JavaScript `window.capptain` er blevet omdøbt `window.azureEngagement` , men du kan bruge den `window.engagement` alias til API-opkald. Du kan ikke bruge aliaset for at definere SDK konfigurationen.

For eksempel `capptain.deviceId` bliver `engagement.deviceId`, `capptain.agent.startActivity` bliver `engagement.agent.startActivity`, og så videre.
