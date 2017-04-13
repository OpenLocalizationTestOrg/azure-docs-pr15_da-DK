<properties 
    pageTitle="store-sendgrid-Java-How-to-Send-email-example" 
    description="Hvordan du kan sende mail ved hjælp af SendGrid fra Java i en Azure-installation" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Hvordan du kan sende mail ved hjælp af SendGrid fra Java i en Azure-installation

I følgende eksempel viser, hvordan du kan bruge SendGrid til at sende mails fra en webside, der er hostet i Azure. Programmet resulterende spørger brugeren for e-mail-værdier, som vist på følgende skærmbillede.

![E-mail-formular][emailform]

Den resulterende mail ser nogenlunde sådan følgende skærmbillede.

![E-mail-meddelelse][emailsent]

Du skal gøre følgende for at bruge koden i dette emne:

1. Indhente krukker javax.mail for eksempel fra <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Føje krukker til din Java build sti.
3. Hvis du bruger Eklipse til at oprette Java programmet, kan du medtage SendGrid biblioteker i programmet installationsfilen (KRIG) ved hjælp af Eklipses installation samling funktion. Hvis du ikke bruger Eklipse til at oprette denne Java-program, sikre, at bibliotekerne, der er medtaget i den samme Azure rolle som Java-program og føjet til klasse stien til dit program.


Du skal også have dine egne SendGrid brugernavn og adgangskode, for at kunne sende mailen. For at komme i gang med SendGrid skal se, [hvordan du sender en mail ved hjælp af SendGrid fra Java](store-sendgrid-java-how-to-send-email.md).

Velkendte med oplysninger i [oprette en Hej verden programmet til Azure i Eklipse](http://msdn.microsoft.com/library/windowsazure/hh690944)eller med andre teknikker til at være vært Java-programmer i Azure, hvis du ikke bruger Eklipse, anbefales desuden.

## <a name="create-a-web-form-for-sending-email"></a>Oprette en webformular til at sende mail

Følgende kode viser, hvordan du kan oprette en webformular for at hente brugerdata for at sende mail. Filen JSP hedder **emailform.jsp**henblik på dette indhold.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Oprette koden for at sende mail

Følgende kode, der kaldes, når du udfører på formularen i emailform.jsp, opretter mail og sender den. Filen JSP hedder **sendemail.jsp**henblik på dette indhold.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Ud over at sende e-mailen, giver emailform.jsp et resultat for brugeren et eksempel er følgende skærmbillede:

![Sende mail resultat][emailresult]

## <a name="next-steps"></a>Næste trin

Installere programmet til Beregn emulatoren og køre emailform.jsp i en browser, angive værdier i formularen, skal du klikke på **Send denne e-mail**og derefter se resultaterne i sendemail.jsp.

Denne kode har fået viser dig, hvordan du bruger SendGrid i Java på Azure. Før du anvender til Azure fremstilling, kan du vil tilføje flere fejlhåndtering eller andre funktioner. Eksempel: 

* Du kan bruge Azure lagerplads BLOB eller SQL-Database til at gemme e-mail-adresser og e-mail-meddelelser, i stedet for ved hjælp af en webformular. Oplysninger om brug af Azure lagerplads BLOB i Java, se, [hvordan du bruger tjenesten Blob Storage fra Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Du kan finde oplysninger om brug af SQL-Database i Java [Ved hjælp af SQL-Database i Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Du kan bruge `RoleEnvironment.getConfigurationSettings` til at hente SendGrid brugernavnet og adgangskoden fra din installation konfigurationsindstillinger, i stedet for at bruge webformularen til at hente disse værdier. Oplysninger om den `RoleEnvironment` klasse, skal du se [Brug af biblioteket Azure Service Runtime i JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) og Azure Service Runtime pakke dokumentationen på <http://dl.windowsazure.com/javadoc>.
* Du kan finde flere oplysninger om brug af SendGrid i Java se, [hvordan du sender en mail ved hjælp af SendGrid fra Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
