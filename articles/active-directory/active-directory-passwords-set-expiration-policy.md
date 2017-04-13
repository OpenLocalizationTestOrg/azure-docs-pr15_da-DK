<properties
    pageTitle="Angive udløbspolitikker for i Azure Active Directory | Microsoft Azure"
    description="Lær, hvordan du kan tjekke udløbspolitikker for og ændre bruger udløb af adgangskoder, enten enkeltvis eller flere ad gangen til Azure Active directory-adgangskoder"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Angive adgangskode udløbspolitikker for i Azure Active Directory

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

Som en global administrator for en skybaseret Microsoft-tjeneste, kan du bruge Microsoft Azure Active Directory-modulet til Windows PowerShell til at konfigurere brugeradgangskoder ikke til at udløbe. Du kan også bruge Windows PowerShell-cmdlet'er til at fjerne den-udløber aldrig konfiguration, eller for at se, hvilken bruger adgangskoder er konfigureret ikke til at udløbe. I denne artikel indeholder hjælp til skytjenester, som Microsoft Intune og Office 365, der er afhængige af Microsoft Azure Active Directory for identitets-og mappe.

  > [AZURE.NOTE] Kun adgangskoder for brugerkonti, ikke som synkroniseres via katalogsynkronisering kan konfigureres til ikke for at udløbe. Du kan finde flere oplysninger om katalogsynkronisering, på listen over emner i [Oversigt over mappesynkronisering](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Hvis du vil bruge Windows PowerShell-cmdletter, skal installere du først dem.

## <a name="what-do-you-want-to-do"></a>Hvad vil du gøre?

- [Sådan kontrolleres Udløbspolitikken for en adgangskode](#how-to-check-expiration-policy-for-a-password)

- [Angive en adgangskode til at udløbe](#set-a-password-to-expire)

- [Angive en adgangskode, så den ikke udløber](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Sådan kontrolleres Udløbspolitikken for en adgangskode

1.  Oprette forbindelse til Windows PowerShell ved hjælp af legitimationsoplysninger for administrator din virksomhed.

2.  Gør et af følgende:

    - Hvis du vil se, om en enkelt brugers adgangskode er konfigureret til aldrig at udløbe, skal du køre følgende cmdlet ved hjælp af brugerens hovednavn (UPN) (for eksempel aprilr@contoso.onmicrosoft.com) eller bruger-ID på den bruger, du vil kontrollere:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - Hvis du vil se indstillingen "Adgangskoden udløber aldrig" for alle brugere, skal du køre følgende cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Angive en adgangskode til at udløbe

1.  Oprette forbindelse til Windows PowerShell ved hjælp af legitimationsoplysninger for administrator din virksomhed.

2.  Gør et af følgende:

    - Hvis du vil konfigurere adgangskoden for én bruger, så adgangskoden udløber, skal du køre følgende cmdlet ved hjælp af brugerens hovednavn (UPN) eller brugeren bruger-ID:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - Hvis du vil konfigurere adgangskoderne for alle brugere i organisationen, så de udløber, skal du bruge følgende cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Angive en adgangskode til aldrig at udløbe

1. Oprette forbindelse til Windows PowerShell ved hjælp af legitimationsoplysninger for administrator din virksomhed.

2.  Gør et af følgende:

    - For at angive adgangskoden for én bruger til aldrig at udløbe, skal du køre følgende cmdlet ved hjælp af brugerens hovednavn (UPN) eller brugeren bruger-ID:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - Hvis du vil konfigurere adgangskoderne for alle brugere i en organisation til aldrig at udløbe skal du køre følgende cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Næste trin

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
