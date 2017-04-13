<properties
    pageTitle="Azure AD-domænetjenester: Aktivere synkronisering af adgangskoder | Microsoft Azure"
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
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Aktivere synkronisering af adgangskoder til Azure AD-domænetjenester
I foregående opgaver, du har aktiveret Azure AD-domænetjenester for din Azure AD-lejer. Den næste opgave er at aktivere legitimationsoplysninger hashes kræves til NTLM og Kerberos-godkendelse til at synkronisere til Azure AD-domænetjenester. Når legitimationsoplysninger synkronisering er konfigureret, kan brugerne logge på til administrerede domænet ved hjælp af deres virksomhedens legitimationsoplysninger.

Trinnene er forskellige afhængigt af om din organisation har et kun skyen Azure AD lejer eller er indstillet til at synkronisere med din lokale mappe ved hjælp af Azure AD-forbindelse.

<br>

> [AZURE.SELECTOR]
- [Kun skyen Azure AD-lejer](active-directory-ds-getting-started-password-sync.md)
- [Synkroniseret Azure AD-lejer](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Opgave 5: Aktivere synkronisering af adgangskoder til AAD Domain Services til en skybaseret kun Azure AD-lejer
Azure AD-domænetjenester kræver legitimationsoplysninger hashes i et format, der passer til NTLM og Kerberos-godkendelse til at godkende brugere på administrerede domænet. Medmindre du aktiverer AAD Domain Services for din lejer, Azure AD ikke oprette eller gemme legitimationsoplysninger hashes i det format, der kræves til NTLM eller Kerberos-godkendelse. Indlysende sikkerhedsmæssige årsager gemmer Azure AD også ikke nogen legitimationsoplysninger i klar tekst-formular. Azure AD har derfor ikke en metode til at generere disse NTLM- eller Kerberos legitimationsoplysninger hashes baseret på brugernes eksisterende legitimationsoplysninger.

> [AZURE.NOTE] Hvis din organisation har et skyen kun Azure AD-lejer, brugere, der skal bruge Azure AD-domænetjenester skal ændre deres adgangskode.

Denne adgangskode Skift proces får legitimationsoplysninger hashes, der kræves, før Azure AD Domain Services for Kerberos og NTLM-godkendelse der kan oprettes i Azure AD. Du kan enten udløbe adgangskoder for alle brugere i lejeren, der skal bruge Azure AD-domænetjenester eller Fortæl disse brugere til at ændre deres adgangskode.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Aktivere NTLM og Kerberos legitimationsoplysninger hash oprettelse for en kun skyen Azure AD-lejer
Her er instruktioner, skal du angive slutbrugere, så de kan ændre deres adgangskoder:

1. Gå til siden Azure AD Access Panel for din organisation på [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Vælg fanen **profil** på denne side.

3. Klik på feltet **Skift adgangskode** på denne side.

    ![Oprette et virtuelt netværk til Azure AD-domænetjenester.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Hvis du ikke kan se indstillingen **Skift adgangskode** på siden Access Panel, kan du sikre dig, at din organisation har konfigureret [Administration af adgangskoder i Azure AD](../active-directory/active-directory-passwords-getting-started.md).

4. Skriv din eksisterende (gamle) adgangskode og skriv en ny adgangskode og bekræft den, på siden **Skift adgangskode** . Klik på **Send**.

    ![Oprette et virtuelt netværk til Azure AD-domænetjenester.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Når du har ændret din adgangskode, vil den nye adgangskode snart kan bruges i Azure AD-domænetjenester. Når du har et par minutter (typisk der minutter om 20), du kan logge på computere, der er knyttet til administrerede domænet med den nyligt ændrede adgangskode.

<br>

## <a name="related-content"></a>Relateret indhold

- [Sådan opdaterer du din egen adgangskode](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Introduktion til administration af adgangskoder i Azure AD](../active-directory/active-directory-passwords-getting-started.md).

- [Aktivere synkronisering af adgangskoder til AAD Domain Services til en synkroniseret Azure AD-lejer](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrere en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-administer-domain.md)

- [Deltage i en Windows virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-windows-vm.md)

- [Deltage i en Red Hat Enterprise Linux virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
