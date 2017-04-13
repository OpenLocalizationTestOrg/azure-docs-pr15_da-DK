<properties 
    pageTitle="Sådan bruger du SendGrid e-mail-tjenesten (PHP) | Microsoft Azure" 
    description="Få mere at vide hvordan sende mail med SendGrid mail-tjenesten på Azure. Kodeeksempler, der er skrevet i PHP." 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Hvordan du bruger tjenesten SendGrid mail fra PHP

Denne vejledning viser, hvordan til at udføre almindelige programming opgaver med SendGrid mail-tjenesten på Azure. Eksemplerne er skrevet i PHP.
De scenarier, der er omfattet omfatter **bygning af mail**, **afsendelse af mail**og **tilføje vedhæftede filer**. Du kan finde flere oplysninger om SendGrid og sende mails, i afsnittet [Næste trin](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Hvad er SendGrid e-mail-tjenesten?

SendGrid er en [skybaseret mailtjeneste] , som indeholder pålidelig [transaktions maillevering], skalerbarhed og realtid analytics sammen med fleksible API'er, som gør det nemt at brugerdefinerede integration. Almindelige SendGrid brugsscenarier omfatter:

-   Automatisk sende kvitteringer til kunder
-   Administrere fordeling lister til at sende kunder månedlige e-løbesedler og særlige tilbud
-   Indsamling af realtid målepunkter for ting som blokerede e-mail og kunderne
-   Oprettelse af rapporter til at identificere tendenser
-   Viderestilling af kundeforespørgsler
- Besked via mail fra dit program

Du kan finde flere oplysninger [https://sendgrid.com][].

## <a name="create-a-sendgrid-account"></a>Oprette en SendGrid-konto

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Brug af SendGrid fra dit PHP program

Brug af SendGrid i Azure PHP programmet kræver ingen speciel konfiguration eller kodning. Da SendGrid er en tjeneste, kan det åbnes på nøjagtigt samme måde fra et program i skyen, som muligt fra et lokalt program.

## <a name="how-to-send-an-email"></a>Sådan: sende en mail

Du kan sende mail ved hjælp af SMTP- eller Web API leveres af SendGrid.

### <a name="smtp-api"></a>SMTP-API

Brug *Swift mail*, et komponent-baseret bibliotek for at sende mails fra PHP programmer til at sende mail ved hjælp af SendGrid SMTP-API. Du kan hente biblioteket *Swift mail* fra [http://swiftmailer.org/download][] v5.3.0 (Brug [Komponist] at installere Swift mail). At sende mail med biblioteket omfatter oprettelse af forekomster af det <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_mail</span>, og <span class="auto-style2">Swift\_meddelelse</span> klasser, angive de ønskede egenskaber, og kald den <span class="auto-style2">Swift\_Mailer::send</span> metode.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>Web API

Brug PHP'S [curl funktionen][] til at sende mail ved hjælp af SendGrid Web API.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

Sendgrid's Web API ligner en REST-API, selvom den ikke er helt igennem en RESTful API da begge få i de fleste opkald, og Send verber kan bruges i flæng.

## <a name="how-to-add-an-attachment"></a>Sådan: tilføje en vedhæftet fil

### <a name="smtp-api"></a>SMTP-API

Sende en vedhæftet fil ved hjælp af SMTP-API omfatter en yderligere linje af kode til eksempel scriptet til at sende en mail med Swift mail.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

Den ekstra linje af kode er som følger:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Denne linje af kode kalder metoden Vedhæft på den <span class="auto-style2">Swift\_meddelelse</span> objekt og bruger statiske metode <span class="auto-style2">fromPath</span> på den <span class="auto-style2">Swift\_vedhæftet fil</span> klasse at hente og vedhæfte en fil til en meddelelse.

### <a name="web-api"></a>Web API

Sende en vedhæftet fil ved hjælp af Web API minder meget om at sende en mail via Web API. Bemærk, at i eksemplet, der følger efter parameter matrixen skal indeholde dette element:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Eksempel:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Sådan: bruge filtre til at aktivere sidefødder, registrering og analyse

SendGrid indeholder flere e-mail-funktionen ved hjælp af "filtre". Dette er indstillinger, der kan føjes til en e-mail-meddelelse, du vil aktivere bestemte funktioner som aktivering Klik på registrering, Google analytics, abonnement registrering og osv.

Filtre kan anvendes på en meddelelse ved hjælp af egenskaben filtre. Hvert filter er angivet af en hash, der indeholder filter-specifikke indstillinger. I følgende eksempel aktiverer filteret sidefod og angiver en tekstmeddelelse der skal føjes til bunden af mail.
I dette eksempel bruger vi [sendgrid php bibliotek].
Brug [Komponist] til at installere bibliotek:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Eksempel:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af tjenesten SendGrid mail, skal du følge disse links for at få mere at vide.

-   SendGrid dokumentation: <https://sendgrid.com/docs>
-   SendGrid PHP bibliotek: <https://github.com/sendgrid/sendgrid-php>
-   SendGrid særlige tilbud for Azure kunder: <https://sendgrid.com/windowsazure.html>

Du kan finde flere oplysninger, se også [PHP Developer Center](/develop/php/).


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/Download]: http://swiftmailer.org/download
  [Krøllet funktionen]: http://php.net/curl
  [cloud-baseret mailtjeneste]: https://sendgrid.com/email-solutions
  [transaktions maillevering]: https://sendgrid.com/transactional-email
  [sendgrid php bibliotek]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Komponist]: https://getcomposer.org/download/
