<properties
    pageTitle="Sådan får du en Azure AD-lejer | Microsoft Azure"
    description="Sådan får du en Azure Active Directory-lejer til registrering og opbygning af programmer."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Sådan får du en Azure Active Directory-lejer

I Azure Active Directory (Azure AD) er en [lejer](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) repræsentant for en organisation.  Det er en dedikeret forekomst af den Azure AD-tjeneste, som en organisation modtager og ejer, når den tilmelder sig en skybaseret Microsoft-tjeneste som Azure, Microsoft Intune eller Office 365.  Hver Azure AD-lejer er væsentligt forskellige og adskilt fra andre Azure AD-lejere.  

En lejer indeholder brugere i en virksomhed og oplysningerne om dem - deres adgangskoder, profil brugerdata, tilladelser og osv.  Den indeholder også grupper, programmer og andre oplysninger, der vedrører en organisation og dens sikkerhed.

Hvis du vil tillade Azure AD-brugere til at logge på dit program, skal du registrere dit program i en lejer af dine egne.  Hvis du publicerer et program i en Azure AD-lejer er **absolut gratis**.  Faktisk opretter de fleste udviklere flere lejere og programmer til forsøg, udvikling, arrangere og testformål.  Organisationer, hvor du tilmelder dig og forbruge dit program kan eventuelt vælge at købe licenser, hvis de ønsker at drage fordel af avancerede directory-funktioner.

Så hvordan går om at få en Azure AD-lejer?  Processen kan være et lille forskellige, hvis du:

- [Har et eksisterende Office 365-abonnement](#use-an-existing-office-365-subscription)
- [Har et eksisterende Azure-abonnement der er knyttet til en Microsoft-Account](#use-an-msa-azure-subscription)
- [Har et eksisterende Azure-abonnement der er knyttet til en organisationskonto](#use-an-organizational-azure-subscription)
- [Ingen af ovenstående og vil starte fra bunden](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Brug en eksisterende Office 365-abonnement
Hvis du har et eksisterende Office 365-abonnement, har du allerede en Azure AD-lejer! Du kan logge på [Azure-portalen](https://portal.azure.com) med din O365-konto og begynde at bruge Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Bruge et MSA Azure-abonnement
Hvis du har tidligere har tilmeldt sig til et Azure-abonnement med Account individuelle Microsoft, har du allerede en lejer!  Når du logger på [Azure-portalen](https://portal.azure.com), vil du automatisk være logget på din standard-lejer. Du kan frit bruge denne lejer efter behov -, men du overveje at oprette en organisatoriske administratorkonto.

Gør du ved at følge disse trin.  Alternativt kan du oprette en ny lejer og oprette en administrator i lejeren følge en lignende proces.

1.  Log på [Azure-portalen](https://portal.azure.com) med din individuelle konto
2.  Gå til afsnittet "Azure Active Directory" i portalen (findes i den venstre navigationslinje under **Flere tjenester**)
3.  Du bør automatisk være logget på den "standard mappe", hvis du ikke kan du skifte mapper ved at klikke på navnet på din konto i øverste højre hjørne.
4.  Vælg **Tilføj en bruger**fra sektionen **Hurtige opgaver** .
5.  Angiv følgende oplysninger i formularen Tilføj bruger:

    - Navn: (Vælg en passende værdi)
    - Brugernavn: (Vælg et brugernavn for denne-administrator)
    - Profil: (Udfyld de relevante værdier for fornavn, sidste navn, stilling og afdeling)
    - Rolle: Global Administrator

6.  Når du har fuldført formularen bruger og modtager den midlertidige adgangskode til den nye administrative bruger, skal du sørge for at registrere denne adgangskode, som du skal logge på med den nye bruger for at ændre adgangskoden. Du kan også sende adgangskoden direkte til den bruger, der anvender en alternativ mail.
7.  Klik på **Opret** for at oprette den nye bruger.
8.  Log på [https://login.microsoftonline.com](https://login.microsoftonline.com) med den nye konto for at ændre den midlertidige adgangskode, og ændre adgangskoden når der anmodes om.


## <a name="use-an-organizational-azure-subscription"></a>Bruge et organisatorisk Azure-abonnement
Hvis du har tidligere har tilmeldt sig til et Azure-abonnement med din virksomhedskonto, har du allerede en lejer!  [Azure-portalen](https://portal.azure.com), skal du finde en lejer, når du navigerer til "Flere tjenester" og "Azure Active Directory."  Du kan frit at bruge denne lejer efter behov. 


## <a name="start-from-scratch"></a>Starte fra bunden
Hvis alle ovenstående er volapyk for dig, ikke bekymre dig.  Bare gå [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) til at tilmelde sig Azure med en ny organisation.  Når du har fuldført processen, har du din helt egen Azure AD-lejer med navnet på det domæne, du har valgt under Log af.  Du kan finde din lejer på [Azure-portalen](https://portal.azure.com)ved at navigere til "Azure Active Directory" i venstre navigation fra.

Som en del af processen med at tilmelde sig Azure, skal du angive kreditkortoplysninger.  Du kan fortsætte uden bekymringer – skal du ikke betale for udgive programmer i Azure AD eller oprette nye lejere.
