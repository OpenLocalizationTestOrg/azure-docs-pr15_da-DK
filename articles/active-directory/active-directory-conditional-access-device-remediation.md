<properties
    pageTitle="Fejlfinding af Azure Active Directory adgangsproblemer | Microsoft Azure"
    description="Lær trin, du kan udføre for at løse problemer med adgang til din organisations onlineressourcer."
    services="active-directory"
    keywords="enhed-baserede betinget adgang, enhed registrering, aktivere registrering af enhed, enhed registrering og MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Fejlfinding af Azure Active Directory adgangsproblemer

Du forsøger at få adgang til din organisations SharePoint Online intranet, og du får fejlmeddelelsen "adgang nægtet". Hvad laver du?

I denne artikel beskrives afhjælpning trin, der kan hjælpe dig med at løse adgangsproblemer med organisationens onlineressourcer.

Hjælp til løsning af Azure Active Directory (Azure AD) få adgang til problemer, skal du gå til sektionen i artiklen, der dækker platform din enhed:

-   Windows-enhed
-   iOS-enhed (se tilbage snart for at få hjælp med iPhones og iPads.)
-   Android-enhed (Kig forbi snart for at få hjælp med Android-telefoner og tablets.)

## <a name="access-from-a-windows-device"></a>Adgang fra en Windows-enhed

Hvis enheden kører en af følgende platforme, kan du se i de næste afsnit for fejl, der vises, når du forsøger at få adgang til et program eller en tjeneste:

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Enheden er ikke registreret

Hvis enheden ikke er registreret med Azure AD og programmet er beskyttet med en enhed-baseret politik, kan du muligvis vist en side, der viser en af disse fejlmeddelelser:

!["Du kan komme dertil fra her" meddelelser til ikke-registrerede enheder] (./media/active-directory-conditional-access-device-remediation/01.png "Scenarie")

Hvis enheden er medlem af et domæne til Active Directory i organisationen, kan du prøve følgende:

1.  Sørg for, at du logger på Windows ved hjælp af din arbejdskonto (Active Directory-konto).
2.  Oprette forbindelse til virksomhedens netværk via et virtuelt privat netværk (VPN) eller DirectAccess, som sørger.
3.  Når du har forbindelse, kan du trykke på Windows-logotasten + L-tasten til at låse din Windows-session.
4.  Angiv dine legitimationsoplysninger til arbejde konto for at låse op din Windows-session.
5.  Vent et minut, og prøv derefter at få adgang til programmet eller tjenesten igen.
6.  Hvis du får vist den samme side, skal du klikke på linket **Få mere at vide** , og derefter kontakte din administrator med oplysninger.

Hvis enheden ikke er medlem af et domæne og kører Windows 10, har du to muligheder:

- Køre Azure AD-joinforbindelse
- Føje din arbejds- eller skolekonto til Windows

Du kan finde oplysninger om, hvordan disse indstillinger er forskellige, [ved hjælp af Windows 10-enheder på din arbejdsplads](active-directory-azureadjoin-windows10-devices.md).

Køre Azure AD deltage i, skal du udføre følgende trin til platformen enheden kører på. (Azure AD-joinforbindelse er ikke tilgængelig på Windows-telefoner).

**Windows 10 Mærkedag Update**

1.  Åbn appen **Indstillinger** .
2.  Klik på **konti** > **Access arbejdet eller i skolen**.
3.  Klik på **Opret forbindelse**.
4.  Klik på **Deltag i denne enhed til Azure AD**.
5.  Godkende for din organisation kan du angive Multi-Factor godkendelse, hvis du bliver bedt om, og følg derefter de trin, der vises.
6.  Log af, og derefter logge på med din arbejdskonto.
7.  Prøv at åbne programmet igen.


**Windows 10 November 2015 Update**

1.  Åbn appen **Indstillinger** .
2.  Klik på **System** > **om**.
3.  Klik på **Deltag i Azure AD**.
4.  Godkende for din organisation kan du angive Multi-Factor godkendelse, hvis du bliver bedt om, og følg derefter de trin, der vises.
5.  Log af, og derefter logge på med din arbejds-konto (Azure AD-konto).
6.  Prøv at åbne programmet igen.

For at tilføje kontoen arbejds- eller skolekonto, skal du gøre følgende trin:

**Windows 10 Mærkedag Update**

1.  Åbn appen **Indstillinger** .
2.  Klik på **konti** > **Access arbejdet eller i skolen**.
3.  Klik på **Opret forbindelse**.
4.  Godkende for din organisation kan du angive Multi-Factor godkendelse, hvis du bliver bedt om, og følg derefter de trin, der vises.
5.  Prøv at åbne programmet igen.


**Windows 10 November 2015 Update**

1.  Åbn appen **Indstillinger** .
2.  Klik på **konti** > **dine konti**.
3.  Klik på **Tilføj arbejds- eller skolekonto**.
4.  Godkende for din organisation kan du angive Multi-Factor godkendelse, hvis du bliver bedt om, og følg derefter de trin, der vises.
5.  Prøv at åbne programmet igen.

Hvis enheden ikke er medlem af et domæne og kører Windows 8.1, hvis du vil gøre et arbejdsområde, deltage i og tilmelde dig Microsoft Intune, kan du gøre følgende:

1.  Åbn **PC-indstillinger**.
2.  Klik på **netværk** > **på arbejdspladsen**.
3.  Klik på **Deltag i**.
4.  Godkende for din organisation kan du angive Multi-Factor godkendelse, hvis du bliver bedt om, og følg derefter de trin, der vises.
5.  Klik på **Slå**.
6.  Prøv at åbne programmet igen.


### <a name="browser-is-not-supported"></a>Browser understøttes ikke

Du kan blive nægtet adgang, hvis du forsøger at få adgang til et program eller en tjeneste ved hjælp af følgende browsere:

- Chrome, Firefox eller andre browsere end Microsoft Edge eller Microsoft Internet Explorer i Windows 10 eller Windows Server 2016
- Firefox i Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2

Du får vist en fejlside, som ser sådan ud:

!["Du kan komme dertil fra her" meddelelse for ikke-understøttede browsere] (./media/active-directory-conditional-access-device-remediation/02.png "Scenarie")

Kun afhjælpning er at bruge en browser, der understøtter programmet til din enhed platform.

## <a name="next-steps"></a>Næste trin

[Azure Active Directory-betinget adgang](active-directory-conditional-access.md)
