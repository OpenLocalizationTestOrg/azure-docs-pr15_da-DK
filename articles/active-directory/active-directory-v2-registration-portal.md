<properties
    pageTitle="App registrering portalen hjælpeemner | Microsoft Azure"
    description="En beskrivelse af forskellige funktioner i Microsoft app registrering portal."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="app-registration-reference"></a>App registrering reference
Dette dokument indeholder kontekst og beskrivelser af forskellige funktioner, der findes i Microsoft App registrering Portal [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Mine-programmer
Denne liste indeholder alle dine programmer, der er registreret til brug med Azure AD v2.0 slutpunkt.  Disse programmer har mulighed for at logge på brugere med begge personlige konti fra Microsoft-konto og arbejds-eller skolekonto konti fra Azure Active Directory.  Hvis du vil vide mere om Azure AD v2.0 slutpunkt, skal du se vores [v2.0 oversigt](active-directory-appmodel-v2-overview.md).  Disse programmer kan også bruges til at integrere med Microsoft-konto godkendelse slutpunktet, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Live SDK-programmer
Denne liste indeholder alle dine programmer, der er registreret til brug udelukkende med Microsoft-konto.  De er ikke aktiveret til brug med Azure Active Directory ene eller den anden.  Dette er, hvor du kan finde alle programmer, der har tidligere været registreret ved hjælp af MSA udvikler portal på `https://account.live.com/developers/applications`.  Alle funktioner, som du tidligere har udført på `https://account.live.com/developers/applications` kan nu udføres i denne nye portal `https://apps.dev.microsoft.com`.  Hvis du har yderligere spørgsmål om programmerne Microsoft-konto, skal du kontakte os.

## <a name="application-secrets"></a>Programmet hemmeligheder
Programmet hemmeligheder er legitimationsoplysninger, der tillader dit program tilladelse til at udføre pålidelig [klientgodkendelse](http://tools.ietf.org/html/rfc6749#section-2.3) med Azure AD.  I OAuth & OpenID oprette forbindelse, et program hemmeligheder ofte kaldes en `client_secret`.  I v2.0 protokollen, et program, der modtager et sikkerhedstoken til en tilgængelige webstedsplacering (ved hjælp af en `https` farveskema) skal bruge et program hemmeligt til at identificere sig til Azure AD på indløsning af pågældende sikkerhedstoken.  Desuden en oprindelig klient, der godkendelseskontrol tokens på en enhed vil forbudt fra ved hjælp af en programmet hemmeligt til at udføre klientgodkendelse for ikke at opmuntre opbevaring af hemmeligheder i usikre miljøer.

Hver app kan indeholde to gyldigt program hemmeligheder når som helst givet tidspunkt.  Ved at vedligeholde to hemmeligheder, har du ablilty til at udføre periodiske vigtige overgang på tværs af hele dit program-miljø.  Når du har opgraderet helhed af dit program til en ny hemmeligt, kan du slette det gamle hemmeligt og klargøre en ny.

På nuværende tidspunkt er kun to typer af programmet hemmeligheder tilladt i portalen app registrering.  Vælge **Opret ny adgangskode** oprette og gemme en delt hemmeligt i de respektive datalager, som du kan bruge i dit program.  Vælge **Opret ny nøglepar** opretter et nyt offentlige/private nøgler par, som kan hentes og bruges til klientgodkendelse til Azure AD.

## <a name="profile"></a>Profil
Afsnittet profil i portalen app registrering kan bruges til at tilpasse siden Log på for dit program.  Du kan ændre sidens program logo, vilkår for URL-adressen og erklæring om at logge på nuværende tidspunkt.  Logoet skal være et gennemsigtigt 48 x 48 eller 50 x 50 billede af pixel i en GIF-, PNG eller JPEG-fil, der er 15 KB eller mindre.  Prøve at ændre værdierne og få vist den resulterende Log på siden!

## <a name="live-sdk-support"></a>Live SDK Support
Når du aktiverer "Live SDK Support", vil klargøres en hvilken som helst program hemmeligheder, du opretter i begge de Azure AD og Microsoft Account data butikker.  Dette gør dit program tilladelse til at integrere direkte med tjenesten Microsoft-Account (login.live.com).  Hvis du vil oprette en app ved hjælp af Microsoft Account direkte (i modsætning til ved hjælp af Azure AD v2.0 slutpunktet), skal du sikre dig, at Live SDK Support er aktiveret.

Deaktivering af Live SDK understøttelse sikrer, at programmet hemmeligt kun er skrevet i Azure AD-data gemmes.  Azure AD-data store inkorporerer professionel og regler, der tillader det skal opfylde visse standarder, som FISMA overholdelse af angivne standarder.  Hvis du aktiverer Live SDK support, kan dit program ikke opnå overholdelse af nogle af disse standarder.

Hvis du kun nogensinde planlægger at bruge Azure AD v2.0 slutpunktet, kan du sikkert deaktivere Live SDK support.

