<properties
    pageTitle="Publicere apps med Azure AD-proxyen | Microsoft Azure"
    description="Udgive lokale programmer til skyen med Azure AD-proxyen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicere programmer, der bruger Azure AD-proxyen

Azure AD-proxyen hjælper dig med at understøtte remote medarbejdere ved at publicere lokale programmer for at få adgang til via internettet. Med dette punkt, skal du allerede har [aktiveret proxyen på portalen Azure klassisk](active-directory-application-proxy-enable.md). I denne artikel fører dig gennem trinnene til at udgive programmer, der kører på dit lokale netværk og giver sikker fjernadgang fra uden for dit netværk. Når du har fuldført denne artikel, er du klar til at konfigurere programmet med tilpassede oplysninger eller sikkerhedskravene.

> [AZURE.NOTE] Proxyen er en funktion, der er kun tilgængelig, hvis du har opgraderet til Premium eller Basic edition af Azure Active Directory. Du kan finde yderligere oplysninger finder [Azure Active Directory-versioner](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Publicere en app ved hjælp af guiden

1. Log på som administrator på [Azure klassisk portal](https://manage.windowsazure.com/).
2. Gå til Active Directory, og vælg den mappe, hvor du har aktiveret proxyen.

    ![Active Directory - ikon](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Klik på fanen **programmer** , og klik derefter på knappen **Tilføj** i bunden af skærmen

    ![Tilføje program](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Vælg **Publicer et program, der vil være tilgængelige i uden for dit netværk**.

    ![Udgive et program, der er tilgængelige fra uden for dit netværk](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Angiv følgende oplysninger om dit program:

    - **Navn**: et læsevenligt navn til dit program. Det skal være entydigt i mappen.
    - **Interne URL-adresse**: den adresse, som programmet Proxy forbindelsen bruges til at få adgang til programmet fra i dit private netværk. Du kan angive en bestemt sti på back end-serveren for at publicere, mens resten af serveren er ikke-udgivne. Du kan publicere forskellige steder på den samme server og give dem et nyt navn og access regler på denne måde.

        > [AZURE.TIP] Hvis du publicerer en sti, skal du kontrollere, at den indeholder alle de nødvendige billeder, scripts og typografiark for dit program. Eksempelvis hvis din app er på https://yourapp/app og bruger billeder, der findes i https://yourapp/media, skal du publicere https://yourapp/ som stien.

    - **Preauthentication metode**: hvordan proxyen kontrollerer brugere, før du giver dem adgang til dit program. Vælg en af indstillingerne i rullemenuen.

        - Azure Active Directory: Proxyen omdirigerer brugere at logge på med Azure AD, som godkender deres tilladelser for mappen, og programmet.
        - Passthrough: Brugere ikke behøver at godkende for at få adgang til programmet.

    ![Egenskaber for tjenesteprogram](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Klik på fluebenet nederst på skærmen for at fuldføre guiden. Programmet er nu defineret i Azure AD.


## <a name="assign-users-and-groups-to-the-application"></a>Tildele brugere og grupper til programmet

I rækkefølge for dine brugere at få adgang til din udgivet program, skal du tildele dem enkeltvis eller i grupper. (Husk at tildele dig selv access for). Dette kræver, at hver enkelt bruger har en licens til Azure grundlæggende eller højere. Du kan tildele licenser, enkeltvis eller grupper. Du kan finde flere oplysninger i [tildele brugere til et program](active-directory-applications-guiding-developers-assigning-users.md) . 

Dette giver tilladelse til at bruge appen til apps, der kræver forhåndsgodkendelse. Til apps, der ikke kræver forhåndsgodkendelse, kan brugerne stadig tildeles til appen, så den vises på vedkommendes liste med programmet, som Mine Apps.

1. Når du har afsluttet guiden Tilføj App, kan du se Hurtig Start-siden for dit program. For at administrere, hvem der har adgang til app, skal du vælge **brugere og grupper**.

    ![Startvejledning til programmet Proxy tildele brugere - skærmbillede](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Søge efter bestemte grupper i mappen, eller få vist alle dine brugere. For at få vist søgeresultaterne, skal du markere afkrydsningsfeltet.

    ![Søge efter grupper eller brugere - skærmbillede](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Markér hver bruger eller gruppe, du vil tildele til denne app, og klik på **Tildel**. Du bliver bedt om at bekræfte denne handling.

> [AZURE.NOTE] Integreret Windows-godkendelse Apps, skal tildele du brugere og grupper, der synkroniseres fra din lokale Active Directory. Brugere, som du logger på med en Microsoft-konto og gæster kan ikke tildeles til apps, der publiceres med Azure Active Directory-proxyen. Sørg for, at dine brugere, log på med legitimationsoplysningerne, som er del af det samme domæne som den app, du udgiver.

## <a name="test-your-published-application"></a>Teste din udgivet program

Når du har udgivet dit program, kan du teste ud af ved at navigere til den URL-adresse, du har publiceret. Sørg for, at du kan få adgang til det, at den gengiver korrekt, og, at alt fungerer som forventet. Hvis du har problemer med at eller får vist en fejlmeddelelse, kan du prøve at [fejlfindingsvejledningen](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Konfigurere dit program

Du kan ændre publicerede apps eller konfigurere avancerede indstillinger på siden Konfigurer. Du kan tilpasse din app ved at ændre navnet eller overfører et logo på denne side. Du kan også administrere adgangsregler som forhåndsgodkendelse metode eller multi-Factor authentication.

![Avanceret konfiguration](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Når du publicerer programmer ved hjælp af Azure Active Directory proxyen, de vises på listen over programmer i Azure AD, og du kan administrere dem der.

Hvis du deaktiverer proxyen services, når du har udgivet programmer, er de ikke længere tilgængelig uden for dit private netværk. Programmerne slettes ikke.

For at få vist et program, og Sørg for, at det er tilgængeligt, skal du dobbeltklikke på navnet på programmet. Hvis proxyen tjenesten er deaktiveret, og programmet er ikke tilgængelig, vises en advarsel, der vises øverst på skærmen.

Vælg et program på listen for at slette et program, og klik derefter på **Slet**.

## <a name="next-steps"></a>Næste trin

- [Publicere programmer, der bruger dit eget domænenavn](active-directory-application-proxy-custom-domains.md)
- [Aktivere enkelt Log på](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivere betinget adgang](active-directory-application-proxy-conditional-access.md)
- [Arbejde med krav opmærksom på programmer](active-directory-application-proxy-claims-aware-apps.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer
