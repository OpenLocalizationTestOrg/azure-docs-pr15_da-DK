<properties
    pageTitle="Azure Active Directory-B2C: Extensible policy framework | Microsoft Azure"
    description="Et emne extensible politik rammerne af Azure Active Directory B2C og af, hvordan du opretter forskellige politiktyper af"
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

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active Directory-B2C: Extensible policy framework

## <a name="the-basics"></a>De grundlæggende funktioner

Extensible politik rammerne af Azure Active Directory (Azure AD) B2C er core styrken af tjenesten. Politikker beskriver fuldt consumer identitet oplevelser som tilmeldingsprocessen logon eller profil redigering. For eksempel kan en politik for tilmelding du kontrollere funktionsmåder ved at konfigurere følgende indstillinger:

- Kontotyper (konti til sociale som Facebook eller lokale konti som e-mail-adresse), som forbrugere kan bruge til at tilmelde sig programmet.
- Attributter (for eksempel Fornavn, postnummer og sko størrelse) skal indsamles fra forbrugerne under tilmeldingen.
- Brug af Multi-Factor Authentication.
- Det udseende og funktionalitet på alle sider, tilmelding.
- Oplysninger om (som manifester som krav i et token), programmet modtager, når politikken køre er afsluttet.

Du kan oprette flere politikker forskellige typer i din lejer og bruge dem i dine programmer efter behov. Politikker kan genbruges på tværs af programmer. Dette giver mulighed for udviklere til at definere og ændre consumer identitet oplevelser med minimale eller ingen ændringer i deres kode.

Politikker er tilgængelige til brug via en enkel udvikler-grænseflade. Dit program udløser en politik, ved hjælp af en standard HTTP-anmodningen om godkendelse (der passerer en politikparameter i anmodningen) og modtager en tilpasset token som svar. For eksempel den eneste forskel mellem anmoder om aktivering af en politik for tilmelding og dem aktivering af en politik for logon er politikkens navn bruges i "p" streng forespørgselsparameter:

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Du kan finde flere oplysninger om strukturen for politikken, denne [blogindlæg](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Opret en politik for tilmelding

Hvis du vil aktivere tilmelding på dit program, skal du oprette en politik for tilmelding. Denne politik skal skrives i denne artikel beskrives de oplevelser, forbrugere gennemgår under tilmeldingen og indholdet af tokens, som programmet får på vellykket-sig.

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **tilmelding politikker**.
3. Klik på **+ Tilføj** øverst i bladet.
4. **Navn** bestemmer tilmelding politikkens navn bruges af programmet. Angiv eksempelvis "SiUp".
5. Klik på **identitetsudbydere** , og vælg "Mail tilmelding". Du kan også kan du også vælge sociale identitetsudbydere, hvis allerede er konfigureret. Klik på **OK**.
6. Klik på **tilmelding attributter**. Her kan du vælge attributter, du vil indsamle fra forbrugerne under tilmeldingen. For eksempel vælge "Land/område", "Visningsnavnet" og "Postnummer". Klik på **OK**.
7. Klik på **program krav**. Her kan du vælge krav, der skal returneres i tokens sendes tilbage til dit program efter en vellykket tilmelding oplevelse. For eksempel vælge "Visningsnavnet", "Identitetsudbyder", "Postnummer", "Brugeren er nye" og "Brugerens objekt-ID".
8. Klik på **Opret**. Bemærk, at politikken lige har oprettet vises som "**B2C_1_SiUp**" (den **B2C\_1\_ ** fragment føjes automatisk) i bladet **tilmelding politikker** .
9. Åbn politikken ved at klikke på "**B2C_1_SiUp**".
10. Vælg "Contoso B2C app" i rullemenuen **programmer** og `https://localhost:44321/` i den **URL-adresse til svar / omdirigere URI** ned.
11. Klik på **Kør nu**. En ny fane i browseren åbnes, og du kan køre via brugeroplevelsen af tilmelding til dit program.

    > [AZURE.NOTE]
    Det tager til et minut til oprettelse af politik og opdateringer træder i kraft.

## <a name="create-a-sign-in-policy"></a>Opret en politik for logon

Hvis du vil aktivere logon på dit program, skal du oprette en logon-politik. Denne politik skal skrives i denne artikel beskrives de oplevelser, forbrugere gennemgår under logon og indholdet af tokens, som programmet får på vellykket logon.

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **Log på politikker**.
3. Klik på **+ Tilføj** øverst i bladet.
4. **Navn** bestemmer logon politikkens navn bruges af programmet. Angiv eksempelvis "SiIn".
5. Klik på **identitetsudbydere** , og vælg "Lokale konto logon". Du kan også kan du også vælge sociale identitetsudbydere, hvis allerede er konfigureret. Klik på **OK**.
6. Klik på **program krav**. Her kan du vælge krav, der skal returneres i tokens sendes tilbage til dit program efter en vellykket logonoplevelse. For eksempel vælge "Visningsnavnet", "Identitetsudbyder", "Postnummer" og "Brugers objekt-ID". Klik på **OK**.
7. Klik på **Opret**. Bemærk, at politikken lige har oprettet vises som "**B2C_1_SiIn**" (den **B2C\_1\_ ** fragment føjes automatisk) i bladet **politikker logon** .
8. Åbn politikken ved at klikke på "**B2C_1_SiIn**".
9. Vælg "Contoso B2C app" i rullemenuen **programmer** og `https://localhost:44321/` i den **URL-adresse til svar / omdirigere URI** ned.
10. Klik på **Kør nu**. En ny fane i browseren åbnes, og du kan køre via brugeroplevelsen af logger på dit program.

    > [AZURE.NOTE]
    Det tager til et minut til oprettelse af politik og opdateringer træder i kraft.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Opret en politik for tilmelding eller -logon

Denne politik håndterer begge consumer tilmelding og logon oplevelser med en enkelt konfiguration. Forbrugere ført ned højre stien (tilmelding eller logon) afhængigt af konteksten. Det beskriver også indholdet af tokens, som programmet får efter vellykket-sig eller logon.  En eksempel-kode til politikken tilmelding eller logon er [tilgængelige her](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **politikker for tilmelding eller Log på**.
3. Klik på **+ Tilføj** øverst i bladet.
4. **Navn** bestemmer tilmelding politikkens navn bruges af programmet. Angiv eksempelvis "SiUpIn".
5. Klik på **identitetsudbydere** , og vælg "Mail tilmelding". Du kan også kan du også vælge sociale identitetsudbydere, hvis allerede er konfigureret. Klik på **OK**.
6. Klik på **tilmelding attributter**. Her kan du vælge attributter, du vil indsamle fra forbrugerne under tilmeldingen. For eksempel vælge "Land/område", "Visningsnavnet" og "Postnummer". Klik på **OK**.
7. Klik på **program krav**. Her kan du vælge krav, der skal returneres i tokens sendes tilbage til dit program efter en vellykket tilmelding eller logon-oplevelse. For eksempel vælge "Visningsnavnet", "Identitetsudbyder", "Postnummer", "Brugeren er nye" og "Brugerens objekt-ID".
8. Klik på **Opret**. Bemærk, at politikken lige har oprettet vises som "**B2C_1_SiUpIn**" (den **B2C\_1\_ ** fragment føjes automatisk) i bladet **politikker for tilmelding eller Log på** .
9. Åbn politikken ved at klikke på "**B2C_1_SiUpIn**".
10. Vælg "Contoso B2C app" i rullemenuen **programmer** og `https://localhost:44321/` i den **URL-adresse til svar / omdirigere URI** ned.
11. Klik på **Kør nu**. En ny fane i browseren åbnes, og du kan køre gennem tilmelding eller logon brugeroplevelsen, som er konfigureret.

    > [AZURE.NOTE]
    Det tager til et minut til oprettelse af politik og opdateringer træder i kraft.

## <a name="create-a-profile-editing-policy"></a>Oprette en profil, redigere politik

Hvis du vil aktivere profil redigering på dit program, skal du oprette en profil, redigere politik. Denne politik skal skrives i denne artikel beskrives de oplevelser, forbrugere gennemgår under redigering af profil og indholdet af tokens, som programmet får på vellykket gennemførelse.

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **profil redigering politikker**.
3. Klik på **+ Tilføj** øverst i bladet.
4. **Navn** bestemmer den profil, redigering politikkens navn, der bruges af programmet. Angiv eksempelvis "SiPe".
5. Klik på **identitetsudbydere** , og vælg "E-mail-adresse". Du kan også kan du også markere sociale identitetsudbydere, hvis allerede konfigureret. Klik på **OK**.
6. Klik på **profil attributter**. Her kan du vælge attributter, der kan se og redigere forbrugeren. For eksempel vælge "Land/område", "Visningsnavnet" og "Postnummer". Klik på **OK**.
7. Klik på **program krav**. Her kan du vælge krav, der skal returneres i tokens sendes tilbage til dit program efter en vellykket profil redigering oplevelse. For eksempel vælge "Visningsnavnet" og "Postnummeret".
8. Klik på **Opret**. Bemærk, at politikken lige har oprettet vises som "**B2C_1_SiPe**" (den **B2C\_1\_ ** fragment føjes automatisk) i bladet **profil redigere politikker** .
9. Åbn politikken ved at klikke på "**B2C_1_SiPe**".
10. Vælg "Contoso B2C app" i rullemenuen **programmer** og `https://localhost:44321/` i den **URL-adresse til svar / omdirigere URI** ned.
11. Klik på **Kør nu**. En ny fane i browseren åbnes, og du kan køre gennem den profil, der redigerer consumer oplevelse i dit program.

    > [AZURE.NOTE]
    Det tager til et minut til oprettelse af politik og opdateringer træder i kraft.
    
## <a name="create-a-password-reset-policy"></a>Oprette en politik til nulstilling af adgangskoder

Hvis du vil aktivere detaljerede på dit program til nulstilling af adgangskode, skal du oprette en politik til nulstilling af adgangskoder. Bemærk, at nulstilling af adgangskode lejer hele indstilling, der er angivet [her](active-directory-b2c-reference-sspr.md) er stadig tilgængelig for logon politikker. Denne politik skal skrives i denne artikel beskrives de oplevelser, forbrugerne gennemgår under nulstilling af adgangskode og indholdet af tokens, som programmet får på vellykket gennemførelse.

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **politikker til nulstilling af adgangskode**.
3. Klik på **+ Tilføj** øverst i bladet.
4. **Navn** bestemmer politikkens navn, der bruges af programmet til nulstilling af adgangskode. Angiv eksempelvis "SSPR".
5. Klik på **identitetsudbydere** , og vælg "Nulstil adgangskode ved hjælp af e-mail-adresse". Klik på **OK**.
6. Klik på **program krav**. Her kan du vælge krav, der skal returneres i tokens sendes tilbage til dit program, når en vellykket nulstilling af adgangskode oplevelse. For eksempel vælge "Brugers objekt-ID".
7. Klik på **Opret**. Bemærk, at politikken lige har oprettet vises som "**B2C_1_SSPR**" (den **B2C\_1\_ ** fragment føjes automatisk) i bladet **politikker til nulstilling af adgangskode** .
8. Åbn politikken ved at klikke på "**B2C_1_SSPR**".
9. Vælg "Contoso B2C app" i rullemenuen **programmer** og `https://localhost:44321/` i den **URL-adresse til svar / omdirigere URI** ned.
10. Klik på **Kør nu**. En ny fane i browseren åbnes, og du kan køre via adgangskode Nulstil brugeroplevelsen i programmet.

    > [AZURE.NOTE]
    Det tager til et minut til oprettelse af politik og opdateringer træder i kraft.

## <a name="additional-resources"></a>Yderligere ressourcer

- [Token, session og enkelt sign-on – konfiguration](active-directory-b2c-token-session-sso.md).
