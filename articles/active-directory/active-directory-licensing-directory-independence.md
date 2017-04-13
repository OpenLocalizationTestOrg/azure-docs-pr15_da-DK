<properties
   pageTitle="Tilføje og administrere flere Azure Active Directory-kataloger | Microsoft Azure"
   description="Instruktioner og bedste fremgangsmåder til at tilføje og administrere din Azure Active Directory-kataloger, som oplyser om mapper som en fuldstændig uafhængig ressourcer"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Tilføje og administrere flere Azure Active Directory-mapper

Azure Active Directory (Azure AD), hver mappe er en fuldstændig uafhængig ressource: en peer, fuld funktionalitet og logisk uafhængigt af andre mapper, som du administrerer. Der er ingen overordnet / underordnet-relation mellem mapper. Denne uafhængighed mellem mapper omfatter ressource uafhængighed, administrative uafhængighed og synkronisering uafhængighed.

##<a name="resource-independence"></a>Ressource uafhængighed

Hvis du opretter eller slette en ressource i et bibliotek, har ingen indvirkning på en hvilken som helst ressource i et andet bibliotek med delvis undtagelse af eksterne brugere, som beskrevet nedenfor. Hvis du bruger et brugerdefineret domæne 'contoso.com' med én mappe, kan ikke bruges med andre adresselister.

##<a name="administrative-independence"></a>Administrative uafhængighed

Hvis en ikke-administrativ bruger til mappe 'Contoso' opretter en test mappe 'Test' derefter:
- Som standard er den bruger, der opretter en mappe tilføjet som en ekstern bruger i den nye mappe, og tildelt administratorrollen som global i mappen.
- Administratorer af directory 'Contoso' har ingen direkte administratorrettigheder til mappe 'Test', medmindre en administrator af 'Test' giver dem specifikt disse rettigheder. Administratorer af 'Contoso' kan kontrollere adgang til mappe 'Test', hvis de styre brugerkontoen som oprettet 'Test'.
- Hvis du ændrer (tilføje eller fjerne) en administratorrolle for en bruger i et bibliotek, ændringen påvirker ikke en administratorrolle, som brugeren muligvis i en anden mappe.

##<a name="synchronization-independence"></a>Synkronisering uafhængighed

Du kan konfigurere hver Azure AD-mappe uafhængigt af hinanden for at få data, der synkroniseres fra en enkelt forekomst af enten:
  - Værktøjet Directory Synchronization (DirSync) til at synkronisere data med et enkelt AD område.
  - Azure Active Directory Connector til Forefront identitet Manager, at synkronisere data med en eller flere lokale områder og/eller ikke-Azure AD-datakilder.

##<a name="add-an-azure-ad-directory"></a>Tilføje en Azure AD-mappe

Vælg filtypenavnet Azure Active Directory til venstre for at tilføje en Azure AD-mappe i portalen Azure klassisk, og tryk på **Tilføj**.

> [AZURE.NOTE]   I modsætning til andre Azure ressourcer er dine adresselister ikke underordnede ressourcer af et Azure-abonnement. Hvis du annullerer eller tillade din Azure-abonnement udløber, kan du stadig få adgang til dataene directory ved hjælp af PowerShell Azure, Azure Graph API eller andre grænseflader som Office 365 administration. Du kan også knytte et andet abonnement til mappen.

Til en bred oversigt over Azure AD licensering problemer og bedste fremgangsmåder, se [Hvad er Azure Active Directory-licensering?](active-directory-licensing-what-is.md).
