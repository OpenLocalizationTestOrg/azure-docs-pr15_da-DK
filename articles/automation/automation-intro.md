<properties
    pageTitle="Hvad er Azure automatisering | Microsoft Azure"
    description="Se, hvilken værdi, der indeholder Azure automatisering og få svar på ofte stillede spørgsmål, så du kan komme i gang med at oprette, ved hjælp af runbooks og Azure automatisering DTK."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="Hvad er automatisering, azure automatisering, azure automatisering eksempler"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Azure automatisering oversigt

Microsoft Azure automatisering gør muligt for brugerne at automatisere de manuelle, køres lange, fejlbehæftede og ofte gentagne opgaver, der udføres ofte i skyen og enterprise-miljø. Det sparer tid og øger pålideligheden af almindelige administrative opgaver og planlægges endda dem udføres automatisk med jævne mellemrum. Du kan automatisere processer ved hjælp af runbooks eller automatisere Konfigurationsstyring ved hjælp af beskedteksten tilstand konfiguration. I denne artikel indeholder kort oversigt over Azure automatisering og svar på nogle almindelige spørgsmål. Du kan referere til andre artikler i dette bibliotek finde mere detaljerede oplysninger om de forskellige emner.


## <a name="automating-processes-with-runbooks"></a>Automatisere forretningsprocesser med runbooks

En runbook er et sæt af opgaver, som udfører nogle automatiske proces i Azure Automation. Det kan være en enkel proces som starte en virtuel maskine og oprette en logfil, eller du skal muligvis en kompleks runbook, der kombinerer andre mindre runbooks for at udføre en kompleks proces på tværs af flere ressourcer eller endda flere skyer og på lokale miljøer.  

For eksempel du muligvis har en eksisterende manuel proces for afkorter en SQL-database, hvis den nærmer maksimumstørrelse, der indeholder flere trin, som opretter forbindelse til serveren, oprette forbindelse til databasen, få den aktuelle størrelse af databasen, kontrollere Hvis har overskredet grænseværdi for og derefter afkorte den og giver brugere besked. I stedet for manuelt at udføre hver af disse trin, kan du oprette en runbook, som skal udføres alle disse opgaver som en enkelt proces. Du vil starte runbook, Angiv de nødvendige oplysninger som SQL-servernavnet, databasenavnet og modtagerens e-mail- og derefter sidde tilbage, når processen afsluttes. 


## <a name="what-can-runbooks-automate"></a>Hvad kan runbooks automatisere?

Runbooks i Azure Automation er baseret på Windows PowerShell eller Windows PowerShell arbejdsproces, så de gør noget, der kan gøre PowerShell. Hvis et program eller en tjeneste har en API, kan en runbook arbejde med den. Hvis du har en PowerShell-modulet til programmet, kan du indlæse pågældende modul i Azure automatisering og medtage disse cmdlet'er i din runbook. Azure automatisering runbooks Kør i Azure skyen og få adgang til en hvilken som helst skyen ressourcer eller eksterne ressourcer, der kan åbnes fra skyen. Ved hjælp af [Hybrid Runbook arbejder](automation-hybrid-runbook-worker.md)kan runbooks køre i dit lokale datacenter til at administrere lokale ressourcer. 


## <a name="getting-runbooks-from-the-community"></a>Hente runbooks fra community'et

[Galleri med Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) indeholder runbooks fra Microsoft og community'et, du kan enten bruge uændret i dit miljø eller tilpasse dem til din egen formål. De er også nyttigt at som henvisninger til Lær at oprette din egen runbooks. Du kan endda bidrage til din egen runbooks til galleriet, du tror, at andre brugere kan være nyttigt. 


## <a name="creating-runbooks-with-azure-automation"></a>Oprette Runbooks med Azure automatisering 

Du kan [oprette dine egne runbooks](automation-creating-importing-runbook.md) fra bunden eller ændre runbooks fra [Runbook galleriet](http://msdn.microsoft.com/library/azure/dn781422.aspx) for at se dine egne krav. Der findes tre forskellige [typer af runbook](automation-runbook-types.md) , som du kan vælge mellem baseret på dine krav og PowerShell oplevelse. Hvis du foretrækker at arbejde direkte med PowerShell-kode, kan derefter du bruge en [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) eller [PowerShell arbejdsproces runbook](automation-runbook-types.md#powershell-workflow-runbooks) , som du redigere offline eller med [tekstdata editor](http://msdn.microsoft.com/library/azure/dn879137.aspx) i portalen Azure. Hvis du foretrækker at redigere en runbook uden at få vist den underliggende kode, kan du oprette en [grafisk runbook](automation-runbook-types.md#graphical-runbooks) ved hjælp af [grafiske editor](automation-graphical-authoring-intro.md) i portalen Azure. 

Foretrækker du ser læseruden? Se på de under video fra Microsoft Ignite session i maj 2015. Bemærk: Mens de begreber og funktioner, der er beskrevet i denne video er korrekte, Azure automatisering er kommet meget, da denne video er optaget, det nu har en mere omfattende brugergrænseflade i portalen Azure og understøtter flere funktioner.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatisere Konfigurationsstyring med beskedteksten tilstand konfiguration 

[PowerShell DTK](https://technet.microsoft.com/library/dn249912.aspx) er en management platform, som gør det muligt at administrere, installere og gennemtvinge konfiguration for fysisk værter og virtuelle maskiner ved hjælp af en deklarativ PowerShell-syntaks. Du kan definere konfigurationer på en central DTK adskille Server, target computere kan hente og anvende automatisk. DTK indeholder et sæt af PowerShell-cmdletter, som du kan bruge til at administrere konfigurationer og ressourcer.  

[Azure automatisering DTK](automation-dsc-overview.md) er en skybaseret løsning til PowerShell DTK, der indeholder tjenester, der kræves for enterprise-miljøer.  Du kan administrere dine DTK ressourcer i Azure Automation og anvende konfigurationer for virtuelle eller fysiske computere, der hente dem fra en DTK adskille Server i Azure skyen.  Den indeholder også reporting services, der oplyser om vigtige begivenheder f.eks når noder har afveget fra deres tildelte konfiguration, og når en ny konfiguration er anvendt. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Oprette din egen DTK konfigurationer med Automation Azure

[DTK konfigurationer](automation-dsc-overview.md#azure-automation-dsc-terms) angive den ønskede tilstand for en node.  Flere noder kan anvende den samme konfiguration for at sikre, at de alle bevare en identisk tilstand.  Du kan oprette en konfiguration med en hvilken som helst tekst editor på din lokale computer og derefter importere den til Azure automatisering, hvor du kan samle og anvende den noder.


## <a name="getting-modules-and-configurations"></a>Få moduler og konfigurationer 

Du kan få [PowerShell moduler](automation-runbook-gallery.md#modules-in-powershell-gallery) , der indeholder-cmdletter, som du kan bruge i dine runbooks og DTK konfigurationer fra [PowerShell galleriet](http://www.powershellgallery.com/). Du kan Start dette galleri fra Azure-portalen og importere moduler direkte til Azure automatisering, eller du kan hente og importere dem manuelt. Du kan ikke installere modulerne direkte fra Azure-portalen, men du kan hente dem installere dem, som du ville gøre et andet modul. 


## <a name="example-practical-applications-of-azure-automation"></a>Eksempel i praksis Azure automatisering 

Følgende er blot nogle få eksempler på Hvad er typer af automatisering scenarier med Automation Azure. 

* Oprette og kopiere virtuelle maskiner i forskellige Azure-abonnementer. 
* Filen blev kopieret fra en lokal computer til en objektbeholder Azure Blob-lager. 
* Automatisere sikkerhedsfunktioner såsom nægte anmoder om fra en klient, når der registreres manglende adgang til service-angreb. 
* Kontrollér, at computere justere kontinuerligt med konfigurerede sikkerhedspolitik.
* Styre fortløbende installation af programkode, på tværs af skyen og lokale infrastruktur. 
* Oprette en Active Directory-område i Azure for din testmiljø. 
* Afkort en tabel i en SQL-database, hvis DB nærmer maksimumstørrelsen. 
* Fra en fjernplacering Opdater miljøindstillinger for en Azure websted. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Hvordan er Azure automatisering forbundet til andre automatiseringsværktøjer?

[Service Management automatisering (SMA)](http://technet.microsoft.com/library/dn469260.aspx) er beregnet til at automatisere administrationsopgaver i private skyen. Det er installeret lokalt i datacentret som en del af [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). SMA og Azure automatisering bruge de samme runbook formater, der er baseret på Windows PowerShell og arbejdsproces med Windows PowerShell, men SMA understøtter ikke [grafiske runbooks](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) er beregnet til automatisering af lokale ressourcer. Det bruger et andet runbook format end Azure automatisering og Service Management automatisering og har en grafisk grænseflade til at oprette runbooks uden en hvilken som helst scripting. Dens runbooks består af aktiviteter fra Integration Packs, der er skrevet specielt til Orchestrator. 


## <a name="where-can-i-get-more-information"></a>Hvor kan jeg få mere at vide? 

En række ressourcer er tilgængelige for dig at få mere at vide om Azure automatisering og oprette dine egne runbooks. 

* **Azure automatisering bibliotek** er, hvor du befinder dig lige nu. Artiklerne i dette bibliotek indeholder komplette dokumentation til konfiguration og administration af Azure automatisering og til oprettelse af din egen runbooks. 
* [Azure PowerShell-cmdlet'er](http://msdn.microsoft.com/library/jj156055.aspx) indeholder oplysninger til automatisering Azure handlinger ved hjælp af Windows PowerShell. Runbooks Brug disse cmdlet'er til at arbejde med Azure ressourcer. 
* [Administration af Blog](https://azure.microsoft.com/blog/tag/azure-automation/) indeholder de seneste oplysninger om Azure automatisering og andre teknologier til styring fra Microsoft. Du bør Abonner på denne blog for at holde dig opdateret med seneste fra Azure automatisering teamet. 
* [Automatisering Forum](http://go.microsoft.com/fwlink/p/?LinkId=390561) kan du sende spørgsmål om Azure automatisering behandles af Microsoft og automatisering community'et. 
* [Azure automatisering cmdletter](https://msdn.microsoft.com/library/mt244122.aspx) indeholder oplysninger til automatisering administrationsopgaver. Den indeholder cmdletter for at administrere automatisering konti, aktiver, runbooks, DTK.


## <a name="can-i-provide-feedback"></a>Kan jeg give feedback? 

**Skal du give os feedback!** Hvis du leder efter en Azure automatisering runbook løsning eller et integration modul, du sende en anmodning, Script på Script Center. Hvis du har anmodninger om feedback eller en funktion til Azure automatisering, kan du sende dem på [Bruger tale](http://feedback.windowsazure.com/forums/34192--general-feedback). Tak! 


