<properties 
    pageTitle="Azure Mobile aftale Android SDK Integration" 
    description="Seneste opdateringer og procedurer for Android SDK til Azure Mobile aftale"
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="upgrade-procedures"></a>Opgradere procedurer

Hvis du har allerede integreret en ældre version af vores SDK i dit program, har du overveje følgende punkter, når du opgraderer SDK.

Du kan være nødvendigt at følge flere procedurer, hvis du ikke har set flere versioner af SDK. Eksempelvis hvis du overfører fra 1.4.0 til 1.6.0, du skal først følge fremgangsmåden "fra 1.4.0 til 1.5.0" derefter fremgangsmåden "fra 1.5.0 til 1.6.0".

Uanset hvilken version du opgraderer fra, du skal erstatte den `mobile-engagement-VERSION.jar` med et nyt.

##<a name="from-420-to-421"></a>Fra 4.2.0 til 4.2.1

Dette trin kan faktisk udføres på en hvilken som helst version af SDK, er det en forbedring af sikkerhed, når du integrerer rækkevidde aktiviteter.

Du bør nu tilføje `exported="false"` til alle rækkevidde aktiviteter.

Rækkevidde aktiviteter ser nu sådan ud din `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

##<a name="from-400-to-410"></a>Fra 4.0.0 til 4.1.0

SDK nu håndtaget nye tilladelse modellen fra Android M.

Hvis du bruger placering funktioner eller overblik meddelelser skal du læse [dette afsnit](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Ud over den nye tilladelsesmodel understøtter vi nu konfiguration af placering funktioner på kørselstidspunktet.
Vi er stadig kompatible med parametrene manifest for placering, men det frarådes nu. For at bruge runtime konfiguration skal du fjerne de følgende afsnit fra din ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

og Læs [denne opdaterede procedure](mobile-engagement-android-integrate-engagement.md#location-reporting) for at bruge runtime konfiguration i stedet.

##<a name="from-300-to-400"></a>Fra 3.0.0 til 4.0.0

### <a name="native-push"></a>Oprindelig opslagsnål

Oprindelig opslagsnål (GCM/ADM) nu bruges også til i app-meddelelser så skal du konfigurere oprindelige opslagsnål legitimationsoplysninger for alle typer opslagsnål campaign.

Hvis du ikke allerede har gjort skal du følge [denne fremgangsmåde](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Integration af rækkevidde er blevet ændret i ``AndroidManifest.xml``.

Erstat:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Ved

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Der er muligvis en indlæsning skærm nu, når du klikker på en meddelelse (med tekst/web indhold) eller en afstemning.
Du skal tilføje denne for disse kampagner til at arbejde i 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Ressourcer

Integrere den nye `res/layout/engagement_loading.xml` filer til projektet.

##<a name="from-240-to-300"></a>Fra 2.4.0 til 3.0.0

Følgende fremgangsmåde beskriver, hvordan til at overføre en SDK-integration fra tjenesten Capptain, som Capptain SAS til en app drevet af Azure Mobile aftale. Hvis du overfører fra en tidligere version, skal du se webstedet Capptain for at overføre til 2.4.0 først og derefter anvende følgende fremgangsmåde.

>[AZURE.IMPORTANT] Capptain og Mobile aftale er ikke de samme tjenester, og den fremgangsmåde, der er angivet under fremhæves kun Sådan overføres klient-app. Overføre SDK i appen overføres ikke dine data fra Capptain servere til Mobile aftale-serverne.

### <a name="jar-file"></a>GLAS fil

Erstatte `capptain.jar` ved `mobile-engagement-VERSION.jar` i din `libs` mappe.

### <a name="resource-files"></a>Ressourcefiler

Alle filer, ressource, som vi har fået (foranstillet `capptain_`) har skal erstattes af de nye (præfikset `engagement_`).

Hvis du har tilpasset filerne, er det nødvendigt at anvende dine tilpasninger på de nye filer, **alle id'er i ressourcefilerne er også blevet omdøbt**igen.

### <a name="application-id"></a>Program-ID

Aftale bruger nu en forbindelsesstreng til at konfigurere SDK id'erne som program-id.

Du skal bruge `EngagementAgent.init` metode i din starteren aktivitet således:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Forbindelsesstrengen for dit program vises på Azure-portalen.

Fjern eventuelle opkald til `CapptainAgent.configure` som `EngagementAgent.init` erstatter denne metode.

Den `appId` kan ikke længere konfigureres ved hjælp af `AndroidManifest.xml`.

Skal du fjerne denne sektion fra din `AndroidManifest.xml` Hvis du har den:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API

Hvert opkald til en hvilken som helst Java klasse af vores SDK har omdøbes; for eksempel `CapptainAgent.getInstance(this)` skal omdøbes `EngagementAgent.getInstance(this)`, `extends CapptainActivity` skal omdøbes `extends EngagementActivity` osv...

Hvis du er integreret med standard agent indstillingsfiler, standardfilnavnet er nu `engagement.agent` og nøglen er `engagement:agent`.

Når du opretter web meddelelser, Javascript projektmappen er nu `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Mange ændringer er der sket der tjenesten deles ikke længere og mange modtagere kan ikke eksporteres længere.

Angivelsen tjenesten er nu nemmere; Fjern filteret bruges administrationssiden og alle metadata indeni, og Tilføj `exportable=false`.

Plus alt omdøbes for at bruge aftale.

Nu ser den således:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Når du vil aktivere test logfiler, metadata nu er blevet flyttet til mærket programmet og er blevet omdøbt:

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Alle andre metadata er bare blevet omdøbt, her er den komplette liste (over kurset Omdøb kun dem, du bruger):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play og SmartAd registrering er blevet fjernet fra SDK du kun behøver at fjerne denne ikke udskiftes:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Rækkevidde aktiviteter angives nu sådan ud:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Hvis du har brugerdefinerede rækkevidde aktiviteter, du skal kun ændre de bruges administrationssiden handlinger, så det svarer til enten `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` eller `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Broadcast modtagerne er blevet omdøbt samt vi nu føje `exported=false`. Her er den komplette liste over modtagere med den nye specifikation (af kurset Omdøb kun dem, du bruger):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Sporing af modtageren er blevet fjernet, så du skal fjerne dette afsnit:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Bemærk, at angivelsen af implementeringen af broadcast modtageren **EngagementMessageReceiver** er ændret i den `AndroidManifest.xml`. Dette skyldes, at API til at sende og fjerne vilkårlig XMPP meddelelser fra vilkårlig XMPP enheder og API til at sende og modtage meddelelser mellem enheder er blevet fjernet. Dermed, du skal også slette følgende tilbagekald fra implementeringen **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

og

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

Slet derefter alle opkaldet på **EngagementAgent** for:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

og

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Proguard konfiguration kan blive påvirket af rebranding, reglerne nu søger som:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
