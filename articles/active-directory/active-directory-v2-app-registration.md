<properties
    pageTitle="v2.0 app registrering | Microsoft Azure"
    description="Hvordan du kan registrere en app hos Microsoft for at aktivere logon og få adgang til Microsoft-tjenester med v2.0 slutpunktet"
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

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Hvordan du kan registrere en app til v2.0 slutpunktet

For at opbygge en app, der accepterer både MSA & Azure AD-logon, skal du først skal registrere en app hos Microsoft.  På nuværende tidspunkt, kan du ikke kan du bruge en eksisterende apps, har du måske med Azure AD eller MSA - skal du oprette en helt ny.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Besøg Microsoft app registrering portal
Første ting først - gå til [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Dette er en ny app registrering portal, hvor du kan administrere dine Microsoft-apps.

Log på med enten en personlig eller arbejde eller skole Microsoft-konto.  Hvis du ikke har enten Tilmeld dig en ny personlig konto. Gå frem, det tager ikke lang tid – vi venter her.

Er du færdig? Du bør nu kigger på din liste over Microsoft apps, som er sandsynligvis tom.  Lad os ændre dette.

Klik på **Tilføj en app**, og give den et navn.  På portalen tildele din app et globalt entydige program-Id, du skal bruge senere i din kode.  Hvis din app omfatter en serversiden komponent, der skal bruge access tokens til opkald API'er (tror: Office, Azure eller din egen web API), du vil oprette et **Program hemmeligt** her samt.
<!-- TODO: Link for app secrets -->

Føj de platforme, der anvender din app.

- Indeholder en **Omdirigere URI** hvor logon meddelelser kan sendes til webbaserede apps.
- Mobilapps, kopiere ned på Omdiriger uri, der er oprettet automatisk for dig.

Du kan også kan du tilpasse udseendet af dit logon-siden i sektionen profil.  Sørg for at klikke på **Gem** inden du går videre.

> [AZURE.NOTE] Når du opretter et program med [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), registreres programmet i privat lejer på den konto, du kan bruge til at logge på portalen.  Det betyder, at du ikke kan registrere et program i din Azure AD-lejer ved hjælp af en personlig Microsoft-konto.  Hvis du vil eksplicit til at registrere et program i en bestemt lejer, skal du logge på med en konto, der er oprettet i lejeren.

## <a name="build-a-quick-start-app"></a>Oprette en hurtig start app
Nu hvor du har en Microsoft-app, kan du udføre én af vores v2.0 Hurtig start selvstudier.  Her er nogle anbefalinger:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
