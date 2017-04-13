<properties
    pageTitle="Delt Access signaturer oversigt | Microsoft Azure"
    description="Hvad er delt Access signaturer, hvordan de fungerer, og hvordan du kan bruge dem fra Node, PHP og C#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Delt adgang signaturer

*Delt Access signaturer* (SAS) er den primære sikkerhed ordning for tjenesten Bus, herunder begivenhed Hubs, formidlet messaging (køer og emner), og videresendes messaging. I denne artikel beskrives delt Access signaturer, hvordan de fungerer, og hvordan du bruger dem på en måde,-platforme.

## <a name="overview-of-sas"></a>Oversigt over SAS

Delte Access signaturer er en godkendelsesmetoden, der er baseret på SHA 256 sikker hashes eller URI'er. SAS er en meget effektiv metode, der bruges af alle Service Bus tjenester. I faktisk Brug SAS har to komponenter: en *Delt Access-politik* og en *Delt Access signatur* (ofte kaldet en *token*).

Du kan finde flere oplysninger om delt Access signaturer med Service Bus i [delt adgang signatur godkendelse med Service Bus](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Delt Access-politik

En vigtig ting at forstå om SAS er, at det hele begynder med en politik. Du beslutter dig for hver politik for tre dele af oplysninger: **navn**, **omfang**og **tilladelser**. **Navn** er lige det; et entydigt navn i dette område. Omfanget er så nemt: det er URI for den pågældende ressource. For et Service Bus navneområde omfanget er det fulde domænenavn (fulde Domænenavn), f.eks `https://<yournamespace>.servicebus.windows.net/`.

De tilgængelige tilladelser for en politik er stort set ingen forklaring:

  + Send
  + Lytte
  + Administrere

Når du opretter politikken, tildeles en *Primærnøgle* og en *Sekundær nøgle*. Dette er kryptografisk stærk taster. Ikke mister dem eller trænger dem – de er altid tilgængelig i [Azure-portalen][]. Du kan bruge en af de oprettede taster, og du kan gendanne dem når som helst. Men hvis du genoprette eller ændre den primære nøgle i politikken, eventuelle delt Access signaturer oprettet ud fra den vil være ugyldige.

Når du opretter et Service Bus navneområde, oprettes der automatisk en politik for hele navneområdet kaldet **RootManageSharedAccessKey**, og denne politik har alle tilladelser. Du ikke kan logge på som **rod**, så ikke bruge denne politik, medmindre der er en god grund. Du kan oprette flere politikker under fanen **Konfigurer** for navneområdet på portalen. Det er vigtigt at Bemærk, at en enkelt træ niveau i Service Bus (navneområde, kø, begivenhed Hub osv.) kan kun have op til 12 politikker, der er knyttet til den.

## <a name="shared-access-signature-token"></a>Delte Access signatur (token)

Selve politikken er ikke Service Bus adgangstoken. Det er det objekt, som adgangstoken genereres på-ved hjælp af enten den primære eller sekundære nøgle. Tokenet genereres ved at oprette omhyggeligt en streng i følgende format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Hvor `signature-string` er SHA 256 hashværdien af omfanget af tokenet (**omfang** som beskrevet i forrige afsnit) med en CRLF føjet og et udløbstid (i sekunder siden epoke: `00:00:00 UTC` på 1 januar 1970).

Giver resultat ligner følgende pseudo kode og returnerer 32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Værdierne ikke hash'es er i **SharedAccessSignature** strengen, så modtageren kan beregne giver resultat med de samme parametre for at sikre, at den returnerer det samme resultat. URI angiver området, og navnet på den nøgle identificerer politikken skal bruges til at beregne giver resultat. Dette er vigtigt fra et sikkerhed synspunkt. Hvis ikke matcher signaturen, der beregner modtageren (Service Bus), er adgang nægtet. På dette tidspunkt kan du være sikker på, at afsenderen havde adgang til tasten og bør tildeles de rettigheder, der er angivet i politikken.

## <a name="generating-a-signature-from-a-policy"></a>Generere en signatur fra en politik

Sådan du faktisk gøre dette i programkode? Lad os se nærmere på nogle af disse.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>Ved hjælp af den delte Access signatur (på http-niveau)
 
Nu hvor du ved, hvordan du opretter delt Access signaturer for alle objekter i Service Bus, er du klar til at udføre en HTTP-POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
Husk, at det fungerer for alt. Du kan oprette SAS for en kø, emne, abonnement, begivenhed Hub eller relay. Hvis du bruger hver publisher identitet for begivenhed Hubs skal du blot føje `/publishers/< publisherid>`.

Hvis du giver en afsender eller et SAS token-klienten, de har ikke tasten direkte, og de fortryde ikke giver resultat for at hente den. Så har du kontrol over hvad de kan få adgang til, og hvor lang tid. En vigtig ting at huske er, at hvis du ændrer den primære nøgle i politikken, vil eventuelle delt Access signaturer oprettet ud fra det gøres ugyldige.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>Ved hjælp af den delte Access signatur (på AMQP niveau)

I det forrige afsnit, du fik vist hvordan du bruger SAS tokenet med en anmodning om en HTTP POST til at sende data til Service Bus. Som du ved, kan du få adgang til tjenesten Bus ved hjælp af de avancerede meddelelse Queuing Protocol (AMQP), som er den foretrukne protokol til brug af hensyn til ydeevnen i mange situationer. SAS token brugen med AMQP er beskrevet i dokumentet [AMQP Claim-Based sikkerhed Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , der er i arbejde kladde siden 2013, men ordentligt understøttes af Azure i dag.

Før du begynder at sende data til Service Bus, udgiveren skal sende SAS tokenet i en AMQP meddelelse til en klart defineret AMQP node kaldet **$cbs** (du kan se det som en "speciel" kø, der bruges af tjenesten til at købe og validere alle SAS tokens). Udgiveren skal angive feltet **ReplyTo** i AMQP meddelelsen. Dette er den node, hvor tjenesten svar til publisher med resultatet af sikkerhedstoken validering (et enkelt anmodning/svar mønster mellem publisher og service). Denne svar node oprettes "på farten," taler om "dynamisk oprettelse af eksterne node", som beskrevet i specifikationen AMQP 1.0. Når du kontrollere, at SAS Tokenet er gyldig, udgiveren gå frem og begynde at sende data til tjenesten.

Følgende trin viser, hvordan du sender SAS tokenet med AMQP protocol ved hjælp af biblioteket [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Dette er nyttigt, hvis du ikke kan bruge officielle Service Bus SDK (for eksempel på WinRT, .net Compact Framework, .net Framework Micro og sort/hvide) udvikling i C\#. Naturligvis dette bibliotek er nyttigt at forstå, hvordan kravbaseret sikkerhed fungerer på niveauet for AMQP, som du fik vist, hvordan det fungerer på niveauet HTTP (med en anmodning om en HTTP POST og SAS tokenet sendt i overskriften "Godkendelse"). Hvis du ikke har brug for disse deep viden om AMQP, kan du bruge officielle Service Bus SDK med .net Framework-programmer, som kan gøre det for dig.

### <a name="c35"></a>C & #35

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Den `PutCbsToken()` metode modtager *forbindelse* (AMQP forbindelse klasseforekomst som leveret af [AMQP .NET Lite bibliotek](https://github.com/Azure/amqpnetlite)), der repræsenterer TCP-forbindelsen til tjenesten og parameteren *sasToken* , der er SAS tokenet til at sende. 

> [AZURE.NOTE] Det er vigtigt, at forbindelsen er oprettet med **SASL godkendelsesmetoden, der er angivet til ekstern** (og ikke standard almindelig med brugernavn og adgangskode brugte, da du ikke behøver at sende SAS tokenet).

Derefter opretter udgiveren to AMQP links til at sende SAS tokenet og modtage svar (token valideringsresultatet) fra tjenesten.

Meddelelsen AMQP indeholder et sæt af egenskaber, og mere end en enkelt meddelelse. SAS Tokenet er brødteksten i meddelelsen (ved hjælp af dens parametre). Egenskaben **"ReplyTo"** er angivet til nodenavnet for at modtage valideringsresultatet på linket modtager (du kan ændre navnet, hvis du vil, og den skal oprettes dynamisk af tjenesten). Sidste tre programmet/brugerdefinerede egenskaber bruges af tjenesten til at angive, hvilken type handling den skal udføre. Som beskrevet i specifikationen CBS kladde, skal de være **handlingsnavn** ("læg-token"), **Skriv af token** (i dette tilfælde en "servicebus.windows.net:sastoken") og **"navn" på målgruppen** , gælder tokenet (hele objektet).

Når der er sendt SAS tokenet til linket afsenderen, skal udgiveren læse svar på linket, modtager. Svaret er en enkel AMQP meddelelse med et program egenskab med navnet **"statuskode"** , som kan indeholde de samme værdier som en HTTP-statuskode. 

## <a name="next-steps"></a>Næste trin

Se [Service Bus REST-API reference](https://msdn.microsoft.com/library/azure/hh780717.aspx) kan finde flere oplysninger om, hvad du kan gøre med disse SAS tokens.

Se [Service Bus godkendelse og autorisation](service-bus-authentication-and-authorization.md)kan finde flere oplysninger om Service Bus godkendelse. 

Flere eksempler på SAS i C# og JavaScript er i [dette blogindlæg](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Azure-portalen]: https://portal.azure.com