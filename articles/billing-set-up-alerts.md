<properties
    pageTitle="Konfigurere fakturering beskeder for dine abonnementer på Microsoft Azure | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du kan konfigurere påmindelser på din Azure faktura så du kan undgå fakturering overraskelser."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Konfigurere fakturering beskeder for dine Microsoft Azure-abonnementer

Er du bekymret hvor meget du bruger hver måned for dit Azure abonnement? Hvis du er kontoadministratoren for et Azure-abonnement, kan du bruge Azure fakturering beskeder om tjenesten til at oprette tilpassede fakturering beskeder, der hjælper dig med at overvåge og administrere fakturering aktivitet for kontiene Azure.

Denne tjeneste er en preview-tjeneste, så det første, du skal gøre er at blive tilmeldt for den. Gå til [siden Preview funktioner](https://account.windowsazure.com/PreviewFeatures) i portalen Azure-konto management til skal du aktivere denne funktion.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Angive beskeder om grænseværdi og e-mail-modtagerne

Når du modtager bekræftelsen mail, der er slået til tjenesten fakturering for dit abonnement, skal du besøge [siden abonnementer](https://account.windowsazure.com/Subscriptions) i portalen konto. Klik på det abonnement, du vil overvåge, og klik derefter på **beskeder**.

![][Image1]

Klik derefter på **Tilføj påmindelse** for at oprette din første – kan du konfigurere op til fem fakturering beskeder om abonnement med en anden grænseværdi og op til to e-mail-modtagere for hver besked.

![][Image2]

Når du tilføjer en besked, du give den et entydigt navn, Vælg en grænseværdi for udgifter og vælge den mailadresse, hvor sendes påmindelser. Når du konfigurerer grænsen, kan du vælge en **Fakturering samlede** eller **Pengeværdier kredit** på **Besked For** listen. I fakturering alt sendes en besked, når abonnementet bruger overstiger grænsen. For en valuta kreditkontrol sendes en besked, når pengeværdier kredit slipper under grænsen. Valuta kredit gælder normalt for gratis prøveversioner og abonnementer, der er knyttet til MSDN-konti.

![][Image3]

Azure understøtter en hvilken som helst mailadresse, men bekræfte ikke, at e-mail-adresse works, så Kontrollér for stavefejl.

## <a name="check-on-your-alerts"></a>Se, om dine beskeder

Når du har konfigureret beskeder, konto Center indeholder dem og viser hvor mange mere kan du konfigurere. For hver besked vises datoen og klokkeslæt, den blev sendt, uanset om det er en besked for fakturering samlede eller pengeværdier kredit og den grænse, du har konfigureret. Dato- og klokkeslætsformat er 24-timers koordinere (Coordinated Universal Time) og datoen er åååå-mm-dd-format. Klik på plustegnet for en besked på listen for at redigere den, eller klik på feltet Papirkurv kan for at slette den.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
