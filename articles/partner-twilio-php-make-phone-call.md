<properties
    pageTitle="Hvordan du kan foretage et telefonopkald fra Twilio (PHP) | Microsoft Azure"
    description="Lær at foretage et telefonopkald og sende en SMS-meddelelse med tjenesten Twilio API på Azure. Prøver er for PHP program."
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Hvordan du kan foretage et telefonopkald ved hjælp af Twilio i et PHP til computeren på Azure

I følgende eksempel viser, hvordan du kan bruge Twilio til at ringe fra en PHP webside Azure som vært. Programmet resulterende spørger brugeren for telefonopkald værdier, som vist på følgende skærmbillede.

![Azure opkald formular ved hjælp af Twilio og PHP][twilio_php]

Du skal gøre følgende for at bruge koden i dette emne:

1. Få fat på en Twilio konto og godkendelse token. For at komme i gang med Twilio skal evalueres priser på [http://www.twilio.com/pricing][twilio_pricing]. Du kan tilmelde sig en prøveversion konto på [https://www.twilio.com/try-twilio][try_twilio]. Finde oplysninger om API leveres af Twilio, se [http://www.twilio.com/api][twilio_api].
2. Få [Twilio bibliotek til PHP](https://github.com/twilio/twilio-php) eller installere den som en PÆRETRÆER pakke. Se [filen med vigtige](https://github.com/twilio/twilio-php/blob/master/README.md)kan finde flere oplysninger.
3. Installer Azure SDK til PHP. Finde en oversigt over SDK og oplysninger om installation af det, i [Opsætning af Azure SDK til PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Oprette en webformular til at foretage et opkald

Den følgende HTML-kode viser, hvordan du opretter en webside (**callform.html**), der henter brugerdata for opkald:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Oprette koden for at foretage opkaldet
Følgende kode viser, hvordan du opretter en webside (**makecall.php**) der kaldes, når brugeren sender formularen vises som **callform.html**. Den kode, der er vist nedenfor opretter meddelelsen opkald og danner opkaldet. (Brug din Twilio konto og godkendelse token i stedet for de pladsholder værdier, der er tildelt til **$sid** og **$token** i koden nedenfor).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Ud over at foretage opkaldet, viser **makecall.php** nogle opkaldet metadata (eksempel vist i skærmbilledet nedenfor). Du kan finde flere oplysninger om opkaldet metadata, se [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure opkald svar ved hjælp af Twilio og PHP][twilio_php_response]

## <a name="run-the-application"></a>Køre programmet
Næste trin er at installere programmet til Azure websteder. I følgende artikler indeholder oplysninger til at oprette et websted og installation af din kode med ciffer, FTP- eller WebMatrix (men ikke alle oplysningerne i hver artikel er relevant):

* [Oprette et PHP MySQL Azure-websted og installere ved hjælp af ciffer][website-git]
* [Oprette et Azure PHP MySQL-websted og installere ved hjælp af FTP][website-ftp]

## <a name="next-steps"></a>Næste trin
Denne kode henvist til at vise dig grundlæggende funktionalitet ved hjælp af Twilio i PHP på Azure. Før du anvender til Azure fremstilling, kan du vil tilføje flere fejlhåndtering eller andre funktioner. Eksempel:

* I stedet for ved hjælp af en webformular, kan du bruge Azure lagerplads BLOB eller SQL-Database til at gemme telefonnumre og ringe til tekst. Oplysninger om brug af Azure lagerplads BLOB i PHP finder du [Ved hjælp af Azure-lager med PHP programmer][howto_blob_storage_php]. Oplysninger om brug af SQL-Database i PHP finder du [Ved hjælp af SQL-Database med PHP programmer][howto_sql_azure_php].
* **Makecall.php** koden bruger udleveret Twilio URL-adresse ([http://twimlets.com/message][twimlet_message_url]) til at levere svar Twilio Markup Language (TwiML), der oplyser Twilio for at fortsætte med opkaldet. For eksempel TwiML, der returneres kan indeholde et `<Say>` verber, der resulterer i tekst udtalt opkaldsmodtageren. I stedet for ved hjælp af den Twilio har udleveret URL-adresse, kan du oprette din egen tjeneste for at besvare Twilios anmodning; Du kan finde flere oplysninger, se, [hvordan du bruge Twilio til lyd- og SMS-funktioner i PHP][howto_twilio_voice_sms_php]. Du kan finde flere oplysninger om TwiML på [http://www.twilio.com/docs/api/twiml][twiml], og du kan finde flere oplysninger om `<Say>` og andre Twilio verber kan findes på [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Læs retningslinjerne sikkerhed Twilio på [https://www.twilio.com/docs/security][twilio_docs_security].

Du kan finde flere oplysninger om Twilio, se [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Se også
* [Sådan bruges Twilio til tale- og SMS-funktioner i PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
