<properties 
    pageTitle="Azure Multi-Factor Authentication rapporter"
    description="Dette beskriver, hvordan du ændre brugerindstillinger som at tvinge brugere til at gøre processen bevis op igen."
    documentationCenter=""
    services="multi-factor-authentication"
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Administrere brugerindstillinger med Azure Multi-Factor Authentication i skyen

Som administrator, kan du administrere følgende indstillinger for brugeren og enheden.  

- [Markerede brugerne skal angive kontakter metoder igen](#require-selected-users-to-provide-contact-methods-again)
- [Slette brugere eksisterende appadgangskoder](#delete-users-existing-app-passwords)
- [Gendanne MFA på alle afbrudt enheder for en bruger](#restore-mfa-on-all-suspended-devices-for-a-user)






Dette er nyttigt, hvis en computer eller enhed er mistet eller stjæles, eller du skal fjerne en brugere adgang.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Markerede brugerne skal angive kontakter metoder igen

Denne indstilling er tvinger brugeren til at fuldføre registreringsprocessen igen, når vedkommende logger på. Vær opmærksom på, ikke-browserapps fortsat fungerer, hvis brugeren har appadgangskoder for dem.  Du kan slette brugere appadgangskoder ved også at vælge **Slet alle eksisterende appadgangskoder genereres af de valgte brugere**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Hvordan brugerne skal angive kontakter metoder igen




1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Directory under Klik på mappen for den bruger, du vil bruge til at levere deres kontaktmetode igen.
4. Klik på brugere er placeret øverst.
5. Klik på Administrer Multi-Factor Auth. nederst på siden Siden Multi-Factor authentication åbnes.
6. Find den bruger, du vil administrere, og Marker i boksen ud for navnet. Du skal muligvis ændre visningen øverst.
7. Dette vil få vist linket **Administrer brugerindstillinger** til højre. Klik på den.
8. Marker i **kræver valgte brugere, der indeholder kontaktoplysninger metoder igen**.
![Angive kontakter metoder](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Klik på Gem.
11. Klik på Luk

## <a name="delete-users-existing-app-passwords"></a>Slette brugere eksisterende appadgangskoder

Dette sletter alle appadgangskoder, som en bruger har oprettet. Ikke-browser-apps, der er knyttet til disse appadgangskoder vil holde op med at arbejde, indtil der oprettes en ny appadgangskode.

### <a name="how-to-delete-users-existing-app-passwords"></a>Sådan sletter du brugere eksisterende appadgangskoder

1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Directory under Klik på mappen for den bruger, du vil slette appadgangskoder for.
4. Klik på brugere er placeret øverst.
5. Klik på Administrer Multi-Factor Auth. nederst på siden Siden Multi-Factor authentication åbnes.
6. Find den bruger, du vil administrere, og Marker i boksen ud for navnet. Du skal muligvis ændre visningen øverst.
7. Dette vil få vist linket **Administrer brugerindstillinger** til højre. Klik på den.
8. Marker i **slette alle eksisterende appadgangskoder genereres af de valgte brugere**.
![Slette appadgangskoder](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Klik på Gem.
10. Klik på Luk.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Gendanne MFA på alle husket enheder for en bruger

Administratorer har mulighed for at gendanne Multi-Factor Authentication på brugere enheder og browsere. Når du gør dette, afkrydsningsfeltet Husk MFA Derved fjernes fra alle brugerens enheder og browsere, og brugeren bliver bedt om at bruge MFA, når du logger på den næste gang.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Sådan gendannes MFA på alle afbrudt enheder for en bruger

1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Directory under Klik på mappen for den bruger, du vil gendanne mfa på.
4. Klik på brugere er placeret øverst.
5. Klik på Administrer Multi-Factor Auth. nederst på siden Siden Multi-Factor authentication åbnes.
6. Find den bruger, du vil administrere, og Marker i boksen ud for navnet. Du skal muligvis ændre visningen øverst.
7. Dette vil få vist linket **Administrer brugerindstillinger** til højre. Klik på den.
8. Marker **gendanne Multi-Factor authentication på alle husket enheder**
![slette appadgangskoder](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Klik på Gem.
10. Klik på Luk.
