<properties 
    pageTitle="Sådan bruger du besked om Hubs med Python" 
    description="Lær at bruge Azure meddelelse Hubs fra en Python back end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>Sådan bruger du besked om Hubs fra Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
Du kan få adgang til alle meddelelser Hubs funktioner fra en Java/PHP/Python/fonetisk back end-ved hjælp af grænsefladen meddelelse Hub RESTEN, som beskrevet i emnet MSDN [Meddelelse Hubs REST API'er](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Dette er et eksempel på referenceimplementering til implementering af besked om sender i Python og er ikke officielt understøttede beskeder Hub Python SDK.
>
> Dette eksempel er skrevet med Python 3.4.

I dette emne viser vi Sådan:

* Oprette en RESTEN klienten til meddelelser Hubs funktioner i Python.
* Sende meddelelser ved hjælp af Python grænsefladen til meddelelse Hub REST API'er. 
* Få en lagring af HTTP RESTEN anmodning/svaret til fejlfinding/informative formål. 

Du kan følge [få Introduktion selvstudium](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) til din mobil platform efter valg implementering af back end-del i Python.

> [AZURE.NOTE] Omfanget af prøven er kun begrænset for at sende beskeder, og det gøre ikke en hvilken som helst registrering management.

## <a name="client-interface"></a>Klientgrænseflade
Den primære klientgrænseflade kan angive de samme metoder, der er tilgængelige i [.NET meddelelse Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx). Dette gør det muligt at oversætte direkte alle selvstudier og eksempler på nuværende tidspunkt tilgængelig på dette websted og bidraget af community på internettet.

Du kan finde den kode, der er tilgængelige i [Python RESTEN slikpapir eksempel].

For eksempel, til at oprette en klient:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
Sådan sender du en Windows-toastbeskeden:
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>Implementering
Hvis du ikke allerede har, skal du følge vores [få Introduktion selvstudium] op til det sidste afsnit, hvor du har implementere back end.

Alle oplysninger om at implementere en fuld RESTEN slikpapir kan findes på [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). I dette afsnit beskriver vi Python implementeringen af de vigtigste trin, der kræves for at få adgang til meddelelse Hubs RESTEN slutpunkter og sende meddelelser

1. Fortolke forbindelsesstrengen
2. Oprette godkendelse token
3. Sende en meddelelse ved hjælp af HTTP REST-API

### <a name="parse-the-connection-string"></a>Fortolke forbindelsesstrengen

Her er den primære klasse, der implementerer den klient, hvis parametre fortolker forbindelsesstrengen:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Oprette sikkerhedstoken
Oplysninger om sikkerhed token oprettelse findes [her](http://msdn.microsoft.com/library/dn495627.aspx).
De følgende metoder skal føjes til klassen **NotificationHub** til at oprette tokenet baseret på URI for den aktuelle anmodning og de legitimationsoplysninger, der er hentet fra forbindelsesstrengen.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Sende en meddelelse ved hjælp af HTTP REST-API
Første, Lad Brug definere en klasse, der repræsenterer en meddelelse.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Denne klasse er en beholder for helhed oprindelig meddelelse eller et sæt af egenskaber i tilfælde af en skabelon meddelelse et sæt af overskrifter, som indeholder format (Oprindelig platform eller skabelon) og platform-specifikke egenskaber (som Apple udløb egenskab og WNS sidehoveder).

Se [meddelelse Hubs REST API'er dokumentation](http://msdn.microsoft.com/library/dn495827.aspx) og de specifikke meddelelse platforme formater for alle de tilgængelige indstillinger.

Nu med klassen, kan vi skrive send besked om metoder inde i klassen **NotificationHub** .

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

De ovenstående metoder send anmodning om en HTTP POST til /messages slutpunktet af din meddelelse hub med den korrekte brødtekst og overskrifter til at sende meddelelsen.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Brug af fejlfinding egenskab til at aktivere detaljeret logføring
Hvis du aktiverer fejlfinding egenskab under initialisering af besked om hubben skriver detaljeret logføring oplysninger om HTTP-anmodningen og svaret lagring samt detaljerede meddelelse send resultatet. Vi tilføjede for nylig denne egenskab, der kaldes [meddelelse Hubs TestSend egenskab](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) , der returnerer detaljerede oplysninger om med besked om send resultatet. Hvis du vil bruge den - initialiseret ved hjælp af følgende:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Den meddelelse Hub Send anmodning HTTP URL-adresse vedhæftes med en "test" querystring som et resultat. 

##<a name="complete-tutorial"></a>Fuldføre selvstudiet
Du kan nu udføre selvstudiet komme i gang ved at sende meddelelsen fra en Python back end.

Initialiseret kunden besked om Hubs (Udskift forbindelse streng og hub navn som instructed i [få Introduktion selvstudium]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Føj send kode afhængigt af dine mål mobil platform. Dette eksempel tilføjer også højere niveau metoder til at aktivere afsendelse beskeder, der er baseret på en platform fx send_windows_notification til windows. send_apple_notification (for apple) osv. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store og Windows Phone 8.1 (ikke-Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 og 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Kører din Python kode skal give en meddelelse, der vises på målenheden.

## <a name="examples"></a>Eksempler:

### <a name="enabling-debug-property"></a>Hvis du aktiverer fejlfinding egenskab
Når du aktiverer fejlfinding flag under initialisering af NotificationHub, og du får vist detaljerede HTTP-anmodningen og svaret lagring samt NotificationOutcome som følgende sted, hvor du kan forstå, hvilke HTTP-headere sendes i anmodningen og blev modtaget hvilke HTTP-svar fra hubben meddelelse:    ![][1]

Du får vist detaljerede meddelelse Hub resultatet f.eks. 

- Når meddelelsen er sendt til tjenesten Push meddelelse. 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Hvis der ikke er nogen destinationer findes til en hvilken som helst opslagsnål besked om derefter vil du sandsynligvis se følgende i svaret (som angiver, at der ikke er nogen registreringer, der er fundet til at levere meddelelsen sandsynligvis, fordi registreringerne havde nogle uoverensstemmende mærker)

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Transmittere toastbeskeden til Windows 

Bemærk overskrifterne, der bliver sendt ud, når du sender en broadcast toast-besked til Windows-klient. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Sende meddelelse, der angiver et mærke (eller et mærke udtryk)

Bemærk mærker HTTP-headeren, bliver føjet til HTTP-anmodningen (i eksemplet herunder vi sender meddelelsen kun til registreringer med 'sportsgrene' data)

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Send besked om angivelse af flere mærker

Bemærk, hvordan mærker HTTP-headeren ændres, når flere mærker sendes. 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Skabelonbaseret meddelelse

Bemærk, at ændringerne, Formatér HTTP sidehoved og data brødteksten sendes som en del af HTTP-anmodningsteksten:

**Klientsiden - registreret skabelon**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Serversiden - afsendelse af data**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>Næste trin
I dette emne viste vi hvordan du opretter en simpel Python RESTEN klienten til meddelelser Hubs. Her kan du:

* Hent fuld [Python RESTEN slikpapir eksempel], der indeholder alle ovenstående kode.
* Lær mere om meddelelse Hubs mærkning funktion i [Bryd nyheder selvstudium]
* Lær mere om funktionen besked om Hubs skabeloner til i [forbindelse med lokalisering nyheder selvstudium]

<!-- URLs -->
[Python RESTEN slikpapir eksempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Hent Introduktion selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Bryd nyheder selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Lokalisering nyheder selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
