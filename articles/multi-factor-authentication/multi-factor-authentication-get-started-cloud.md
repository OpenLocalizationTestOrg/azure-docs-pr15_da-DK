<properties
    pageTitle="Komme i gang Azure MFA i skyen | Microsoft Azure"
    description="Dette er den Microsoft Azure Multi-Factor authentication side, der beskriver, hvordan du kommer i gang med Azure MFA i skyen."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introduktion til Azure Multi-Factor Authentication i skyen
I denne artikel indeholder en gennemgang Sådan Introduktion til brug af Azure Multi-Factor Authentication i skyen.

> [AZURE.NOTE]  Følgende dokumentation indeholder oplysninger om hvordan du aktiverer brugere ved hjælp af **Azure klassisk Portal**. Hvis du leder efter oplysninger om, hvordan du konfigurerer Azure Multi-Factor Authentication for O365 brugere, se [konfigurere Multi-Factor authentication til Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA i skyen](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Forudsætninger
De følgende forudsætninger der kræves, før du kan aktivere Azure Multi-Factor Authentication for dine brugere.


1. [Tilmeld dig til et abonnement, Azure](https://azure.microsoft.com/pricing/free-trial/) - Hvis du ikke allerede har et Azure-abonnement, skal du tilmelding til en. Hvis du lige fra bunden og bruger Azure MFA kan du bruge et prøveabonnement
2. [Opret en multi-Factor Auth udbyder](multi-factor-authentication-get-started-auth-provider.md) og tildele den til mappen eller [tildele licenser til brugere](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licenser der er tilgængelige for brugere, der har Azure MFA, Azure AD Premium eller Enterprise mobilitet pakke (EMS).  MFA er inkluderet i Azure AD Premium og Redigere. Hvis du har nok licenser, behøver du ikke at oprette en udbyder af Auth.


## <a name="turn-on-two-step-verification-for-users"></a>Slå totrinsbekræftelse for brugere
For at starte, der kræver to start bekræftelse på for en bruger, skal du ændre brugerens status fra deaktiveret aktiverede.  Yderligere oplysninger om bruger stater, se [Bruger tilstande i Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Brug følgende fremgangsmåde til at aktivere MFA for dine brugere.

### <a name="to-turn-on-multi-factor-authentication"></a>Slå Multi-Factor authentication

1.  Log på [Azure klassisk portalen](https://manage.windowsazure.com) som administrator.
2.  Klik på **Active Directory**i venstre side.
3.  Vælg mappen for den bruger, du vil aktivere under mappe.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Klik på **brugere**er placeret øverst.
5.  Klik på **Administrer Multi-Factor Auth**nederst på siden. Der åbnes en ny browserfane.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Find den bruger, du vil aktivere for totrinsbekræftelse. Du skal muligvis ændre visningen øverst. Sørg for, at status er **deaktiveret.** 
 ![Aktivér bruger](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Placer en **kontrollere** i feltet ud for deres navn.
7.  Klik på **Aktiver**i højre side.
![Aktivér bruger](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Klik på **Aktiver Multi-Factor auth**.
![Aktivér bruger](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Bemærk, at brugerens status er ændret fra **deaktiveret** til **aktiveret**.
![Giver brugerne mulighed for](./media/multi-factor-authentication-get-started-cloud/user.png)

Når du har aktiveret dine brugere, skal du give dem via mail. Næste gang de forsøger at logge på, bliver de bedt om at tilmelde deres konto for totrinsbekræftelse. Når de begynder at bruge totrinsbekræftelse, skal de også konfigurere appadgangskoder til at undgå at blive låst af ikke-browserapps.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Bruge PowerShell til at automatisere aktivering af totrinsbekræftelse

Hvis du vil ændre den [tilstand](multi-factor-authentication-whats-next.md) , ved hjælp af [Azure AD PowerShell](../powershell-install-configure.md), kan du bruge følgende.  Du kan ændre `$st.State` skal være lig med en af følgende tilstande:

- Aktiveret
- Tvungen
- Deaktiveret  

> [AZURE.IMPORTANT]  Vi ikke skal mod at blive flyttet brugere direkte fra tilstanden Deaktiver gennemtvunget tilstand. Ikke-browser-baseret apps stopper arbejde fordi brugeren ikke har gennemgået MFA registrering og fået en [appadgangskode](multi-factor-authentication-whats-next.md#app-passwords). Hvis du har ikke-browser-baseret apps og kræver appadgangskoder, anbefaler vi, at du går fra tilstanden deaktiveret til aktiveret. Dette gør det muligt for brugere til at registrere og få deres appadgangskoder. Når du har, kan du flytte dem til gennemtvunget.

Ved hjælp af PowerShell ville være en indstilling for flere, så brugerne. I øjeblikket er der nogen flere Aktivér funktioner i portalen Azure, og du har brug at markere de enkelte brugere enkeltvis. Det kan være helt en opgave, hvis du har mange brugere. Ved at oprette en PowerShell script ved hjælp af følgende fremgangsmåder, du kan gentage gennem en liste over brugere og aktiverer dem.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Her er et eksempel:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Yderligere oplysninger, du [bruger tilstande i Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Næste trin
Nu hvor du har konfigureret Azure Multi-Factor Authentication i skyen, kan du konfigurere og konfigurere din installation. Du kan finde flere oplysninger i [Konfigurere Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) .
