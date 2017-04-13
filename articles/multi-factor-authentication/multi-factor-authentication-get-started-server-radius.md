<properties 
    pageTitle="RADIUS-godkendelse og Azure Multi-Factor Authentication Server"
    description="Dette er den Azure Multi-Factor authentication side, der hjælper med at implementere RADIUS-godkendelse og Azure Multi-Factor Authentication Server."
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



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>RADIUS-godkendelse og Azure Multi-Factor Authentication Server

Sektionen RADIUS-godkendelse giver dig mulighed at aktivere og konfigurere RADIUS godkendelse for den Azure Multi-Factor Authentication Server. RADIUS er en standard protokol at acceptere anmodninger om godkendelse og til at behandle disse anmodninger. Azure Multi-Factor Authentication Server fungerer som en RADIUS-server og er indsat mellem RADIUS-klienten (fx VPN maskinen) og din godkendelse målet, som kan være Active Directory (AD), en LDAP-adresseliste eller en anden RADIUS-server, hvis du vil tilføje Azure Multi-Factor Authentication. Til Azure Multi-Factor Authentication til funktionen, skal du konfigurere Azure Multi-Factor Authentication Server, så den kan kommunikere med både klient-servere og mål-godkendelse. Azure Multi-Factor Authentication serveren accepterer anmodninger fra en RADIUS-klient, valideret legitimationsoplysninger forhold til godkendelse målet, tilføjer Azure Multi-Factor Authentication og sender svar tilbage til RADIUS-klienten. Hele godkendelsen lykkes kun, hvis den både den primære og Azure Multi-Factor godkendelse lykkes.

>[AZURE.NOTE]
>MFA serveren understøtter kun PAP (adgangskode godkendelse protocol) og MSCHAPv2 (Microsofts udfordring Password Authentication Protocol) RADIUS protokoller, når der fungerer som en RADIUS-server.  Andre protokoller f.eks (extensible authentication protocol) kan bruges, når serveren MFA fungerer som en RADIUS proxy til en anden RADIUS-server, som understøtter denne protokol som Microsoft NPS.
></br>
>Når du bruger andre protokoller i denne konfiguration, fungerer envejs SMS og ed tokens ikke, da MFA serveren ikke er kan starte en vellykket RADIUS udfordring svar ved hjælp af denne protokol.


![RADIUS-godkendelse](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Konfiguration af RADIUS godkendelse

Installer Azure Multi-Factor Authentication Server på en Windows server for at konfigurere RADIUS-godkendelse. Hvis du har en Active Directory-miljø, skal serveren, der være tilsluttet domænet i netværket. Benyt følgende fremgangsmåde til at konfigurere Azure Multi-Factor Authentication Server:

1. Klik på ikonet RADIUS-godkendelse i menuen til venstre i Azure Multi-Factor Authentication Server.
2. Markér afkrydsningsfeltet Aktivér RADIUS godkendelse.
3. Ændre godkendelse porte og Accounting porte, hvis tjenesten Azure Multi-Factor Authentication RADIUS skal bindes til ikke-standard porte lytte til RADIUS anmodninger fra de klienter, der skal konfigureres, under fanen klienter.
4. Klik på Tilføj... knappen.
5. Angive maskinen/serveren, der skal godkende IP-adressen til Azure Multi-Factor Authentication Server, navnet på et program (valgfrit) og en delt hemmeligt i dialogboksen Tilføj RADIUS-klienten. Delte hemmeligheden skal være den samme på både Azure Multi-Factor Authentication Server og maskinen/server. Navnet på programmet vises i Azure Multi-Factor Authentication rapporter og kan vises i SMS eller Mobile-App godkendelse meddelelser.
6. Markér afkrydsningsfeltet Kræv Multi-Factor Authentication bruger match, hvis alle brugere har været eller der skal importeres til serveren og underlagt Multi-Factor authentication. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra Multi-Factor authentication, Lad feltet være umarkerede. Se Hjælp-filen til yderligere oplysninger om denne funktion.
7. Afkrydsningsfeltet Aktivér fallback ed token Hvis brugere anvender Azure Multi-Factor Authentication mobilapp godkendelse, og du vil bruge ed adgangskoderne til mødet som en fallback godkendelse på out-of-band telefonen ringer, SMS eller push meddelelsen.
8. Klik på knappen OK.
9. Du kan gentage trin 4-8 for at tilføje yderligere RADIUS-klienter.
10. Klik på fanen placering.
11. Hvis Azure Multi-Factor Authentication Server er installeret på en server, der er medlem af et domæne, i en Active Directory-miljø, kan du vælge Windows-domæne.
12. Hvis brugere skal godkendes mod en LDAP-adresseliste, kan du vælge LDAP-binding. Når du bruger LDAP-binding, skal du klikke på ikonet katalogintegration og redigere LDAP-konfiguration på fanen Indstillinger, så serveren kan bundet til adresselisten. Instruktioner til konfiguration af LDAP kan findes i konfigurationsvejledning LDAP-Proxy.
13. Hvis brugere skal godkendes mod en anden RADIUS-server, kan du vælge RADIUS server(e).
14. Konfigurer serveren, serveren vil proxy RADIUS anmodninger om til ved at klikke på Tilføj... knappen.
15. Angiv IP-adressen på RADIUS-serveren og en delt hemmeligt i dialogboksen Tilføj RADIUS Server. Delte hemmeligheden skal være den samme på både Azure Multi-Factor Authentication Server og RADIUS-serveren. Ændre godkendelse port og Accounting port, hvis forskellige porte anvendes af RADIUS-serveren.
16. Klik på knappen OK.
17. Du skal tilføje Azure Multi-Factor Authentication Server som en RADIUS-klient i andre RADIUS-serveren, så behandler anmodninger om adgang, der sendes til den fra Azure Multi-Factor Authentication Server. Du skal bruge det samme delte hemmeligt, der er konfigureret i Azure Multi-Factor Authentication Server.
18. Du kan gentage dette trin for at tilføje yderligere RADIUS-servere og konfigurere den rækkefølge, hvori serveren skal ringe til dem med knapperne Flyt op og Flyt ned. Dette er fuldført Azure Multi-Factor Authentication serverkonfiguration. Serveren lytter nu på de konfigurerede porte for RADIUS anmodninger om adgang fra konfigurerede klienter.   


## <a name="radius-client-configuration"></a>RADIUS klientkonfiguration

Brug retningslinjer for at konfigurere RADIUS-klienten skal:

- Konfigurere din maskinen server til at godkende via RADIUS Azure Multi-Factor Authentication serverens IP-adresse, som skal fungere som RADIUS-serveren.
- Brug det samme delte hemmeligt, der blev konfigureret ovenfor.
- Konfigurere RADIUS timeouten til 30-60 sekunder, så der er tid til at validere brugerens legitimationsoplysninger, udføre Multi-Factor authentication, modtage svar og derefter besvare RADIUS-anmodning om adgang.
