<properties
   pageTitle="Azure virtuelt DotNet Core selvstudium 1 | Microsoft Azure"
   description="Azure virtuelt DotNet Core selvstudium"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automatisere programmet installationer til virtuelle Azure-computere

I denne serie indeholder oplysninger om installation og konfiguration af Azure ressource og programmer, der bruger Azure ressource Administrer skabeloner. Et eksempel på en skabelon er implementeret i denne serie, og skabelonen installation undersøges. Formålet med denne serie er at Uddan på relationen mellem Azure ressourcer, og at give hænder på erfaring med at udrulle fuldt integreret Azure ressourcestyring skabeloner. Dette dokument forudsætter et grundlæggende niveau af viden med Azure Ressourcestyring, før du starter selvstudiet fortrolig med grundlæggende begreber Azure ressourcestyring.

## <a name="music-store-application"></a>Musik store-program

Bruges i denne serie prøven er et .net Core programmet simulere en musik Store indkøb oplevelse. Dette program kan installeres på en Linux eller Windows virtuelle system, eksempel installationer er blevet oprettet for begge. Programmet omfatter et webprogram og en SQL-database. Før du læser artikler i denne serie, installere programmet ved hjælp af knappen installation findes på denne side. Når helt installeret, ligner programmet / Azure arkitekturen i det følgende diagram. 

Skabelonen musik Store ressourcestyring kan findes her, [Musik Store Linux skabelon]( https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)

![Musik Store-program](./media/virtual-machines-linux-dotnet-core/music-store.png)

Hver af disse komponenter, herunder skabelonen knytte JSON gennemgås i følgende artikler med fire.

- [**Programmet arkitektur**](./virtual-machines-linux-dotnet-core-2-architecture.md) – programkomponenter som websteder og databaser skal knyttes til Azure computerressourcer som virtuelle maskiner og Azure SQL-databaser. Dette dokument indeholder en gennemgang tilknytning Beregn har du brug for, at Azure ressourcer og implementere disse ressourcer med en skabelon til Azure ressourcestyring. 

- [**Adgang og sikkerhed**](./virtual-machines-linux-dotnet-core-3-access-security.md) – når vært programmer i Azure, er det nødvendigt at overveje, hvordan programmet åbnes, og hvordan forskellige komponenter adgang til programmer hinanden. Dette dokument indeholder oplysninger om at levere og sikring af internetadgang til et program og access mellem programkomponenter.

- [**Tilgængelighed og skalering**](./virtual-machines-linux-dotnet-core-4-availability-scale.md) – tilgængelighed og skalering referere til programmer muligheden for at holde kører under infrastruktur nedetid og muligheden for at skalere Beregn ressourcer for at imødekomme programmet behov. Denne dokumentoplysninger de komponenter, der er behov for at installere en belastning afstemmes og meget tilgængeligt program.

- [**Programmet Installation**](./virtual-machines-linux-dotnet-core-5-app-deployment.md) – når du installerer programmer på Azure virtuelle maskiner, den metode, hvormed de binære filer programmet er installeret på den virtuelle maskine skal der tages. Dette dokument indeholder oplysninger om installation af automatisering programmer ved hjælp af Azure virtuelt brugerdefineret Script udvidelser.

Målet når udvikler Azure ressourcestyring skabeloner er at automatisere installationen af Azure infrastruktur og installation og konfiguration af alle programmer, der hostes på denne Azure infrastruktur. Samarbejde med disse artikler indeholder et eksempel på denne oplevelse.

## <a name="deploy-the-music-store-application"></a>Installere programmet musik store

Programmet musik Store kan installeres ved hjælp af denne knap.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-linux%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Skabelonen Azure ressourcestyring kræver følgende parameterværdier.

|Parameternavn |Beskrivelse   |
|---|---|
|SSHKEYDATA   | SSH vigtige data, der bruges til at sikre adgang til den virtuelle maskine. Du kan finde oplysninger om oprettelse af en SSH vigtige par [oprette SSH taster til Linux FOS i Azure](virtual-machines-linux-mac-create-ssh-keys.md).  |
|ADMINUSERNAME   | Administrator brugernavn, der bruges på den virtuelle maskine og Azure SQL-Database.  |
|SQLADMINPASSWORD | Adgangskode, der bruges på Azure SQL-Database.  |
|NUMBEROFINSTANCES | Antallet af virtuelle maskiner skal oprettes. Hver af disse virtuelle maskiner hoste webprogrammet musik Store, og al trafik er på tværs af dem. |
|PUBLICIPADDRESSDNSNAME | Globalt entydige DNS-navn, der er knyttet til den offentlige IP-adresse. |

Når skabelon installationen er færdig, skal du gå til den offentlige IP-adresse ved hjælp af enhver browser. .Net Core musik websted der præsenteres.

## <a name="next-steps"></a>Næste trin

<hr>

[Trin 1 - programmet arkitektur med Azure ressourcestyring skabeloner](./virtual-machines-linux-dotnet-core-2-architecture.md)

[Trin 2 - adgang og sikkerhed i Azure ressourcestyring skabeloner](./virtual-machines-linux-dotnet-core-3-access-security.md)

[Trin 3 - tilgængelighed og skalering i Azure ressourcestyring skabeloner](./virtual-machines-linux-dotnet-core-4-availability-scale.md)

[Trin 4 - program-installation med Azure ressourcestyring skabeloner](./virtual-machines-linux-dotnet-core-5-app-deployment.md)


