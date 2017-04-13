<properties
   pageTitle="Konfigurere godkendelse af med Amazon webtjenester | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du opretter og kontrollerer en AWS legitimationsoplysningerne for runbooks i Azure Automation administrere AWS ressourcer."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="AWS godkendelse, konfigurere aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Godkende Runbooks med Amazon webtjenester
Automatisere almindelige opgaver med ressourcer i Amazon Web Services (AWS) kan gøres med Automation runbooks i Azure.  Du kan automatisere mange opgaver i AWS med Automation runbooks samme måde som med ressourcer i Azure.  Alle, som kræves er to ting:

* En AWS abonnement og et sæt legitimationsoplysninger.  Specifikt dit AWS hurtigtast og hemmeligt nøgle.  Du kan finde flere oplysninger, skal du gennemgå artiklen om [Ved hjælp af AWS legitimationsoplysninger](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* En Azure abonnement og automatisering konto.  Du kan finde flere oplysninger om oprettelse af en Azure automatisering konto, skal du gennemgå artiklen om [Konfigurer Azure køre som konto](../automation/automation-sec-configure-azure-runas-account.md).  

Hvis du vil godkende med AWS, skal du angive et sæt AWS legitimationsoplysninger til at godkende din runbooks kører fra Azure automatisering. Hvis du allerede har en Automation-konto, der er oprettet, og du vil bruge den til at godkende med AWS, kan du følge trinnene i følgende afsnit.  Hvis du vil dedikeret en konto til runbooks destinationens AWS ressourcer, skal du først oprette en ny [konto automatisering Kør som](../automation/automation-sec-configure-azure-runas-account.md) (Spring over muligheden for at oprette en tjeneste sikkerhedskonto) og derefter følge trinnene nedenfor.

## <a name="configure-automation-account"></a>Konfigurere automatiske konto
Til Azure automatisering til at kommunikere med AWS, skal du først at hente dine AWS legitimationsoplysninger og gemme dem som et aktiv i Azure Automation.  Udfør følgende trin, der er beskrevet i dokumentets AWS [Administration af Access-taster til kontoen AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) til at oprette en Access-nøgle og kopiere **Access nøgle-ID** og **Hemmeligt hurtigtast** (du kan også hente filen nøgle for at gemme den et sted, hvor sikkert).

Når du har oprettet og kopieret dine AWS sikkerhednøgler, skal du oprette et legitimationsoplysninger aktiv med en Azure automatisering konto til at gemme dem og referere til dem med dine runbooks sikkert.  Følg trinnene i afsnittet **til at oprette en ny legitimationsoplysninger** i artiklen [legitimationsoplysninger aktiver i Azure automatisering](../automation/automation-certificates.md/###To create a new credential with the Azure portal) , og Angiv følgende oplysninger:

1. Angiv **AWScred** eller en passende værdi efter dine naming standarder i feltet **navn** .  
2. Skriv din **Access-ID** og din **Hemmeligt hurtigtast** i feltet **adgangskode** og **Bekræft adgangskode** i feltet **brugernavn** .   

## <a name="next-steps"></a>Næste trin

- Reivew løsning artiklen [automatisere installation af en VM i Amazon Web Services](../automation/automation-scenario-aws-deployment.md) for at se, hvordan du opretter runbooks for at automatisere opgaver i AWS.
