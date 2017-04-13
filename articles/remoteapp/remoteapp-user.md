<properties
    pageTitle="Føje en bruger til din Azure RemoteApp samling | Microsoft Azure"
    description="Lær at føje brugere til din Azure RemoteApp samling"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Sådan føjer du en bruger til din Azure RemoteApp samling

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Før dine brugere kan se og bruge dine apps i Azure RemoteApp, har du giver dem adgang til din samling. Dette er den nemme del: Angiv fanen **Brugeradgang** kontooplysningerne for brugeren, og klik derefter på markeringen.

Hvilken konto der har du brug for? Det afhænger typen af websteder, du har oprettet (skyen eller hybrid), og om du bruger Office 365 ProPlus i samlingen.

## <a name="supported-user-identities"></a>Understøttede bruger-id'er

Typerne forskellige af websteder (skyen kontra hybrid) understøtter ved hjælp af forskellige bruger-id'er for at få adgang til programmer.  

For en hybrid samling RemoteApp skal du konfigurere en Active Directory-domæneinfrastruktur på det lokale miljø og en Azure Active Directory-lejer med katalogintegration (og eventuelt single sign-on). Desuden skal du oprette nogle Active Directory-objekter i den lokale mappe.  

For en skyen samling RemoteApp, kan enhver bruger, der Azure Active Directory understøtter identiteter givet brugeradgang til RemoteApp medtage Microsoft Accounts.  Se tabellen nedenfor.

Office 365-brugere er Azure Active Directory-brugere. Hvis de har Azure Active Directory hybrid, kan Directory synkroniseret konti, de få tildelt brugeradgang i et RemoteApp-hybridinstallation.   

Du kan bruge denne tabel som en hurtig reference, identitet understøttes i din samling, og hvad Active Directory-kravene er.

|Brugerkonti |Skyen   |Hybrid|
|--------------|--------|------|
|Microsoft-konto|     Ja|    Nej|
|Azure Active Directory (Azure AD)| | |
|Azure AD-sky kun    |Ja    |Nej |
|ADsync med synkronisering af adgangskoder  |Ja    |Ja    |
|ADsync uden synkronisering af adgangskoder|  Ja |Nej |
|ADsync med AD FS  |Ja    |Ja    |
|[3 tredjepart Azure understøttes identitetsudbydere](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (eksempel Ping) |Ja    |Ja|
|Multi-Factor Authentication    |Ja    |Ja    |

Se [flere oplysninger](remoteapp-ad.md) om konfiguration af Active Directory for RemoteApp.


> [AZURE.NOTE] Azure Active Directory-brugere skal være fra de, der er knyttet til dit abonnement. (Du kan få vist og redigere dit abonnement på fanen **Indstillinger** på portalen. Se [ændre Azure Active Directory-lejer bruges af RemoteApp](remoteapp-changetenant.md) kan finde flere oplysninger).

## <a name="office-365-proplus-user-account-information"></a>Office 365 ProPlus brugerkontooplysninger
Hvis du bruger Office 365 ProPlus skabelon billedet i din af websteder *eller* , hvis du har oprettet et brugerdefineret billede, der bruger Office 365, kan du kun tilføje Azure Active Directory-brugere, der har Office 365-abonnementer til standarddomænet for dit abonnement. Se [Brug af Office 365 med Azure RemoteApp](remoteapp-o365.md) kan finde flere oplysninger.
