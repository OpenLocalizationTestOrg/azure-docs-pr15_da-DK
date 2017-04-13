<properties
pageTitle="Bruge data fra en lokal SQL Server-database i Machine Learning | Azure"
description="Bruge data fra en lokal SQL Server-database til at udføre avanceret analyse med Azure Machine Learning."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Udføre avancerede analyser med Azure Machine Learning ved hjælp af data fra en lokal SQL Server-database


Ofte virksomheder, der arbejder med lokale data vil gerne Udnyt skalaen og fleksibilitet af skyen til deres computer læ arbejdsmængder. Men ønsker ikke mulighed at afbryde deres aktuelle forretningsprocesser og arbejdsprocesser ved at flytte deres lokale data til skyen. Azure Machine Learning understøtter nu læse dine data fra en lokal SQL Server-database og derefter kurser og vundne en model med disse data. Du skal ikke længere manuelt kopiere og synkronisere data mellem i skyen og lokal server. I stedet kan modulet **Importere Data** i Azure Machine Learning Studio nu læse direkte fra din lokale SQL Server-database for din undervisning og vundne job. 

Denne artikel indeholder en oversigt over, hvordan du vandindtrængen lokal SQL server-data til Azure Machine Learning. Det antages, at du allerede kender, Azure Machine Learning begreber som *arbejdsområder, moduler, datasæt, forsøg osv*...

> [AZURE.NOTE] Denne funktion er ikke tilgængelig for gratis arbejdsområder. Du kan finde flere oplysninger om Machine Learning se priser og lag, [Azure Machine Learning priser](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Installere Microsoft Data Management Gateway

For at få adgang til en lokal SQL Server-database i Azure Machine Learning skal du downloade og installere Microsoft Data Management Gateway. Når du konfigurerer gateway forbindelsen i Machine Learning Studio har du mulighed for at hente og installere gatewayen ved hjælp af dialogboksen **Hent og Registrer datastyringsgateway** , som beskrevet nedenfor.

Du kan også installere Datastyringsgateway forvejen ved at downloade og køre MSI-installationspakke fra [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Vælg den nyeste version, vælge 32-bit eller 64-bit efter behov for din computer. MSI-filen kan også bruges til at opgradere en eksisterende Datastyringsgateway til den nyeste version, med alle indstillinger bevares.

Gatewayen har følgende forudsætninger:

- Understøttede Windows-operativsystemversioner er Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 og Windows Server 2012 R2.
- Anbefalet konfiguration for gatewaycomputeren er mindst 2 GHz, 4 kerner, 8 GB RAM og 80 GB disk.
- Hvis værtsmaskinen går i dvale, kunne ikke gatewayen svare på anmodninger om data. Derfor konfigurere en relevante strømstyringsplan på computeren, før du installerer gatewayen. Gateway-installationen vises en meddelelse, hvis computeren er konfigureret til at dvale.
- Fordi kopi aktivitet optræder på en bestemt frekvens, følger ressourceforbrug (CPU, hukommelse) på maskinen også det samme mønster med spidsbelastning og inaktiv klokkeslæt. Ressource anvendelsen afhænger også stærkt mængden data, der flyttes. Når flere kopi job er i gang skal du se Ressourceforbrug gå op under spidsbelastning. Mens den mindste konfiguration, der er nævnt ovenfor er teknisk tilstrækkelige, kan du vil have en konfiguration med flere ressourcer end den mindste konfiguration afhængigt af din specifikke indlæsning af flytning af data.

Du bør overveje følgende, når du konfigurerer og bruger en Datastyringsgateway:

- Du kan installere kun én forekomst af Datastyringsgateway på en enkelt computer.
- Du kan bruge en enkelt gateway til flere lokale datakilder.
- Du kan oprette forbindelse flere gateways på forskellige computere til den samme lokale datakilde.
- Du kan konfigurere en gateway for kun én arbejdsområde ad gangen. Gateways kan ikke deles på tværs af arbejdsområder på nuværende tidspunkt.
- Du kan konfigurere flere gateways for et enkelt arbejdsområde. Du vil muligvis bruge en gateway, der er tilsluttet din test datakilder under udvikling og en fremstilling gatewayen, når du er klar til at giver muligheder.
- Gatewayen behøver ikke at være på den samme computer som datakilde, men holde tættere på datakilden reducerer tid for gateway til at oprette forbindelse til datakilden. Vi anbefaler, at du installerer gatewayen på en computer, der er anderledes end den, der er vært den lokale datakilde, så gateway og datakilden ikke konkurrere for ressourcer.
- Hvis du allerede har en gateway, der er installeret på computeren fungerer Power BI eller Azure Data Factory scenarier, kan du installere en separat gateway til Azure Machine Learning på en anden computer. 

    > [AZURE.NOTE] Du kan ikke køre Datastyringsgateway og Power BI gatewayen på den samme computer.

- Du skal bruge Datastyringsgateway til Azure Machine Learning, selvom du bruger Azure ExpressRoute til andre data. Skal du behandle datakilden som en lokal datakilde (som er bag en firewall) selv når du bruger ExpressRoute, og brug af Datastyringsgateway til at oprette forbindelse mellem maskine læring og datakilden. 

Du kan finde detaljerede oplysninger om forudsætninger for installation, installationstrin og tip til fejlfinding i artiklen, [flytte data mellem lokale datakilder og skyen med Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), begynder med sektionen [Overvejelser ved brug af Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Vandindtrængen data fra din lokale SQL Server-database til Azure Machine Learning


I denne gennemgang, vil du konfigurere en Datastyringsgateway i et Azure Machine Learning arbejdsområde, konfigurerer den og derefter læse data fra en lokal SQL Server-database.

> [AZURE.TIP] Før du starter, skal du deaktivere browserens pop op-blokering for `studio.azureml.net`. Hvis du bruger Google Chrome-browseren, skal du hente og installere en af de flere plug-ins, der er tilgængelige på Google Chrome webbutik [Skal du klikke på én gang lokalnummer App](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Trin 1: Oprette en gateway

Det første trin er at oprette og konfigurere gatewayen adgang til din lokale SQL-database.

1. Log på [Azure Machine Learning Studio](https://studio.azureml.net/Home/) , og vælg det arbejdsområde, som du vil arbejde.

2. Klik på bladet **Indstillinger** i venstre side, og klik derefter på fanen **DATA GATEWAYS** øverst.

3. Klik på **Ny DATASTYRINGSGATEWAY** nederst på skærmen.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. Angiv **Gatewaynavn** og mulighed for at tilføje en **Beskrivelse**i dialogboksen **Ny datastyringsgateway** . Klik på pilen i det nederste højre hjørne for at gå til næste trin i konfigurationen.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. I Download og Registrer gateway dialogboksen data, du kopiere den GATEWAY REGISTRERINGSNØGLE til Udklipsholder.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Hvis du har endnu ikke er hentet og installeret Microsoft Data Management Gateway, klik derefter på **Hent datastyringsgateway**. Du føres til Microsoft Download Center, hvor du kan vælge den gatewayversion, du skal bruge, kan du hente det og installere den. Du kan finde detaljerede oplysninger om forudsætninger for installation, installationstrin og tip til fejlfinding i sektionerne starten af artiklen [flytte data mellem lokale datakilder og skyen med Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Når gatewayen er installeret, Data Management Gateway Configuration Manager åbnes, og dialogboksen **registrere gatewayen** vises. Indsæt **Gateway registreringsnøgle** , du kopierede til Udklipsholder, og klik på **Registrer**.

8. Hvis du allerede har en gateway, der er installeret, kører Data Management Gateway Konfigurationsstyring, skal du klikke på **Skift-tasten**, indsætte  **Gateway registreringsnøgle** , du kopierede til Udklipsholder og klikke på **OK**.

9. Når installationen er fuldført, vises dialogboksen **registrere gatewayen** til Microsoft Data Management Gateway Configuration Manager. Indsæt NØGLEN GATEWAY registrering, som du kopierede til udklipsholderen ovenfor, og klik på **Registrer**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. Gateway-konfigurationen er fuldført, når følgende værdier er angivet under fanen **Startside** i Microsoft Data Management Gateway Configuration Manager:

    - **Gateway-navn** og **navnet på forekomsten** er angivet til navnet på gatewayen.

    - **Registrering** er indstillet til **registreret**.

    - **Status** er angivet til **startet**.

    - På statuslinjen nederst viser **prøver at oprette forbindelse til Data Management Gateway skybaseret tjeneste** sammen med et grønt flueben.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure Machine Learning Studio bliver også opdateret, når registreringen er gået igennem.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. I dialogboksen **Hent og registrere datastyringsgateway** , skal du markere afkrydsningsfeltet for at fuldføre konfigurationen. Siden **Indstillinger for** viser gatewaystatus som "Online". I ruden til højre finder du status og andre nyttige oplysninger.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. I Konfigurationsstyring til Microsoft Data Management Gateway Skift til fanen **certifikat** . Det certifikat, der er angivet under denne fane bruges til at kryptere/dekryptere legitimationsoplysninger for det lokale datalager, som du angiver i portalen. Dette er den standardcertifikat, der er oprettet. Microsoft anbefaler at ændre det til dit eget certifikat, som du sikkerhedskopierer i systemet certifikat administration. Klik på **Skift** for at bruge dit eget certifikat i stedet.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (valgfrit) Hvis du vil aktivere detaljeret logføring for at foretage fejlfinding af problemer med gateway, i Microsoft Data Management Gateway Konfigurationsstyring skifte til fanen **diagnosticering** og markere indstillingen **Aktiver logføring for fejlfinding** . Oplysninger om logføring kan findes i Windows Logbog under **tjenester logfiler for programmer og**  - &gt; **Datastyringsgateway** node. Du kan også bruge fanen **diagnosticering** til at teste forbindelsen til en lokal datakilde ved hjælp af gatewayen.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Dette er fuldført gatewayen konfigurere proces i Azure Machine Learning.
Du er nu klar til at bruge dine lokale data.

Du kan oprette og konfigurere flere gateways i Studio for de enkelte arbejdsområder. Du kan for eksempel har en gateway, du vil oprette forbindelse til din test datakilder under udvikling, og en anden gateway til din fremstilling datakilder. Azure Machine Learning giver dig mulighed for at konfigurere flere gateways, afhængigt af din virksomhedsmiljø. Du kan ikke dele en gateway mellem arbejdsområder i øjeblikket, og kun én gateway kan være installeret på en enkelt computer. Se [Overvejelser ved brug af Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) i artiklen, [flytte data mellem lokale datakilder og skyen med Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)til flere overvejelser ved installation af gatewayen.

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Trin 2: Brug gatewayen til at læse data fra en lokal datakilde

Når du konfigurerer gatewayen, kan du tilføje et modul **Importere Data** til forsøg, der indlæser dataene fra lokal SQL Server-databasen.

1.  I Machine Learning Studio, Vælg fanen **forsøg** , skal du klikke på **+ Ny** i det nederste venstre hjørne, og vælg **Tom forsøg** (eller Vælg en af flere eksempel forsøg tilgængelig).

2.  Find, og træk modulet, **Importere Data** til forsøg lærred.

3.  Klik på **Gem som** under lærredet. Angiv "Azure Machine Learning lokal SQL Server selvstudium" for forsøg navnet, Vælg arbejdsområdet, og klik på **OK** markeringen i afkrydsningsfeltet.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Klik på modulet **Importdata** for at markere den og derefter vælge "Lokal SQL-Database" i ruden **Egenskaber** til højre for lærredet på rullelisten **datakilde** .

5.  Vælg **datastyringsgateway** du har installeret og registreret. Du kan konfigurere en anden gateway ved at vælge "(Tilføj nye Datastyringsgateway...)".

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Angive SQL **Database-servernavnet** og **Databasenavn**, sammen med SQL- **databaseforespørgsel** du vil udføre.

7.  Klik på **Enter værdier** under **brugernavn og din adgangskode** , og Angiv dine databaselegitimationsoplysninger. Du kan bruge Windows-integreret godkendelse eller SQL Server-godkendelse afhængigt af hvordan din lokale SQL Server er konfigureret.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    Meddelelsen "værdier, der kræves", ændres til "værdier sæt" med et grønt flueben. Du skal blot angive legitimationsoplysningerne én gang, medmindre oplysningerne om database eller adgangskode ændres. Azure Machine Learning bruger det certifikat, du angav, da du installerede gatewayen til at kryptere legitimationsoplysninger i skyen. Azure lagrer aldrig lokale legitimationsoplysninger uden kryptering.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Klik på **Kør** for at køre forsøget.

Når forsøget afsluttes kan kører du få vist dataene, du har importeret fra databasen ved at klikke på outputporten i modulet **Importdata** og vælge **visuelle effekter**.

Når du er færdig med at udvikle din forsøg, kan du installere og giver muligheder for din model. Ved hjælp af batchen udførelse af tjenesten, vil data fra den lokale SQL Server-database, der er konfigureret i modulet **Importdata** blive læst og bruges til pointmodel for kundeemner. Du kan bruge tjenesten anmode om svar resultatværdier lokale data, Microsoft anbefaler at bruge [Excel-tilføjelsesprogram](machine-learning-excel-add-in-for-web-services.md) i stedet. I øjeblikket, understøttes skrivning til en lokal SQL Server-database via **Eksportér Data** ikke i din forsøg eller publicerede webtjenester.

