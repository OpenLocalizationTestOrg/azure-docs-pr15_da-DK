<properties
   pageTitle="Integrering af Azure Sikkerhedscenter beskeder med Azure log integration (Preview) | Microsoft Azure"
   description="I denne artikel hjælper dig med at komme i gang med at samle Sikkerhedscenter beskeder med Azure log integration."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Integrering af Sikkerhedscenter beskeder med Azure log integration (Preview)

Mange sikkerhedshandlinger og hændelsen svar teams er afhængige af en løsning sikkerhedsoplysninger og begivenhed Management (SIEM) som udgangspunktet for triaging og undersøge sikkerhedsadvarsler. Med Azure log integration kan kunder synkronisere Azure Sikkerhedscenter påmindelser, sammen med virtuelt sikkerhedshændelser, der indsamles via Azure diagnosticerings- og Azure overvågningslogge med deres log analytics eller SIEM løsning i i realtid.

Azure log integration fungerer med HP ArcSight, Splunk, IBM QRadar og andre.

## <a name="what-logs-can-i-integrate"></a>Hvilke logfiler kan integrere?

Azure giver omfattende logføring for hver tjeneste. Disse logfiler kategoriseres som:

- **Kontrolelementet/Management logfiler** som give indsigt i handlingerne Azure ressourcestyring oprette, Opdater og Slet.
- **Logfiler over dataplan** som give indsigt i de hændelser, der opstår, når du bruger en Azure ressource. Et eksempel er Windows hændelseslog - sikkerhed og logge på programmet i en virtuel maskine.

Azure log integration understøtter i øjeblikket integration af:

- Azure VM logfiler
- Azure overvågningslogge
- Azure Sikkerhedscenter beskeder

## <a name="install-azure-log-integration"></a>Installer Azure log integration

Hent [Azure logge integration](https://www.microsoft.com/download/details.aspx?id=53324).

Tjenesten Azure log integration indsamler telemetridata fra den maskine, som den er installeret.  Telemetridata, der indsamles er:

- Undtagelser, der opstår under udførelse af Azure logge integration
- Målepunkter om antallet af forespørgsler og hændelser behandlet
- Statistik om hvilke Azlog.exe kommandolinjeparametre bliver brugt

> [AZURE.NOTE] Du kan slå fra samling af telemetridata ved fravalg af denne indstilling.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrere Azure overvågningslogge and Security Center beskeder

1. Åbn kommandoprompten og **cd** til **c:\Program Files\Microsoft Azure Log Integration**.

2. Kør kommandoen **azlog createazureid** til at oprette en [Azure Active Directory Service hovedstolen](../active-directory/active-directory-application-objects.md) i Azure Active Directory (AD)-lejere, der hoster Azure-abonnementer.

    Du vil blive bedt om dit Azure logon.

    > [AZURE.NOTE] Du skal være ejer-abonnement eller en samtidig administratoren af abonnementet.

    Godkendelse til Azure sker via Azure AD.  Oprette en tjeneste hovedstolen for Azure log integration opretter den Azure AD-identitet, får adgang til at læse fra Azure abonnementer.

3. Køre den **azlog Godkend <SubscriptionID> ** kommandoen til at tildele læseradgang på dit abonnement til tjenesten hovedstolen oprettede i trin 2. Hvis du ikke angiver en **SubscriptionID**, skal derefter tjenesten hovedstolen tildeles rollen Læser for alle abonnementer, du har adgang til.

    > [AZURE.NOTE] Du kan få vist advarsler, hvis du kører kommandoen **Godkend** umiddelbart efter kommandoen **createazureid** , fordi der er nogle ventetid mellem hvornår Azure AD-konto er oprettet, og når kontoen er tilgængelig til brug. Hvis du venter omkring 10 sekunder, når du har kørt kommandoen **createazureid** til at køre kommandoen **Godkend** , skal du ikke se disse advarsler.

4. Kontrollere for at bekræfte, at JSON overvågningslogfiler er der følgende mapper:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Kontrollér følgende mapper for at bekræfte, at Sikkerhedscenter beskeder findes i dem:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Peg standard SIEM fil domænes forbindelsen til den relevante mappe til pipe dataene på forekomsten af SIEM. Se [SIEM konfigurationer](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) på konfigurationen af SIEM.

Hvis du har spørgsmål om Azure Log Integration, bedes du sende en mail til [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Azure overvågningslogge og egenskaben definitioner, i:

- [Overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md)
- [Liste over management begivenheder i et abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) - til at hente overvågningsloghændelser.

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.
