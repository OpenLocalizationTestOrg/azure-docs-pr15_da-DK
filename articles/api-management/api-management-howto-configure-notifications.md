<properties 
    pageTitle="Sådan konfigureres beskeder og e-mail-skabeloner i Azure API Management" 
    description="Lær, hvordan du konfigurerer meddelelser og e-mail-skabeloner i Azure API Management." 
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

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Sådan konfigureres beskeder og e-mail-skabeloner i Azure API Management

API administration giver mulighed for at konfigurere beskeder til bestemte hændelser og konfigurere de mailskabeloner, der bruges til at kommunikere med administratorer og udviklere af en API Management forekomst. Dette emne viser, hvordan du kan konfigurere beskeder for de tilgængelige hændelser og indeholder en oversigt over konfiguration af e-mail-skabelonerne bruges til disse begivenheder.

## <a name="publisher-notifications"> </a>Konfigurere beskeder til publisher

Klik på **Administrer** i portalen Azure klassisk for din API Management-tjenesten for at konfigurere beskeder. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **beskeder** fra menuen **Administration af API** til venstre for at få vist de tilgængelige notifikationer.

![Publisher-beskeder][api-management-publisher-notifications]

Følgende liste over begivenheder kan konfigureres til meddelelser.

-   **Anmodninger om abonnement (kræver godkendelse)** - den angivne e-mail-modtagere og brugere får besked om abonnement anmodninger API produkter, der kræver godkendelse via mail.
-   **Ny abonnementer** - den angivne e-mail-modtagere og brugere får besked om ny API-abonnement via mail.
-   **Programmet galleriet anmodninger** - den angivne e-mail-modtagere og brugere får besked via mail, når nye programmer er sendt til galleriet programmet på computeren.
-   **BCC** - den angivne e-mail-modtagere og brugere modtager mail blind carbon copy alle mails, der sendes til udviklere.
-   **Nyt problem eller kommentar** - den angivne e-mail-modtagere og brugere skal modtage besked via mail når et nyt problem eller kommentar sendes på portalen udvikler.
-   **Luk konto meddelelse** - den angivne e-mail-modtagere og brugere får besked via mail, når en konto er lukket.
-   **Approaching abonnement kvotegrænse for** - følgende e-mail-modtagere og brugere får besked via mail, når abonnementet brugen bliver tæt kvote.

Du kan angive e-mail-modtagere, der bruger tekstboksen e-mail-adresse for hver enkelt hændelse, eller du kan vælge brugere fra en liste.

Hvis du vil angive mailadresser have besked, skal du indtaste dem i tekstboksen e-mail-adresse. Hvis du har flere mailadresser, skal du adskille dem med kommaer.

![Modtagere af besked][api-management-email-addresses]

For at angive, hvilke brugere at få besked, skal du klikke på **Tilføj modtager**, Markér afkrydsningsfeltet ud for brugerne, skal have besked, og klik på **OK**.

>Bemærk, at det er kun administratorer vises på listen.

Når du har konfigureret på modtagere af besked, skal du klikke på **Gem** for at anvende de opdaterede meddelelse modtagere.

>Hvis du har navigeret væk fra fanen **Publisher notifikationer** portalen publisher giver dig besked hvis der er ændringer.

## <a name="email-templates"> </a>Konfigurer e-mail-skabeloner

API Administration indeholder e-mail-skabeloner til e-mail-meddelelser, der sendes i forbindelse med administration og ved hjælp af tjenesten. Der findes følgende e-mail-skabeloner.

-   Programmet galleriet bidrag godkendt
-   Udvikler farewell bogstav
-   Udvikler kvotegrænse for nærmer meddelelse
-   Inviter bruger
-   Ny kommentar, der er føjet til et problem
-   Nyt problem, der er modtaget
-   Nyt abonnement, der er aktiveret
-   Abonnement fornyet bekræftelse
-   Anmodning om abonnement afviser
-   Anmodning om abonnement modtaget

Du kan ændre disse skabeloner som ønskede.

Klik på **beskeder** i menuen **Administration af API** til venstre for at få vist og konfigurere e-mail-skabeloner til din API Management-forekomst, og vælg fanen **E-mail-skabeloner** .

![E-mail-skabeloner][api-management-email-templates]

For at få vist eller ændre en bestemt skabelon, skal du vælge den fra rullelisten **skabeloner** .

![E-mail-skabeloner liste][api-management-email-templates-list]

Hver mailskabelon har et emne i almindelig tekst og en definition af brødteksten i HTML-format. Hvert element kan tilpasses som ønskede.

![Skabelon maileditor][api-management-email-template]

Listen **parametre** indeholder en liste over parametre, og når indsat i emnet eller brødteksten, bliver erstattet den angivne værdi, når e-mailen sendes. Placer markøren, hvor du vil parameteren for at gå for at indsætte en parameter, og klik på pilen til venstre for parameternavnet på.

Klik på **Vis udskrift** eller **sende en test** for at se, hvordan mailen vil se eller sende en mail med test.

>Bemærk, at parametrene ikke erstattes med faktiske værdier, når du få vist eller sende en test.
>
>Klik på **Gem**for at gemme ændringerne i e-mail-skabelonen eller for at annullere ændringerne skal du klikke på **Annuller**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance