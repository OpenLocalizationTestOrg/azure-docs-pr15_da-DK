<properties 
    pageTitle="Oprette og administrere Hybrid forbindelser | Microsoft Azure" 
    description="Lær at oprette en hybrid forbindelse, administrere forbindelsen og installere den hybride Forbindelsesstyring. MABS, WABS" 
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
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Oprette og administrere Hybrid forbindelser


## <a name="overview-of-the-steps"></a>Oversigt over trinnene
1. Oprette en Hybrid forbindelse ved at angive det **værtsnavn** eller **fulde Domænenavn** på den lokale ressource i dit private netværk.
2. Link din Azure Onlines eller Azure-mobilapps til Hybrid forbindelsen.
3. Installere Forbindelsesstyring Hybrid på din lokale ressource og oprette forbindelse til den pågældende Hybrid-forbindelse. Portalen Azure giver et enkelt klik oplevelse for at installere og oprette forbindelse.
4. Administrere Hybrid forbindelser og deres forbindelse taster.

I dette emne beskrives disse trin. 

> [AZURE.IMPORTANT] Det er muligt at angive et slutpunkt Hybrid forbindelse til en IP-adresse. Hvis du bruger en IP-adresse, du kan eller ikke kan når lokal ressource, afhængigt af din kunde. Hybrid forbindelsen, afhænger af klienten at gøre et DNS-opslag. I de fleste tilfælde er __klient__ din programkode. Hvis klienten ikke udføre en DNS-opslag (det ikke forsøger at løse IP-adressen, som om det var et domænenavn (x.x.x.x)), og derefter trafik ikke sendes gennem Hybrid forbindelsen.
>
> For eksempel (pseudocode) definerer du **10.4.5.6** som din lokale vært:
> 
> **I følgende scenario fungerer:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **I følgende scenario virker:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Oprette en Hybrid-forbindelse

En Hybrid forbindelse kan oprettes i portalen Azure bruger Web Apps **eller** ved hjælp af BizTalk-tjenester. 

**Oprette Hybrid forbindelser, der bruger Web Apps**, skal du se [Forbinde Azure Web Apps til en ressource, On-Premises](../app-service-web/web-sites-hybrid-connection-get-started.md). Du kan også installere Hybrid forbindelse Manager (HCM) fra dine WebApp, som er den foretrukne metode. 

**Oprette hybride forbindelser i BizTalk Services**:

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk-tjenester** i den venstre navigationsrude, og vælg derefter BizTalk Service. 

    Hvis du ikke har en eksisterende BizTalk Service, kan du [oprette en BizTalk Service](biztalk-provision-services.md).
3. Klik på fanen **Hybrid forbindelser** :  
![Under fanen hybride forbindelser][HybridConnectionTab]

4. Vælg **Opret en Hybrid forbindelse** , eller klik på knappen **Tilføj** i proceslinjen. Angiv følgende oplysninger:

    Egenskaben | Beskrivelse
--- | ---
Navn | Hybrid forbindelsesnavn skal være entydigt og kan ikke det samme navn som BizTalk Service. Du kan angive et navn, men du kan være specifikke med sit formål. Som eksempler kan nævnes:<br/><br/>Løn*SQL Server*<br/>SupplyList*SharepointServer*<br/>Kunder*OracleServer*
Host Name | Angiv den fulde værtsnavn kun værtsnavn eller IPv4-adressen på den lokale ressource. Som eksempler kan nævnes:<br/><br/>mySQLServer<br/>*mySQLServer*. *Domæne*. corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *yourCompany*.com<br/>10.100.10.10<br/><br/>Hvis du bruger IPv4-adressen, opmærksom på, at din klient eller et program kode ikke kan løse IP-adressen. Se vigtigt bemærkning øverst i dette emne.
Port | Angiv portnummeret på den lokale ressource. Hvis du bruger Web Apps, Angiv eksempelvis port 80 eller port 443. Hvis du bruger SQL Server, skal du angive port 1433.

5. Markér afkrydsningsfeltet for at fuldføre konfigurationen. 

#### <a name="additional"></a>Yderligere

- Du kan oprette flere Hybrid forbindelser. Se den [BizTalk-tjenester: udgaver diagram](biztalk-editions-feature-chart.md) for antallet af forbindelser, der tillades. 
- Hver Hybrid forbindelsen er oprettet med et par af forbindelsesstrenge: programmet nøgler, SEND og On-premises taster, LYTTE. Hvert par har en primær og sekundær nøgle. 


## <a name="LinkWebSite"></a>Sammenkæde din Azure App Service WebApp eller en Mobilapp

For at knytte en Web App eller Mobile-App i Azure App-tjeneste til en eksisterende hybride forbindelse, Vælg **Brug en eksisterende hybride forbindelse** i bladet hybride forbindelser. Se [Access lokale ressourcer ved hjælp af hybrid forbindelser i Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Installere på Hybrid Forbindelsesstyring lokalt

Når en Hybrid forbindelsen er oprettet, kan du installere Forbindelsesstyring Hybrid på den lokale ressource. Det kan hentes fra Azure web-apps eller fra din BizTalk Service. BizTalk Services trin: 

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk-tjenester** i den venstre navigationsrude, og vælg derefter BizTalk Service. 
3. Vælg fanen **Hybrid forbindelser** :  
![Under fanen forbindelser hybrid][HybridConnectionTab]
4. Vælg **Lokalt konfiguration**i proceslinjen:  
![Lokalt installation][HCOnPremSetup]
5. Vælg **Installer og Konfigurer** at køre eller overføre Forbindelsesstyring Hybrid på det lokale system. 
6. Vælg markering til at starte installationen. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Yderligere
- Hybrid Forbindelsesstyring kan være installeret på følgende operativsystemer:

    - Windows Server 2008 R2 (.NET Framework 4.5 + og Windows Management Framework 4.0 + påkrævet)
    - Windows Server 2012 (Windows Management Framework 4.0 + påkrævet)
    - Windows Server 2012 R2


- Når du har installeret Forbindelsesstyring Hybrid, sker der følgende: 

    - Hybrid forbindelsen hostes på Azure er automatisk konfigureret til at bruge den primære programmet forbindelsesstreng. 
    - On-Premises ressourcen er automatisk konfigureret til at bruge primære On-Premises forbindelsesstrengen.

- Forbindelsesstyring Hybrid skal bruge en gyldig lokalt forbindelsesstreng til godkendelse. Azure Web Apps eller Mobile-Apps skal bruge en gyldig ansøgning forbindelsesstreng til godkendelse.
- Du kan skalere Hybrid forbindelser ved at installere en anden forekomst af Hybrid Forbindelsesstyring på en anden server. Konfigurere lokale lytteren at bruge den samme adresse som første lokale lytteren. I så fald er trafikken tilfældigt fordelte (round robin) mellem de aktive lokalt lyttere. 


## <a name="ManageHybridConnection"></a>Administrere Hybrid forbindelser
Hvis du vil administrere dine Hybrid forbindelser, kan du:

- Bruge Azure-portalen, og gå til din BizTalk Service. 
- Brug [REST API'er](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Kopiér/Genopret Hybrid forbindelsesstrenge

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk-tjenester** i den venstre navigationsrude, og vælg derefter BizTalk Service. 
3. Vælg fanen **Hybrid forbindelser** :  
![Under fanen forbindelser hybrid][HybridConnectionTab]
4. Vælg Hybrid forbindelsen. Vælg **Administrer forbindelse**på proceslinjen:  
![Administrere indstillinger][HCManageConnection]

    **Administrere forbindelse** viser programmet og lokal forbindelse strengene. Du kan kopiere forbindelse strengene eller genoprette nøglen Access bruges i forbindelsesstrengen. 

    **Hvis du vælger Genopret**, Access delt nøgle bruges i forbindelsesstrengen ændres. Benyt følgende fremgangsmåde:
    - Vælg **Synkroniser taster** i programmet Azure på Azure klassisk portalen.
    - Kør igen **Lokal installation**. Når du kører igen On-Premises konfigurationen, konfigureres automatisk lokale ressourcen for at bruge den opdaterede primære forbindelsesstreng.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Brug Gruppepolitik til at styre de lokale ressourcer, der bruges af en Hybrid-forbindelse

1. Hente [Hybrid Forbindelsesstyring Administrative skabeloner](http://www.microsoft.com/download/details.aspx?id=42963).
2. Udpak filerne.
3. På den computer, der ændrer Gruppepolitik, skal du gøre følgende:  

    - Kopiér den. ADMX filerne til mappen *%WINROOT%\PolicyDefinitions* .
    - Kopiér den. ADML filerne til mappen *%WINROOT%\PolicyDefinitions\en-us* .

Når kopieret, kan du bruge redigeringsprogrammet til gruppepolitik til at ændre politikken.




## <a name="next"></a>Næste

[Oprette forbindelse Azure Webapps til en lokal ressource](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Oprette forbindelse til lokal SQL Server fra Azure Webapps](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Oversigt over dataforbindelser hybrid](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Se også

[REST-API til administration af BizTalk-tjenester på Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk-tjenester: Udgaver diagram](biztalk-editions-feature-chart.md)  
[Oprette en BizTalk Service ved hjælp af Azure klassisk portal](biztalk-provision-services.md)  
[BizTalk-tjenester: Dashboard, overvåge og skala faner](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
