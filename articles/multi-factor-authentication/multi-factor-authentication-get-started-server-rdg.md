<properties 
    pageTitle="Remote Desktop Gateway og Azure Multi-Factor Authentication Server ved hjælp af RADIUS"
    description="Dette er den Azure Multi-Factor authentication side, der hjælper med at implementere Remote Desktop (RD) Gateway og Azure Multi-Factor Authentication Server ved hjælp af RADIUS."
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
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Remote Desktop Gateway og Azure Multi-Factor Authentication Server ved hjælp af RADIUS

I mange tilfælde bruger Remote Desktop Gateway lokale NPS til at godkende brugere. Dette dokument beskrives, hvordan du omdirigere RADIUS anmodning om ud fra den Remote Desktop Gateway (via den lokale NPS) til multi-Factor Authentication serveren.

Multi-Factor Authentication Server skal være installeret på en separat server, som kan derefter proxy RADIUS-anmodningen tilbage til NPS på Remote Desktop Gateway-serveren. Når NPS valideret brugernavnet og adgangskoden, returneres et svar til multi-Factor Authentication serveren, som udfører den anden faktor godkendelse før returnere et resultat til gatewayen.





## <a name="configure-the-rd-gateway"></a>Konfigurere RD Gateway

Gatewayen RD skal konfigureres for at sende RADIUS-godkendelse til en Azure Multi-Factor Authentication Server. Når RD Gateway er blevet installeret, konfigureret og er arbejder, gå til egenskaberne RD Gateway. Gå til fanen RD KNOP Store og ændre det til at bruge en Central server, der kører NPS i stedet for lokale server, der kører NPS. Føje en eller flere Azure Multi-Factor Authentication-servere som RADIUS-servere og angive en delt hemmeligt for hver server.





## <a name="configure-nps"></a>Konfigurere NPS

RD Gateway bruger NPS til at sende RADIUS indkaldelsen til Azure Multi-Factor Authentication. Timeout for en skal ændres for at forhindre, at gatewayen RD fra timeout, før Multi-Factor authentication er fuldført. Benyt følgende fremgangsmåde for at konfigurere NPS.

1. Udvide menuen RADIUS-klienter og Server i kolonnen yderst til venstre i NPS, og klik på Remote RADIUS Server-grupper. Gå til egenskaberne for GRUPPEN TS GATEWAY SERVER. Rediger den RADIUS-servere, der vises, og gå til fanen justering af belastning. Ændre den "antallet af sekunder uden svar før betragtes som anmodning sluppet" og "Antallet af sekunder mellem anmodninger, når serveren er identificeret som ikke-tilgængelige" til 30-60 sekunder. Klik på fanen godkendelse/konto, og Sørg for, at de RADIUS porte, der er angivet svarer til de porte, multi-Factor Authentication serveren vil lytter på.
2. NPS være også konfigureret til at modtage RADIUS godkendelser tilbage fra Azure Multi-Factor Authentication serveren. Klik på RADIUS-klienter i menuen til venstre. Tilføje Azure Multi-Factor Authentication Server som en RADIUS-klient. Vælg et navn, og Angiv en delt hemmeligt.
3. Udvid sektionen politikker i venstre navigationsrude, og klik på forbindelse anmode om politikker. Den skal indeholde en anmode om forbindelser kaldet TS GATEWAY godkendelse POLITIK, der blev oprettet, da RD Gateway blev konfigureret. Denne politik videresender RADIUS anmodninger til multi-Factor Authentication serveren.
4. Kopiér denne politik for at oprette en ny. Tilføje en betingelse, der svarer til det fulde navn klient med det fulde navn, der er angivet i trin 2 ovenfor for Azure Multi-Factor Authentication Server RADIUS-klienten i den nye politik. Ændre godkendelsesudbyderen af til lokale Computer. Denne politik sikrer, at når der modtages en RADIUS anmodning fra Azure Multi-Factor Authentication serveren, forekommer godkendelsen lokalt i stedet for at sende en anmodning om RADIUS tilbage til Azure Multi-Factor Authentication serveren hvilket resulterer i en løkke betingelse. Hvis du vil forhindre, at Løkkebetingelsen, være denne nye politik bestilt over oprindelige politikken, der videresender til multi-Factor Authentication serveren.

## <a name="configure-azure-multi-factor-authentication"></a>Konfigurere Azure Multi-Factor Authentication


--------------------------------------------------------------------------------



Azure Multi-Factor Authentication serveren er konfigureret som en RADIUS proxy mellem RD Gateway og NPS.  Det skal være installeret på en server, medlem af et domæne, der er adskilt fra RD Gateway-serveren. Brug følgende fremgangsmåde til at konfigurere Azure Multi-Factor Authentication Server.

1. Åbn Azure Multi-Factor Authentication serveren, og klik på ikonet RADIUS-godkendelse. Markér afkrydsningsfeltet Aktivér RADIUS godkendelse.
2. Sikre, at portene, der svarer til Hvad er konfigureret i NPS, og klik på Tilføj under fanen klienter... knappen. Tilføje RD Gateway server IP-adresse, programnavn (valgfrit) og et delt hemmeligt. Delte hemmeligheden skal være den samme på både Azure Multi-Factor Authentication Server og RD Gateway.
3. Klik på fanen placering, og vælg alternativknappen RADIUS server(e).
4. Klik på Tilføj... knappen. Angiv IP-adresse, delte hemmeligt og porte af NSP-serveren. Medmindre ved hjælp af en central NPS RADIUS-klienten og RADIUS målet være den samme. Delte hemmeligheden skal svare til én konfigurationen i sektionen RADIUS klienten af NSP-serveren.

![RADIUS-godkendelse](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
