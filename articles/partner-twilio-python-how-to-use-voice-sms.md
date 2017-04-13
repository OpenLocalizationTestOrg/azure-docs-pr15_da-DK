<properties
    pageTitle="Sådan bruges Twilio til tale- og SMS (PHP) | Microsoft Azure"
    description="Lær at foretage et telefonopkald og sende en SMS-meddelelse med tjenesten Twilio API på Azure. Kodeeksempler, der er skrevet i PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Sådan bruges Twilio til tale- og SMS-funktioner i PHP
Denne vejledning viser, hvordan til at udføre almindelige programming opgaver med Twilio API-tjenesten på Azure. De scenarier, der er omfattet omfatter foretage et telefonopkald og afsendelse af en kort meddelelse Service (SMS) meddelelse. Du kan finde flere oplysninger om Twilio og brug af tale og SMS i dine programmer, afsnittet [Næste trin](#NextSteps) .

## <a id="WhatIs"></a>Hvad er Twilio?
Twilio tænd fremtiden for kommunikation, så udviklere integrere stemme, VoIP og messaging til programmer. De Virtualiser alle nødvendige i et skybaseret, global miljø, udsætte via Twilio kommunikation API platform infrastruktur. Programmer er nem at opbygge og SVG. Få glæde af fleksibilitet med løn-som-dig gå priser, og få glæde af skyen pålidelighed.

**Twilio stemme** gør det muligt for dine programmer til at foretage og modtage telefonopkald. **Twilio SMS** gør det muligt for dit program til at sende og modtage sms'er. **Twilio** gør det muligt at foretage VoIP-opkald fra en hvilken som helst telefon, tablet eller en browser, og understøtter WebRTC.

## <a id="Pricing"></a>Twilio priser og særlige tilbud

Azure kunder får [særtilbud] 10 af Twilio kreditkontrol, når du opgraderer kontoen Twilio. Denne Twilio kredit kan anvendes på en hvilken som helst Twilio brugen ($10 kredit svarer til at sende op til 1.000 SMS-meddelelser eller modtage op til 1000 indgående tale-minutter, afhængigt af placeringen af din telefon tal og meddelelse eller -opkald destination). Indløse dette Twilio kredit og Kom i gang med: [ahoy.twilio.com/azure].

Twilio er en overkommelige tjeneste. Der er ikke konfigureres gebyrer, og du kan lukke din konto på en hvilken som helst tidspunkt. Du kan finde flere oplysninger på [Twilio priser] [twilio_pricing].

## <a id="Concepts"></a>Begreber
Twilio API er et RESTful API, hvor du kan tale og SMS funktionalitet programmer. Klientbiblioteker er tilgængelige på flere sprog se en liste, [Twilio API biblioteker] [twilio_libraries].

Vigtige aspekter af Twilio API er Twilio verber og Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio verber
API gør brug af Twilio verber; for eksempel på ** &lt;sig&gt; ** verber får Twilio til at levere tydeligt en meddelelse på et opkald.

Følgende er en liste over Twilio verber. Få mere at vide om de andre verber og funktioner via [Twilio Markup Language dokumentation] [http://www.twilio.com/docs/api/twiml].

* ** &lt;Numeriske&gt;**: opretter forbindelse kalderen til en anden telefon.
* ** &lt;Indsamle&gt;**: indsamler numeriske cifre, der er angivet på telefon-tastatur.
* ** &lt;Læg på&gt;**: slutter et opkald.
* ** &lt;Afspille&gt;**: afspilles en lydfil.
* ** &lt;Pause&gt;**: venter uovervåget på et angivet antal sekunder.
* ** &lt;Post&gt;**: stemmen kalderen-poster og returnerer en URL-adressen for en fil, der indeholder optagelsen.
* ** &lt;Omdirigere&gt;**: overfører kontrollen over et opkald eller en SMS til TwiML på en anden URL-adresse.
* ** &lt;Afvise&gt;**: afviser et indgående opkald til din Twilio tal uden fakturering du
* ** &lt;Sig&gt;**: konverterer tekst til tale, der er foretaget i et opkald.
* ** &lt;Sms&gt;**: sender en SMS-meddelelse.

### <a id="TwiML"></a>TwiML
TwiML er et sæt XML-baserede instruktioner, der er baseret på de Twilio verber, der oplyser Twilio af, hvordan du behandle et opkald eller SMS.

Som et eksempel, vil de følgende TwiML konvertere teksten **Hej verden** til tale.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Når dit program kalder Twilio API, er en af API parametrene URL-adressen, der returnerer TwiML svaret. Til udviklingsformål, kan du bruge udleveret Twilio URL-adresser til at levere TwiML svarene bruges af dine programmer. Du kan også er vært for din egen URL-adresser for at frembringe TwiML svarene, og en anden mulighed er at bruge objektet **TwiMLResponse** .

Du kan finde flere oplysninger om Twilio verber, deres attributter og TwiML, se [TwiML] [twiml]. Yderligere oplysninger om Twilio API, se [Twilio API] [twilio_api].

## <a id="CreateAccount"></a>Oprette en Twilio-konto
Når du er klar til at få en Twilio konto skal du tilmelde dig på [Prøv Twilio] [try_twilio]. Du kan starte med en gratis konto og opgradere din konto senere.

Når du tilmelder dig en Twilio-konto, modtager du en konto-ID og et godkendelse token. Begge skal bruges til at foretage Twilio API opkald. For at forhindre uautoriseret adgang til din konto, skal du holde din godkendelse token sikker. Din konto-ID og godkendelse token er synlig på [Twilio kontoside] [twilio_account]i felterne mærket **konto SID** og **AUTH TOKEN**, henholdsvis.

## <a id="create_app"></a>Oprette et PHP tjenesteprogram
Et PHP-program, der bruger tjenesten Twilio kører i Azure er ikke anderledes end andre PHP programmer, der bruger tjenesten Twilio. Mens Twilio services er RESTEN-baserede og kan kaldes fra PHP på flere måder, i denne artikel fokuserer på hvordan du bruger Twilio services med [Twilio bibliotek til PHP fra GitHub][twilio_php]. Du kan finde flere oplysninger om brug af biblioteket Twilio til PHP, se [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Detaljeret vejledning til at opbygge og implementere et Twilio/PHP program til Azure findes på [hvordan du kan foretage et telefonopkald ved hjælp af Twilio i et PHP til computeren på Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurere dit program kan bruge Twilio biblioteker
Du kan konfigurere dit program tilladelse til at bruge biblioteket Twilio til PHP på to måder:

1. Hente biblioteket Twilio til PHP fra GitHub ([https://github.com/twilio/twilio-php][twilio_php]), og Føj mappen **tjenester** til dit program.

    -ELLER-

2. Installere biblioteket Twilio til PHP som en PÆRETRÆER pakke. Det kan være installeret med følgende kommandoer:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Når du har installeret biblioteket Twilio til PHP, kan du derefter tilføje en **require_once** sætning øverst i dine PHP filer til at referere til biblioteket:

        require_once 'Services/Twilio.php';

Du kan finde flere oplysninger, se [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Sådan: foretage en udgående opkald
Følgende viser, hvordan du foretager en udgående opkald ved hjælp af klassen **Services_Twilio** . Denne kode bruges også et Twilio har udleveret websted til at returnere svaret Twilio Markup Language (TwiML). Erstatte din værdier for telefonnumre **fra** og **til** , og Sørg for, at du kontrollerer telefonnummer **fra** til kontoen Twilio før du kører koden.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Som nævnt, bruger denne kode et Twilio har udleveret websted til at returnere TwiML svaret. Du kan i stedet bruge dit eget websted til at levere TwiML svaret. se, [hvordan du giver TwiML svar fra dit eget websted](#howto_provide_twiml_responses)kan finde flere oplysninger.


- **Bemærk**: for at fejlfinde SSL-certifikat valideringsfejl, skal du se [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Sådan: Send en SMS-meddelelse
Følgende viser, hvordan du kan sende en SMS-meddelelse, ved hjælp af klassen **Services_Twilio** . Tal **fra** leveres af Twilio for prøveversion konti til at sende SMS-beskeder. **Til** antallet skal kontrolleres for kontoen Twilio før du kører koden.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Sådan: angive TwiML svar fra dit eget websted
Når programmet starter et opkald til Twilio API, sender Twilio din anmodning om til en URL-adresse, du forventer at returnere en TwiML svar. Ovenstående eksempel bruger udleveret Twilio URL-adressen [http://twimlets.com/message][twimlet_message_url]. (Når TwiML er beregnet til brug af Twilio, kan du se it i din browser. For eksempel skal du klikke på [http://twimlets.com/message] [ twimlet_message_url] at få vist en tom `<Response>` element; Klik på [http://twimlets.com/message?Message%5B0%5D=Hello%20World] som et andet eksempel[ twimlet_message_url_hello_world] at se en `<Response>` element, der indeholder en `<Say>` element.)

I stedet for den Twilio har udleveret URL-adresse, kan du oprette dit eget websted, der returnerer HTTP-svar. Du kan oprette webstedet på alle sprog, der returnerer XML-svar; Dette emne antages det, du vil bruge PHP til at oprette TwiML.

Siden følgende PHP resulterer i et TwiML svar, der står **Hej verden** på opkald.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Som du kan se fra eksemplet ovenfor, er TwiML svaret blot en XML-dokument. Biblioteket Twilio til PHP indeholder klasser, der genererer TwiML for dig. I eksemplet nedenfor giver tilsvarende svaret, som vist ovenfor, men bruger den **Services\_Twilio\_Twiml** klasse i biblioteket Twilio til PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Du kan finde flere oplysninger om TwiML, se [https://www.twilio.com/docs/api/twiml][twiml_reference].

Når du har din PHP side er konfigureret til at give TwiML svar, bruge URL-adressen på siden PHP, som overføres URL-adressen til den `Services_Twilio->account->calls->create` metode. Eksempelvis hvis du har et webprogram, der hedder **MyTwiML** installeret på en Azure hostet tjeneste, og navnet på siden PHP er **mytwiml.php**, URL-adressen kan overføres til **Services_Twilio -> konto -> opkald -> Opret** som vist i følgende eksempel:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Yderligere oplysninger om brug af Twilio i Azure med PHP, se, [hvordan du foretage et telefonopkald ved hjælp af Twilio i et PHP til computeren på Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Sådan: Brug af flere Twilio Services
Ud over eksempler, der er vist her, indeholder Twilio webbaseret API'er, som du kan bruge til at udnytte flere Twilio funktionalitet fra Azure-program. Få flere detaljer, finder du i [Twilio API dokumentation] [twilio_api_documentation].

## <a id="NextSteps"></a>Næste trin
Nu hvor du har lært det grundlæggende omkring af tjenesten Twilio, skal du følge disse links for at få mere at vide:

* [Retningslinjer for Twilio sikkerhed] [twilio_security_guidelines]
* [Twilio Sådan hjælpelinjer og eksempler på kode] [twilio_howtos]
* [Hurtig start Twilio selvstudier][twilio_quickstarts]
* [Twilio på GitHub] [twilio_on_github]
* [Tale med Twilio Support] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
