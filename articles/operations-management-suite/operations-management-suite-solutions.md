<properties
   pageTitle="Løsninger i handlinger Management-pakken (OMS) | Microsoft Azure"
   description="Løsninger, der udvider funktionaliteten af handlinger Management pakke (OMS) ved at indsende pakket management scenarier, som kunder kan føje til deres OMS arbejdsområde.  I denne artikel indeholder oplysninger om hvordan tilpassede løsninger, der er oprettet af kunder og partnere."
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
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Management-løsninger i handlinger Management pakke (OMS) (Preview)

>[AZURE.NOTE]Dette er foreløbig dokumentation for management-løsninger i OMS, der aktuelt findes i Vis udskrift.    

Management-løsninger, der udvider funktionaliteten af handlinger Management pakke (OMS) ved at indsende pakket management scenarier, som kunder kan føje til deres miljø.  Ud over [løsninger, der leveres af Microsoft](../log-analytics/log-analytics-add-solutions.md)-partnere og kunder kan oprette management-løsninger, der skal bruges i deres egen miljø eller stilles til rådighed for kunder via community'et.

## <a name="finding-and-installing-management-solutions"></a>Søge efter og installere management-løsninger
Der findes flere metoder til at finde og installere management-løsninger, som beskrevet i følgende afsnit.

### <a name="azure-marketplace"></a>Azure Marketplace
Management-løsninger, der leveres af Microsoft, og der er tillid til partnere kan installeres fra Azure Marketplace i portalen Azure.

1. Log på portalen Azure.
2. Vælg **flere tjenester**i venstre rude.
3. Enten Rul ned til **løsninger** , eller skriv *løsninger* i dialogboksen **Filter** .
4. Klik på knappen **+ Tilføj** .
5. Søge efter løsninger, du er interesseret i enten ved at gennemse, klikke på knappen **Filter** eller at skrive i feltet **Søg Everthing** .
6. Klik på en marketplace element for at få vist detaljerede oplysninger.
4. Klik på **Opret** for at åbne ruden **Tilføj løsning** .
5. Du bliver bedt om at nødvendige oplysninger som [OMS workspace og automatisering konto](#oms-workspace-and-automation-account) ud over værdier for alle parametre i en løsning.
6. Klik på **Opret** for at installere løsningen.

### <a name="oms-portal"></a>OMS Portal
Management-løsninger, der leveres af Microsoft kan installeres fra løsningsgalleriet på portalen OMS.

1. Log på portalen OMS.
2. Klik på feltet **Løsningsgalleriet** .
2. Lær om hver tilgængelige løsning på siden OMS løsningsgalleriet. Klik på navnet på den løsning, du vil føje til OMS.
3. På siden for den løsning, du vælger, vises detaljerede oplysninger om løsningen. Klik på **Tilføj**.
4. Et nyt felt til den løsning, du har tilføjet vises på Oversigt i side i OMS, og du kan begynde at bruge den, når tjenesten OMS behandler dine data.

### <a name="azure-quickstart-templates"></a>Azure Hurtig start skabeloner
Medlemmer af gruppen kan sende management-løsninger til Azure Hurtig start skabeloner.  Du kan hente disse skabeloner til senere installation eller undersøge dem til at se, hvordan du kan [oprette dine egne løsninger](#creating-a-solution).

1. Følg den proces, der er beskrevet i [OMS workspace og automatisering konto](#oms-workspace-and-automation-account) til at sammenkæde et arbejdsområde og konto.
2. Gå til [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/).  
3. Søge efter en løsning, som du er interesseret i.
4. Vælg løsningen fra resultaterne for at se detaljerne.
5. Klik på knappen **Installer til Azure** .
6. Du bliver bedt om at angive oplysninger som ressourcegruppe og placering ud over værdier for alle parametre i en løsning.
7. Klik på **Køb** for at installere løsningen.

### <a name="deploy-azure-resource-manager-template"></a>Installere Azure ressourcestyring skabelon
Løsninger, du får fra community'et eller som du [selv oprette](#creating-a-solution) implementeres som en ressourcestyring skabelon, og du kan bruge en af de almindelige metoder til [implementering af en skabelon](../resource-group-template-deploy-portal.md).  Vær opmærksom på, før du installerer løsningen, skal du oprette og sammenkæde [OMS workspace og automatisering konto](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>OMS workspace og automatisering konto
De fleste management-løsninger kræver et [OMS arbejdsområde](../log-analytics/log-analytics-manage-access.md) til at indeholde visninger og en [automatisering konto](../automation/automation-security-overview.md#automation-account-overview) skal indeholde runbooks og relaterede ressourcer. Arbejdsområdet og konto skal opfylde følgende krav.

- En løsning kan kun bruge én OMS workspace og én konto med Automation.  
- OMS workspace og automatisering konto, der bruges af en løsning skal være knyttet til hinanden. Et OMS arbejdsområde kan kun sammenkædes med én automatisering konto, og en konto med Automation kan kun sammenkædes med én OMS arbejdsområde.
- For at være kædet sammen, skal være OMS workspace og automatisering konto i det samme ressourcegruppe og område.  Undtagelsen er en OMS arbejdsområde i af US område og og automatisering konto i af US 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Oprette en kæde mellem et arbejdsområde til OMS og automatisering konto
Hvordan du angiver OMS arbejdsområdet og automatisering konto afhænger installationsmetode til din løsning.

- Når du installerer en Microsoft-løsning via portalen OMS, den er installeret i det aktuelle OMS arbejdsområde, og der kræves ingen Automation-konto.

- Når du installerer en løsning via Azure Marketplace, du bliver bedt om en OMS workspace og automatisering konto, og der oprettes sammenkædningen mellem dem til dig.  

- Du skal knytte OMS workspace og automatisering konto før du installerer løsningen for løsninger uden for Azure Marketplace.  Du kan gøre dette ved at vælge en hvilken som helst løsning i Azure Marketplace og vælge den OMS arbejdsområde og automatisering konto.  Du behøver at installere faktisk løsningen, fordi linket oprettes, så snart OMS arbejdsområde og automatisering konto er markeret.  Når linket er oprettet, kan du bruge denne OMS workspace og automatisering konto til en løsning. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Bekræfte sammenkædningen mellem en OMS arbejdsområde og automatisering konto
Du kan kontrollere sammenkædningen mellem et OMS arbejdsområde og en automatisering konto på følgende måde.

1. Vælg kontoen, automatisering i portalen Azure.
2. Rul ned til bunden af ruden **Indstillinger** .
3. Hvis der er en sektion med navnet **OMS ressourcer** i ruden **Indstillinger** , er denne konto knyttet til et OMS arbejdsområde.
4. Vælg **arbejdsområde** til at få vist oplysninger om arbejdsområdet OMS knyttet til denne konto med Automation.


## <a name="listing-management-solutions"></a>Listen over management-løsninger
Brug følgende fremgangsmåde til at få vist management-løsningerne i de arbejdsområder, der er knyttet til abonnementet Azure.

1. Log på portalen Azure.
2. Vælg **flere tjenester**i venstre rude.
3. Enten Rul ned til **løsninger** , eller skriv *løsninger* i dialogboksen **Filter** .
4. Løsninger, der er installeret i alle dine arbejdsområder vises.

Bemærk, at du kan få vist kun de Microsoft løsninger, der er installeret i det aktuelle arbejdsområde ved hjælp af portalen OMS.

## <a name="removing-a-management-solution"></a>Fjerne en management-løsning
Når en management-løsning er fjernet, fjernes også alle de ressourcer i en løsning.  

1. Find løsningen i portalen Azure anvendelse af proceduren i [listen over løsninger](#listing-solutions).
2. Vælg den løsning, du vil fjerne.
3. Klik på knappen **Slet** .

## <a name="creating-a-management-solution"></a>Oprette en management-løsning
Komplet vejledning til oprettelse af management-løsninger, der er tilgængelige på [opretter solutions i handlinger Management pakke (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Næste trin

- Søg [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates) til eksempler på forskellige ressourcestyring skabeloner.
- Oprette dine egne [management-løsninger](operations-management-suite-solutions-creating.md).
 