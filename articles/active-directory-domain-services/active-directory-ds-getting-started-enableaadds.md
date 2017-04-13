<properties
    pageTitle="Azure AD-domænetjenester: Aktivere Azure AD-domænetjenester | Microsoft Azure"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Aktivere Azure AD-domænetjenester

## <a name="task-3-enable-azure-ad-domain-services"></a>Opgave 3: Aktivere Azure AD-domænetjenester
I denne opgave skal aktivere du Azure AD-domænetjenester til adresselisten. Udføre de følgende trin til konfiguration for at aktivere Azure AD-domænetjenester til adresselisten.

1. Gå til **Azure klassisk portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Vælg noden **Active Directory** i den venstre rude.

3. Vælg Azure AD-lejer (bibliotek), du vil aktivere Azure AD-domænetjenester.

    ![Vælg Azure AD-mappe](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Klik på **Konfigurer** .

    ![Konfigurere fanen for directory](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Rul ned til afsnittet **-domænetjenester**.

    ![Afsnit Domain Services konfiguration](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Skift indstillingen med titlen **Aktivér domain services for denne mappe** til **Ja**. Du oplever nogle flere konfigurationsindstillinger for Azure AD-domænetjenester vises på siden.

    ![Aktivere Domain Services](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Når du aktiverer Azure AD-domænetjenester for din lejer, Azure AD genererer og gemmer de Kerberos og NTLM-legitimationsoplysninger hashes, der kræves til godkendelse af brugere.

7. Angiv **DNS-domænenavnet på domænetjenester**.

   - Standarddomænenavn af mappen (det vil sige, slutter med den **. onmicrosoft.com** domænesuffiks) er valgt som standard.

   - Listen indeholder alle domæner, der er konfigureret til adresselisten Azure AD-, herunder bekræftet samt ikke-bekræftede domæner, som du konfigurere i fanen 'Domains'.

   - Desuden kan du også skrive et brugerdefineret domænenavn. I dette eksempel har vi har skrevet i et brugerdefineret domænenavn 'contoso100.com'.

     > [AZURE.WARNING] Sikre, at præfikset domæne af navnet på det domæne, du angiver (for eksempel 'contoso100' i 'contoso100.com' domain name) er mindre end 15 tegn. Du kan ikke oprette et Azure AD Domain Services-domæne med et domæne præfiks, der er mere end 15 tegn.

8. Sørg for, at DNS-domænenavn, du har valgt til administrerede domænet ikke allerede findes i det virtuelle netværk. Kontrollere specifikt, hvis:

   - Du har allerede et domæne med det samme DNS-domænenavn på det virtuelle netværk.

   - det virtuelle netværk, du har valgt har en VPN-forbindelse til dit lokale netværk, og du har et domæne med det samme DNS-domænenavn på dit lokale netværk.

   - Du har en eksisterende skytjeneste med dette navn på det virtuelle netværk.

9. Næste trin er at vælge et virtuelt netværk, hvor du vil have Azure AD-domænetjenester skal være tilgængeligt. Vælg virtuelt netværk og dedikeret undernet, du har oprettet i rullemenuen med titlen **forbinde domænetjenester til denne virtuelle netværk**.

   - Sørg for, at det virtuelle netværk, du har angivet, tilhører en Azure område, der understøttes af Azure AD-domænetjenester. Referere til siden [Azure tjenester efter område](https://azure.microsoft.com/regions/#services/) at kende områderne Azure, Azure AD-domænetjenester er tilgængelig.

   - Virtuelle netværk, der hører til et område, hvor Azure AD-domænetjenester ikke understøttes vises ikke i på rullelisten.
   
   - Bruge et dedikeret undernet inden for det virtuelle netværk til Azure AD-domænetjenester. Kontrollér, at du ikke markerer gateway-undernettet. Se [netværk overvejelser i forbindelse med](active-directory-ds-networking.md). 

   - På samme måde, vises virtuel netværk, der er oprettet ved hjælp af Azure ressourcestyring ikke i på rullelisten. Ressourcestyring-baserede virtuelle netværk, understøttes ikke i øjeblikket ved Azure AD-domænetjenester.

10. Klik på **Gem** for at aktivere Azure AD-domænetjenester, fra opgaveruden nederst på siden.

11. Siden viser en ' ventende... " fase, mens Azure AD-domænetjenester aktiveres for adresselisten.

    ![Aktivere Domain Services - afventer tilstand](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Azure AD-domænetjenester giver høj tilgængelighed domænets administrerede. Når du aktiverer Azure AD-domænetjenester, Bemærk de IP-adresser, hvormed Domain Services er tilgængelige på den virtuelle netværk vises én ad gangen. Den anden IP-adresse vises kort, snart tjenesten giver høj tilgængelighed for dit domæne. Når høj tilgængelighed er konfigureret og aktive for dit domæne, skal du se to IP-adresser i sektionen **domain services** under fanen **Konfigurer** .

12. Efter omkring 20-30 minutter, kan du se den første IP-adresse, hvor Domain Services er tilgængelig på netværket virtuelle i feltet **IP-adresse** på siden **Konfigurer** .

    ![Domain Services aktiveret - første IP klargjort](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Når høj tilgængelighed er aktiveret for dit domæne, kan du se to IP-adresser, der vises på siden. Domænet administrerede findes i dine valgte virtuelt netværk på disse to IP-adresser. Bemærk ned til IP-adresserne så du kan opdatere DNS-indstillingerne for netværket virtuel. Dette trin kan virtuelle maskiner på det virtuelle netværk til at oprette forbindelse til domænet for handlinger som forbindelse til et domæne.

    ![Domain Services aktiveret - begge IP'er klargjort](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Afhængigt af størrelsen af din Azure AD-lejer (antal brugere, grupper osv.), synkronisering til domænet administrerede tager et stykke tid. Denne synkronisering sker i baggrunden. Det kan tage en dag eller to for alle brugere, gruppemedlemskaber og legitimationsoplysninger til at blive synkroniseret til store lejere med tusindvis af objekter.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Opgave 4 – Update DNS-indstillingerne for det Azure virtuelle netværk
Den næste Konfigurationsopgave er at [opdatere DNS-indstillingerne for det Azure virtuelle netværk](active-directory-ds-getting-started-dns.md).
