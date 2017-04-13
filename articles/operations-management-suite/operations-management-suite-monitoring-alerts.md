<properties 
   pageTitle="Giv besked management i Microsoft overvågning produkter | Microsoft Azure"
   description="En advarsel angiver et problem, der kræver handling fra en administrator.  I denne artikel beskrives forskellene i hvordan beskeder oprettes og administreres i System Center Operations Manager (SCOM) og Log analyser og viser de bedste fremgangsmåder i udnytte de to produkter til en strategi for beskeder om administration af hybrid." 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Administrere beskeder med Microsoft overvågning 

En advarsel angiver et problem, der kræver handling fra en administrator.  Der er klare forskelle mellem System Center Operations Manager (SCOM) og Log Analytics i handlinger Management pakke (OMS) med hensyn til oprettelse af vigtige beskeder om, hvordan de administreres og analysere, og hvordan du får besked, der blev fundet et problem, der er vigtige.

## <a name="alerts-in-operations-manager"></a>Beskeder i Operations Manager
Beskeder i SCOM genereres af individuelle regler eller skærme til at angive et bestemt problem.  En skærm kan oprette en besked, når den indsætter en fejltilstand, mens en regel kan oprette en besked for at angive nogle vigtige problem, der ikke er er direkte relateret til tilstanden for et administreret objekt.  Management packs indeholder en række forskellige arbejdsgange, oprette beskeder for programmet eller tjenesten, de styrer.  En del af processen med at konfigurere en ny management pack justering af den for at sikre, at du ikke modtager for meget beskeder for problemer, du ikke kan huske kritiske.

![SCOM besked visning](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM giver fuldført beskeder om management beskeder, der har en status, som kan ændres af administratorer, som de arbejder på at løse problemet.  Når problemet er løst, angiver administratoren påmindelsen for at lukket for, hvornår den ikke længere vises i visninger, der viser aktive beskeder.  Beskeder, der er dannet ud fra skærme kan løses automatisk, når skærmen skifter til en sund tilstand.

![Beskeder om status](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Beskeder i Log Analytics
En besked i Log Analytics er oprettet ud fra en log forespørgsel, der kører automatisk med jævne mellemrum.  Du kan oprette en besked om fra en log forespørgsel.  Hvis forespørgslen returnerer resultater, der opfylder de kriterier, du angiver, oprettes der en besked.  Det kan skyldes en bestemt forespørgsel, der opretter en besked, hvis en bestemt hændelse allerede findes, eller du kan bruge en mere generel forespørgsel, der søger efter en hvilken som helst fejlhændelse, der er relateret til et bestemt program.

Log Analytics-beskeder er skrevet til OMS lager som en hændelse og kan hentes med en log forespørgsel.  De har ikke en status som SCOM begivenheder, så du kan angive, når problemet er løst.

![OMS besked](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Når SCOM bruges som datakilde til Log Analytics, skrives SCOM beskeder til OMS lager, som de oprettes og ændres.  

![SCOM besked](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[Beskeder om Management-løsning](http://technet.microsoft.com/library/mt484092.aspx) indeholder en oversigt over aktive beskeder og flere almindelige forespørgsler til at hente forskellige sæt beskeder.  Dette giver dig mere effektiv analyse af dine beskeder end en rapport i SCOM.  Du kan fokusere på fra opsummeringer til detaljerede data og oprette ad hoc-forespørgsler for at hente forskellige typer beskeder.

![Beskeder om management-løsning](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Beskeder
Beskeder i SCOM sender dig en mail eller tekst i svar til beskeder, der opfylder bestemte kriterier.  Du kan oprette forskellige meddelelsesabonnementer, der har forskellige personer besked, afhængigt af disse kriterier som det objekt, der skal overvåges, alvorlighed af beskeden, slags problem, der findes, eller tidspunktet på dagen.

Få abonnementer kan bruges til at implementere en fuldstændig anmeldelse strategi for et stort antal management packs.

![SCOM beskeder](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Log Analytics kan give dig besked via mail, en besked er blevet oprettet ved at angive en e-mail-meddelelse handling på hver [reglen](http://technet.microsoft.com/library/mt614775.aspx).  Det har ikke mulighed for SCOM Abonner på mange beskeder med en enkelt regel.  Du skal også oprette dine egne regler for påmindelser, da OMS ikke giver nogen forudkonfigureret.

![Log Analytics-beskeder](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Du kan ikke helt administrere SCOM beskeder i Log Analytics via, da du kan kun redigere dem i konsollen handlinger.  Log Analytics er nyttig som en del af en besked om management behandle via til at levere analyseværktøjerne pågældende alene SCOM ikke har.

## <a name="alert-remediation"></a>Beskeder om afhjælpning
[Afhjælpning](http://technet.microsoft.com/library/mt614775.aspx) refererer til et forsøg på at løse det problem, identificeret med en påmindelse automatisk.
  
SCOM giver dig mulighed at køre diagnosticering og regresbeløb som svar på en skærm, at angive en beskadiget tilstand.  Dette sker samtidig til den skærm, oprette beskeden.  Diagnosticerings- og regresbeløb implementeres typisk som et script, der kører på agenten.  En diagnostic forsøger at indsamle flere oplysninger om fundet problemet, mens en genoprettelse forsøger at løse problemet.

Log Analytics giver dig mulighed at starte en [Azure automatisering runbook](https://azure.microsoft.com/documentation/services/automation/) eller ringe til en webhook som svar på en Log Analytics-besked.  Runbooks kan indeholde kompleks logik, der er implementeret i PowerShell.  Scriptet kører i Azure og har adgang til en hvilken som helst Azure ressourcer eller eksterne ressourcer, der er tilgængelige fra skyen.  Azure automatisering har mulighed for at udføre runbooks på en server i dit lokale datacenter, men denne funktion er ikke tilgængelige, når du starter runbook svar Log Analytics-beskeder.

Både tilbagebetalinger i SCOM og runbooks i OMS kan indeholde PowerShell-scripts, men genfindes sværere at oprette og administrere, fordi de skal findes i en management pack.  Runbooks er gemt i Azure automatisering, som indeholder funktioner til redigering, test og administration af runbooks.

Hvis du bruger SCOM som datakilde til Log Analytics, kan du oprette en logfil Analytics besked ved hjælp af en log forespørgsel til at hente SCOM beskeder, der er gemt i OMS-lager.  Dette giver dig tilladelse til at køre en Azure automatisering runbook som svar på en SCOM besked.  Da runbook kører i Azure, vil dette naturligvis ikke være en interessant strategi for tilbagebetalinger af lokale problemer.

## <a name="next-steps"></a>Næste trin

- Se detaljerne for [beskeder i System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).