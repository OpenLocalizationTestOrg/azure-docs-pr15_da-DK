<properties
    pageTitle="Angive enhed-baserede betinget access-politik for Azure Active Directory-kompatible programmer | Microsoft Azure"
    description="Indstille enhed-baserede betinget access politikker til Azure AD-forbindelse programmer."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Angive enhed-baserede betinget access-politik for Azure Active Directory-kompatible programmer


Azure Active Directory (Azure AD) enhed-baserede betinget access kan hjælpe dig med at beskytte organisationsressourcer fra:

- Access forsøger fra ukendt eller ikke-administreret enheder.
- Enheder, der ikke opfylder sikkerhedspolitikker for din organisation.

Du kan finde en oversigt over betinget adgang, [betinget Azure Active Directory-adgang](active-directory-conditional-access.md).

Du kan angive politikker for enhed-baserede betinget access til at beskytte disse programmer:

- Office 365 SharePoint Online, til at beskytte din organisations websteder og dokumenter
- Office 365 Exchange Online, til at beskytte din organisations mail
- Software som en tjenesteprogrammer (SaaS), der er forbundet til Azure AD til godkendelse
- Lokale programmer, der er publiceret ved hjælp af Azure AD-proxyen services

For at angive en enhed-baserede betinget access-politik i portalen Azure, skal du gå til bestemte programmet i kataloget.


  ![Listen over programmer i mappen Azure portalen] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Programmer")


Vælg programmet, og klik derefter på fanen **Konfigurer** for at angive betinget access-politikken.  


  ![Konfigurer programmet] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Enhed baseret adgangsregler")




Hvis du vil angive en enhed-baserede betinget access-politik i sektionen **enhed baseret adgangsregler** i **Aktivere adgangsregler**, Vælg **på**.

En enhed-baserede betinget access-politik består af tre komponenter:

- **Anvend på**. Omfanget af brugere, som politikken anvendes på.

- **Regler for enhed**. Betingelserne, en enhed skal opfylde, før det kan få adgang til programmet.

- **Aktivering af programmet**. Klientprogrammer (oprindeligt kontra browser) politikken anvendes på.

  ![De tre komponenter i en enhed-baseret access-politik] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Enhed baseret adgangsregler")


## <a name="select-the-users-the-policy-applies-to"></a>Vælg de brugere, som politikken anvendes på

Du kan vælge omfanget af denne politik gælder for brugere i sektionen **Anvend på** .

Du har to muligheder, når du opretter en access politikken scope for brugere:

- **Alle brugere**. Anvend politikken for alle brugere adgang til programmet.

- **Grupper**. Begrænse politikken til brugere, som er medlem af en bestemt gruppe.

![Anvend politik for alle brugere eller til en gruppe] (./media/active-directory-conditional-access-policy-connected-applications/11.png "Gælder for")


 Hvis en bruger fra en politik, skal du markere afkrydsningsfeltet **undtagen** . Dette er nyttigt, når du har brug at give tilladelser til en bestemt bruger midlertidigt adgang til programmet. Vælg denne indstilling, f.eks, hvis nogle af dine brugere har enheder, der ikke er klar til betinget adgang. Enhederne muligvis ikke være registreret, eller de er ved at være væk overholdelse af angivne standarder.


## <a name="select-the-conditions-that-devices-must-meet"></a>Vælg de betingelser, som skal opfylde enheder

Brug **Enhed regler** til at angive betingelserne en enhed skal opfylde for at få adgang til programmet.

Du kan angive enhed-baserede betinget adgang for disse filtyper enhed:

- Windows 10 Mærkedag Update, Windows 8.1 og Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 og Windows Server 2008 R2
- iOS-enheder (iPad, iPhone)
- Android-enheder

Understøttelse af Mac kommer snart.

  ![Anvend politik til enheder] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Programmer")

 >[AZURE.NOTE] Du kan finde oplysninger om forskellene mellem medlem af et domæne og Azure AD-joinforbundne enheder [ved hjælp af Windows 10-enheder på din arbejdsplads](active-directory-azureadjoin-windows10-devices.md).

Har du to muligheder for enhed regler:

- **Alle enheder skal være kompatible**. Alle Enhedsplatforme, få adgang til programmet skal være kompatible. Enheder, der kører på platforme, der ikke understøtter enhed-baserede betinget adgang er nægtet adgang.

- **Kun valgte enheder skal være kompatible**. Kun bestemte Enhedsplatforme skal være kompatible. Andre platforme eller andre platforme, der kan få adgang til programmet, du har adgang.

  ![Angive omfanget for enhed regler] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Programmer")

Azure AD-joinforbundne enheder er kompatible, hvis de er markeret som **kompatibel** i kataloget ved Intune eller en tredjepart mobilenhed administrationssystem, der integreres med Azure AD.

En enhed, der er medlem af et domæne er kompatibel Hvis:

- Det er registreret med Azure AD. Mange organisationer behandler medlem af et domæne enheder som der er tillid til enheder.
- Det er markeret som **kompatibel** i Azure AD af System Center Configuration Manager 2016.

 ![Medlem af et domæne enheder, der er kompatible] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Regler for enhed")

Windows personlige enheder er kompatible, hvis de er markeret som **kompatibel** i kataloget ved Intune eller en tredjepart mobilenhed administrationssystem, der integreres med Azure AD.

Ikke-Windows enheder er kompatible, hvis de er markeret som **kompatibel** i mappen med Intune.

 >[AZURE.NOTE] Du kan finde yderligere oplysninger om, hvordan du konfigurerer Azure AD til overholdelse af enhed i forskellige systemer [betinget Azure Active Directory-adgang](active-directory-conditional-access.md).


Du kan vælge en eller flere Enhedsplatforme til en enhed-baseret access-politik. Dette omfatter Android, iOS, Windows Mobile (Windows 8.1 telefoner og tablets) og Windows (alle andre Windows enheder, herunder alle Windows 10-enheder).
Evaluering af politikker opstår kun på de valgte platforme. Hvis en enhed, der forsøger at få adgang ikke kører en af de valgte platforme, kan enheden har adgang til programmet, hvis brugeren har adgang. Ingen enhed politik evalueres.

![Vælg platforme for enhed regler] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Regler for enhed")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Angive evaluering af politikker for en type af programmet

Angiv typen programmer vil evaluere politikken for bruger eller enhed adgang i sektionen **Programmet aktivering** .

Har du to muligheder for typen program tilladelse til at medtage:

- Browser og oprindelige programmer
- Oprindelige programmer

![Vælg browser eller oprindelige programmer] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Programmer")

For at gennemtvinge access-politik for programmer, skal du vælge **For browseren og oprindelige programmer**. Derefter kan du medtage:

- Browsere (for eksempel Microsoft Edge i Windows 10) eller Safari i iOS.
- Programmer, der bruger Active Directory Authentication Library (ADAL) på en hvilken som helst platform, eller, der bruger WebAccountManager (WAM) API i Windows 10.

>[AZURE.NOTE] Du kan finde flere oplysninger om understøttelse af browsere og andre overvejelser for en bruger, der har adgang til en enhed-baseret, certifikat nøglecenter beskyttet-program, [betinget Azure Active Directory-adgang](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Beskytte mail access fra Exchange ActiveSync-baserede programmer

I Office 365 Exchange Online-programmer, kan du bruge Exchange ActiveSync blokere mail adgang til Exchange ActiveSync-baseret mail-programmer.

![Indstillinger for Exchange ActiveSync-kompatibilitet] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Programmer")

![Kræver en kompatibel enhed for at få adgang til mail] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Programmer")


## <a name="next-steps"></a>Næste trin

- [Azure Active Directory-betinget adgang](active-directory-conditional-access.md)
