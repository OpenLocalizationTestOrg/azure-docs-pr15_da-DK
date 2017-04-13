
<properties
    pageTitle="App krav til Azure RemoteApp | Microsoft Azure"
    description="Få mere at vide om kravene til apps, du vil bruge i Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="app-requirements"></a>Krav til App

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp understøtter streaming 32-bit eller 64-bit Windows-baserede programmer fra et Windows Server 2012 R2 billede. De fleste eksisterende 32-bit eller 64-bit Windows-baserede programmer kører "som det er" i Azure RemoteApp (Remote Desktop Services eller tidligere kendt som Terminal Services) miljø. Men der er forskel mellem kører og kører godt – nogle programmer korrekt og udføre godt, mens andre ikke. Følgende oplysninger indeholder en vejledning til udvikling af programmer i et Remote Desktop Services-miljø og for at sikre kompatibilitet.

Tip: Vi arbejder på at oprette nogle arbejdseksempler til apps for dig. Du får vist nye emner, der diskuterer ved hjælp af Microsoft Access, QuickBooks og App-V i RemoteApp.

## <a name="requirements"></a>Krav
Disse tre krav, hjælpe Hvis fulgt, dit køre godt i RemoteApp-program:

1.  Programmer, der opfylder alle [kravene til certificering for Windows-programmer](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) og overholde [Remote Desktop Services programming retningslinjer](https://msdn.microsoft.com/library/aa383490.aspx) har fuldført kompatibilitet med RemoteApp.
2.  Programmer bør aldrig gemme data lokalt på billedet eller RemoteApp forekomster, der kan gå tabt.  Når du opretter en RemoteApp samling, sammenkædes forekomster og er uden status og bør kun indeholde programmer. Gemme data i en ekstern kilde eller i brugerens profil.
3.  Brugerdefinerede billeder bør aldrig indeholde data, der kan gå tabt.  

## <a name="testing-your-apps"></a>Test dine apps
Brug disse trin for at teste programmer:

1.  Installere Windows Server 2012 R2 og dit program
2.  Aktivere Fjernskrivebord
3.  Opret to brugerkonti, BrugerA og UserB, tilføje begge brugerkonti til sikkerhedsgruppen Fjernskrivebord.
4.  Du kan markere flere sessioner kompatibiliteten ved at oprette to samtidig RDS sessioner til PC under start af programmet.
5.  Validere app funktionsmåde

## <a name="application-development-guidelines"></a>Programmet udvikling retningslinjer
Brug følgende retningslinjer for udviklingsprogrammer for RemoteApp.

### <a name="multiple-users"></a>Flere brugere

- Installere et [program for en enkelt bruger ](https://msdn.microsoft.com/library/aa380661.aspx)kan skabe problemer i et flerbruger miljø.
- Programmer skal [lagre bruger-specifikke oplysninger](https://msdn.microsoft.com/library/aa383452.aspx) i bruger-specifikke placeringer, adskilt fra globale oplysninger, der gælder for alle brugere.
- RemoteApp bruger flere [navneområder for kerneobjekter](https://msdn.microsoft.com/library/aa382954.aspx). en global navneområde bruges primært af tjenester i klient/server-programmer.
- Det er ikke sikkert at forudsætter, at navnet på den computer eller den [IP-adresse](https://msdn.microsoft.com/library/aa382942.aspx) , der er tildelt til computeren er knyttet til en enkelt bruger da flere brugere kan være logget på samtidig en Remote Desktop sessionsværten (RD sessionsværten)-server.

### <a name="performance"></a>Ydeevne
- Deaktivere [grafikeffekter](https://msdn.microsoft.com/library/aa380822.aspx) , før du tilføjer din app til RemoteApp.
- Deaktiver [baggrundsopgaverne](https://msdn.microsoft.com/library/aa380665.aspx) eller oprette effektive baggrundsopgaver, der ikke er ressource-intensivt for at maksimere CPU tilgængelighed for alle brugere.
- Du skal finjustere og saldo programmet [tråd brugen](https://msdn.microsoft.com/library/aa383520.aspx) for et flerbruger, med flere processorer miljø.
- For at optimere ydeevnen, er det god praksis for programmer til at [registrere](https://msdn.microsoft.com/library/aa380798.aspx) , om de kører i en klientsession med.
