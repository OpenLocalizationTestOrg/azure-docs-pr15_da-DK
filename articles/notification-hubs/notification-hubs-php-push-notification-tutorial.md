<properties 
    pageTitle="Sådan bruger du besked om Hubs med PHP" 
    description="Lær at bruge Azure meddelelse Hubs fra en PHP back end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>Sådan bruger du besked om Hubs fra PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan få adgang til alle meddelelser Hubs funktioner fra en Java/PHP/fonetisk back end-ved hjælp af grænsefladen meddelelse Hub RESTEN, som beskrevet i emnet MSDN [Meddelelse Hubs REST API'er](http://msdn.microsoft.com/library/dn223264.aspx).

I dette emne viser vi Sådan:

* Oprette en RESTEN klienten til meddelelser Hubs funktioner i PHP;
* Følg [få Introduktion selvstudium](notification-hubs-ios-apple-push-notification-apns-get-started.md) til din mobil platform efter valg implementering af back end-del i PHP.

## <a name="client-interface"></a>Klientgrænseflade
Den primære klientgrænseflade kan angive de samme metoder, der er tilgængelige i [.NET meddelelse Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx), dette gør det muligt at oversætte direkte alle selvstudier og eksempler på nuværende tidspunkt tilgængelig på dette websted og bidraget af community på internettet.

Du kan finde den kode, der er tilgængelige i [PHP RESTEN slikpapir eksempel].

For eksempel, til at oprette en klient:

    $hub = new NotificationHub("connection string", "hubname"); 

Sådan sendes en iOS oprindelig meddelelse:
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementering
Hvis du ikke allerede har, skal du følge vores [få Introduktion selvstudium] op til det sidste afsnit, hvor du har implementere back end.
Hvis du vil kan du også bruge kode fra [PHP RESTEN slikpapir eksempel] og gå direkte til afsnittet [færdigt selvstudiet](#complete-tutorial) .

Alle oplysninger om at implementere en fuld RESTEN slikpapir kan findes på [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). I dette afsnit beskriver vi PHP implementeringen af de vigtigste trin, der kræves for at få adgang til meddelelse Hubs RESTEN slutpunkter:

1. Fortolke forbindelsesstrengen
2. Oprette godkendelse token
3. Udføre HTTP opkaldet

### <a name="parse-the-connection-string"></a>Fortolke forbindelsesstrengen

Her er den primære klasse, der implementerer klienten, hvis parametre, der fortolker forbindelsesstrengen:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Oprette sikkerhedstoken
Oplysninger om sikkerhed token oprettelse findes [her](http://msdn.microsoft.com/library/dn495627.aspx).
Følgende metode skal føjes til klassen **NotificationHub** til at oprette tokenet baseret på URI for den aktuelle anmodning og de legitimationsoplysninger, der er hentet fra forbindelsesstrengen.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Sende en meddelelse
Lad os definere først en klasse, der repræsenterer en meddelelse.

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

Denne klasse er en objektbeholder til helhed oprindelig meddelelse eller et sæt af egenskaber på store og små bogstaver af en skabelon meddelelse og et sæt af overskrifter, som indeholder format (Oprindelig platform eller skabelon) og platform-specifikke egenskaber (som Apple udløb egenskab og WNS sidehoveder).

Se [meddelelse Hubs REST API'er dokumentation](http://msdn.microsoft.com/library/dn495827.aspx) og de specifikke meddelelse platforme formater for alle de tilgængelige indstillinger.

Men med denne klasse, kan vi nu skrive send besked om metoder inde i klassen **NotificationHub** .

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

De ovenstående metoder send anmodning om en HTTP POST til /messages slutpunktet af din meddelelse hub med den korrekte brødtekst og overskrifter til at sende meddelelsen.

##<a name="complete-tutorial"></a>Fuldføre selvstudiet
Du kan nu udføre selvstudiet komme i gang ved at sende meddelelsen fra en PHP back end.

Initialiseret kunden besked om Hubs (Udskift forbindelse streng og hub navn som instructed i [få Introduktion selvstudium]):

    $hub = new NotificationHub("connection string", "hubname"); 

Føj send kode afhængigt af dine mål mobil platform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store og Windows Phone 8.1 (ikke-Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 og 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Kører din PHP kode skal give nu, en meddelelse, der vises på målenheden.


## <a name="next-steps"></a>Næste trin
I dette emne viste vi hvordan du opretter en simpel Java RESTEN klienten til meddelelser Hubs. Her kan du:

* Hent fuld [PHP RESTEN slikpapir eksempel], der indeholder alle ovenstående kode.
* Lær mere om meddelelse Hubs mærkning funktion i [Bryd nyheder selvstudiet]
* Få mere at vide om skubbe beskeder til individuelle brugere i [Giv brugerne besked selvstudium]

Du kan finde flere oplysninger, se også [PHP Developer Center](/develop/php/).

[PHP RESTEN slikpapir eksempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Hent Introduktion selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
