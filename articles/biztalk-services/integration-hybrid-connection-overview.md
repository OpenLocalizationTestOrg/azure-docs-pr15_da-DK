<properties
    pageTitle="Oversigt over dataforbindelser hybrid | Microsoft Azure"
    description="Få mere at vide om Hybrid forbindelser, sikkerhed, TCP-porte og understøttede konfigurationer. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Oversigt over dataforbindelser hybrid
Introduktion til Hybrid forbindelser, viser en liste over de understøttede konfigurationer og viser de nødvendige TCP-porte.


## <a name="what-is-a-hybrid-connection"></a>Hvad er en hybrid forbindelse

Hybrid forbindelser er en funktion af Azure BizTalk-tjenester. Hybrid forbindelser giver en nem og nem måde at forbinde funktionen Web Apps i Azure App Service (tidligere websteder) og funktionen Mobile-Apps i Azure App Service (tidligere Mobile Services) til lokale ressourcer bag firewallen.

![Hybrid forbindelser][HCImage]

Hybrid forbindelser fordele omfatter:

- Webapps og Mobile-Apps kan få adgang til eksisterende lokale data og -tjenester sikkert.
- Flere Web Apps eller Mobile-Apps kan dele en Hybrid forbindelse for at få adgang til en lokal ressource.
- Minimale TCP-portnumre kræves for at få adgang til netværket.
- Programmer, der bruger Hybrid forbindelser få adgang til kun den bestemte lokal ressource, der er blevet publiceret via Hybrid forbindelse.
- Kan oprette forbindelse til en lokal ressource, der bruger en statisk TCP-port, såsom SQL Server, MySQL, HTTP Web API'er og de fleste brugerdefineret Web-tjenester.

    > [AZURE.NOTE] TCP-baserede tjenester, der bruger dynamiske porte (såsom FTP-passiv tilstand eller udvidet passiv tilstand) understøttes ikke i øjeblikket. LDAP også understøttes ikke. LDAP bruger en statisk TCP-port, men det kan også bruge UDP. Som et resultat, er det ikke understøttet.

- Kan bruges med alle strukturer, der understøttes af Web Apps (.NET PHP Java, Python, og Node.js) og Mobile-Apps (Node.js, .NET).
- Webapps og Mobile-Apps kan få adgang til lokale ressourcer på samme måde som hvis internettet eller Mobile-App er placeret på dit lokale netværk. De samme forbindelse streng, der bruges i det lokale miljø kan også bruges på Azure.


Hybrid forbindelser giver også virksomhedsadministratorer med CTRL og indsigt i virksomhedsressourcer af hybrid programmer, herunder:

- Ved hjælp af indstillinger for Gruppepolitik, administratorer kan tillade Hybrid forbindelser på netværket og også angive ressourcer, der kan åbnes af hybrid-programmer.
- Begivenhed og Overvåg logge på virksomhedens netværk giver indsigt i de ressourcer, der åbnes af Hybrid forbindelser.


## <a name="example-scenarios"></a>Eksempelscenarier

Hybrid forbindelser understøtter følgende kombinationer af framework og programmet:

- .NET framework adgang til SQL Server
- .NET framework adgang til HTTP/HTTPS-tjenester med WebClient
- PHP adgang til SQL Server, MySQL
- Java adgang til SQL Server, MySQL og Oracle
- Java adgang til HTTP/HTTPS-tjenester

Når du bruger Hybrid forbindelser til at få adgang til lokale SQL Server, skal du overveje følgende:

- SQL Express navngivne forekomster skal være konfigureret til at bruge statisk porte. Som standard navngivne SQL Express forekomster Brug dynamiske porte.
- SQL Express standard forekomster bruge en statisk port, men TCP skal være aktiveret. TCP er ikke aktiveret som standard.
- Når du bruger Clustering tilgængelighed grupper på `MultiSubnetFailover=true` tilstand understøttes ikke i øjeblikket.
- Den `ApplicationIntent=ReadOnly` understøttes ikke i øjeblikket.
- SQL-godkendelse kan det være nødvendigt som metoden til slut godkendelse, der understøttes af programmet Azure og lokale SQL server.


## <a name="security-and-ports"></a>Sikkerhed og porte

Hybrid forbindelser bruger delt Access signatur (SAS) tilladelse til at sikre forbindelser fra Azure-programmer og den lokale Hybrid Forbindelsesstyring til Hybrid forbindelsen. Separat forbindelse nøgler er oprettet for programmet og den lokale hybride Forbindelsesstyring. Disse taster på forbindelse kan rullet og tilbagekaldt uafhængigt af hinanden.

Hybrid forbindelser giver for problemfri og sikker fordelingen af de pågældende taster programmerne, og den lokale Hybrid Forbindelsesstyring.

Se [oprette og administrere Hybrid forbindelser](integration-hybrid-connection-create-manage.md).

*Programmet godkendelse er adskilt fra Hybrid forbindelsen*. En relevant autorisation metode kan bruges. Metoden godkendelse afhænger af metoderne til slut godkendelse understøttes på tværs af Azure skyen og komponenterne i det lokale miljø. For eksempel Azure programmet giver adgang til en lokal SQL Server. I dette scenarie skal muligvis SQL godkendelse den godkendelsesmetode, der understøttes til slut.

#### <a name="tcp-ports"></a>TCP-porte
Hybrid forbindelser kræver kun udgående TCP- eller HTTP-forbindelse fra dit private netværk. Du behøver ikke at åbne en hvilken som helst Firewallportene eller ændre din netværkskonfiguration kanten for at tillade en indgående forbindelse til netværket.

Følgende TCP-porte anvendes af Hybrid forbindelser:

Port | Hvorfor du skal bruge dem.
--- | ---
9350 - 9354 | Disse porte bruges til overførsel af data. Service Bus relay manager sonder port 9350 til at afgøre, om TCP connectivity er tilgængelig. Hvis det er tilgængeligt, derefter antages det, at port 9352 også er tilgængelige. Datatrafik handler om port 9352. <br/><br/>Tillad udgående forbindelser til disse porte.
5671 | Når port 9352 bruges til datatrafik, bruges port 5671 som salgskanalen kontrolelement. <br/><br/>Tillad udgående forbindelser til denne port.
80, 443 | Disse porte bruges til nogle data anmodninger om til Azure. Hvis port 9352 og 5671 ikke er brugbare, er *derefter* port 80 og 443 også fallback porte, der bruges til overførsel af data og salgskanalen kontrolelement.<br/><br/>Tillad udgående forbindelser til disse porte. <br/><br/>**Bemærk!** Det anbefales ikke at bruge dem som de fallback porte i stedet for de andre TCP-porte. HTTP/WebSocket bruges som protokollen i stedet for oprindelige TCP til data kanaler. Det kan medføre lavere ydeevne.



## <a name="next-steps"></a>Næste trin

[Oprette og administrere Hybrid forbindelser](integration-hybrid-connection-create-manage.md)<br/>
[Oprette forbindelse Azure Webapps til en lokal ressource](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Oprette forbindelse til lokal SQL Server fra en Azure-WebApp](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Se også

[REST-API til administration af BizTalk-tjenester på Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[BizTalk-tjenester: udgaver diagram](biztalk-editions-feature-chart.md)<br/>
[Oprette en BizTalk Service Azure portalen](biztalk-provision-services.md)<br/>
[BizTalk-tjenester: Dashboard, overvåge og skala faner](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
