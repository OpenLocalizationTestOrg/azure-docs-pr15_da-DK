<properties 
    pageTitle="LDAP-godkendelse og Azure Multi-Factor Authentication Server"
    description="Dette er den Azure Multi-Factor authentication side, der hjælper med at implementere LDAP-godkendelse og Azure Multi-Factor Authentication Server."
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

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-godkendelse og Azure Multi-Factor Authentication Server


Azure Multi-Factor Authentication serveren er som standard konfigureret til at importere eller synkroniserer brugere fra Active Directory. Det kan dog være konfigureret til bindes til forskellige LDAP-mapper, som en ADAM directory eller bestemte Active Directory-domænecontrolleren. Når konfigureret til at oprette forbindelse til en mappe via LDAP, kan Azure Multi-Factor Authentication serveren konfigureres til at fungere som en LDAP-proxy til at udføre godkendelse. Det kan også til brug af LDAP-binding et RADIUS mål for foreløbig godkendelse af brugere, når du bruger IIS-godkendelse eller primær godkendelse i Azure Multi-Factor Authentication User Portal.

Når du bruger Azure Multi-Factor Authentication som en LDAP-proxy, indsættes Azure Multi-Factor Authentication Server mellem LDAP-klienten (fx VPN maskinen, programmet) og directory LDAP-serveren for at tilføje Multi-Factor authentication. Til Azure Multi-Factor Authentication til funktionen, skal være konfigureret Azure Multi-Factor Authentication serveren for at kommunikere med både klient-servere og LDAP-adresseliste. I denne konfiguration Azure Multi-Factor Authentication serveren accepterer LDAP-anmodninger fra klient servere og programmer og sender dem til den ønskede LDAP directory server til at validere de primære legitimationsoplysninger. Hvis svaret fra LDAP-adresseliste viser, at de primære legitimationsoplysninger er gyldige, Azure Multi-Factor Authentication udfører andet faktor-godkendelse og sender svar tilbage til LDAP-klienten. Hele godkendelsen lykkes kun, hvis både godkendelse til LDAP-serveren og multi-Factor authentication lykkes.





## <a name="ldap-authentication-configuration"></a>Konfiguration af LDAP-godkendelse


Installer Azure Multi-Factor Authentication Server på en Windows server for at konfigurere LDAP-godkendelse. Benyt følgende fremgangsmåde:

1. Klik på ikonet LDAP-godkendelse i menuen til venstre i Azure Multi-Factor Authentication Server.
2. Markér afkrydsningsfeltet Aktivér LDAP-godkendelse.![LDAP-godkendelse](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Ændre TCP-port og SSL-port, hvis Azure Multi-Factor Authentication LDAP-tjenesten skal bindes til ikke-standard porte lytte til LDAP-anmodninger fra de klienter, der skal konfigureres, under fanen klienter.
4. Hvis du planlægger at bruge LDAPS fra klienten til Azure Multi-Factor Authentication Server, skal være installeret et SSL-certifikat på den server, der kører på serveren på. Klik på Søg... knappen ud for feltet SSL-certifikat, og vælg det certifikat, der anvendes til sikker forbindelse.
5. Klik på Tilføj... knappen.
6. Angive maskinen, server eller -programmer, der skal godkende IP-adressen til serveren og navnet på et program (valgfrit) i dialogboksen Tilføj LDAP-klient. Navnet på programmet vises i Azure Multi-Factor Authentication rapporter og kan vises i SMS eller Mobile-App godkendelse meddelelser.
7. Markér afkrydsningsfeltet Kræv Azure Multi-Factor Authentication bruger match, hvis alle brugere har været eller der skal importeres til serveren og underlagt mutli faktor-godkendelse. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra mutli faktor-godkendelse, Lad feltet være umarkerede. Se Hjælp-filen til yderligere oplysninger om denne funktion.
8. Du kan gentage trin 5 til 7 for at tilføje yderligere LDAP-klienter.
9. Når Azure Multi-Factor Authentication er konfigureret til at modtage LDAP-godkendelse, skal den proxy disse godkendelse til LDAP-adresseliste. Derfor nedtonet Target fanen kun viser en enkelt, indstilling for at bruge en LDAP-destination. Klik på ikonet katalogintegration for at konfigurere LDAP-adresselisteforbindelse.
10. Vælg Brug bestemte LDAP-konfiguration alternativknappen under fanen Indstillinger.
11. Klik på Rediger... knappen.
12. Udfyld felterne med de oplysninger, der kræves for at oprette forbindelse til LDAP-adresseliste i dialogboksen Rediger LDAP-konfiguration. Beskrivelser af felterne, der medtages i tabellen nedenfor. Bemærk: Disse oplysninger er også medtaget i Azure Multi-Factor Authentication Server Hjælp-filen.![Katalogintegration](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Test LDAP-forbindelse ved at klikke på knappen Test.
14. Hvis LDAP-forbindelse der er oprettet, kan du klikke på knappen OK.
15. Klik på fanen filtre. Serveren er allerede konfigureret til at indlæse beholdere, sikkerhedsgrupper og brugere fra Active Directory. Hvis binder til en anden LDAP-adresseliste, skal du sandsynligvis at redigere de filtre, der vises. Klik på linket Hjælp for flere oplysninger om filtre.
16. Klik på attributter. Serveren er allerede konfigureret til at knytte attributter fra Active Directory.
17. Klik på Rediger, hvis binder til en anden LDAP-mappe eller ændrer attributtilknytningerne forudkonfigurerede... knappen.
18. Rediger LDAP-attributtilknytninger til adresselisten i dialogboksen Rediger attributter. Navne på attributter kan indtastes eller vælges ved at klikke på den... knappen ved siden af hvert felt.
19. Klik på linket Hjælp for flere oplysninger om attributter.
20. Klik på knappen OK.
21. Klik på ikonet Indstillinger for virksomhed, og vælg fanen brugernavn opløsning.
22. Hvis forbindelse til Active Directory fra en server, der er medlem af et domæne, skal du kunne forlade Brug Windows sikkerhed id'erne (sikkerheds-id'er) efter tilsvarende brugernavne alternativknappen markeret. Ellers skal du vælge attributten Brug LDAP entydigt id for tilsvarende brugernavne alternativknap. Når markeret, vil Azure Multi-Factor Authentication serveren forsøge at løse hver brugernavn til et entydigt id i LDAP-adresseliste. En LDAP-søgning kan udføres på feltet Username-attributter, der er defineret i den katalogintegration > under fanen attributter. Når en bruger godkendes, brugernavnet oversættes til det entydige id i LDAP-adresseliste, og det entydige id, der skal bruges til tilsvarende brugeren i datafilen Azure Multi-Factor Authentication. Dette giver mulighed for case-insensitive sammenligninger, som også som lange og korte brugernavn formater dette fuldfører Azure Multi-Factor Authentication serverkonfiguration. Serveren lytter nu på de konfigurerede porte til LDAP-anmodninger om adgang fra konfigurerede klienter, og Angiv til proxy disse anmodninger om til LDAP-adresseliste til godkendelse.


## <a name="ldap-client-configuration"></a>Konfiguration af LDAP-klient

Brug retningslinjer for at konfigurere LDAP-klienten skal:

- Konfigurere din maskinen, server eller program tilladelse til at godkende via LDAP til Azure Multi-Factor Authentication Server, som hvis det var LDAP-adresseliste. Du skal bruge de samme indstillinger, som du normalt ville bruge til at oprette forbindelse direkte til din LDAP-adresseliste, med undtagelse af servernavnet eller IP-adresse, som skal findes på Azure Multi-Factor Authentication serveren.
- Konfigurere LDAP-timeoutværdien til 30-60 sekunder, så der er tid til at validere brugerens legitimationsoplysninger med LDAP-adresseliste, udføre andet faktor-godkendelse, modtage svar og derefter besvare LDAP-anmodning om adgang.
- Hvis bruger LDAPS, maskinen eller forespørgsler LDAP-serveren skal have tillid til det SSL-certifikat, der er installeret på Azure Multi-Factor Authentication serveren.
