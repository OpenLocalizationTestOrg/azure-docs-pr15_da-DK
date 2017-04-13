<properties
   pageTitle="Hente data fra Azure Analysis Services | Microsoft Azure"
   description="Lær at oprette forbindelse til og hente data fra en Analysis Services-server i Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="get-data-from-azure-analysis-services"></a>Hente data fra Azure Analysis Services
Når du har oprettet en server i Azure, og brug en tabelmodel, er brugerne i organisationen klar til at oprette forbindelse og begynde at udforske data.

Azure Analysis Services understøtter klientforbindelser ved hjælp af [opdateret objektmodeller](#client-libraries); TOM, AMO, Adomd.Net eller MSOLAP, til at oprette forbindelse via xmla til serveren. For eksempel Power BI, Power BI Desktop, Excel eller en tredjepart klientprogrammet, der understøtter objektmodeller.

## <a name="server-name"></a>Servernavn
Når du opretter en Analysis Services-server i Azure, kan du angive et entydigt navn og det område, hvor serveren er der skal oprettes. Når du angiver navnet på den server i en forbindelse, er den server naming farveskema:
```
<protocol>://<region>/<servername>
```
 Hvor protocol er streng **asazure**, området har Uri for det område, hvor serveren, der blev oprettet (for eksempel for Vest USA westus.asazure.windows.net) og servernavn er navnet på din entydige server i området.

## <a name="get-the-server-name"></a>Finde navnet på serveren
Før du opretter forbindelse, skal du finde navnet på serveren. **Azure** -portalen > server > **Oversigt over** > **servernavn**, kopiere og indsætte hele servernavnet. Hvis andre brugere i organisationen opretter forbindelse til denne server for, får du vil dele med dem, denne servernavn. Når du angiver et servernavn, skal hele stien bruges.

![Få servernavn i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connect-in-power-bi-desktop"></a>Oprette forbindelse i Power BI Desktop

> [AZURE.NOTE] Denne funktion er Preview.

1. Klik på **Hent Data**i [Power BI Desktop](https://powerbi.microsoft.com/desktop/), > **databaser** > **Azure Analysis Services**.

2. Indsæt servernavnet fra Udklipsholder i **Server**.

3. I **databasen**, hvis du kender navnet på den tabelmodel database eller perspektiv, du vil oprette forbindelse til, indsætte det her. Ellers kan du lade feltet være tomt. Du kan vælge en database eller perspektiv på det næste skærmbillede.

4. Forlad standardindstillingen **forbinde live** valgt, og derefter trykke på **Opret forbindelse**. Hvis du bliver bedt om at angive en konto skal du angive din virksomhedskonto.

5. Udvid serveren, i **Navigator**, Vælg model eller perspektiv, du vil oprette forbindelse til, derefter klikke på **Opret forbindelse**. Et enkelt klik på en model eller perspektiv viser alle objekter for den pågældende visning.


## <a name="connect-in-power-bi"></a>Oprette forbindelse i Power BI
1. Oprette en Power BI Desktop-fil, der har en direkte forbindelse til din model på din server.

2. Klik på **Hent Data**i [Power BI](https://powerbi.microsoft.com), > **filer**. Find og vælg filen.


## <a name="connect-in-excel"></a>Oprette forbindelse i Excel
Oprette forbindelse til Azure Analysis Services-serveren i Excel understøttes ved hjælp af Hent Data i Excel 2016 eller Power-forespørgsel i tidligere versioner. [MSOLAP.7 udbyder](https://aka.ms/msolap) er påkrævet. Oprette forbindelse ved hjælp af guiden Importér tabel i Power Pivot understøttes ikke.

1. Klik på **Hent eksterne Data**i Excel 2016, under fanen **Data** , > **Fra andre kilder** > **Fra Analysis Services**.

2. I guiden Dataforbindelse, i **servernavn**skal du indsætte servernavnet fra Udklipsholder. Vælg derefter i **logonoplysninger**, skal **bruge følgende brugernavn og adgangskode**, og skriv derefter den organisatoriske brugernavn, for eksempel nancy@adventureworks.com, og din adgangskode.

    ![Oprette forbindelse i Excel-logon](./media/analysis-services-connect/aas-connect-excel-logon.png)

4. Vælg database og model eller perspektiv i **Vælg Database og tabel**, og klik derefter på **Udfør**.

    ![Oprette forbindelse i Excel Vælg model](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Forbindelsesstreng
Når du opretter forbindelse til Azure Analysis Services ved hjælp af tabelformat objektmodellen, Brug følgende forbindelsesstreng-formater:

###### <a name="integrated-azure-active-directory-authentication"></a>Integreret Azure Active Directory-godkendelse
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
Integreret godkendelse vil løfte Azure Active Directory legitimationsoplysninger cachen, hvis det er muligt. Hvis ikke, vises vinduet Azure logon.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory-godkendelse med brugernavn og din adgangskode
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>Klientbiblioteker
Når du opretter forbindelse til Azure Analysis Services fra Excel eller andre grænseflader som TOM, AsCmd, ADOMD.NET, kan du muligvis installere de seneste udbyder klientbiblioteker. Få de seneste nyheder:  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>



## <a name="next-steps"></a>Næste trin
[Administrere din server](analysis-services-manage.md)
