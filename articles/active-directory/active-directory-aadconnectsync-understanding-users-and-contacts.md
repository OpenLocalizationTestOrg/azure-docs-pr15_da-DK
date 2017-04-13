<properties
    pageTitle="Azure AD-forbindelse synkronisering: om brugere og kontaktpersoner | Microsoft Azure"
    description="Forklarer, brugere og kontakter i Azure AD-forbindelse synkronisering."
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
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure AD-forbindelse synkronisering: om brugere og kontakter

Der er flere forskellige årsager, hvorfor du gerne vil have flere Active Directory-områder, og der er flere forskellige installation topologier. Almindelige modeller har en konto er ressourcerelaterede installation og GAL sync'ed områder efter en fusion & acquisition. Men selvom der er kun modeller, hybrid modeller er almindelige samt. Standardkonfigurationen i Azure AD-forbindelse Synkroniser forudsætter ikke en bestemt model, men afhængigt af hvordan brugeren tilsvarende er valgt i installationsvejledningen, kan der ses forskellige funktionsmåder.

I dette emne, vi gennemgår, hvordan standardkonfigurationen virkemåde i visse topologier. Vi gennemgår konfigurationen og redigeringsprogrammet synkronisering regler kan bruges til at se på konfigurationen.

Der er et par almindelige regler konfigurationen antages:

- Uanset hvilken rækkefølge, vi importere fra kilden aktive kataloger, skal end resultatet altid være den samme.
- En aktiv konto bidrager altid logon oplysninger, herunder **userPrincipalName** og **sourceAnchor**.
- En deaktiveret konto vil bidrage userPrincipalName og sourceAnchor, medmindre en sammenkædet postkasse, hvis der er ingen aktive konto til at blive tilføjet.
- En konto hos en sammenkædet postkasse bruges aldrig til userPrincipalName og sourceAnchor. Det antages, at en aktiv konto fundet senere.
- Et kontakter objekt kan klargøres til Azure AD, som en kontakt eller som en bruger. Du egentlig ikke indtil alle kilde Active Directory-områder er blevet behandlet.

## <a name="contacts"></a>Kontaktpersoner

Har du kontakter, der repræsenterer en bruger i en anden skov er almindelige efter en fusion & acquisition hvor en GALSync løsning broer to eller flere Exchange-områder. Objektet kontakter altid samle fra connector-plads til metaverse ved hjælp af attributten mail. Hvis der allerede findes en kontakt eller brugerobjekt med den samme e-mail-adresse, er objekterne, der sammenføjet sammen. Dette er konfigureret i reglen **i fra AD – Kontakt deltage i**. Der er også en regel med navnet **i fra AD – Kontakt almindelige** med et attribut flow til metaverse attribut **sourceObjectType** med konstant **Kontakt**. Denne regel har meget lav rangorden så hvis en hvilken som helst brugerobjektet er knyttet til det samme metaverse-objekt, og klik derefter på reglen **i fra AD-bruger almindelige** vil bidrage værdien bruger til attributten. Med denne regel har denne attribut værdien kontakt, hvis ingen bruger er sluttet og værdien, der bruger Hvis mindst én bruger, der er fundet.

Til klargøring af et objekt til Azure AD, opretter den udgående regel **ud til AAD – Kontakt deltage** et kontakter objekt Hvis metaverse attributten **sourceObjectType** er indstillet til at **kontakte**. Hvis denne attribut er indstillet til **brugeren**, derefter reglen **ud til AAD – bruger deltage** opretter et brugerobjekt i stedet.
Det er muligt, at et objekt er opgraderet fra kontakt bruger, når flere kilde aktive kataloger er importeret og synkroniseret.

For eksempel i en GALSync topologi kan vi finde kontaktobjekter for alle i den anden skov når vi importerer det første område. Dette vil fase nye kontaktobjekter i AAD forbindelsen. Når vi senere importere og synkronisere det andet område, kan vi finde de rigtige brugere og deltage i dem til de eksisterende metaverse-objekter. Vi derefter sletter objektet kontakter i AAD og oprette et nyt brugerobjekt i stedet.

Hvis du har en topologi hvor brugere og repræsenteres som kontakter, Kontrollér, at du vælger for at matche brugere på attributten mail i installationsvejledningen. Hvis du vælger en anden indstilling, vil du har en rækkefølge afhængige konfiguration. Kontaktobjekter sammenføjes altid på attributten mail, men brugerobjekter sammenføjes kun på attributten mail, hvis denne indstilling er valgt i installationsvejledningen. Du kan derefter ender med to forskellige objekter i metaverse med det samme mail-attribut Hvis objektet kontakter er importeret før brugerobjektet. Under eksport til Azure AD, vil være udløst en fejl. Denne funktionalitet er som design, og angiver ugyldige data eller, topologien ikke blev korrekt identificeret under installationen.

## <a name="disabled-accounts"></a>Deaktiverede konti

Deaktiverede konti er synkroniseret samt til Azure AD. Deaktiverede konti er almindelige til at repræsentere ressourcer i Exchange, for eksempel mødelokalerne. Undtagelsen er brugere med en sammenkædet postkasse som nævnt tidligere, vil disse aldrig klargøre en konto til Azure AD.

Antages det, hvis der findes en deaktiveret brugerkonto, og derefter vi kan ikke finde en anden aktive konto senere og objektet er klargjort til Azure AD med userPrincipalName og sourceAnchor, der er fundet. I tilfælde af en anden aktive konto sammenføjes på det samme objekt metaverse, blive derefter dens userPrincipalName og sourceAnchor brugt.

## <a name="changing-sourceanchor"></a>Ændre sourceAnchor

Når et objekt er eksporteret til Azure AD, og klik derefter det ikke er tilladt at ændre sourceAnchor længere. Når objektet, der er eksporteret er metaverse attributten **cloudSourceAnchor** konfigureret med værdien **sourceAnchor** accepteret af Azure AD. Hvis **sourceAnchor** ændres og ikke svarer til **cloudSourceAnchor**, bliver reglen **ud til AAD – bruger deltage i** Udløs fejl **sourceAnchor attribut er ændret**. Konfiguration eller data skal i dette tilfælde skal rettes, så den samme sourceAnchor er til stede i metaverse igen, før objektet, der kan synkroniseres igen.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Azure Active Directory forbinde-synkronisering: Tilpasse indstillinger for synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
