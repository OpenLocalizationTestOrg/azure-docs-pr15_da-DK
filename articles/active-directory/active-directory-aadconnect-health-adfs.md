
<properties
    pageTitle="Brug af Azure AD forbinde tilstand med AD FS | Microsoft Azure"
    description="Dette er den Azure AD forbinde tjenestetilstand på siden hvordan til at overvåge dine lokale AD FS infrastruktur."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>Brug af Azure AD forbinde tilstand med AD FS
Følgende dokumentation er specifikke for overvågning infrastrukturen AD FS med Azure AD forbinde tilstand. Du kan finde oplysninger om overvågning Azure AD-forbindelse (Sync) med Azure AD forbinde sundhed [Ved hjælp af Azure AD forbinde tilstand for synkronisering](active-directory-aadconnect-health-sync.md). Oplysninger om overvågning af Active Directory Domain Services med Azure AD forbinde tilstand finder du desuden [Ved hjælp af Azure AD forbinde tilstand med Active Directory-Domænetjenester](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Beskeder for AD FS
Azure AD forbinde sundhed afsnittet beskeder indeholder en oversigt over aktive beskeder. Hver besked indeholder relevante oplysninger, opløsning trin og links til relateret dokumentation.

Du kan dobbeltklikke på en aktiv eller løst besked, hvis du vil åbne en ny blade med yderligere oplysninger, trin, du kan bruge til at løse besked og links til relevante dokumentation. Du kan også få vist historiske data på beskeder, der er blevet løst tidligere.

![Azure AD Connect sundhed Portal](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>Brugsstatistik for AD FS
Azure AD forbinde sundhed Brugsstatistik analyserer godkendelse trafikken til og fra din sammenslutningsservere. Du kan dobbeltklikke på feltet brugen analyser for at åbne bladet brugen analytics, som viser flere målepunkter og grupperinger.

>[AZURE.NOTE] Hvis du vil bruge Brugsstatistik med AD FS, skal du kontrollere, at AD FS overvågning er aktiveret. Se [Aktivere overvågning for AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs)kan finde flere oplysninger.

![Azure AD Connect sundhed Portal](./media/active-directory-aadconnect-health/report1.png)

Vælg flere statistikker ved at angive et tidsinterval, eller for at ændre grupperingen, skal du højreklikke på brugen analytics diagrammet, og vælg Rediger diagram. Du kan derefter angive tidsrum, Vælg en anden metrikværdi og ændre grupperingen. Du kan se fordelingen af godkendelse trafikken baseret på forskellige "måleenheder" og gruppere hver metrisk ved hjælp af relevante "grupper efter" parametre er beskrevet i følgende tabel:

| Metrisk | Gruppere efter | Hvilke grupperingen betyder, og hvorfor det er nyttigt? |
| ------ | -------- | -------------------------------------------- |
| Total anmoder om: Det samlede antal anmodninger behandles af tjenesten sammenslutning | Alle | Viser antallet af samlede antal anmodninger uden gruppering. |
|  | Program | Grupperer samlede anmodningerne baseret på den målrettede afhængige part. Denne gruppering er nyttigt at forstå, hvilket program modtager hvor stor en procentdel af den samlede trafik. |
|  | Server | Grupperer samlede anmodningerne baseret på den server, behandlet anmodningen. Denne gruppering er nyttigt at forstå Indlæs fordelingen af den samlede trafik. |
|  | Deltag i arbejdsområde | Grupperer de samlede anmodninger, baseret på om de kommer fra enheder, der er tilsluttet sig arbejdsplads (kendt). Denne gruppering er nyttigt at forstå, hvis dine ressourcer åbnes ved hjælp af enheder, der er ukendte til infrastruktur identitet. |
|  | Godkendelsesmetode | Grupperer samlede anmodningerne baseret på den anvendte godkendelsesmetode, som bruges til godkendelse. Denne gruppering er nyttigt at forstå den almindelige godkendelsesmetode, der anvendes til godkendelse. Følgende er de mulige godkendelsesmetoder <ol> <li>Integreret Windows-godkendelse (Windows)</li> <li>Formularbaseret godkendelse (formularer)</li> <li>SSO (Single Sign-On)</li> <li>X509 certifikat godkendelse (certifikat)</li> <br>Hvis sammenslutningsserverne modtager anmodningen med en SSO Cookie, tælles anmodningen som SSO (Single Sign-On). I så fald Hvis cookie er gyldige, brugeren bliver ikke bedt om at angive legitimationsoplysninger og bliver problemfri adgangen til programmet. Denne funktionalitet er almindelige, hvis du har flere afhængige parter beskyttet af sammenslutningsserverne. |
|  | Netværksplacering | Grupperer samlede anmodningerne baseret på netværksplacering for brugeren. Det kan være et intranet eller ekstranet. Denne gruppering er praktisk at vide, hvor stor en procentdel af trafikken kommer fra intranet eller ekstranet. |
| Samlet antal mislykkede anmodninger: Det samlede antal mislykkede anmodninger behandlet af tjenesten sammenslutning. <br> (Denne metrikværdi er kun tilgængelig på AD FS til Windows Server 2012 R2)| Fejltype | Viser antallet af fejl, der er baseret på foruddefinerede fejl. Denne gruppering er nyttigt at forstå de almindelige typer fejl. <ul><li>Forkert brugernavn eller adgangskode: fejl på grund af forkert brugernavn eller adgangskode.</li> <li>"Ekstranet låsning": mislykkede forsøg på grund af anmodninger modtaget fra en bruger, der er låst ude fra ekstranet </li><li> "Udløbet adgangskode": mislykkede forsøg på grund af brugere, der logger på med en udløbet adgangskode.</li><li>"Deaktiveret konto": mislykkede forsøg på grund af brugere, der logger med en deaktiveret konto.</li><li>"Enhed godkendelse": mislykkede forsøg på grund af brugere ned til at godkende ved hjælp af enhed godkendelse.</li><li>"Bruger certifikatgodkendelse": mislykkede forsøg på grund af brugere ned til at godkende på grund af et ugyldigt certifikat.</li><li>"MFA": mislykkede forsøg på grund af brugeren ned til at godkende ved hjælp af Multi-Factor Authentication.</li><li>"Andre legitimationsoplysninger": "Udstedelse godkendelse": mislykkede forsøg på grund af mislykkede forsøg på godkendelse.</li><li>"Udstedelse delegering": mislykkede forsøg på grund af udstedelse delegering fejl.</li><li>"Token accept": mislykkede forsøg på grund af ADFS afvise token fra en tredjepart identitetsudbyder.</li><li>"Protokollen": fejl på grund af fejl i protokollen.</li><li>"Ukendt": alle. Eventuelle andre fejl, der ikke falder inden for de definerede kategorier.</li> |
|  | Server | Grupper fejlene baseret på serveren. Denne gruppering er nyttigt at forstå fejl fordeling på tværs af servere. Ulige fordeling kan være en indikator på en server i en defekt tilstand. |
|  | Netværksplacering | Grupper fejlene baseret på sted på anmodninger (intranet vs ekstranet) netværket. Denne gruppering er nyttigt at forstå typer forespørgsler, der ned. |
|  | Program | Grupperer fejlene i baseret på programmet målrettede (afhængige part). Denne gruppering er nyttigt at forstå, hvilke målrettede programmet ser de fleste antal fejl. |
| Bruger: gennemsnitlige antal entydige brugere, der er aktive i systemet | Alle | Denne metrikværdi indeholder en optælling af gennemsnitlige antal brugere ved hjælp af tjenesten sammenslutning i det valgte tidspunkt udsnit. Brugerne, der er ikke grupperet. <br>Gennemsnitlige afhænger af det tidsrum, der er markeret. |
|  | Program | Grupperer det gennemsnitlige antal brugere, der er baseret på programmet målrettede (afhængige part). Denne gruppering er nyttigt at forstå, hvor mange brugere bruger hvilket program. |


## <a name="performance-monitoring-for-ad-fs"></a>Overvågning af ydeevnen for AD FS
Azure AD forbinde sundhedsovervågning indeholder overvågning oplysninger om målepunkter. Valg af feltet overvågnings, åbnes en ny blade med detaljerede oplysninger om målene.


![Azure AD Connect sundhed Portal](./media/active-directory-aadconnect-health/perf1.png)


Ved at vælge indstillingen Filter øverst på bladet, kan du filtrere efter serveren for at se en individuel server målepunkter. Ændre målepunkter, skal du højreklikke på overvågning diagrammet under bladet overvågning og vælge Rediger diagram. Derefter fra den nye blade, der åbnes, kan du vælge flere statistikker fra rullelisten og angive et tidsinterval til at få vist ydelsesdata.

## <a name="reports-for-ad-fs"></a>Rapporter til AD FS
Azure AD forbinde sundhed indeholder rapporter om aktivitet og ydeevnen af AD FS. Disse rapporter hjælper administratorer med at få indsigt i aktiviteter på deres AD FS-servere.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Top 50 brugere med mislykkede logonforsøg brugernavn og adgangskode

En af de almindelige årsager til en anmodning om mislykkedes godkendelse på en AD FS-server er en anmodning med ugyldige legitimationsoplysninger, det vil sige, en forkert brugernavn eller adgangskode. Som regel sker der med brugere på grund af komplekse adgangskoder, glemt adgangskode eller slåfejl.

Men der er andre årsager, som kan medføre et uventet antal anmodninger behandles af dine AD FS-servere, f.eks.: et program, der cache brugerlegitimationsoplysninger og legitimationsoplysningerne udløbe eller en ondsindet bruger forsøger at logge på en konto hos en række kendte adgangskoder. Disse to eksempler er gyldige grunde, der kan medføre en stigning i anmodninger.

Azure AD forbinde tilstand for ADFS indeholder en rapport om øverste 50 brugere med mislykkede logonforsøg på grund af ugyldigt brugernavn eller adgangskode. Denne rapport opnås ved at behandle de overvågningshændelser, der genereres af alle AD FS-servere i farme

![Azure AD Connect sundhed Portal](./media/active-directory-aadconnect-health-adfs/report1a.png)

I denne rapport har du nem adgang til de følgende stykker af oplysninger:

- Samlet antal mislykkede anmodninger med forkert brugernavn og adgangskode i de seneste 30 dage
- Gennemsnitlige antal brugere, der mislykkedes med et forkert brugernavn/adgangskode login dagen.


Klik på denne del, fører dig til bladet hovedrapport, hvor du kan få mere at vide. Denne blade omfatter en graf med cirkulerer oplysninger til at oprette en oprindelig plan om anmodninger med forkert brugernavn eller adgangskode. Desuden indeholder den liste over toppen 50 brugere med antallet af mislykkede forsøg.

Grafen indeholder følgende oplysninger:

- Det samlede antal mislykkede logonforsøg på grund af en forkert brugernavn og adgangskode på grundlag af hver dag.
- Det samlede antal entydige brugere, der ikke blev logon på grundlag af hver dag.
- Klient IP-adressen på til sidste anmodning

![Azure AD Connect sundhed Portal](./media/active-directory-aadconnect-health-adfs/report3a.png)

Rapporten indeholder følgende oplysninger:

| Rapportelement | Beskrivelse
| ------ | -------- |
|Bruger-ID| Viser det bruger-ID, der blev brugt. Denne værdi er, hvad brugeren skrev, hvilket i nogle tilfælde er den forkerte bruger-ID bruges.|
|Mislykkede forsøg| Viser det samlede antal mislykkede forsøg for den specifikke bruger-ID. Tabellen er sorteret med mest antallet af mislykkede forsøg i faldende rækkefølge.|
|Sidste fejl| Viser tidsstempel, når sidste fejlen opstod.
|Sidste fejl IP-adresse | Viser klient IP-adressen fra den seneste forkert anmodning.|



>[AZURE.NOTE] Denne rapport opdateres automatisk, når hver to timer med de nye oplysninger der indsamles inden for denne periode. Logonforsøg inden for de sidste to timer kan derfor ikke medtages i rapporten.



## <a name="related-links"></a>Relaterede links

* [Azure AD Connect tilstand](active-directory-aadconnect-health.md)
* [Azure AD Connect sundhed Agent-Installation](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect sundhed handlinger](active-directory-aadconnect-health-operations.md)
* [Ved hjælp af Azure AD forbinde tilstand til synkronisering](active-directory-aadconnect-health-sync.md)
* [Ved hjælp af Azure AD forbinde tilstand med AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect sundhed ofte stillede spørgsmål](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect sundhed versionshistorik](active-directory-aadconnect-health-version-history.md)
