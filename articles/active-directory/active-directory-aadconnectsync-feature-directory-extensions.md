<properties
   pageTitle="Azure AD-forbindelse synkronisering: Directory filtypenavne | Microsoft Azure"
   description="Dette emne beskrives funktionen directory filtypenavne i Azure AD-forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD-forbindelse synkronisering: Directory filtypenavne
Directory filtypenavne gør det muligt at udvide skemaet på Azure AD med dine egne attributter fra lokale Active Directory. Denne funktion giver dig mulighed at opbygge LOB apps forbrug attributter, du fortsat administrere lokale. Følgende attributter kan blive brugt via [Azure AD Graph directory filtypenavne](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) eller [Microsoft Graph](https://graph.microsoft.io/). Du kan se de attributter, der er tilgængelige med [Azure AD Graph explorer](https://graphexplorer.cloudapp.net) og [Microsoft Graph explorer](https://graphexplorer2.azurewebsites.net/) henholdsvis.

Ingen Office 365 arbejdsbelastningen bruger disse attributter på nuværende tidspunkt.

Du konfigurere, hvilke yderligere attributter, du vil synkronisere i brugerdefinerede indstillinger stien i installationsguiden.
![Skema lokalnummer guiden](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) installationen viser følgende attributter, som er gyldige kandidater:

- Bruger- og objekttyper
- Enkeltværdi-attributter: streng, boolesk, heltal, binært tal
- Med flere valgmuligheder attributter: streng, binært tal

Listen over attributter læses fra cachen oprettes under installationen af Azure AD-forbindelse. Hvis du har udvidet Active Directory-skemaet med flere attributter, den [skal opdateres skema](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) før disse nye attributter er synlige.

Et objekt kan have op til 100 directory filtypenavne attributter. Den maksimale længde er 250 tegn. Hvis en værdi for attributten er længere, afkortes argumentet af synkronisering af programmet.

Under installationen af Azure AD-forbindelse, er et program registreret hvor disse attributter er tilgængelige. Du kan se dette program i portalen Azure.  
![Skema lokalnummer App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Følgende attributter er nu tilgængelig via Graph:  
![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Attributterne er præfikset lokalnummer\_{AppClientId}\_. AppClientId har den samme værdi for alle attributter i Azure AD-biblioteket.

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
