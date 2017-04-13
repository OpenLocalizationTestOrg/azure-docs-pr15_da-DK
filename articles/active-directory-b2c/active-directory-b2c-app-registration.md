<properties
    pageTitle="Azure Active Directory-B2C: Programmet registrering | Microsoft Azure"
    description="Hvordan du kan registrere dit program med Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory-B2C: Registrere dit program

## <a name="prerequisite"></a>Nødvendige program

Hvis du vil oprette et program, der accepterer forbrugerne tilmelding og -logon, skal du først registrere programmet med en Azure Active Directory B2C lejer. Få din egen lejer ved hjælp af trinnene beskrevet i [oprette en Azure AD B2C lejer](active-directory-b2c-get-started.md). Når du har fulgt trinnene i denne artikel, har du B2C funktioner blade fastgjort til din Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Gå til bladet B2C funktioner

Hvis du har B2C funktioner blade fastgjort til din Startboard, ser du bladet, så snart du logger på [Azure-portalen](https://portal.azure.com/) som den globale Administrator af B2C lejeren.

Du kan også få adgang til bladet ved at klikke på **Gennemse** , og klik derefter på **Azure AD B2C** i den venstre navigationsrude på [Azure-portalen](https://portal.azure.com/).

> [AZURE.IMPORTANT] Du skal være Global Administrator i B2C lejeren skal kunne få adgang til bladet B2C funktioner. En Global Administrator fra en hvilken som helst anden lejer eller en bruger fra en hvilken som helst lejeradministration kan ikke åbne den.  Du kan skifte til din B2C lejer ved hjælp af lejer omskifter i det øverste højre hjørne af portalen Azure.

## <a name="register-an-application"></a>Registrere et program

1. Klik på **programmer**bladet B2C funktioner på Azure-portalen.
2. Klik på **+ Tilføj** øverst i bladet.
3. Angiv et **navn** til det program, som beskriver dit program til forbrugere. For eksempel kan du angive "Contoso B2C app".
4. Hvis du skriver en webbaseret program, kan du slå indstillingen **Medtag online / web API** til **Ja**. **Svar URL-adresser** er slutpunkter hvor Azure AD B2C vil returnere en hvilken som helst tokens, der anmoder om dit program. For eksempel angive `https://localhost:44321/`. Hvis dit webprogram kan også ringe nogle web API sikret med Azure AD B2C, får du vil oprette et **Program hemmeligt** samt ved at klikke på knappen **Generer nøgle** .

    > [AZURE.NOTE] Et **Program hemmeligt** er et vigtigt legitimationsoplysninger, og skal være fastgjort korrekt.

5. Hvis du skriver en mobil-program, kan du slå indstillingen **Medtag oprindelig klient** til **Ja**. Kopiere hele vejen ned standard **Omdirigere URI** , der oprettes automatisk for dig.
6. Klik på **Opret** for at registrere dit program.
7. Klik på det program, du lige har oprettet, og Kopiér ned globalt entydige **ID for klient** , som du skal bruge senere i din kode.

> [AZURE.IMPORTANT] Programmer, der er oprettet i bladet B2C funktioner skal administreres i den samme placering. Hvis du redigerer B2C programmer ved hjælp af PowerShell eller en anden portal de bliver ikke-understøttede og sandsynligvis virker ikke med Azure AD B2C.

## <a name="build-a-quick-start-application"></a>Opbygge et program i Hurtig Start

Nu hvor du har et program, der er registreret med Azure AD B2C, kan du udføre én af vores Hurtig start selvstudier til kommer i gang. Her er nogle anbefalinger:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
