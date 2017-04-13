<properties
   pageTitle="Generisk SQL Connector trin for trin | Microsoft Azure"
   description="I denne artikel er føre dig gennem et simpelt HR system trinvise ved hjælp af generisk SQL-Connector."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-step-by-step"></a>Generisk SQL Connector trinvise
Dette emne er en trinvis vejledning. Det opretter en simpel HR Northwind og bruge det til at importere nogle brugere og deres gruppemedlemskab.

## <a name="prepare-the-sample-database"></a>Forberede eksempeldatabasen
På en server, der kører SQL Server, ved at køre scriptet SQL findes i [tillæg A](#appendix-a). Dette script opretter en eksempeldatabasen med navnet GSQLDEMO. Objektmodellen for den oprettede database ser ud som dette billede:  
![Objektmodel](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Også oprette en bruger, du vil bruge til at oprette forbindelse til databasen. I denne gennemgang, er brugeren kaldet FABRIKAM\SQLUser og placeret i domænet.

## <a name="create-the-odbc-connection-file"></a>Oprette filen ODBC-forbindelse
Generisk SQL forbindelsen bruger ODBC til at oprette forbindelse til fjernserveren. Først skal vi oprette en fil med ODBC-forbindelsesoplysninger.

1. Start ODBC-administrationsprogram på din server:  
![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Vælg fanen **DSN-fil**. Klik på **Tilføj...**.
![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Out of box driver works fine, så markere det og klikke på **Næste >**.  
![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Giv filen et navn, som **GenericSQL**.  
![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Klik på **Udfør**.  
![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Tid til at konfigurere forbindelsen. Giver en god beskrivelse for datakilden og navnet på den server, der kører SQL Server.  
![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Vælg, hvordan du vil godkende med SQL. I dette tilfælde skal bruge vi Windows-godkendelse.  
![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Angiv navnet på eksempeldatabasen, **GSQLDEMO**.  
![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Hold Alt som standard på dette skærmbillede. Klik på **Udfør**.  
![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. For at bekræfte, at alt fungerer som forventet, skal du klikke på **Test datakilde**.  
![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Sørg for, at testen er fuldført.  
![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. ODBC-konfigurationsfil skulle nu være synlig i DSN-fil.  
![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Vi har nu den fil, vi har brug for, og du kan begynde at oprette forbindelsen.

## <a name="create-the-generic-sql-connector"></a>Oprette generisk SQL-forbindelse

1. Vælg **forbindelser** og **oprette**i UI synkronisering Service Manager. Vælg **Generisk SQL (Microsoft)** , og give den et beskrivende navn.  
![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Find den DSN-fil, du oprettede i forrige afsnit, og Overfør det til serveren. Angive legitimationsoplysninger for at oprette forbindelse til databasen.  
![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. I denne gennemgang vi gør det nemt for os og sige, at der er to objekttyper, **brugere** og **grupper**.
![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. For at finde attributterne, vil vi forbindelsen til at registrere disse attributter ved at kigge på selve tabellen. Da **brugere** er et reserveret ord i SQL, har vi brug at angive det i kantede parenteser [].  
![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Tid til at definere attributten anker og attributten DN. For **brugere**bruge vi en kombination af to attributter brugernavnet og medarbejder-id. For **gruppen**skal vi bruge gruppenavn (ikke realistisk i virkelige liv, men denne gennemgang, fungerer det).
![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Ikke alle attribut datatyper kan findes i en SQL-database. Attributten referencetypen ikke særlig. Objekttype gruppe skal vi ændre OwnerID og MemberID refereres til.  
![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. De attributter, vi markeret som referenceattributter i det forrige trin Brug objekttypen disse værdier er en reference til. I dette tilfælde bruger objekttype.  
![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Vælg **vandmærke** som delta strategi på siden globale parametre. Også skrive i dato/klokkeslæt-format **åååå-MM-dd HH:mm:ss**.
![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Vælg begge objekttyper på siden **Konfigurer partitioner og hierarkier** .
![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. På siden **Vælg objekttyper** og **Vælge attributter**skal du markere både objekttyper og alle attributter. Klik på **Udfør**på siden **Konfigurer ankre** .

## <a name="create-run-profiles"></a>Oprette køre profiler

1. Vælg **forbindelser**, og **Konfigurere køre profiler**i UI synkronisering Service Manager. Klik på **ny profil**. Vi begynder med **Fuld Import**.  
![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Vælg typen **Fuldstændig Import (kun fase)**.  
![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Vælg partitionen **objekt = bruger**.  
![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Vælg **tabel** , og skriv **[USERS]**. Rul ned til sektionen med flere valgmuligheder objekt type, og Angiv dataene, som i det følgende billede. Klik på **færdig med** at gemme trinnet.
![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png)  
![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Vælg **nyt trin**. Denne gang, Vælg **objekt = gruppe**. Brug af konfiguration som i det følgende billede på den sidste side. Klik på **Udfør**.  
![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)  
![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Valgfrit: Hvis du vil, kan du konfigurere yderligere køre profiler. I denne gennemgang skal bruges kun fuld importen.
7. Klik på **OK** for at afslutte redigeringen af køre profiler.

## <a name="add-some-test-data-and-test-the-import"></a>Tilføje nogle testdata og teste importen
Udfyld nogle test af dataene i din eksempeldatabasen. Når du er klar, Vælg **Kør** og **Fuldstændig import**.

Her er en bruger med to telefonnumre og en gruppe med nogle medlemmer.  
![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png)  
![CS2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)  

## <a name="appendix-a"></a>Tillæg A
**SQL-script til at oprette eksempeldatabasen**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
