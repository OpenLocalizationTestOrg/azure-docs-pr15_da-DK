
<properties 
    pageTitle="Sådan bruges Azure RemoteApp med Office 365-brugerkonti | Microsoft Azure"
    description="Lær at bruge Azure RemoteApp med min Office 365-brugerkonti"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Sådan bruges Azure RemoteApp med Office 365-brugerkonti

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Hvis du har en Office 365-abonnement, du har en Azure Active Directory, der gemmer dine brugernavne og adgangskoder, der bruges til at få adgang til Office 365-tjenester. For eksempel, når brugerne aktiverer Office 365 ProPlus er de godkendelse mod Azure AD at kontrollere, om licenser. De fleste kunder vil gerne bruge den samme mappe med Azure RemoteApp.

Hvis du installerer Azure RemoteApp bruger du højst sandsynligt et Azure abonnement, som er knyttet til en anden Azure AD. Hvis du vil bruge din Office 365-mappe, skal du flytte Azure abonnementet til denne mappe.

For at få oplysninger om, hvordan du installerer Office 365-klientprogrammer, se, [hvordan du bruger Office 365-abonnement med Azure RemoteApp](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Fase 1: Registrere dit gratis Office 365 Azure Active Directory-abonnement
Hvis du bruger Azure klassisk portalen skal du følge trinnene i [registrere abonnementet gratis Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) at få administratoradgang til din Azure AD via Azure Management-portalen. Som et resultat af denne proces skal du kunne logge på portalen Azure og se mappen der – på dette tidspunkt du kan ikke se meget mere da det fulde Azure abonnement, du bruger med Azure RemoteApp er i en anden mappe.

Husk, at brugernavn og adgangskode til den administratorkonto, du har oprettet i dette trin – de skal bruges i fase 2.

Hvis du ved hjælp af portalen Azure, kan du se, [hvordan du kan registrere og aktivere et gratis Azure Active Directory ved hjælp af Office 365-portalen](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Fase 2: Ændre den Azure AD, der er knyttet til abonnementet Azure.
Vi vil ændre dit Azure-abonnement fra den aktuelle mappe til mappen Office 365 vi arbejdet med fase 1.

Følg de instruktioner, der er beskrevet i [ændre Azure Active Directory-leje i Azure RemoteApp](remoteapp-changetenant.md). Særligt opmærksomme på følgende trin:

- Trin #1: Hvis du har installeret Azure RemoteApp (ARA) i dette abonnement, skal du kontrollere, at du fjerner alle Azure AD-brugerkonti fra en hvilken som helst ARA samlinger først, før du forsøger andet. Alternativt kan du overveje at slette alle eksisterende af websteder.
- Trin #2: Dette er et vigtigt trin. Du skal bruge en Microsoft-konto (fx @outlook.com) som en tjenesteadministratoren på dit abonnement dette skyldes, at vi ikke kan have en hvilken som helst brugerkonti fra den eksisterende Azure AD, der er knyttet til abonnementet – Hvis vi gør, vi vil ikke kunne flytte den til en anden Azure AD.
- Trin #4: Når du tilføjer en eksisterende mappe, systemet bliver bedt om at logge på med en administratorkonto for den pågældende mappe. Sørg for at bruge administratorkontoen fra fase 1.
- Trin #5: Ændre den overordnede mappe abonnementet til din Office 365-mappe. Afslut resultatet skal vises, under Indstillinger -> abonnementer dit abonnement vises mappen Office 365. 
![Ændre den overordnede mappe abonnementet](./media/remoteapp-o365user/settings.png)
 

På dette tidspunkt er abonnementet Azure RemoteApp knyttet til din Office 365 Azure AD Du kan bruge eksisterende Office 365-brugerkonti med Azure RemoteApp!




