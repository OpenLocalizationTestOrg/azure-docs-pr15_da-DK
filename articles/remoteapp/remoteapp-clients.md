
<properties
    pageTitle="Få adgang til dine apps fra enhver enhed | Microsoft Azure"
    description="Få mere at vide, hvilke klienter understøttes til Azure RemoteApp og hvordan du kan få adgang til dine apps."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="accessing-your-apps-in-azure-remoteapp"></a>Få adgang til dine apps i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

En af beauties af Azure RemoteApp er, at du kan få adgang til apps fra enhver af dine enheder. Endnu bedre kan du begynde at arbejde på en enhed og derefter problemfri overgang til en anden enhed og vælge højre afbrydelsen. At komme i gang skal du hente den relevante klient til din enhed og logge på tjenesten.

I dette emne gennemgår vi de klienter, der understøttes i øjeblikket, og hvordan du hente dem, før jeg viser dig, hvordan du logger på RemoteApp fra hver af klienterne.

## <a name="supported-clients"></a>Understøttede klienter

Du kan få adgang til RemoteApp ved hjælp af nedenstående trin, hvis din enhed kører en af disse operativsystemer:

 - Windows 10 
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows Phone 8.1
 - iOS
 - Mac OS X
 - Android


 Hvad med tynde klienter? Følgende Windows integreret tynde klienter understøttes:

- Windows integreret Standard 7
- Windows integreret 8 Standard
- Windows integreret 8.1 branche Pro
- Windows 10 IoT Enterprise


## <a name="downloading-the-client"></a>Hente klienten

Uanset hvilken platform, du bruger, kan du har brug for at få adgang til RemoteApp klienten findes på siden [hente klienten Fjernskrivebord](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) .

Hvis du klikker på de forskellige links enten direkte begynder at hente klienten eller sender du til klienten overførselssiden i app store for den pågældende platform. Installere klienten ved at følge vejledningen på skærmen.

Når du har installeret klienten på din enhed og startet det, kan du gå til afsnittet tilsvarende nedenfor for at se, hvordan du logger på RemoteApp fra klienten.

## <a name="android"></a>Android

Når du har installeret Microsoft Remote Desktop appen fra Google Play store, kan du finde den på applisten under **Fjernskrivebord**.

1. Start appen giver dig til en tom forbindelse Center, medmindre du har allerede ved hjælp af appen. Tryk på Tilføj knappen **"" +""** for at komme i gang med Azure RemoteApp, og tryk på **Azure RemoteApp**. 

     ![Tøm Connection Center](./media/remoteapp-clients/Android1.png)

2. Du skal logge på med din e-mail-adresse at få adgang til tjenesten. Tryk på **Introduktion**.

    ![Log på prompten](./media/remoteapp-clients/Android2.png)

3. Indtast din **mailadresse** på den næste side, og tryk på **Fortsæt**. Dette starter den proces, ved hjælp af Azure Active Directory-logon.

    ![Første Azure Active Directory-side](./media/remoteapp-clients/Android3.png)

4. Følg vejledningen på skærmen for at logge på med din Microsoft-konto (tidligere kaldet "LiveID") eller organisations-id'et. Når du er logget, er du muligvis præsenteret med en side med en oversigt over alle invitationer, du har modtaget. Hvis du er, Vælg invitationer, du har tillid til, og tryk på **udført**. 

    ![Invitationer side](./media/remoteapp-clients/Android4.png)

5. Når du har accepteret invitationen, bliver på listen over apps, du har adgang til hentet til din enhed og gjort tilgængelige i forbindelse Center. Tryk på en af apps til at begynde at bruge den.

    ![Connection Center med et feed](./media/remoteapp-clients/Android5.png)

6. Hvis du ikke har en invitation, men du stadig kan prøve tjenesten. Tryk på **Gå til gratis prøveversion** , når du bliver bedt om at gøre det.

    ![Demo feed meddelelse](./media/remoteapp-clients/Android6.png)

7. Dette giver dig adgang til et grundlæggende sæt over apps for at komme i gang med RemoteApp.

    ![Demo feedet for Azure RemoteApp](./media/remoteapp-clients/Android7.png)

## <a name="ios"></a>iOS

Når du har installeret Microsoft Remote Desktop-app fra App store, kan du finde den på applisten under **RD klient**.

1. Start appen giver dig til en tom forbindelse Center, medmindre du har allerede ved hjælp af appen. Tryk på Tilføj knappen **"" +""** for at komme i gang med Azure RemoteApp, og tryk på **Tilføj Azure RemoteApp**.

    ![Tøm Connection Center](./media/remoteapp-clients/IOS1.png)

2. Du har brug for at logge på med din e-mail-adresse at få adgang til tjenesten, for at starte processen, indtast din **mailadresse** og tryk på **Fortsæt**.

    ![Log på prompten](./media/remoteapp-clients/picture1.png)

3. Følg vejledningen på skærmen for at logge på med din Microsoft-konto (LiveID) eller organisation-ID. Når du er logget, er du muligvis præsenteret med en side med en oversigt over alle invitationer, du har modtaget. Hvis du er, Vælg invitationer, du har tillid til, og tryk på **udført**.

    ![Invitationer side](./media/remoteapp-clients/IOS3.png)

4. Når du har accepteret invitationen, bliver på listen over apps, du har adgang til hentet til din enhed og gjort tilgængelige i forbindelse Center. Tryk på en af apps til at starte det og begynde at bruge den.

    ![Connection Center med et feed](./media/remoteapp-clients/IOS4.png)

5. Hvis du ikke har en invitation, men du stadig kan prøve tjenesten. Tryk på **Gå til gratis prøveversion** , når du bliver bedt om at gøre det.

    ![Demo feed meddelelse](./media/remoteapp-clients/IOS5.png)

6. Dette giver dig adgang til et grundlæggende sæt over apps for at komme i gang med RemoteApp.

    ![Feedet for Azure RemoteApp demo](./media/remoteapp-clients/IOS6.png)

## <a name="mac-os-x"></a>Mac OS X

Når du har installeret Microsoft Remote Desktop-app fra App store, kan du finde den på applisten under **Microsoft Remote Desktop**.

1. Start appen giver dig til en tom forbindelse Center, medmindre du har allerede ved hjælp af appen. Klik på knappen **Azure RemoteApp** for at komme i gang med Azure RemoteApp skal.

    ![Tøm Connection Center](./media/remoteapp-clients/Mac1.png)

2. Du har brug for at logge på med din e-mail-adresse at få adgang til tjenesten, for at starte processen, trykke på **Kom i gang**.

    ![Log på prompten](./media/remoteapp-clients/Mac2.png)

3. Indtast din **mailadresse** på den næste side, og tryk på **Fortsæt**. Dette starter Log på ved hjælp af Azure Active Directory.

    ![Første Azure Active Directory-side](./media/remoteapp-clients/picture2.png)

4. Følg vejledningen på skærmen for at logge på med din Microsoft-konto (LiveID) eller organisation-ID. Når du er logget, er du muligvis præsenteret med en side med en oversigt over alle invitationer, du har modtaget. Hvis du er, Vælg invitationer, du har tillid til, og Luk dialogboksen.

    ![Invitationer side](./media/remoteapp-clients/Mac4.png)

5. Når du har accepteret invitationen, bliver på listen over apps, du har adgang til hentet til din enhed og gjort tilgængelige i forbindelse Center. Dobbeltklik på en af apps til at starte det og begynde at bruge den.

    ![Connection Center med et feed](./media/remoteapp-clients/Mac5.png)

6. Hvis du ikke har en invitation, men du stadig kan prøve tjenesten. Klik på **Gå til gratis prøveversion** , når du bliver bedt om at gøre det.

    ![Demo feed meddelelse](./media/remoteapp-clients/Mac6.png)

7. Dette giver dig adgang til et grundlæggende sæt over apps for at komme i gang med RemoteApp.

    ![Demo feedet for Azure RemoteApp](./media/remoteapp-clients/Mac7.png)

## <a name="windows-all-supported-versions-except-windows-phone"></a>Windows (alle understøttede versioner, undtagen Windows Phone)

Klienten starter automatisk, når det er afsluttet installeret, men når du har brug at få adgang til den igen senere den ikke kan findes på applisten under navnet på **Azure RemoteApp**.

1. Brevfletningsfunktionen starter klienten, den første side, du får vist vil du gerne have til Azure RemoteApp. Klik på **Introduktion**for at fortsætte.

    ![Velkomstsiden af Azure RemoteApp-klienten](./media/remoteapp-clients/Windows1.png)

2. Den næste side starter tegnet i processen til Azure RemoteApp ved hjælp af Azure Active Directory. Denne proces ser velkendt, hvis du har brugt Microsoft services tidligere. Start med at skrive din **mailadresse** , og klik på **Fortsæt**.

    ![Første Azure Active Directory-prompt](./media/remoteapp-clients/Windows2.png)

3. Følg vejledningen på skærmen for at logge på med din Microsoft-konto (LiveID) eller organisation-ID. Når du er logget, er du muligvis præsenteret med en side med en oversigt over alle invitationer, du har modtaget. Hvis du er, Vælg invitationer, du har tillid til, og klik på **udført**.

    ![Invitationer side af Azure RemoteApp-klienten](./media/remoteapp-clients/Windows3.png)

4. Når du har accepteret invitationen, bliver på listen over apps, du har adgang til hentet til din enhed og gjort tilgængelige i forbindelse Center. Dobbeltklik på en af apps til at starte det og begynde at bruge den.

    ![Forbindelse midten af Azure RemoteApp-klient](./media/remoteapp-clients/Windows4.png)

5. Hvis ingen har sendt dig en invitation endnu, bare rolig vi har du dækket! Du skal stadig har adgang til en demonstration af websteder, så du kan teste tjenesten.

    ![Demo feedet for Azure RemoteApp](./media/remoteapp-clients/Windows5.png)

## <a name="windows-phone-81"></a>Windows Phone 8.1

Når du har installeret Microsoft Remote Desktop-app fra Windows Phone 8.1 store, kan du finde den på applisten under **Fjernskrivebord**.

1. Start appen giver dig direkte til en tom forbindelse Center, medmindre du har allerede ved hjælp af appen. For at komme i gang med Azure RemoteApp skal du trykke på Tilføj knappen **"" +""** i bunden af skærmen.

    ![Tøm Connection Center](./media/remoteapp-clients/WinPhone1.png)

2. Derefter skal du trykke på **Azure RemoteApp**.

    ![Tilføje element](./media/remoteapp-clients/WinPhone2.png)

3. Du har brug for at logge på med din e-mail-adresse at få adgang til tjenesten, for at starte processen, tryk på **Opret forbindelse**.

    ![Log på prompten](./media/remoteapp-clients/WinPhone3.png)

4. Indtast din **mailadresse** på den næste side, og tryk på **Fortsæt**. Dette starter Log på ved hjælp af Azure Active Directory.

    ![Første Azure Active Directory-side](./media/remoteapp-clients/WinPhone4.png)

5. Følg vejledningen på skærmen for at logge på med din Microsoft-konto (LiveID) eller organisation-ID. Når du er logget, er du muligvis præsenteret med en side med en oversigt over alle invitationer, du har modtaget. Hvis du er, Vælg invitationer, du har tillid til, og tryk på **Gem**.

    ![Invitationer side](./media/remoteapp-clients/WinPhone5.png)

6. Når du har accepteret invitationen, bliver på listen over apps, du har adgang til hentet til din enhed og gjort tilgængelige i forbindelse Center. Tryk på en af apps til at starte det og begynde at bruge den.

    ![Connection Center med et feed](./media/remoteapp-clients/WinPhone6.png)

7. Hvis du ikke har en invitation, men du stadig kan prøve tjenesten. Tryk på **Ja** , når du bliver bedt om at gøre det.

    ![Demo feed meddelelse](./media/remoteapp-clients/WinPhone7.png)

8. Dette giver dig adgang til et grundlæggende sæt over apps for at komme i gang med RemoteApp.

    ![Demo feedet for Azure RemoteApp](./media/remoteapp-clients/WinPhone8.png)
 