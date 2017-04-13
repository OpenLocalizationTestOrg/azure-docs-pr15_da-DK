<properties 
    pageTitle="Hvordan administrere brugerkonti i Azure API Management | Microsoft Azure" 
    description="Få mere at vide om at oprette eller invitere brugere i Azure API Management" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Hvordan du kan administrere brugerkonti i Azure API Management

I administration af API er udviklere brugerne af de API'er, som du fremvise ved hjælp af API Management. Denne vejledning viser til at oprette og invitere udviklere at bruge den API'er og produkter, du foretager tilgængelige for dem med din API Management forekomst. Finde oplysninger om administration af brugerkonti fra et program, i dokumentationen [brugerobjektet](https://msdn.microsoft.com/library/azure/dn776330.aspx) i referencen [API Management RESTEN](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Oprette en ny udvikler

Hvis du vil oprette en ny udvikler, skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher. Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

![Publisher-portalen][api-management-management-console]

Klik på **brugere** fra menuen **Administration af API** til venstre, og klik derefter på **Tilføj bruger**.

![Oprette udvikler][api-management-create-developer]

Angiv den **mail**, **din adgangskode**og **navnet** for den nye udvikler, og klik på **Gem**.

![Oprette udvikler][api-management-add-new-user]

Som standard nyoprettede udvikler konti er **aktiv**, og der er knyttet til gruppen **udviklere** .

![Ny udvikler][api-management-new-developer]

Udvikler konti, der er i tilstanden **aktiv** kan bruges til at få adgang til alle de API'er, som de har abonnementer. Hvis du vil knytte nyoprettede udvikleren med flere grupper, se, [hvordan du knytte grupper med udviklere][].

## <a name="invite-developer"> </a>Invitere en udvikler

Invitere en udvikler, klik på **brugere** fra menuen **Administration af API** til venstre, og klik derefter på **Inviter bruger**.

![Invitere udvikler][api-management-invite-developer]

Angiv navn og mailadresse på udvikleren, og klik på **Inviter**.

![Invitere udvikler][api-management-invite-developer-window]

Der vises en bekræftelsesmeddelelse, men den nyligt inviterede udvikler vises ikke på listen indtil, når de har accepteret invitationen. 

![Invitere bekræftelse][api-management-invite-developer-confirmation]

Når en udvikler er inviteret, sendes en mail til udvikleren. Denne e-mail er oprettet ved hjælp af en skabelon og kan tilpasses. Du kan finde yderligere oplysninger finder [Konfigurer e-mail-skabeloner][].

Når invitationen er accepteret, aktiveres kontoen.

## <a name="block-developer"></a> Deaktiver eller Genaktiver en udvikler-konto

Som standard er nyoprettede eller inviterede udvikler konti **aktiv**. Klik på **Bloker**for at deaktivere en udvikler-konto. For at genaktivere en blokerede udvikler konto skal du klikke på **Aktivér**. En blokeret udvikler konto kan ikke få adgang til portalen udvikler eller ringe til en hvilken som helst API'er. Hvis du vil slette en brugerkonto, skal du klikke på **Slet**.

![Bloker udvikler][api-management-new-developer]

## <a name="reset-a-user-password"></a>Nulstille en brugeradgangskode

For at nulstille adgangskoden til en brugerkonto, skal du klikke på navnet på kontoen.

![Nulstil adgangskode][api-management-view-developer]

Klik på **Nulstil adgangskode** for at sende et link til brugerne for at nulstille adgangskoden for.

![Nulstil adgangskode][api-management-reset-password]

Hvis du vil arbejde fra et program med brugerkonti, i dokumentationen [brugerobjektet](https://msdn.microsoft.com/library/azure/dn776330.aspx) i referencen [API Management RESTEN](https://msdn.microsoft.com/library/azure/dn776326.aspx) . For at nulstille en adgangskode til brugerkonto til en bestemt værdi, kan du bruge handlingen [opdatere en bruger](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) og angive den ønskede adgangskode.

## <a name="pending-verification"></a>Afventer godkendelse

![Afventer godkendelse][api-management-pending-verification]

## <a name="next-steps"> </a>Næste trin

Når en udvikler konto er oprettet, kan du knytte den til roller og abonnere på produkter og API'er. Se, [hvordan du oprette og bruge grupper][]kan finde flere oplysninger.


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Sådan oprettes og bruge grupper]: api-management-howto-create-groups.md
[Sådan tilknyttes udviklere grupper]: api-management-howto-create-groups.md#associate-group-developer

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Konfigurere e-mail-skabeloner]: api-management-howto-configure-notifications.md#email-templates