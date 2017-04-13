<properties
    pageTitle="Konfigurere sikker LDAP (LDAPS) i Azure AD-domænetjenester | Microsoft Azure"
    description="Konfigurere Secure LDAP (LDAPS) til en administreret Azure AD Domain Services-domæne"
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
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurere Secure LDAP (LDAPS) til en administreret Azure AD Domain Services-domæne
I denne artikel beskrives, hvordan du kan aktivere Secure Lightweight Directory Access Protocol (LDAPS) domænets Azure AD-domænetjenester administrerede. Sikker LDAP er også kendt som ' Lightweight Directory Access Protocol (LDAP) over Secure Sockets Layer (SSL) / TLS Transport Layer Security ()'.

## <a name="before-you-begin"></a>Inden du går i gang
For at udføre de opgaver, der er angivet i denne artikel, skal du:

1. Et gyldigt **Azure-abonnement**.

2. Et **directory Azure AD** - enten synkroniseret med en lokal adresseliste eller en kun skyen mappe.

3. **Azure AD-domænetjenester** skal være aktiveret for Azure AD-mappen. Hvis du ikke har gjort det, skal du følge alle opgaverne i [Introduktion til](./active-directory-ds-getting-started.md).

4. Et **certifikat, der skal bruges til at aktivere sikker LDAP**.
    - **Anbefalede** - få et certifikat fra din virksomhed CA eller offentlige nøglecenter. Indstillingen for konfiguration er mere sikker.
    - Alternativt kan kan du også vælge at [oprette et selvsigneret certifikat](#task-1---obtain-a-certificate-for-secure-ldap) som vist senere i denne artikel.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Krav til sikker LDAP-certifikat
Hente et gyldigt certifikat i henhold til de følgende retningslinjer, før du aktiverer sikker LDAP. Du støder på fejl, hvis du forsøger at aktivere sikker LDAP domænets administrerede med en ugyldig/forkerte certifikat.

1. **Du har tillid til udsteder** - certifikatet, der skal være udstedt af et nøglecenter, der er tillid til ved computere, der har brug for at oprette forbindelse til det domæne, ved hjælp af sikker LDAP. Denne nøglecenter muligvis organisationens enterprise nøglecenter eller et offentligt nøglecenter, der er tillid til disse computere.

2. **Levetid** - certifikatet, der skal gælde for mindst de næste 3-6 måneder. Sikker LDAP-adgang til domænet administrerede afbrydes, når certifikatet udløber.

3. **Emnenavn** - emnenavnet på certifikatet, der skal være et jokertegn for dit administrerede domæne. Eksempelvis hvis domænet har navnet 'contoso100.com', certifikatets emnenavn skal være ' *. contoso100.com'. Angiv DNS-navn (emne andet navn) til dette jokertegn navn.

3. **Tasten brugen** - certifikatet, der skal være konfigureret til følgende bruger - digitale signaturer og Kodeomsætning.

4. **Formålet med certifikatet** - certifikatet skal være gyldige for SSL server-godkendelse.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Opgave 1 – få et certifikat for sikker LDAP
Den første opgave omfatter bestille et certifikat, der bruges til sikker LDAP-adgang til administrerede domænet. Har du to muligheder:

- Få et certifikat fra et nøglecenter. Nøglecenter muligvis organisationens enterprise CA eller et offentligt nøglecenter.

- Oprette et selvsigneret certifikat.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Alt (anbefales) – få et sikkert LDAP-certifikat fra et nøglecenter
Hvis din organisation installerer en enterprise offentlig nøgle infrastruktur, skal du hente et certifikat fra enterprise nøglecenter (CA) for organisationen. Hvis din organisation henter dens certifikater fra et offentligt nøglecenter, skal du få sikkert LDAP-certifikat fra den offentlige nøglecenter.

Når der anmodes om et certifikat, kan du sikre dig, at du følger de krav, der er beskrevet i [kravet om sikkert LDAP-certifikat](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] Klientcomputere, der har brug for at oprette forbindelse til den administrerede domæne ved hjælp af sikker LDAP skal have tillid til LDAPS certifikatets udsteder.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Alternativ B - Opret et selvsigneret certifikat til sikker LDAP
Du kan vælge at oprette et selvsigneret certifikat til sikker LDAP, hvis:

- certifikater i din organisation er ikke udstedt af et nøglecenter i virksomheden eller
- Du forventer ikke at bruge et certifikat fra et offentligt nøglecenter.

**Oprette et selvsigneret certifikat ved hjælp af PowerShell**

Åbn et nyt vindue i PowerShell som **Administrator** på din Windows-computer, og Skriv følgende kommandoer, for at oprette et nyt selvsigneret certifikat.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

Erstat 'contoso100.com' med domænenavn DNS for domænet Azure AD-domænetjenester administrerede i det foregående eksempel.

![Vælg Azure AD-mappe](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Den nyoprettede selvsigneret certifikat er placeret i den lokale computer lager.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Opgave 2 – Eksportér sikker LDAP certifikat til en. PFX-fil
Før du starter denne opgave, kan du sikre dig, at du har fået secure LDAP-certifikat fra dit enterprise nøglecenter eller et offentligt nøglecenter eller har oprettet et selvsigneret certifikat.

Udfør følgende trin, hvis du vil eksportere LDAPS certifikat til en. PFX-fil.

1. Tryk på knappen **Start** , og skriv **R**. I dialogboksen **Kør** , Skriv **mmc** , og klik på **OK**.

    ![Start MMC-konsollen](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. **Kontrol af brugerkonti** prompt, klik på **Ja** for at starte MMC (Microsoft Management Console) som administrator.

3. Fra menuen **filer** skal du klikke på **Tilføj/fjern Snap-i...**.

    ![Føje snap-in til MMC-konsol](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. Vælg snap-in-programmet **certifikater** i dialogboksen **Tilføj eller fjern Snap-ins** , og klik på den **Tilføj >** knappen.

    ![Føje snap-in'en Certifikater til MMC-konsol](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. Vælg **computerkonto** guiden **certifikater snap-in** , og klik på **Næste**.

    ![Tilføje certifikater snap-in til computerkonto](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. På siden **Vælg Computer** skal du vælge **lokale computer: (computeren, som denne konsol kører)** og klikke på **Udfør**.

    ![Tilføje certifikater snap-in - Vælg computer](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. Klik på **OK** for at føje snap-in-programmet Certifikater til MMC i dialogboksen **Tilføj eller fjern Snap-ins** .

    ![Føje snap-in'en Certifikater til MMC - færdig](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. Klik for at udvide **Rod for konsol**i MMC-vinduet. Du bør se certifikater snap-in-indlæst. Klik på **certifikater (lokalcomputer)** til at udvide. Klik for at udvide noden **personlige** , efterfulgt af noden **certifikater** .

    ![Åbne personlige certifikater](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Du bør se et selvsigneret certifikat, vi oprettede. Du kan undersøge egenskaberne for certifikatet for at sikre, at miniature, der matcher rapporteres for windows PowerShell, da du oprettede certifikatet.

10. Markér afkrydsningsfeltet selvsigneret certifikat, og **Højreklik**. Vælg **Alle opgaver** fra genvejsmenuen, og vælg **eksportere …**.

    ![Eksportere certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. Klik på **Næste**i **Guiden Eksporter for certifikat**.

    ![Eksportguiden i certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. Vælg **Ja, Eksporter privat nøgle**, og klik på **Næste**på siden **Eksporter privat nøgle** .

    ![Eksportere certifikat privat nøgle](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] Du skal eksportere den private nøgle sammen med certifikatet. Hvis du angiver en PFX, ikke som indeholder den private nøgle til certifikatet, mislykkes aktivere sikker LDAP domænets administrerede.

13. På siden **Filformat til eksport** vælge **Personal Information Exchange - PKCS #12 (. PFX)** som formatet for det eksporterede certifikat.

    ![Eksportere certifikat-filformat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Kun den. PFX-filformat understøttes. Ikke eksportere certifikat til den. Virksomheden-filformat.

14. På siden **sikkerhed** vælge indstillingen **adgangskode** og skriv en adgangskode for at beskytte den. PFX-fil. Husk denne adgangskode, da det er påkrævet i den næste opgave. Klik på **Næste** for at fortsætte.

    ![Adgangskode til certifikat eksport ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Noter denne adgangskode. Du skal bruge den samtidig med at sikre LDAP for denne administrerede domæne i [opgave 3 - aktivere sikker LDAP for administrerede domænet](#task-3---enable-secure-ldap-for-the-managed-domain)

15. Angiv det filnavn og placering, hvor du vil eksportere certifikat på siden **filer til eksport** .

    ![Sti til eksportere certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. Klik på **Udfør** for at eksportere certifikatet til en PFX-fil på den efterfølgende side. Du bør se bekræftelsesdialogboks, når certifikatet, der er eksporteret.

    ![Eksportere certifikat, der er udført](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Opgave 3 - Aktivér sikker LDAP for administrerede domænet
Hvis du vil aktivere sikker LDAP, skal du udføre de følgende trin til konfiguration:

1. Gå til **[Azure klassisk portal](https://manage.windowsazure.com)**.

2. Vælg noden **Active Directory** i den venstre rude.

3. Vælg den Azure AD mappe (også kaldet "lejer"), som du har aktiveret Azure AD-domænetjenester.

    ![Vælg Azure AD-mappe](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Klik på **Konfigurer** .

    ![Konfigurere fanen for directory](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Rul ned til afsnittet **-domænetjenester**. Du bør se muligheden med titlen **Secure LDAP (LDAPS)** , som vist på følgende skærmbillede:

    ![Afsnit Domain Services konfiguration](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Klik på knappen **Konfigurer certifikat …** for at få vist dialogboksen **Konfigurer certifikat til sikker LDAP** .

    ![Konfigurere certifikat til sikker LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Klik på mappeikonet under **PFX fil med certifikat** til at angive PFX-fil, som indeholder det certifikat, du vil bruge til sikker LDAP-adgang til administrerede domænet. Også angive den adgangskode, du har angivet, når du eksporterer certifikatet til PFX-fil. Klik derefter på knappen udført nederst.

    ![Angiv sikker LDAP PFX-fil og en adgangskode](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. Afsnittet **domænetjenester** i fanen **Konfigurer** skal få nedtonet og er i den **ventende...** tilstand for et par minutter. I denne periode LDAPS certifikatet er bekræftet nøjagtigheden og sikker LDAP er konfigureret for dit administrerede domæne.

    ![Secure LDAP - afventer tilstand](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Det tager om 10 til 15 minutter for at aktivere sikker LDAP domænets administrerede. Hvis den medfølgende sikkert LDAP-certifikat ikke svarer til de krævede kriterier, sikker LDAP er ikke aktiveret for mappen, og du får vist en fejl. Eksempel: domænenavnet er forkert, certifikatet er udløbet eller udløber snart osv.

9. Når sikker LDAP er blevet aktiveret for domænet administrerede den **ventende...** meddelelse bør forsvinde. Du bør se miniature for det certifikat, der vises.

    ![Sikker LDAP aktiveret](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Opgave 4 – Aktivér sikker LDAP-adgang via internettet
**Valgfri opgave** - Hvis du ikke vil have adgang til administrerede domænet ved hjælp af LDAPS via internettet, skal du springe denne Konfigurationsopgave.

Før du begynder denne opgave, sikre, at du har fuldført trinnene beskrevet i [opgave 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Du bør se muligheden for at **AKTIVERE SECURE LDAP-adgang via internettet** i sektionen **domain services** på siden **Konfigurer** . Denne indstilling er som standard angivet til **Nej** , da internetadgang til administrerede domænet over sikker LDAP er som standard deaktiveret.

    ![Sikker LDAP aktiveret](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Slå **AKTIVERE sikker LDAP-adgang ved hjælp af internettet** til **Ja**. Klik på knappen **Gem** på panelet nederst.
    ![Sikker LDAP aktiveret](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. Sektionen **domæneservices** under fanen **Konfigurer** skal få nedtonet og er i den **ventende...** tilstand for et par minutter. Efter et stykke tid, er internetadgang til domænet administrerede over sikker LDAP aktiveret.

    ![Secure LDAP - afventer tilstand](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Det tager omkring 10 minutter for at aktivere internetadgang over sikker LDAP domænets administrerede.

4. Når sikker LDAP-adgang til domænet administrerede via internettet er blevet aktiveret, den **ventende...** meddelelse bør forsvinde. Du bør se den eksterne IP-adresse, der kan bruges til at få adgang til mappen over LDAPS i feltet **Ekstern IP-adresse til LDAPS adgang**.

    ![Sikker LDAP aktiveret](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Opgave-5 - konfiguration af DNS for at få adgang til administrerede domænet fra internettet
**Valgfri opgave** - Hvis du ikke vil have adgang til administrerede domænet ved hjælp af LDAPS via internettet, skal du springe denne Konfigurationsopgave.

Før du begynder denne opgave, sikre, at du har fuldført trinnene beskrevet i [opgave 4](#task-4---enable-secure-ldap-access-over-the-internet).

Når du har aktiveret sikker LDAP-adgang via internettet for dit administrerede domæne, skal du opdatere DNS, så klientcomputere kan finde denne administrerede domæne. I slutningen af opgave 4 vises en ekstern IP-adresse under fanen **Konfigurer** i **Ekstern IP-adresse til LDAPS adgang**.

Konfigurere din eksterne DNS-udbyder, så denne eksterne IP-adresse peger på DNS-navnet på den administrerede domæne (for eksempel ' contoso100.com'). I vores eksempel skal vi oprette følgende DNS-post:

    contoso100.com  -> 52.165.38.113

Det var det – nu er du klar til at oprette forbindelse til det administrerede domæne ved hjælp af sikker LDAP via internettet.

> [AZURE.WARNING] Husk, at klientcomputere skal have tillid til LDAPS certifikatets udsteder for at kunne oprette forbindelse til det administrerede domæne ved hjælp af LDAPS. Hvis du bruger et enterprise nøglecenter eller et offentligt der er tillid til nøglecenter, behøver du ikke foretage dig noget, da klientcomputere have tillid til disse nøglecentre. Hvis du bruger et selvsigneret certifikat, skal du installere den offentlige del af et selvsigneret certifikat på butikken, der er tillid til certifikatet på klientcomputeren.

<br>

## <a name="related-content"></a>Relateret indhold

- [Administrere en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-administer-domain.md)
