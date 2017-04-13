<properties
    pageTitle="Azure AD-forbindelse: Få Introduktion ved hjælp af hurtig indstillinger | Microsoft Azure"
    description="Lær at hente, installere og køre konfigurationsguiden til Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Introduktion til Azure AD-forbindelse ved hjælp af hurtig indstillinger
Azure AD Connect **Express indstillinger** bruges, når du har en enkelt skov topologi og [synkronisering af adgangskoder](../active-directory-aadconnectsync-implement-password-synchronization.md) til godkendelse. **Express indstillinger** er standardindstillingen og bruges til det mest almindeligt udløst scenario. Du er kun nogle få korte klik væk for at udvide din lokale mappe til skyen.

Før du går i gang med at installere Azure AD-forbindelse, Sørg for at [hente Azure AD-forbindelse](http://go.microsoft.com/fwlink/?LinkId=615771) , og Fuldfør trinnene i den foreløbige nødvendige [Azure AD-forbindelse: Hardware og forudsætninger, der er](../active-directory-aadconnect-prerequisites.md).

Hvis udtrykkelig indstillinger ikke svarer til topologien, kan du se [relateret dokumentation](#related-documentation) til andre scenarier.

## <a name="express-installation-of-azure-ad-connect"></a>Hurtig installation af Azure AD-forbindelse
Du kan se disse trin i handling i sektionen [videoer](#videos) .

1. Log på som administrator af en lokal på serveren, du vil installere Azure AD-forbindelse på. Du skal gøre dette på serveren, du vil være Synkroniser server.
2. Gå til, og dobbeltklik på **AzureADConnect.msi**.
3. Marker feltet accepterer at licensvilkårene på velkomstsiden, og klik på **Fortsæt**.  
4. Klik på **Indstillinger for brug af udtrykkelig**på skærmbilledet Express indstillinger.  
![Velkommen til Azure AD-forbindelse](./media/active-directory-aadconnect-get-started-express/express.png)
5. Angiv brugernavnet og adgangskoden til en global administrator for din Azure AD på siden Opret forbindelse til Azure AD skærm. Klik på **Næste**.  
![Oprette forbindelse til Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Hvis du modtager en fejl og har problemer med forbindelse, se [fejlfinding af forbindelsesproblemer](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. Angiv brugernavnet og adgangskoden til en enterprise-administratorkonto på siden Opret forbindelse til Active Directory-Domænetjenester skærm. Du kan angive delen domæne i NetBios eller fulde Domænenavn, det vil sige, FABRIKAM\administrator eller fabrikam.com\administrator. Klik på **Næste**.  
![Oprette forbindelse til Active Directory-Domænetjenester](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. [**Azure AD - logon**](../active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) konfigurationssiden viser kun, hvis du ikke har udfyldt [bekræfte dine domæner](../active-directory-add-domain.md) i [forudsætninger](../active-directory-aadconnect-prerequisites.md).
![Ikke-bekræftede domæner](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
Hvis du får vist denne side kan derefter gennemse hver domæne, der er markeret **Ikke tilføjet** og **Ikke godkendt**. Kontrollér, at disse domæner, du bruger er blevet godkendt i Azure AD. Klik på Opdater symbolet, når du har bekræftet dit domæne.
8. Er du klar til at konfigurere skærmen, klik på **Installer**.
    - Du kan også på siden klar til at konfigurere side, du kan fjerne markeringen af afkrydsningsfeltet **starte synkroniseringen som konfigurationen er fuldført** . Du skal fjerne markeringen af dette afkrydsningsfelt, hvis du vil foretage yderligere konfiguration, som [filtrering](../active-directory-aadconnectsync-configure-filtering.md). Hvis du fjerne markeringen af denne indstilling, konfigurerer synkronisering i guiden, men lader planlæggeren deaktiveret. Det kan ikke køre, indtil du aktivere den manuelt ved at [køre installationsguiden](../active-directory-aadconnectsync-installation-wizard.md).
    - Hvis du har Exchange i din lokale Active Directory, også har du mulighed for at aktivere [**Exchange-hybridinstallation**](https://technet.microsoft.com/library/jj200581.aspx). Aktivér denne indstilling, hvis du vil have Exchange-postkasser både i skyen og lokalt på samme tid.
![Klar til at konfigurere Azure AD-forbindelse](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Når installationen er fuldført, skal du klikke på **Afslut**.
10. Når installationen er fuldført, logge af og log på igen, før du bruge synkronisering Service Manager eller synkronisering regel Editor.

## <a name="videos"></a>Videoer

Se en video om brug af hurtig installation:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## <a name="next-steps"></a>Næste trin
Nu hvor du har installeret Azure AD-forbindelse kan du [kontrollere installationen og tildele licenser](../active-directory-aadconnect-whats-next.md).

Lær mere om disse funktioner, som er blevet aktiveret til installationen: [automatisk opgradering](../active-directory-aadconnect-feature-automatic-upgrade.md), [Undgå utilsigtede sletninger](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)og [Azure AD forbinde tilstand](../active-directory-aadconnect-health-sync.md).

Få flere oplysninger om disse almindelige emner: [scheduler og hvordan du kan udløse synkronisering](../active-directory-aadconnectsync-feature-scheduler.md).

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Relateret dokumentation

Emne |  
--------- | ---------
Oversigt over Azure AD-forbindelse | [Integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md)
Installere ved hjælp af brugerdefinerede indstillinger | [Brugerdefineret installation af Azure AD-forbindelse](active-directory-aadconnect-get-started-custom.md)
Opgradering fra DirSync | [Opgradere fra Azure AD-Synkroniseringsværktøj (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Konti, der bruges til installation | [Flere oplysninger om Azure AD-forbindelse konti og tilladelser](active-directory-aadconnect-accounts-permissions.md)
