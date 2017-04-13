<properties
    pageTitle="Azure AD-domænetjenester: Opret eller Vælg et virtuelt netværk | Microsoft Azure"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Oprette eller vælge et virtuelt netværk til Azure AD-domænetjenester

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Retningslinjer for at vælge en Azure virtuelt netværk
> [AZURE.NOTE] **Før du begynder**: refererer til [netværk overvejelser i forbindelse med Azure AD-domænetjenester](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Opgave 2: Opret en Azure virtuelt netværk
Den næste Konfigurationsopgave er at oprette en Azure virtuelt netværk og et undernet inden for den. Du kan aktivere Azure AD-domænetjenester i dette undernet i netværket virtuel. Hvis du allerede har et eksisterende virtuelt netværk, du foretrækker at bruge, kan du springe dette trin.

> [AZURE.NOTE] Sørg for, at det Azure virtuelle netværk, du opretter eller vælger at bruge med Azure AD-domænetjenester hører til et Azure område, der understøttes af Azure AD-domænetjenester. Se siden [Azure tjenester efter område](https://azure.microsoft.com/regions/#services/) at kende områderne Azure, Azure AD-domænetjenester er tilgængelig.

Bemærk navnet på det virtuelle netværk ned så du vælge det højre virtuelle netværk når du aktiverer Azure AD-domænetjenester i en af efterfølgende konfigurationstrin.

Udføre de følgende trin til konfiguration for at oprette en Azure virtuelt netværk, hvor du vil aktivere Azure AD-domænetjenester.

1. Gå til **Azure klassisk portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Vælg noden **netværk** i venstre rude.

    ![Netværk node](./media/active-directory-domain-services-getting-started/networks-node.png)

3. Klik på **Ny** i opgaveruden nederst på siden.

    ![Virtuelt Network node](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Vælg **Virtuelt netværk**i noden **Netværkstjenester** .

5. Klik på **Hurtig oprettelse** for at oprette et virtuelt netværk.

    ![Oprette virtuelle netværk - hurtig](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Angiv et **navn** til netværket virtuel. Du kan også vælge at konfigurere **adresseområde** eller **maksimale VM Tæl** til dette netværk. Nu kan du lade indstillingen **DNS-server** , der er angivet til 'Ingen'. Du kan opdatere DNS-serveren konfiguration efter din Aktivér Azure AD-domæne tjenester.

7. Sørg for, at du vælger et understøttede Azure område i rullemenuen **placering** . Se siden [Azure tjenester efter område](https://azure.microsoft.com/regions/#services/) at kende områderne Azure, Azure AD-domænetjenester er tilgængelig.

8. Klik på knappen **Opret et virtuelt netværk** for at oprette dit virtuelle netværk.

    ![Oprette et virtuelt netværk til Azure AD-domænetjenester.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Når det virtuelle netværk er oprettet, Vælg det virtuelle netværk, og klik på fanen **KONFIGURER** .

    ![Oprette et undernet](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Gå til afsnittet **virtuelt netværk adresse mellemrum** . Klik på **Tilføj undernet** , og Angiv et undernet med navnet **AaddsSubnet**. Klik på **Gem** for at oprette undernettet.

    ![Oprette et undernet til Azure AD-domænetjenester.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Opgave 3 – Aktivér Azure AD Domain Services
Den næste Konfigurationsopgave er at [aktivere Azure AD-domænetjenester](active-directory-ds-getting-started-enableaadds.md).
