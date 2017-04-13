<properties 
    pageTitle="Tilføje et certifikat til Java CA store | Microsoft Azure" 
    description="Få mere at vide, hvordan du tilføjer et certifikat nøglecenter (CA) certifikat til Java CA certifikat (cacerts) store for Twilio tjeneste eller Azure Service Bus." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Tilføje et certifikat til Java CA certifikater Store
Følgende trin viser, hvordan du tilføje et certifikat nøglecenter (CA) certifikat til Java CA certifikat (cacerts) store. Eksemplet bruges er for CA-certifikatet kræves af tjenesten Twilio. Oplysningerne senere i emnet beskriver, hvordan du installere CA-certifikatet til Azure Service Bus. 

Du kan bruge keytool til at tilføje CA-certifikatet før zippe din JDK og tilføje den til projektet Azure **approot** mappe, eller du kan køre en Azure opstart opgave, der bruges keytool til at tilføje certifikatet. I dette eksempel antages det, du vil tilføje et certifikat før JDK der zip. Desuden et bestemt CA-certifikat, der skal bruges i eksemplet, men trinnene, hvordan du får et andet CA-certifikat og importere dem i cacerts store ville være tilsvarende.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Tilføje et certifikat til cacerts store

1. Kør følgende for at se, hvilke certifikater er installeret på en kommandoprompt, der er angivet til din JDK **jdk\jre\lib\security** mappe:

    `keytool -list -keystore cacerts`

    Du bliver bedt om store adgangskoden. Standardadgangskoden, er **changeit**. (Hvis du vil ændre adgangskoden, skal du se i dokumentationen til keytool på <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) I dette eksempel antages det, at certifikatet, der MD5 fingeraftryk 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 ikke står på listen, og som du vil importere den (dette bestemt certifikat bruges af tjenesten Twilio API).
2. Få certifikatet fra listen over certifikater, der er angivet på [GeoTrust rodcertifikater](http://www.geotrust.com/resources/root-certificates/). Højreklik på linket for certifikatet, der serienummeret 35:DE:F4:CF og gemme den til mappen **jdk\jre\lib\security** . Til anvendelse af dette eksempel, det blev gemt i en fil med navnet **Equifax\_Secure\_certifikat\_Authority.cer**.
3. Importere certifikatet via følgende kommando:

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Når du bliver bedt om at have tillid til dette certifikat, hvis certifikatet, der har MD5 fingeraftryk 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, svar ved at skrive **y**.
4. Kør følgende kommando for at sikre, at CA-certifikatet er blevet importeret:

    `keytool -list -keystore cacerts`

5. ZIP-JDK og tildele den til projektet Azure **approot** mappe.

Du kan finde oplysninger om keytool <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Azure rodcertifikater

Dine programmer, der bruger Azure tjenester (såsom Azure Service Bus) skal have tillid til certifikatet Baltimore CyberTrust Root. (Starten 15 April 2013 Azure startede overførsel fra GTE CyberTrust Global Root til Baltimore CyberTrust Root. Denne overførsel tog adskillige måneder at fuldføre.)

Baltimore certifikat muligvis allerede installeret i din cacerts store, så huske at køre den **keytool-listen** kommandoen først at se, hvis den findes allerede.

Hvis du vil tilføje Baltimore CyberTrust Root, der er serienummer 02:00:00:b9 og SHA1 fingeraftryk d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Det kan hentes fra <https://cacert.omniroot.com/bc2025.crt>, gemmes i en lokal fil med filtypenavnet **.cer**, og derefter importeres ved hjælp af **keytool** , som vist ovenfor.

## <a name="next-steps"></a>Næste trin

Se [Azure Root Certificate overførslen](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx)kan finde flere oplysninger om bruges af Azure rodcertifikater.

Du kan finde flere oplysninger om Java, [Java Developer Center](/develop/java/).
