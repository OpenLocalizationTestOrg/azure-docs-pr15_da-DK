<properties
   pageTitle="Azure automatisering sikkerhed | Microsoft Azure"
   description="I denne artikel indeholder en oversigt over automatisering sikkerhed og de forskellige godkendelsesmetoder, der er tilgængelige for automatisering konti i Azure automatisering."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="automatisering sikkerhed, sikker automatisering" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Azure automatisering sikkerhed
Azure automatisering gør det muligt at automatisere opgaver med ressourcer i Azure, lokale miljø, og med andre skyen udbydere som Amazon Web Services (AWS).  Hvis en runbook til at udføre handlingerne påkrævet, skal skal den have tilladelser sikker adgang til ressourcer med minimale rettigheder skal findes på abonnementet.  
I denne artikel dækker de forskellige godkendelse scenarier, der understøttes af Azure automatisering og viser dig, hvordan du kommer i gang baseret på miljøet eller miljøer, du vil administrere.  

## <a name="automation-account-overview"></a>Automatisering Account overview
Når du starter Azure automatisering for første gang, skal du oprette mindst én konto med Automation. Automatisering konti muligt at isolere ressourcerne automatisering (runbooks, aktiver, konfigurationer) fra de ressourcer, der er indeholdt i andre automatisering konti. Du kan bruge automatisering konti til at adskille ressourcer til separat logiske miljøer. For eksempel kan du bruge en konto til udvikling, en anden til fremstilling og et andet til dit lokale miljø.  En Azure automatisering konto er forskellig fra din Microsoft-konto eller konti, der er oprettet i abonnementet Azure.

Automatisering ressourcer for hver konto med Automation er knyttet til en enkelt Azure region, men automatisering konti kan administrere ressourcer i en hvilken som helst område. Den primære grund til at oprette automatisering konti i forskellige områder der ville være Hvis du har politikker, der kræver data og ressourcer, der kan være isolerede til et bestemt område.

>[AZURE.NOTE]Automatisering konti, og de ressourcer, de indeholder, der er oprettet i portalen Azure, kan ikke åbnes i portalen Azure klassisk. Hvis du vil administrere disse konti eller deres ressourcer med Windows PowerShell, skal du bruge Azure ressourcestyring moduler.

Alle de opgaver, du udfører mod ressourcer ved hjælp af Azure ressourcestyring og Azure-cmdletter i Azure Automation skal godkendes Azure ved hjælp af Azure Active Directory-organisationsidentitet legitimationsoplysninger-baseret godkendelse.  Certifikat-baseret godkendelse var den oprindelige godkendelsesmetode med Azure Service Management tilstand, men det var komplicerede til installation.  Godkendelse til Azure med Azure AD bruger blev introduceret tilbage i 2014 ikke kun at forenkle processen til at konfigurere en konto til godkendelse, men også understøtte muligheden for at godkende uden interaktion til Azure med en enkelt brugerkonto, har arbejdet med både Azure ressourcestyring og klassisk ressourcer.   

I øjeblikket, når du opretter en ny konto med Automation i portalen Azure, oprettes automatisk:

-  Køre som konto, som opretter en ny tjeneste hovedstolen i Azure Active Directory, et certifikat og tildeler den bidragyder rollebaseret adgangskontrol (RBAC), der skal bruges til at administrere ressourcestyring ressourcer ved hjælp af runbooks.
-  Klassisk Kør som konto ved at uploade et certifikat, administration, som bruges til at administrere Azure Service Management eller klassisk ressourcer ved hjælp af runbooks.  

Rollebaseret adgangskontrol er tilgængelig med Azure Resource Manager til at give tilladte handlinger til et Azure AD-brugerkontoen og Kør som konto og godkende hovedstolen pågældende tjeneste.  Læs [Rollebaseret adgangskontrol i Azure automatisering artikel](../automation/automation-role-based-access-control.md) for at få yderligere oplysninger for at hjælpe med at udvikle din model til administration af automatisering tilladelser.  

Runbooks, der kører på en Hybrid Runbook arbejder i dit datacenter eller mod computing tjenester i AWS kan ikke bruge den samme metode, der typisk bruges til godkendelse af runbooks til Azure ressourcer.  Dette skyldes, at disse ressourcer, der kører uden for Azure og derfor kræver sin egen sikkerhedslegitimationsoplysninger, der er defineret i automatisering til at godkende ressourcer, som de skal have adgang til lokalt.  

## <a name="authentication-methods"></a>Metoder til brugergodkendelse

Den følgende tabel opsummerer de forskellige godkendelsesmetoder for de enkelte miljøer, der understøttes af Azure automatisering og i artiklen, som beskriver, hvordan du konfigurerer godkendelse for din runbooks.

Metode  |  Miljø  | Artikel
----------|----------|----------
Azure AD-brugerkonto | Azure ressourcestyring og Azure Service Management | [Godkende Runbooks med Azure AD-brugerkonto](../automation/automation-sec-configure-aduser-account.md)
Azure Kør som konto | Azure ressourcestyring | [Godkende Runbooks med Azure Kør som konto](../automation/automation-sec-configure-azure-runas-account.md)
Azure klassisk Kør som konto | Azure Service Management | [Godkende Runbooks med Azure Kør som konto](../automation/automation-sec-configure-azure-runas-account.md)
Windows-godkendelse | Lokalt Datacenter | [Godkende Runbooks for hybride Runbook medarbejdere](../automation/automation-hybrid-runbook-worker.md)
AWS legitimationsoplysninger | Amazon webtjenester | [Godkende Runbooks med Amazon webtjenester (AWS)](../automation/automation-sec-configure-aws-account.md)



