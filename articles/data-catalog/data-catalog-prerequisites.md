<properties
   pageTitle="Azure datakatalog forudsætninger | Microsoft Azure"
   description="Azure datakatalog forudsætninger - hvad du har brug at komme i gang med Azure datakatalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-prerequisites"></a>Azure datakatalog forudsætninger

## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>Hvad behøver jeg for at komme i gang med Azure datakatalog?

Der er et par ting, skal du tager sig af, før du kan konfigurere **Azure datakatalog**. Bare rolig – de tager ikke lang tid!

## <a name="azure-subscription"></a>Azure-abonnement
Hvis du vil konfigurere Azure datakatalog, skal du være ejer eller medejer af et Azure-abonnement.

Azure abonnementer hjælpe dig med at organisere adgang til skyen serviceressourcer som Azure datakatalog. De også Hjælp du styre, hvordan rapporteres Ressourceforbrug, faktureret, og som er betalt. Hvert abonnement kan have en anden fakturering og betaling installation, så du kan have forskellige abonnementer og forskellige planer ved afdeling, project, regionalt kontor osv. Hver skytjeneste hører til et abonnement, og du skal have et abonnement, før du konfigurerer Azure datakatalog. Se [Administrer konti, abonnementer og Administrationsroller](../active-directory/active-directory-assign-admin-roles.md)for at få mere for at vide.

## <a name="azure-active-directory"></a>Azure Active Directory
Hvis du vil konfigurere Azure datakatalog, skal du være logget på ved hjælp af en Azure Active Directory-brugerkonto.

Azure Active Directory (Azure AD) gør det nemt for din virksomhed til at administrere identitet og access, både i skyen og i det lokale miljø. Brugere kan bruge en enkelt arbejds- eller skolekonto til single sign-on til en hvilken som helst skyen og lokale-webprogrammet. Azure datakatalog bruger Azure AD til at godkende sign-on –. Hvis du vil vide mere, skal du se [Hvad er Azure Active Directory](../active-directory/active-directory-whatis.md).

> [AZURE.NOTE] [Azure portal](http://portal.azure.com/) kan brugere til at logge på med en personlig Microsoft-Account eller en Azure Active Directory-arbejds- eller skolekonto. Hvis du vil konfigurere Azure datakatalog ved hjælp af portalen Azure eller ved hjælp af [datakatalog portal](http://www.azuredatacatalog.com) skal du være logget på ved hjælp af en Azure Active Directory-konto, ikke en personlig konto.

## <a name="active-directory-policy-configuration"></a>Konfiguration af Active Directory-politik

I nogle situationer kan brugere støde på en situation, hvor de kan logge på, til portalen Azure datakatalog, men når de forsøger at logge på værktøjet datakilde registrering de får en fejlmeddelelse, der forhindrer dem i at logge på. Dette problem problem kan opstå, når brugeren er på virksomhedens netværk, eller kan forekomme, når brugeren opretter forbindelse fra uden for virksomhedens netværk.

Værktøjet til datakilde registrering bruger formulargodkendelse til at validere brugerlogon mod Active Directory. Til vellykket logon, skal være aktiveret formulargodkendelse i politikken globale godkendelse af en Active Directory-administrator.

Den globale godkendelsespolitik giver godkendelsesmetoder til aktiveres separat til intranet og ekstranet forbindelser, som vist nedenfor. Logon fejl kan opstå, hvis formulargodkendelse ikke er aktiveret for det netværk, hvor brugeren opretter forbindelse.

 ![Active Directory globale godkendelsespolitik](./media/data-catalog-prerequisites/global-auth-policy.png)

Du kan finde yderligere oplysninger finder [Konfigurere politikker for godkendelse](https://technet.microsoft.com/library/dn486781.aspx).
