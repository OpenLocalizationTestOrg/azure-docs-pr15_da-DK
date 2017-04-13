<properties
    pageTitle="Azure Active Directory-B2C: Page UI tilpasning hjælper værktøjet | Microsoft Azure"
    description="En hjælper værktøj, der bruges til at vise, funktionen siden Brugergrænsefladen tilpasning i Azure Active Directory B2C"
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
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory-B2C: En hjælper værktøj, der bruges til at vise, funktionen siden Tilpasning af brugergrænsefladen

I denne artikel er en companion til den [primære brugergrænseflade tilpasning artikel](active-directory-b2c-reference-ui-customization.md) i Azure Active Directory (Azure AD) B2C. Følgende trin beskriver, hvordan du øvelse funktionen til tilpasning af Brugergrænsefladen på siden ved hjælp af eksempel HTML og CSS indhold, som vi har lavet.

## <a name="get-an-azure-ad-b2c-tenant"></a>Få en Azure AD B2C lejer

Før du kan tilpasse noget, skal du at [få en Azure AD B2C lejer](active-directory-b2c-get-started.md) , hvis du ikke allerede har en.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Opret en politik for tilmelding eller -logon

Vi har lavet Eksempelindholdet kan bruges til at customze to sider i en [politik for tilmelding eller logon](active-directory-b2c-reference-policies.md): [Samlet logonside](active-directory-b2c-reference-ui-customization.md) og [selv gøres gældende attributter side](active-directory-b2c-reference-ui-customization.md). Når du [opretter din tilmelding eller logon politik](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), tilføje lokale konti (mailadresse), Facebook, Google og Microsoft som **id-udbydere**. Dette er de eneste IDPs, der accepterer vores eksempel HTML-indhold.  Du kan også tilføje et undersæt af disse IDPs, hvis du ønsker.

## <a name="register-an-application"></a>Registrere et program

Du skal til at [registrere et program](active-directory-b2c-app-registration.md) i din B2C lejer, der kan bruges til at udføre din politik. Når du registrerer dit program, har du nogle indstillinger, du kan bruge for at køre din tilmelding politik:

- Opbygge et Azure AD B2C Hurtig start-programmer, der er angivet i afsnittet "Introduktion" i [logge op og logge på forbrugere i programmerne](active-directory-b2c-overview.md#getting-started).
- Bruge programmet færdigbyggede [Azure AD B2C tennisklub](https://aadb2cplayground.azurewebsites.net) . Hvis du vælger at bruge tennisklub, skal du registrere et program i din B2C lejer ved hjælp af den **omdirigere URI** `https://aadb2cplayground.azurewebsites.net/`.
- Brug knappen **Kør nu** på din politik på [Azure-portalen](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Tilpasse din politik

Hvis du vil tilpasse udseendet af din politik, skal du først oprette HTML og CSS-filer med de specifikke konventioner af Azure AD B2C. Du kan overføre din statisk indhold til en offentligt tilgængelig placering, så Azure AD B2C kan få adgang til den. Det kan være din egen dedikeret webserver, Azure Blob-lager, Azure indhold netværk til levering af eller alle andre statisk ressource-vært udbyder. Kun krav er, at dit indhold er tilgængelig via HTTPS og kan åbnes ved hjælp af CORS. Når du har vises dine statisk indhold på internettet, kan du redigere din politik for at pege på denne placering og præsentere indholdet til dine kunder. [Primære brugergrænseflade tilpasning artikel](active-directory-b2c-reference-ui-customization.md) beskrives detaljerede oplysninger om, hvordan funktionen Azure AD B2C tilpasning fungerer.

Med henblik på dette selvstudium har vi allerede oprettet nogle eksempler på indhold og hostes på Azure Blob-lager. Eksempelindhold er en meget grundlæggende tilpasning i temaet af vores fiktivt firma, "Wingtip Toys". Hvis du vil prøve det i dit eget politik, skal du følge disse trin:

1. Log på din lejer på [Azure-portalen](https://portal.azure.com/) , og gå til bladet B2C funktioner.
2. Klik på **politikker for tilmelding eller logge på** og derefter klikke på din politik (eksempelvis "b2c\_1\_log\_op\_log\_i").
3. Klik på **siden til tilpasning af brugergrænseflade** og **samlede tilmelding eller Log på siden**.
4. Skift indstillingen **Brug brugerdefineret side** til **Ja**. Angiv i feltet **brugerdefineret side URI** `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Klik på **OK**.
5. Klik på **lokale konti tilmelding til en side**. Skift indstillingen **Brug brugerdefineret skabelon** til **Ja**. Angiv i feltet **brugerdefineret side URI** `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Gentag de samme trin for **sociale tilmelding kontoside**.
 Klik på **OK** to gange for at lukke Brugergrænsefladen tilpasning blade.
6. Klik på **Gem**.

Nu kan du prøve din tilpassede politik. Du kan bruge dit eget program eller Azure AD B2C tennisklub, hvis du vil, men du kan også bare klikke på kommandoen **Kør nu** i bladet politik. Vælg dit program i feltet ned, og vælg den relevante Omdiriger URI. Klik på knappen **Kør nu** . En ny fane i browseren åbnes, og du kan køre via brugeroplevelsen af tilmelding til dit program med det nye indhold i stedet!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Overføre Eksempelindholdet til Azure Blob-lager

Hvis du vil gerne bruge Azure Blob-lager til at hoste din sideindhold, kan du oprette din egen lagerplads konto og brug vores B2C hjælper til at overføre dine filer.

### <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **+ Ny** > **Data + lagerplads** > **lagerplads konto**. Du skal et Azure-abonnement til at oprette en Azure Blob-lager-konto. Du kan tilmelde dig en gratis prøveversion på [Azure websted](https://azure.microsoft.com/pricing/free-trial/).
3. Angiv et **navn** til kontoen lagerplads (eksempelvis "contoso"), og vælg de relevante valg for **priser niveau**, **ressourcegruppe** og **abonnement**. Sørg for, at du har markeret indstillingen **Fastgør til Startboard** . Klik på **Opret**.
4. Gå tilbage til Startboard, og klik på lagerplads-konto, du lige har oprettet.
5. Klik på **beholdere**i sektionen **Oversigt over** , og klik derefter på **+ Tilføj**.
6. Angiv et **navn** for objektbeholderen (eksempelvis "b2c") og vælge **Blob** som den **type adgang**. Klik på **OK**.
7. Objektbeholderen, du har oprettet vises på listen i bladet **BLOB** . Notér URL-adressen til objektbeholderen; for eksempel bør se nogenlunde sådan `https://contoso.blob.core.windows.net/b2c`. Luk bladet **BLOB** .
8. Klik på **tasterne** på bladet lagerplads konto, og notér værdierne i felterne **Kontonavn lager** og **Access primærnøgle** .

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **+ Ny** > **Data + lagerplads** > **Storage konto**. Du skal et Azure-abonnement til at oprette en Azure Blob-lager-konto. Du kan tilmelde dig en gratis prøveversion på [Azure websted](https://azure.microsoft.com/pricing/free-trial/).
3. Vælg **Blob-lager** under **Type konto**, og lade de andre værdier som standard.  Du kan redigere ressourcegruppe og placering, hvis du ønsker.  Klik på **Opret**.
4. Gå tilbage til Startboard, og klik på lagerplads-konto, du lige har oprettet.
5. Klik på **+ objektbeholder**i sektionen **Oversigt** .
6. Angiv et **navn** for objektbeholderen (eksempelvis "b2c") og vælge **Blob** som den **type adgang**. Klik på **OK**.
7. Åbn objektbeholder **Egenskaber**, og notér URL-adressen til objektbeholderen; for eksempel bør se nogenlunde sådan `https://contoso.blob.core.windows.net/b2c`. Luk bladet objektbeholder.
8. Klik på **Ikonet nøgle** på bladet lagerplads konto og notér værdierne i felterne **Kontonavn lager** og **Primærnøgle i Access** .

> [AZURE.NOTE]
    **Primære hurtigtast** er et vigtigt legitimationsoplysninger.

### <a name="download-the-helper-tool-and-sample-files"></a>Hente hjælper og indsamle filer

Du kan hente [Azure Blob-lager hjælper og indsamle filer som en .zip-fil](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) eller klone fra GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Denne lager indeholder en `sample_templates\wingtip` directory, der indeholder eksempel HTML, CSS og billeder. For disse skabeloner til at referere til din egen Azure Blob-lager-konto, skal du redigere HTML-filer. Åbn `unified.html` og `selfasserted.html` og erstatte alle forekomster af `https://localhost` med URL-adressen til dit eget objektbeholder, du skrev ned i de forrige trin. Du skal bruge den absolutte sti til HTML-filer, fordi i dette tilfælde HTML-koden kan løses ved hjælp af Azure AD under domænet `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Overføre eksempelfiler

I den samme lager udpakke `B2CAzureStorageClient.zip` og køre den `B2CAzureStorageClient.exe` filer i. Dette program skal blot overføre alle filerne i den mappe, du angiver din lagerplads-konto, og aktivere CORS adgang til disse filer. Hvis du har fulgt trinnene ovenfor, HTML og CSS-filer nu peger på kontoen lagerplads. Bemærk, at navnet på din lagerplads konto er den del, der står foran `blob.core.windows.net`; for eksempel `contoso`. Du kan kontrollere, at indholdet er overført korrekt, ved at forsøge at få adgang til `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` i en browser. Også bruge [http://test-cors.org/](http://test-cors.org/) til at kontrollere, at indholdet er nu CORS aktiveret. (Se efter "XHR status: 200" i resultatet.)

### <a name="customize-your-policy-again"></a>Tilpasse din politik igen

Nu hvor du har overført eksempelindhold til din egen lagerplads konto, skal du redigere din tilmelding til en politik for at referere til den. Gentage trinnene i afsnittet ["Tilpas din politik"](#customize-your-policy) ovenfor, denne gang ved hjælp af din egen lagerplads konto URL-adresser. For eksempel placeringen af din `unified.html` fil ville være `<url-of-your-container>/wingtip/unified.html`.

Du kan nu bruge knappen **Kør nu** eller dit eget program til at udføre din politik igen. Resultatet ser næsten præcis den samme – du altid har de samme eksempel HTML og CSS i begge tilfælde. Men din politikker nu refererer til din egen forekomst af Azure Blob-lager, og du kan frit redigere og overføre filer igen, som du skal du. Du kan finde flere oplysninger om tilpasning af HTML og CSS referere til [primære brugergrænseflade tilpasning artikel](active-directory-b2c-reference-ui-customization.md).
