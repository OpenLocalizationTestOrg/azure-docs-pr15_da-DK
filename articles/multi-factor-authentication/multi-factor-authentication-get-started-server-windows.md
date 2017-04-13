<properties 
    pageTitle="Windows-godkendelse og Azure Multi-Factor Authentication Server"
    description="Dette er den Azure Multi-Factor authentication side, der hjælper med at implementere Windows-godkendelse og Azure Multi-Factor Authentication Server."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows-godkendelse og Azure Multi-Factor Authentication Server

Sektionen Windows-godkendelse kan administratoren for at aktivere og konfigurere Windows-godkendelse til et eller flere programmer.  Følgende er en liste over ting, du skal huske på, før du konfigurerer Windows-godkendelse.

-  Genstart er påkrævet, før Azure Multi-Factor Authentication Terminal Services er gældende.
-  Hvis 'Kræver Azure Multi-Factor Authentication bruger match' er markeret, og du ikke er på brugerlisten, kan du kan ikke logge på computeren efter genstart.
-  Der er tillid til IP'er er afhængig af om programmet kan give klienten IP med godkendelsen. I øjeblikket kun Terminal Services understøttes.  







>[AZURE.NOTE]Denne funktion understøttes ikke til sikker Terminal Services på Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Hvis du vil sikre et program med Windows-godkendelse, skal du benytte følgende fremgangsmåde.

1. Klik på ikonet Windows-godkendelse i Azure Multi-Factor Authentication Server.
![Windows-godkendelse](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Markér afkrydsningsfeltet Aktivér Windows-godkendelse. Dette felt er markeret som standard.
3. Under fanen programmer gør det muligt for administratoren for at konfigurere et eller flere programmer til Windows-godkendelse.
4. Vælg en server eller et program – Angiv, om serveren/programmet er aktiveret. Klik på OK.
5. Klik på Tilføj... knappen.
6. Fanen der er tillid til IP'er gør det muligt at springe Azure Multi-Factor Authentication til Windows-sessioner, der kommer fra bestemte IP'er. Eksempelvis hvis medarbejdere bruge programmet fra kontoret og derhjemme, skal beslutte du du ikke vil deres telefoner ringer til Azure Multi-Factor Authentication, mens du er på kontoret. Dette, skal du angive office-undernettet som der er tillid til IP'er post.
7. Klik på Tilføj... knappen.
8. Vælg enkelt IP-adresse, hvis du vil springe over en enkelt IP-adresse.
9. Vælg IP-område, hvis du vil have at springe et hele IP-område. Eksempel på 10.63.193.1-10.63.193.100.
10. Vælg undernet, hvis du vil have til at angive et område af IP-adresser ved hjælp af undernet notation. Angiv det undernet Start-IP- og vælge den relevante netmaske fra på rullelisten.
11. Klik på knappen OK.
