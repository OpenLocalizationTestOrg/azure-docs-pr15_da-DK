<properties
    pageTitle="Godkendelse og autorisation i Azure-Mobilapps | Microsoft Azure"
    description="Grundlæggende reference og oversigt over godkendelsestyper / godkendelse af funktioner til Azure Mobile-Apps"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Godkendelse og autorisation i Azure-Mobilapps

## <a name="what-is-app-service-authentication--authorization"></a>Hvad er App Service godkendelse / godkendelse?

> [AZURE.NOTE] Dette emne bliver overført til en konsolideret [App Service godkendelse / godkendelse](../app-service/app-service-authentication-overview.md) emne, som omhandler Web, Mobile og API Apps.

App-tjenesten godkendelse / godkendelse er en funktion, som gør det muligt for dit program til at logge på brugere uden kode ændringer, der kræves på app back-end. Det gør det nemt at beskytte dit program og arbejde med data for hver bruger.

App Service bruger organisationsnetværket identitet, hvor en 3 tredjepart **identitetsudbyder** ("IDP") gemmer konti og godkender brugere, og programmet bruger denne identitet i stedet for sin egen. App Service understøtter fem id-udbydere af feltet: _Azure Active Directory_, _Facebook_, _Google_, _Microsoft-konto_og _Twitter_. Du kan også udvide denne understøttelse for dine apps ved at integrere en anden identitetsudbyder eller din egen brugerdefinerede identitet løsning.

Din app kan udnytte et vilkårligt antal disse identitetsudbydere, så du kan give dine slutbrugere med indstillinger for hvordan de logger på.

Hvis du ønsker at komme i gang, skal du se en af følgende selvstudier:

- [Føje godkendelse til din iOS-app]
- [Føje godkendelse til din Xamarin.iOS-app]
- [Føje godkendelse til din Xamarin.Android-app]
- [Føje godkendelse til din Windows-app]

## <a name="how-authentication-works"></a>Hvordan fungerer godkendelse

For at godkende ved hjælp af en af udbyderne identitet, skal du først konfigurere identitetsudbyder at vide om dit program. Identitetsudbyder derefter giver dig id'er og hemmeligheder, som du angiver tilbage til programmet. Dette er fuldført på tillidsforhold og giver mulighed for App-tjenesten til at validere identiteter, der er angivet til den.

Disse trin gennemgås i følgende emner:

- [Sådan konfigureres din app for at bruge Azure Active Directory-logon]
- [Sådan konfigureres din app for at bruge Facebook-logon]
- [Sådan konfigureres din app Hvis du vil bruge Google-logon]
- [Sådan konfigureres din app for at bruge Microsoft Account logon]
- [Sådan konfigureres din app for at bruge Twitter-logon]

Når alt er konfigureret på back-end, kan du ændre din klient til at logge på. Der er to fremgangsmåder her:

- Ved hjælp af en enkelt linje af kode, kan de Mobile-Apps klient-SDK logge på brugere.
- Udnytte en udgivet af en given identitetsudbyder til at etablere identitet og derefter få adgang til App Service SDK.

>[AZURE.TIP] De fleste programmer skal bruge en udbyder af SDK, at få en mere oprindelig følelse login oplevelse og til at udnytte Opdater support og andre providerspecifikke fordele.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Hvordan fungerer godkendelse uden en udbyder af SDK

Hvis du ikke vil konfigurere en udbyder af SDK, kan du tillade Mobile-Apps til at udføre logon for dig. Klient-Mobile-Apps SDK kan åbne en webvisning til udbyderen efter eget valg og udfylde logonsiden. Nogle gange om blogge og du får vist dette refereres til som "server forløb" eller "server styrede flow" siden serveren-fora administrerer logon og klient-SDK modtager aldrig udbyder tokenet.

Den kode, der kræves for at starte dette flow dækkes i selvstudiet godkendelse for hver platform. Klient-SDK har et App Service token enden af strømmen, og tokenet vedhæftes automatisk alle anmodninger om at backend-version.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Hvordan fungerer godkendelse med en udbyder af SDK

Arbejde med en udbyder kan SDK logge ind-oplevelsen til tættere interaktivt platform OS app'en kører på. Dette giver dig også en udbyder token og visse brugeroplysninger på klienten, hvilket gør det meget nemmere at bruge graph API'er og tilpasse brugeroplevelsen. Nogle gange på blogge og fora vises dette kaldes "klient strømmen" eller "klient styrede flow" siden kode på klienten håndtering af logon og Klientkoden har adgang til en udbyder token.

Når der opnås en udbyder token, skal den sendes til App-tjeneste til validering. Klient-SDK har et App Service token enden af strømmen, og tokenet vedhæftes automatisk alle anmodninger om at backend-version. Udvikleren kan også holde en reference til udbyder tokenet, hvis de så vælger.

## <a name="how-authorization-works"></a>Hvordan fungerer godkendelse

App-tjenesten godkendelse / godkendelse Fremviser flere valgmuligheder for **handling skal udføre, når anmodning ikke er godkendt**. Før din kode modtager en given anmodning, kan du få App Service Kontrollér Hvis anmodningen er godkendt og hvis ikke, at afvise den og forsøger at få brugeren til at logge på, før du prøver igen.

En af mulighederne er at har ikke-godkendt anmodninger Omdiriger til en af udbyderne identitet. I en webbrowser, vil dette overtage brugeren til en ny side. Dog ikke blive omdirigeret din mobilklient på denne måde, og ikke-godkendte svar, modtager en HTTP _401 Uautoriseret_ svar. Givet, den første anmodning, der er kunden gør skal altid være til logon slutpunktet, og derefter kan du foretage opkald til andre API'er. Hvis du forsøger at ringe til en anden API inden du logger på, er kunden får en fejlmeddelelse.

Hvis du vil have mere detaljerede styre over hvilke slutpunkter kræver godkendelse, kan du også vælge "ingen handling (tillade anmodning)" ikke-godkendte anmodninger om. I dette tilfælde er alle godkendelse beslutninger udskudt til din programkode. Dette kan du også at give adgang til bestemte brugere, der er baseret på brugerdefinerede reglerne.

## <a name="documentation"></a>Dokumentation

Følgende selvstudier viser, hvordan du føjer godkendelse til din mobile klienter, der bruger App Service:

- [Føje godkendelse til din iOS-app]
- [Føje godkendelse til din Xamarin.iOS-app]
- [Føje godkendelse til din Xamarin.Android-app]
- [Føje godkendelse til din Windows-app]

Følgende selvstudier viser, hvordan du konfigurerer App Service for at udnytte forskellige godkendelsesprovidere:

- [Sådan konfigureres din app for at bruge Azure Active Directory-logon]
- [Sådan konfigureres din app for at bruge Facebook-logon]
- [Sådan konfigureres din app Hvis du vil bruge Google-logon]
- [Sådan konfigureres din app for at bruge Microsoft Account logon]
- [Sådan konfigureres din app for at bruge Twitter-logon]

Hvis du vil bruge et identitetssystem end dem, der findes her, kan du også udnytte [Gennemse understøttelse af brugerdefinerede godkendelse i .NET server SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Føje godkendelse til din iOS-app]: app-service-mobile-ios-get-started-users.md
[Føje godkendelse til din Xamarin.iOS-app]: app-service-mobile-xamarin-ios-get-started-users.md
[Føje godkendelse til din Xamarin.Android-app]: app-service-mobile-xamarin-android-get-started-users.md
[Føje godkendelse til din Windows-app]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Sådan konfigureres din app for at bruge Azure Active Directory-logon]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Sådan konfigureres din app for at bruge Facebook-logon]: app-service-mobile-how-to-configure-facebook-authentication.md
[Sådan konfigureres din app Hvis du vil bruge Google-logon]: app-service-mobile-how-to-configure-google-authentication.md
[Sådan konfigureres din app for at bruge Microsoft Account logon]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Sådan konfigureres din app for at bruge Twitter-logon]: app-service-mobile-how-to-configure-twitter-authentication.md
