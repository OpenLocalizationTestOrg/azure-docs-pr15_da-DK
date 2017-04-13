<properties 
    pageTitle="Sådan bruger du mailtjeneste SendGrid (.NET) | Microsoft Azure" 
    description="Få mere at vide hvordan sende mail med SendGrid mail-tjenesten på Azure. Kode eksempler, der er skrevet i C# og bruger API'EN .NET." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Hvordan du kan sende mail ved hjælp af SendGrid med Azure


## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan til at udføre almindelige programming opgaver med SendGrid mail-tjenesten på Azure. Eksemplerne er skrevet i C\#
og bruge .NET API. De scenarier, der er dækket omfatter **bygning af mail**, **afsendelse af mail**, **tilføje vedhæftede filer**og **ved hjælp af filtre**. Du kan finde flere oplysninger om SendGrid og sende mails, i afsnittet [Næste trin][] .

## <a name="what-is-the-sendgrid-email-service"></a>Hvad er SendGrid e-mail-tjenesten?

SendGrid er en [skybaseret mailtjeneste] , som indeholder pålidelig [transaktions maillevering], skalerbarhed og realtid analytics sammen med fleksible API'er, som gør det nemt at brugerdefinerede integration. Almindelige SendGrid brugsscenarier omfatter:

-   Automatisk sende kvitteringer til kunder.
-   Administrere fordeling lister til at sende kunder månedlige e-løbesedler og særlige tilbud.
-   Indsamling af realtid målepunkter for ting som blokerede mail og kunderne.
-   Oprettelse af rapporter til at identificere tendenser.
-   Viderestilling af kundeforespørgsler.
-   Behandling af indgående mails.

Få mere at vide under [https://sendgrid.com](https://sendgrid.com) eller vores [C# bibliotek][sendgrid csharp]

## <a name="create-a-sendgrid-account"></a>Oprette en SendGrid-konto

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Reference klassebibliotek SendGrid .NET

[SendGrid NuGet pakke](https://www.nuget.org/packages/Sendgrid) er den nemmeste måde at få SendGrid API og for at konfigurere dit program med alle afhængigheder. NuGet er en Visual Studio-typenavn inkluderet med Microsoft Visual Studio 2015, der gør det nemt at installere og opdatere biblioteker og værktøjer. 

> [AZURE.NOTE] Besøg [http://www.nuget.org](http://www.nuget.org)for at installere NuGet, hvis du kører en version af Visual Studio, der er tidligere end Visual Studio 2015 skal, og klik på knappen **Installer NuGet** .

For at installere pakken SendGrid NuGet i dit program skal du gøre følgende:

1.  Oprettet et nyt projekt.

    ![Oprette et nyt projekt][create-new-project]

2.  Vælg en skabelon.

    ![Vælg en skabelon][select-a-template]

3.  I **Solution Explorer**, skal du højreklikke på **referencer**, og klik derefter på **Administrer NuGet pakker**.

4.  Søg efter **SendGrid** , og vælg **SendGrid** elementet på resultatlisten.

    ![SendGrid NuGet pakke][SendGrid-NuGet-package]

5.  Klik på **Installer** for at fuldføre installationen, og luk derefter dialogboksen.

Sendgrid's .NET klassebibliotek kaldes **SendGridMail**. Den indeholder følgende navneområder:

-   **SendGridMail** til oprettelse og arbejde med mail elementer.
-   **SendGridMail.Transport** til at sende mail ved hjælp af **SMTP-** protokollen eller HTTP 1.1-protokollen med **Web/RESTEN**.

Tilføj følgende kode navneområdeerklæringer til toppen af enhver C\# fil, hvor du vil automatisk få adgang til tjenesten SendGrid mail.
**System.Net** og **System.Net.Mail** er .NET Framework navneområder, der er inkluderet, fordi de indeholder typer, du bruger ofte med SendGrid APIs.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Sådan: oprette en mail

Bruge objektet **SendGridMessage** til at oprette en mail. Når meddelelsesobjektet er oprettet, kan du angive egenskaber og metoder, herunder mail afsenderen, modtageren mail og emne og brødteksten i mailen.

Følgende eksempel viser, hvordan du opretter et fuldt ud på forhånd mail objekt:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Se [sendgrid csharp][] på GitHub kan finde flere oplysninger om alle egenskaber og metoder, der understøttes af typen **SendGrid** .

## <a name="how-to-send-an-email"></a>Sådan: sende en mail

Når du har oprettet en e-mail-meddelelse, kan du sende det ved hjælp af Web API leveres af SendGrid. Du kan også [Brug. Nettooversigts indbygget i biblioteket](https://sendgrid.com/docs/Code_Examples/csharp.html).

Afsendelse af mail kræver, at du angiver din SendGrid kontolegitimationsoplysninger (brugernavn og adgangskode) eller SendGrid API-nøgle. API-nøgle er den foretrukne metode. Hvis du har brug for oplysninger om, hvordan du konfigurerer API taster, skal du gå til vores [dokumentation](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Du kan gemme disse legitimationsoplysninger via Azure-portalen ved at klikke på KONFIGURER og tilføje nøgle/værdi-par under "app indstillinger".

 ![Azure-app-indstillinger][azure_app_settings]

 Derefter kan du åbne dem på følgende måde: 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Ved hjælp af legitimationsoplysninger:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Ved hjælp af API-nøgle:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Følgende eksempler viser, hvordan du kan sende en meddelelse ved hjælp af Web API.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Sådan: tilføje en vedhæftet fil

Vedhæftede filer kan føjes til en meddelelse ved at kalde metoden **AddAttachment** og angiver navnet og stien til den fil, du vil vedhæfte.
Du kan medtage flere vedhæftede filer ved at ringe denne metode, når for hver fil, du vil vedhæfte. I følgende eksempel viser, at tilføje en vedhæftet fil til en meddelelse:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Du kan også tilføje vedhæftede filer fra de data, **Stream**. Det kan gøres ved at ringe på samme måde som ovenfor, **AddAttachment**, men ved overførsel af strømmen af dataene, og filnavnet du vil have det til at vise som i meddelelsen. I dette tilfælde skal du tilføje biblioteket System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Sådan: bruge apps til at aktivere sidefødder, registrering og analyse

SendGrid indeholder flere e-mail-funktionen ved hjælp af apps. Dette er indstillinger, der kan føjes til en e-mail-meddelelse, du vil aktivere bestemte funktioner såsom Klik på registrering, Google analytics, abonnement sporing, og så videre. En komplet liste over apps, skal du se [App-indstillinger][].

Apps kan anvendes til **SendGrid** mails ved hjælp af metoder, der er implementeret som en del af klassen **SendGrid** .

I følgende eksempler viser sidefoden og klikke på opfølgning filtre:

### <a name="footer"></a>Sidefod

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Klik på registrering

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Sådan: Brug af flere SendGrid services

SendGrid tilbyder webbaseret API'er og webhooks, som du kan bruge til at udnytte flere SendGrid funktionalitet fra Azure-program. Du kan finde detaljerede oplysninger i [dokumentationen til SendGrid API][].

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af tjenesten SendGrid mail, skal du følge disse links for at få mere at vide.

*   SendGrid C\# bibliotek repo: [sendgrid csharp][]
*   SendGrid API dokumentation: <https://sendgrid.com/docs>
*   SendGrid særlige tilbud for Azure kunder: [https://sendgrid.com](https://sendgrid.com)

  [Næste trin]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [App-indstillinger]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API dokumentation]: https://sendgrid.com/docs
  
  [cloud-baseret mailtjeneste]: https://sendgrid.com/email-solutions
  [transaktions maillevering]: https://sendgrid.com/transactional-email
 
