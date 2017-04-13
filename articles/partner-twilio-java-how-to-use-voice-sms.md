<properties 
    pageTitle="Sådan bruges Twilio til tale- og SMS (Java) | Microsoft Azure" 
    description="Lær at foretage et telefonopkald og sende en SMS-meddelelse med tjenesten Twilio API på Azure. Kodeeksempler, der er skrevet i Java." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Sådan bruges Twilio til tale- og SMS-funktioner i Java

Denne vejledning viser, hvordan til at udføre almindelige programming opgaver med Twilio API-tjenesten på Azure. De scenarier, der er omfattet omfatter foretage et telefonopkald og afsendelse af en kort meddelelse Service (SMS) meddelelse. Du kan finde flere oplysninger om Twilio og brug af tale og SMS i dine programmer, afsnittet [Næste trin](#NextSteps) .

## <a id="WhatIs"></a>Hvad er Twilio?
Twilio er et telefoni webtjeneste API, hvor du kan bruge din eksisterende web sprog og færdigheder til at oprette tale og SMS-programmer. Twilio er en tredjeparts-tjeneste (ikke en funktion til Azure og ikke en Microsoft-produkt).

**Twilio stemme** gør det muligt for dine programmer til at foretage og modtage telefonopkald. **Twilio SMS** gør det muligt for dine programmer til at foretage og modtage SMS-meddelelser. **Twilio klient** giver mulighed for dine programmer til at aktivere stemmekommunikation ved hjælp af eksisterende Internetforbindelser, herunder mobile forbindelser.

## <a id="Pricing"></a>Twilio priser og særlige tilbud
Oplysninger om Twilio priser er tilgængelige på [Twilio priser] [twilio_pricing]. Azure kunder får et [særtilbud][special_offer]: en gratis kredit på 1000 tekster eller 1000 indgående minutter. For at tilmelde dig dette tilbud, eller få mere at vide, skal du gå til [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Begreber
Twilio API er et RESTful API, hvor du kan tale og SMS funktionalitet programmer. Klientbiblioteker er tilgængelige på flere sprog se en liste, [Twilio API biblioteker] [twilio_libraries].

Vigtige aspekter af Twilio API er Twilio verber og Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio verber
API gør brug af Twilio verber; for eksempel på ** &lt;sig&gt; ** verber får Twilio til at levere tydeligt en meddelelse på et opkald. 

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
Når du er klar til at få en Twilio-konto, skal du tilmelde dig på [Prøv Twilio] [try_twilio]. Du kan starte med en gratis konto og opgradere din konto senere.

Når du tilmelder dig en Twilio-konto, modtager du en konto-ID og et godkendelse token. Begge skal bruges til at foretage Twilio API opkald. For at forhindre uautoriseret adgang til din konto, skal du holde din godkendelse token sikker. Din konto-ID og godkendelse token er synlig på [Twilio kontoside] [twilio_account]i felterne mærket **konto SID** og **AUTH TOKEN**, henholdsvis.

## <a id="create_app"></a>Oprette en Java-program
1. Få Twilio JAR og tildele den til din Java build sti og din KRIG installation samling. På [https://github.com/twilio/twilio-java][twilio_java], du kan hente GitHub kilderne og oprette dine egne glas eller hente en færdigbyggede glas (med eller uden afhængigheder).
2. Sikre, at din JDK **cacerts** keystore indeholder Equifax Secure nøglecenter certifikatet med MD5 fingeraftryk 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienummeret 35:DE:F4:CF og SHA1 fingeraftryk er D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Dette er certifikat nøglecenter (CA) certifikat til [https://api.twilio.com] [ twilio_api_service] -tjenesten, som kaldes, når du bruger Twilio APIs. Oplysninger om at sikre, at din JDK **cacerts** keystore indeholder korrekte CA-certifikatet, skal du se [tilføje et certifikat til Java CA certifikat Store][add_ca_cert].

Detaljeret vejledning til brug af biblioteket Twilio klient til Java findes på [hvordan du kan foretage et telefonopkald ved hjælp af Twilio i et Java-program på Azure][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurere dit program kan bruge Twilio biblioteker
I din kode, kan du tilføje **importere** sætninger øverst på din kildefiler for Twilio pakker eller klasser, du vil bruge i dit program. 

Om Java-kildefiler:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Til Java Server Page (JSP) kildefiler:

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
**Importere** -sætninger kan være forskellige afhængigt af hvilken Twilio pakker eller kategorier, du vil bruge.

## <a id="howto_make_call"></a>Sådan: foretage en udgående opkald
Følgende viser, hvordan du foretager en udgående opkald ved hjælp af klassen **CallFactory** . Denne kode bruges også et Twilio har udleveret websted til at returnere svaret Twilio Markup Language (TwiML). Erstatte din værdier for telefonnumre **fra** og **til** , og Sørg for, at du kontrollerer telefonnummer **fra** til kontoen Twilio før du kører koden.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Du kan finde flere oplysninger om de parametre, der blev sendt til metoden **CallFactory.create** , se [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Som nævnt, bruger denne kode et Twilio har udleveret websted til at returnere TwiML svaret. Du kan i stedet bruge dit eget websted til at levere TwiML svaret. se, [hvordan du giver TwiML svar i en Java-programmet på Azure](#howto_provide_twiml_responses)kan finde flere oplysninger.

## <a id="howto_send_sms"></a>Sådan: Send en SMS-meddelelse
Følgende viser, hvordan du kan sende en SMS-meddelelse, ved hjælp af klassen **SmsFactory** . **Fra** tal, **4155992671**, der leveres af Twilio for prøveversion konti til at sende SMS-beskeder. **Til** antallet skal kontrolleres for kontoen Twilio før du kører koden.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Du kan finde flere oplysninger om de parametre, der blev sendt til metoden **SmsFactory.create** , se [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Sådan: angive TwiML svar fra dit eget websted
Når programmet starter et opkald til Twilio API, sender for eksempel via metoden **CallFactory.create** Twilio din anmodning om til en URL-adresse, du forventer at returnere en TwiML svar. Ovenstående eksempel bruger udleveret Twilio URL-adressen [http://twimlets.com/message][twimlet_message_url]. (Når TwiML er beregnet til brug af webtjenester, kan du se TwiML i din browser. For eksempel skal du klikke på [http://twimlets.com/message] [ twimlet_message_url] at få vist en tom ** &lt;svar&gt; ** element; Klik på [http://twimlets.com/message?Message%5B0%5D=Hello%20World] som et andet eksempel[ twimlet_message_url_hello_world] at se en ** &lt;svar&gt; ** element, der indeholder en ** &lt;sig&gt; ** element.)

I stedet for den Twilio har udleveret URL-adresse, kan du oprette dit eget websted i URL-adresse, der returnerer HTTP-svar. Du kan oprette webstedet på alle sprog, der returnerer HTTP-svar Dette emne antages det, du skal vært for URL-adresse i en JSP side.

Siden følgende JSP resulterer i et TwiML svar, der står **Hej verden** på opkald.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

Siden følgende JSP resulterer i et TwiML svar, der står noget tekst, har flere pauser og siger, at du kan finde oplysninger om Twilio API-version og Azure rollenavn.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Parameteren **ApiVersion** findes i anmodninger om Twilio voice (ikke SMS anmodninger). For at se tilgængelige anmodning parametrene for Twilio tale og SMS anmodninger skal du se <https://www.twilio.com/docs/api/twiml/twilio_request> og <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, henholdsvis. Miljøvariablen **RoleName** fås som en del af en Azure-installation. (Hvis du vil tilføje brugerdefinerede miljøvariabler, så de kan hentes fra **System.getenv**, skal du se afsnittet miljø variabler i [Diverse rolle konfigurationsindstillinger][misc_role_config_settings].)

Når du har din JSP side er konfigureret til at give TwiML svar, skal du bruge URL-adressen på siden JSP som URL-adressen givet til **CallFactory.create** metode. Eksempelvis hvis du har et webprogram, der hedder MyTwiML, der er installeret på en Azure hostet tjeneste, og navnet på siden JSP er mytwiml.jsp, URL-adressen kan overføres til **CallFactory.create** som vist i følgende:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

En anden mulighed for at svare med TwiML er via klassen **TwiMLResponse** , der findes i pakken **com.twilio.sdk.verbs** .

Yderligere oplysninger om brug af Twilio i Azure med Java, se, [hvordan du foretage et telefonopkald ved hjælp af Twilio i et Java-program på Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Sådan: Brug af flere Twilio Services
Ud over eksempler, der er vist her, indeholder Twilio webbaseret API'er, som du kan bruge til at udnytte flere Twilio funktionalitet fra Azure-program. Få flere detaljer, finder du i [Twilio API dokumentation] [twilio_api_documentation].

## <a id="NextSteps"></a>Næste trin
Nu hvor du har lært det grundlæggende omkring af tjenesten Twilio, skal du følge disse links for at få mere at vide:

* [Retningslinjer for Twilio sikkerhed] [twilio_security_guidelines]
* [Twilio Sådan og eksempler på kode] [twilio_howtos]
* [Hurtig start Twilio selvstudier][twilio_quickstarts] 
* [Twilio på GitHub] [twilio_on_github]
* [Tale med Twilio Support] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
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
