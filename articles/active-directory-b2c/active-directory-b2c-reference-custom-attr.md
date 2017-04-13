<properties
    pageTitle="Azure Active Directory-B2C: Brugerdefinerede attributter | Microsoft Azure"
    description="Sådan bruges brugerdefinerede attributter i Azure Active Directory B2C for at indsamle oplysninger om dine brugere"
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
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory-B2C: Bruge brugerdefinerede attributter til at indsamle oplysninger om dine brugere

Azure Active Directory (Azure AD) B2C mappen leveres med et indbygget sæt af oplysninger (attributter): Fornavn, efternavn, by, postnummer og andre attributter. Alle med adgang til forbrugere programmerne har dog entydige krav på hvad attributter til at indsamle fra forbrugere. Du kan udvide sæt af egenskaber, der er gemt på hver enkelt consumer konto med Azure AD B2C. Du kan oprette brugerdefinerede attributter på [Azure portal](https://portal.azure.com/) og bruge det i din tilmelding politikker, som vist nedenfor. Du kan også læse og skrive følgende attributter ved hjælp af [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
[Azure AD Graph API Directory skema filtypenavnene](https://msdn.microsoft.com/library/azure/dn720459.aspx)bruges brugerdefinerede attributter.

## <a name="create-a-custom-attribute"></a>Oprette en brugerdefineret attribut

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **brugerattributter**.
3. Klik på **+ Tilføj** øverst i bladet.
4. Angiv et **navn** til den brugerdefinerede attribut (eksempelvis "ShoeSize") og eventuelt en **Beskrivelse**. Klik på **Opret**.

    > [AZURE.NOTE]
    Der findes i øjeblikket kun "Streng" **Datatype** .

Den brugerdefinerede attribut er nu tilgængelig på listen over **brugerattributter**og til brug i din tilmelding politikker.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Bruge en brugerdefineret attribut i din tilmelding politik

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **tilmelding politikker**.
3. Klik på din tilmelding politik (eksempelvis "B2C_1_SiUp") for at åbne den. Klik på **Rediger** på øverst del af bladet.
4. Klik på **tilmelding attributter** , og vælg den brugerdefinerede attribut (eksempelvis "ShoeSize"). Klik på **OK**.
5. Klik på **program krav** , og vælg den brugerdefinerede attribut. Klik på **OK**.
6. Klik på **Gem** øverst på bladet.

Du kan bruge funktionen "Kør nu" på politikken for at bekræfte brugeroplevelsen. Du bør nu se "ShoeSize" på listen over attributter, der indsamles i forbindelse med forbrugere tilmelding, og se den i tokenet sendes tilbage til dit program.

## <a name="notes"></a>Noter

- Sammen med tilmelding politikker, kan brugerdefinerede attributter også bruges i tilmelding eller logon politikker og redigere politikker-profil.
- Der er et kendt begrænsning af brugerdefinerede attributter. Det er kun oprettet første gang det bruges i en hvilken som helst politik og ikke, når du føjer den til listen over **brugerattributter**.
