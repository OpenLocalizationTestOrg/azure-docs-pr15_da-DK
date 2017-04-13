<properties
   pageTitle="Fejlfinding: 'Active Directory' element er mangler eller er ikke tilgængelig | Microsoft Azure "
   description="Hvad skal udføres, når Active Directory menupunkt ikke vises i portalen Azure administration."
   services="active-directory"
   documentationCenter="na"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Fejlfinding: 'Active Directory' element er mangler eller er ikke tilgængelig

Mange af vejledningen til at bruge Azure Active Directory-funktioner og tjenester begynder med "Gå til administrationsportalen Azure, og klik på **Active Directory**." Men hvad skal du gøre, hvis elementet Active Directory lokalnummer eller menuen ikke vises, eller hvis det er markeret **Ikke tilgængelig**? Dette emne er udviklet til at hjælpe. Det beskrives de betingelser, under hvilket **Active Directory** , ikke vises eller ikke er tilgængelig, og forklarer, hvordan du vil fortsætte.

## <a name="active-directory-is-missing"></a>Active Directory mangler

Typisk vises et **Active Directory** -element i den venstre navigationsmenu. Vejledningen i Azure Active Directory procedurer forudsætter, at elementet er i visningen.

![Skærmbillede: Active Directory i Azure](./media/active-directory-troubleshooting/typical-view.png)

Active Directory-element vises i den venstre navigationsmenu, når en af følgende betingelser er sande. Ellers vises elementet ikke.

* Den aktuelle bruger logget med en Microsoft-konto (tidligere kendt som et Windows Live ID).

    ELLER

* Azure lejeren har en mappe, og den aktuelle konto er administrator af en mappe.

    ELLER

* Azure lejeren har mindst én Azure AD-adgangskontrol (ACS) navneområde. Du kan finde yderligere oplysninger finder [Access kontrolelement Namespace](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    ELLER

* Azure lejeren har mindst én Azure Multi-Factor Authentication udbyder. Se [Administrere Azure Multi-Factor Authentication udbydere](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)kan finde flere oplysninger.

Hvis du vil oprette et adgangskontrol navneområde eller en multi-Factor Authentication udbyder, skal du klikke på **+ Ny** > **App Services** > **Active Directory**.

Hvis du vil have administratorrettigheder til en mappe, du har administrator tildele en administratorrolle til din konto. Yderligere oplysninger finder du [tildele administratorroller](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory er ikke tilgængelig

Når du klikker på **+ Ny** > **App Services**, et **Active Directory** -element vises. Nærmere betegnet kan vises Active Directory-element, når nogen af Active Directory-funktioner, som mappe, adgangskontrol eller multi-Factor Auth-udbyder, er tilgængelige for den aktuelle bruger.

Men når siden indlæses, elementet er nedtonet, og er markeret **Ikke tilgængelig**. Dette er en midlertidig tilstand. Hvis du vente et par sekunder, elementet, bliver tilgængelig. Hvis forsinkelsen forlænges, løser opdatere websiden ofte problemet.

![Skærmbillede: Active Directory er ikke tilgængelig](./media/active-directory-troubleshooting/not-available.png)
