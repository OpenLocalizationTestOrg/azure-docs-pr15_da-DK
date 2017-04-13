<properties 
    pageTitle="Hvordan du kan foretage et telefonopkald fra Twilio (Java) | Microsoft Azure" 
    description="Lær at foretage et telefonopkald fra en webside ved hjælp af Twilio i et Java-program på Azure." 
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

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Hvordan du kan foretage et telefonopkald ved hjælp af Twilio i et Java-program på Azure 

I følgende eksempel viser, hvordan du kan bruge Twilio til at ringe fra en webside, der er hostet i Azure. Programmet resulterende spørger brugeren for telefonopkald værdier, som vist på følgende skærmbillede.

![Azure opkald formular ved hjælp af Twilio og Java][twilio_java]

Du skal gøre følgende for at bruge koden i dette emne:

1. Få fat på en Twilio konto og godkendelse token. For at komme i gang med Twilio skal evalueres priser på [http://www.twilio.com/pricing][twilio_pricing]. Du kan logge på [https://www.twilio.com/try-twilio][try_twilio]. Finde oplysninger om API leveres af Twilio, se [http://www.twilio.com/api][twilio_api].
2. Få Twilio glas. På [https://github.com/twilio/twilio-java][twilio_java_github], du kan hente GitHub kilderne og oprette dine egne glas eller hente en færdigbyggede glas (med eller uden afhængigheder).
Koden i dette emne blev skrevet ved hjælp af færdigbyggede TwilioJava 3.3.8 med afhængigheder KRUKKEN.
3. Føje KRUKKEN til din Java build sti.
4. Hvis du bruger Eklipse til at oprette denne Java-program skal du medtage Twilio JAR i programmet installationsfilen (KRIG) ved hjælp af Eklipses installation samling funktion. Hvis du ikke bruger Eklipse til at oprette Java programmet, kan du sikre dig Twilio JAR er inkluderet i den samme Azure rolle som Java-program, og føjes til den klasse sti til dit program.
5. Sikre, at din cacerts keystore indeholder Equifax Secure nøglecenter certifikatet med MD5 fingeraftryk 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienummeret 35:DE:F4:CF og SHA1 fingeraftryk er D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Dette er certifikat nøglecenter (CA) certifikat til [https://api.twilio.com] [ twilio_api_service] -tjenesten, som kaldes, når du bruger Twilio APIs. Du kan finde oplysninger om tilføjelse af dette CA-certifikat til din JDK cacert store, under [tilføje et certifikat til Java CA certifikatlager][add_ca_cert].

Desuden kendskab til oplysninger i [oprette en Hej verden program ved hjælp af Azure-værktøjskassen til Eklipse][azure_java_eclipse_hello_world], eller med andre teknikker til at være vært Java-programmer i Azure, hvis du ikke bruger Eklipse anbefales.

## <a name="create-a-web-form-for-making-a-call"></a>Oprette en webformular til at foretage et opkald

Følgende kode viser, hvordan du kan oprette en webformular for at hente brugerdata til at foretage et opkald. Et nyt webprojekt i dynamisk, med navnet **TwilioCloud**, blev oprettet forbindelse med dette eksempel er, og **callform.jsp** blev tilføjet som en JSP-fil.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Oprette koden for at foretage opkaldet
Følgende kode, der kaldes, når brugeren er afsluttet formularen vises som callform.jsp, opretter meddelelsen opkald og danner opkaldet. Filen JSP hedder **makecall.jsp** og blev føjet til projektet **TwilioCloud** henblik på dette eksempel. (Brug din Twilio konto og godkendelse token i stedet for de pladsholder værdier, der er tildelt til **accountSID** og **authToken** i koden nedenfor).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Ud over at foretage opkaldet, viser makecall.jsp Twilio slutpunktet, API-version og opkaldsstatus. Et eksempel er følgende skærmbillede:

![Azure opkald svar ved hjælp af Twilio og Java][twilio_java_response]

## <a name="run-the-application"></a>Køre programmet
Følgende er de overordnede trin til at køre dit program detaljer for disse trin kan findes på [oprettelse af et Hej verden program med Azure-værktøjskassen til Eklipse][azure_java_eclipse_hello_world].

1. Eksportere dine TwilioCloud KRIG til mappen Azure **approot** . 
2. Ændre **startup.cmd** for at udpakke din TwilioCloud KRIG.
3. Samle dit program for Beregn emulatoren.
4. Start installationen i Beregn emulatoren.
5. Åbn en browser, og kør **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Angive værdier i formularen, skal du klikke på **foretage opkaldet**og derefter se resultaterne i makecall.jsp.

Når du er klar til at installere på Azure, genkompiler til installation i skyen, installeres til Azure, og kør http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp i browseren (erstatte din værdi for *your_hosted_name*).

## <a name="next-steps"></a>Næste trin
Denne kode er angivet til at vise dig grundlæggende funktionalitet ved hjælp af Twilio i Java på Azure. Før du anvender til Azure fremstilling, kan du vil tilføje flere fejlhåndtering eller andre funktioner. Eksempel:

* I stedet for ved hjælp af en webformular, kan du bruge Azure lagerplads BLOB eller SQL-Database til at gemme telefonnumre og ringe til tekst. Oplysninger om brug af Azure lagerplads BLOB i Java, se, [hvordan du bruger tjenesten Blob Storage fra Java][howto_blob_storage_java]. Se oplysninger om brug af SQL-Database i Java, [Ved hjælp af SQL-Database i Java][howto_sql_azure_java].
* Du kan bruge **RoleEnvironment.getConfigurationSettings** til at hente Twilio konto-ID og godkendelse token fra din installation konfigurationsindstillinger, i stedet for hårde-kodning værdierne i makecall.jsp. Finde oplysninger om klassen **RoleEnvironment** , [ved hjælp af biblioteket Azure Service Runtime i JSP] [ azure_runtime_jsp] og Azure Service Runtime pakke dokumentation på [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Koden makecall.jsp tildeles en Twilio har udleveret URL-adresse, [http://twimlets.com/message][twimlet_message_url], til variablen **URL-adresse** . Denne URL-adresse indeholder svar Twilio Markup Language (TwiML), der oplyser Twilio for at fortsætte med opkaldet. For eksempel TwiML, der returneres kan indeholde et ** &lt;sig&gt; ** verber, der resulterer i tekst udtalt opkaldsmodtageren. I stedet for ved hjælp af den Twilio har udleveret URL-adresse, kan du oprette din egen tjeneste for at besvare Twilios anmodning; Du kan finde flere oplysninger, se, [hvordan du bruge Twilio til lyd- og SMS-funktioner i Java][howto_twilio_voice_sms_java]. Du kan finde flere oplysninger om TwiML på [http://www.twilio.com/docs/api/twiml][twiml], og du kan finde flere oplysninger om ** &lt;sig&gt; ** og andre Twilio verber kan findes på [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Læs retningslinjerne sikkerhed Twilio på [https://www.twilio.com/docs/security][twilio_docs_security].

Du kan finde flere oplysninger om Twilio, se [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Se også
* [Sådan bruges Twilio til tale- og SMS-funktioner i Java][howto_twilio_voice_sms_java]
* [Føje et certifikat til Java CA Certificate Store][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
