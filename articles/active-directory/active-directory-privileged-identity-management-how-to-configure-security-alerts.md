<properties
   pageTitle="Sådan konfigureres sikkerhedsadvarsler | Microsoft Azure"
   description="Lær, hvordan du konfigurerer sikkerhedsadvarsler til Azure privilegerede identitet Management filtypenavn."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Sådan konfigureres sikkerhedsadvarsler i Azure AD privilegerede identitet administration

## <a name="security-alerts"></a>Sikkerhedsadvarsler
Azure privilegerede identitet Management (PIM) genererer beskeder, når der er mistænkelige eller usikre aktivitet i dit miljø. Når en besked udløses, vises det på dashboardet PIM. Vælg påmindelsen for at se en rapport, der viser de brugere eller roller, der udløste påmindelsen.

![PIM dashboard sikkerhedsadvarsler - skærmbillede][1]



| Giv besked | Udløser | Anbefaling |
| ----- | ------- | -------------- |
| **Roller tildeles uden for PIM** | En administrator blev permanent tildelt til en rolle, uden for grænsefladen PIM. | Gennemse den nye rolletildeling. Da andre tjenester kan kun tildele permanent administratorer, skal du ændre det til et gyldigt tildeling, hvis det er nødvendigt. |
| **Roller er aktiveret for ofte** | Der er for mange genaktiveringer af den samme rolle inden for den tid, der er tilladt i indstillingerne. | Kontakte bruger for at se, hvorfor de har aktiveret rollen så mange gange. Måske er tidsrum, der er for kort til dem til at udføre deres opgaver, eller måske de bruger scripts til at aktivere automatisk en rolle. |
| **Roller kræver ikke godkendelse i flere niveauer for aktivering** | Der findes roller uden MFA er aktiveret i indstillingerne. | Vi kræver MFA for de mest meget privilegerede roller, men anbefaler på det kraftigste, at du aktiverer MFA for aktivering af alle roller. |
| **Administratorer bruger ikke deres privilegerede roller** | Der er berettiget administratorer, der ikke har aktiveret deres roller for nylig. | Start en access-Gennemse for at finde ud af de brugere, der ikke længere har brug for access. |
| **Der er for mange globale administratorer** | Der er flere globale administratorer end anbefales. | Hvis du har et stort antal globale administratorer, er det sandsynligvis, at brugere får flere tilladelser end de skal bruge. Flytte brugere til mindre privilegerede roller, eller Sørg nogle af dem berettiget til rollen i stedet for permanent tildelt. |

## <a name="configure-security-alert-settings"></a>Konfigurere indstillinger for sikkerhedsadvarsler

Du kan tilpasse nogle af sikkerhedsadvarsler i PIM til at fungere sammen med dit miljø og sikkerhedsmål. Følg disse trin for at få bladet indstillinger:

1. Logge på [Azure-portalen](https://portal.azure.com/) , og Vælg feltet **Azure AD privilegerede identitet administration** dashboard.
2. Vælg **administrerede privilegerede roller** > **Indstillinger** > **Indstillinger for beskeder**.

    ![Gå til indstillingerne for beskeder][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>"Roller der aktiveres for ofte" besked

Denne besked udløser, hvis en bruger aktiverer den samme privilegerede rolle flere gange i en bestemt periode. Du kan konfigurere både tidsperioden og antallet aktiveringer.

- **Aktivering fornyelse tidsramme**: angive i dage, timer, minutter og anden den tidsperiode, du vil bruge til at spore mistænkelige fornyelser.

- **Antallet af aktivering fornyelser**: angive antallet aktiveringer fra 2 til 100, som du kan også værd at sikkerhedsmeddelelsen inden for den tidsramme, du vælger. Du kan ændre denne indstilling ved at flytte skyderen eller skrive et tal i tekstfeltet.


### <a name="there-are-too-many-global-administrators-alert"></a>"Der er for mange globale administratorer" besked

PIM udløser denne besked, hvis to forskellige kriterier er opfyldt, og du kan konfigurere dem begge. Du skal først, når en bestemt grænse af globale administratorer. Andet kan være en bestemt procentdel af dit samlede rolletildelinger globale administratorer. Hvis du kun opfylder en af disse målinger, vises beskeden ikke.  

- **Minimum antal globale administratorer**: Angiv antallet af globale administratorer, fra 2 til 100, at du overvejer at beløbet usikre.

- **Procentdel af globale administratorer**: angive procentdelen af administratorer, der er globale administratorer, fra 0% til 100%, altså usikre i dit miljø.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>"Administratorer ikke bruger deres privilegerede roller" besked

Denne besked udløser, hvis en bruger skifter et bestemt tidsrum uden at aktivere en rolle.

- **Antallet af dage**: Angiv antallet af dage fra 0 til 100, som en bruger kan gå uden at aktivere en rolle.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
