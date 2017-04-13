<properties
    pageTitle="Azure AD-domænetjenester: Opdater DNS-indstillingerne for det Azure virtuelle netværk | Microsoft Azure"
    description="Introduktion til Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Azure AD-domænetjenester - Update DNS-indstillinger for det Azure virtuelle netværk

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Opgave 4: Opdatere DNS-indstillingerne for det Azure virtuelle netværk
I før konfigurationsopgaver, har du aktiveret Azure AD-domænetjenester til adresselisten. Den næste opgave er at sikre, at computere i det virtuelle netværk kan oprette forbindelse og bruge disse tjenester. Opdatere DNS-serverindstillinger for netværket virtuel til at pege på de to IP-adresser, hvormed Azure AD-domænetjenester er tilgængelig på det virtuelle netværk.

> [AZURE.NOTE] Bemærk ned IP-adresser til Azure AD-domænetjenester vises under fanen **Konfigurer** i adresselisten, når du har aktiveret Azure AD-domænetjenester for kataloget.

Udføre de følgende trin til konfiguration for at opdatere DNS-serverindstillingen for det virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester.

1. Gå til **Azure klassisk portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Vælg noden **netværk** i venstre rude.

    ![Virtuelt Network node](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. Vælg det virtuelle netværk, hvor du har aktiveret Azure AD Domain Services til at få vist egenskaberne i fanen **Virtuelle netværk** .

4. Klik på **Konfigurer** .

    ![Virtuelt Network node](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Angiv Azure AD-domænetjenester IP-adresser i sektionen **DNS-servere** .

6. Sørg for, at du angiver begge de IP-adresser, der blev vist i afsnittet **Domain Services** under fanen **Konfigurer** i mappen.

7. Klik på **Gem** i ruden nederst på siden for at gemme indstillingerne for DNS-server for denne virtuelle netværk.

   ![Opdatere DNS-serverindstillinger for det virtuelle netværk.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Når du opdaterer DNS-serverindstillingerne for det virtuelle netværk, kan det tage et øjeblik, før virtuelle maskiner på netværket for at få opdaterede DNS-konfigurationen. Hvis en virtuel maskine er i stand til at oprette forbindelse til domænet, kan du tømme DNS-cachen (f.eks.) ' ipconfig/flushdns') på den virtuelle maskine. Denne kommando fremtvinger en opdatering af DNS-indstillingerne på en virtuel maskine.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Opgave 5 – Aktivér synkronisering af adgangskoder til Azure AD-domænetjenester
Den næste Konfigurationsopgave er at [aktivere synkronisering af adgangskoder til Azure AD-domænetjenester](active-directory-ds-getting-started-password-sync.md).
