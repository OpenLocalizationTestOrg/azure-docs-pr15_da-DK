<properties 
    pageTitle="Sådan bruges Twilio til tale- og SMS (fonetisk) | Microsoft Azure" 
    description="Lær at foretage et telefonopkald og sende en SMS-meddelelse med tjenesten Twilio API på Azure. Kodeeksempler, der er skrevet i fonetisk." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Sådan bruges Twilio til tale- og SMS-funktioner i fonetisk
Denne vejledning viser, hvordan til at udføre almindelige programming opgaver med Twilio API-tjenesten på Azure. De scenarier, der er omfattet omfatter foretage et telefonopkald og afsendelse af en kort meddelelse Service (SMS) meddelelse. Du kan finde flere oplysninger om Twilio og brug af tale og SMS i dine programmer, afsnittet [Næste trin](#NextSteps) .

## <a id="WhatIs"></a>Hvad er Twilio?
Twilio er et telefoni webtjeneste API, hvor du kan bruge din eksisterende web sprog og færdigheder til at oprette tale og SMS-programmer. Twilio er en tredjeparts-tjeneste (ikke en funktion til Azure og ikke en Microsoft-produkt).

**Twilio stemme** gør det muligt for dine programmer til at foretage og modtage telefonopkald. **Twilio SMS** gør det muligt for dine programmer til at foretage og modtage SMS-meddelelser. **Twilio klient** giver mulighed for dine programmer til at aktivere stemmekommunikation ved hjælp af eksisterende Internetforbindelser, herunder mobile forbindelser.

## <a id="Pricing"></a>Twilio priser og særlige tilbud
Oplysninger om Twilio priser er tilgængelige på [Twilio priser] [twilio_pricing]. Azure kunder får et [særtilbud][special_offer]: en gratis kredit på 1000 tekster eller 1000 indgående minutter. For at tilmelde dig dette tilbud, eller få mere at vide, skal du gå til [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Begreber
Twilio API er et RESTful API, hvor du kan tale og SMS funktionalitet programmer. Klientbiblioteker er tilgængelige på flere sprog se en liste, [Twilio API biblioteker] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML er et sæt XML-baserede instruktioner, der oplyser Twilio af, hvordan du behandle et opkald eller SMS.

Som et eksempel, vil de følgende TwiML konvertere teksten **Hej verden** til tale.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Alle TwiML dokumenter har `<Response>` som deres rodelementet. Derfra kan bruge du Twilio verber til at definere funktionsmåden for dit program.

### <a id="Verbs"></a>TwiML verber
Twilio verber er XML-koder, der fortæller Twilio hvad til at **gøre**. For eksempel på ** &lt;sig&gt; ** verber får Twilio til at levere tydeligt en meddelelse på et opkald. 

Følgende er en liste over Twilio verber.

* ** &lt;Numeriske&gt;**: opretter forbindelse kalderen til en anden telefon.
* ** &lt;Indsamle&gt;**: indsamler numeriske cifre, der er angivet på telefon-tastatur.
* ** &lt;Læg på&gt;**: slutter et opkald.
* ** &lt;Afspille&gt;**: afspilles en lydfil.
* ** &lt;Pause&gt;**: venter uovervåget på et angivet antal sekunder.
* ** &lt;Post&gt;**: hvem der har tale-poster og returnerer en URL-adressen for en fil, der indeholder optagelsen.
* ** &lt;Omdirigere&gt;**: overfører kontrollen over et opkald eller en SMS til TwiML på en anden URL-adresse.
* ** &lt;Afvise&gt;**: afviser et indgående opkald til din Twilio tal uden fakturering du
* ** &lt;Sig&gt;**: konverterer tekst til tale, der er foretaget i et opkald.
* ** &lt;Sms&gt;**: sender en SMS-meddelelse.

Du kan finde flere oplysninger om Twilio verber, deres attributter og TwiML, se [TwiML] [twiml]. Yderligere oplysninger om Twilio API, se [Twilio API] [twilio_api].

## <a id="CreateAccount"></a>Oprette en Twilio-konto
Når du er klar til at få en Twilio-konto, skal du tilmelde dig på [Prøv Twilio] [try_twilio]. Du kan starte med en gratis konto og opgradere din konto senere.

Når du tilmelder dig en Twilio-konto, får du et gratis telefonnummer for dit program. Du modtager også en konto SID og et auth token. Begge skal bruges til at foretage Twilio API opkald. For at forhindre uautoriseret adgang til din konto, skal du holde din godkendelse token sikker. Din konto SID og auth token er synlig på [Twilio kontoside][twilio_account]i felterne mærket **konto SID** og **AUTH TOKEN**, henholdsvis.

### <a id="VerifyPhoneNumbers"></a>Bekræfte telefonnumre
Ud over antallet får du ved at Twilio, du kan også kontrollere tallene du kontrolelementet (det vil sige din mobiltelefon eller home telefonnummer) til brug i dine programmer. 

Oplysninger om, hvordan du kan kontrollere et telefonnummer, kan du se [Administrere numre] [verify_phone].

## <a id="create_app"></a>Oprette et fonetisk tjenesteprogram
Et fonetisk program, der bruger tjenesten Twilio kører i Azure er ikke anderledes end andre fonetisk programmer, der bruger tjenesten Twilio. Mens Twilio services er RESTful og kan kaldes fra fonetisk på flere måder, i denne artikel fokuserer på hvordan du bruger Twilio services med [Twilio hjælper bibliotek til fonetisk][twilio_ruby].

Først skal [konfigureres en ny Azure Linux VM] [ azure_vm_setup] skal fungere som vært for dit nye fonetisk webprogram. Ignorer de trin, der involverer oprettelse af en gør app, lige er defineret VM. Kontrollér, at du opretter et slutpunkt med en ekstern port 80 og en intern port 5000.

I eksemplerne nedenfor vil vi bruge [Sinatra][sinatra], en meget simpel web ramme for fonetisk. Men du kan selvfølgelig bruge biblioteket Twilio hjælpeprogram til fonetisk med en hvilken som helst anden web framework, herunder fonetisk på gør.

SSH til din nye VM og oprette en mappe til din nye app. Oprette en fil kaldet Gemfile i mappen, og kopier den følgende kode i den:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

På kommandolinjen køre `bundle install`. Dette vil installere de ovenstående afhængigheder. Derefter oprette en fil kaldet `web.rb`. Dette er hvor koden for din online findes. Indsæt følgende kode i den:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

På dette tidspunkt du bør kunne køre kommandoen `ruby web.rb -p 5000`. Dette vil skalaknap af en lille webserver på port 5000. Du skulle kunne gå til denne app i browseren ved at besøge URL-adressen du konfigureres til din Azure VM. Når du kan få fat i din online i browseren, er du klar til at begynde at opbygge en Twilio app.

## <a id="configure_app"></a>Konfigurere dit program du bruger Twilio
Du kan konfigurere din online for at bruge biblioteket Twilio ved at opdatere din `Gemfile` skal indeholde denne linje:

    gem 'twilio-ruby'

På kommandolinjen skal køre `bundle install`. Nu åbner `web.rb` og denne linje øverst, herunder:

    require 'twilio-ruby'

Du er nu alle angive bruge biblioteket Twilio hjælpeprogram til fonetisk i WebApp.

## <a id="howto_make_call"></a>Sådan: foretage en udgående opkald
Følgende viser, hvordan du foretager en udgående opkald. Vigtige begreber omfatter ved hjælp af biblioteket Twilio hjælpeprogram til fonetisk til at foretage opkald REST-API og gengivelse af TwiML. Erstatte din værdier for telefonnumre **fra** og **til** , og Sørg for, at du kontrollerer telefonnummer **fra** til kontoen Twilio før du kører koden.

Tilføje denne funktion til `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Hvis du Åbn af `http://yourdomain.cloudapp.net/make_call` i en browser, der skal udløse opkaldet til Twilio API til foretager opkaldet. De første to parametre i `client.account.calls.create` ret forklarer sig selv: det nummer opkaldet er `from` og det nummer opkaldet er `to`. 

Den tredje parameter (`url`) er den URL-adresse, der Twilio anmoder om at få vejledning på hvad du skal gøre, når opkaldet er oprettet. I dette tilfælde vi konfigureres en URL-adresse (`http://yourdomain.cloudapp.net`), der returnerer en enkelt TwiML dokument og bruger den `<Say>` verber kan du gøre nogle tekst til tale og sig "Hej Abe" til den person, der modtager opkaldet.

## <a id="howto_recieve_sms"></a>Sådan: Modtag en SMS
I det forrige eksempel startet vi en **udgående** telefonopkald. Denne gang, vi bruge det telefonnummer, Twilio opgivet under tilmeldingen til at behandle en **indgående** SMS-meddelelse.

Første, log på dit [Twilio dashboard][twilio_account]. Klik på "Tal" i den øverste navigationslinje, og klik derefter på det Twilio tal, du blev angivet. Du får vist to URL-adresser, som du kan konfigurere. En URL-adresse til tale-anmodningen og en SMS anmode om URL-adresse. Dette er de URL-adresser, der kalder Twilio, hver gang der foretages et telefonopkald eller en SMS-besked sendes til dit nummer. URL-adresserne er også kendt som "web kroge".

Vi vil gerne behandle indgående SMS-beskeder, så lad os Opdater URL-adressen til `http://yourdomain.cloudapp.net/sms_url`. Fortsæt, og klik på Gem ændringer i bunden af siden. Nu tilbage i `web.rb` Lad os program vores program tilladelse til at håndtere dette:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Når ændringen, Sørg for at starte din online igen. Nu få ud af din telefon, og send en SMS til dit Twilio nummer. Du skal straks have en SMS-server, hvor der står "Hey, Tak for ping! Twilio og Azure japanske! ".

## <a id="additional_services"></a>Sådan: Brug af flere Twilio Services
Ud over eksempler, der er vist her, indeholder Twilio webbaseret API'er, som du kan bruge til at udnytte flere Twilio funktionalitet fra Azure-program. Få flere detaljer, finder du i [Twilio API dokumentation] [twilio_api_documentation].

### <a id="NextSteps"></a>Næste trin
Nu hvor du har lært det grundlæggende omkring af tjenesten Twilio, skal du følge disse links for at få mere at vide:

* [Retningslinjer for Twilio sikkerhed] [twilio_security_guidelines]
* [Twilio HowTos og eksempler på kode] [twilio_howtos]
* [Hurtig start Twilio selvstudier][twilio_quickstarts] 
* [Twilio på GitHub] [twilio_on_github]
* [Tale med Twilio Support] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
