<properties
    pageTitle="Azure AD-forbindelse: Aktivere enhed tilbageførsel | Microsoft Azure"
    description="Dette dokument indeholder oplysninger om hvordan du aktiverer enhed tilbageførsel ved hjælp af Azure AD-forbindelse"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD-forbindelse: Aktivering af enhed tilbageførsel

>[AZURE.NOTE] Et abonnement på Azure AD Premium er påkrævet for enhed tilbageførsel.

Følgende dokumentation indeholder oplysninger om, hvordan du aktiverer funktionen enhed tilbageførsel i Azure AD-forbindelse. Enhed tilbageførsel bruges i følgende situationer:

- Aktivere betinget adgang, der er baseret på ADFS-enheder (2012 R2 eller nyere) beskyttet programmer (afhængige part tillidsforhold).

Dette giver ekstra sikkerhed og sikkerhed for, at der gives adgang til programmer kun for enheder, der er tillid til. Se [Administrere risiko med betinget adgang](active-directory-conditional-access.md) og [konfiguration af lokale betinget adgang ved hjælp af Azure Active Directory enhed registrering](https://msdn.microsoft.com/library/azure/dn788908.aspx)kan finde flere oplysninger om betinget adgang.

>[AZURE.IMPORTANT]
<li>Enheder skal være placeret i den samme skov som brugerne. Da enheder skal skrives tilbage til et enkelt område, understøtter denne funktion i øjeblikket ikke en installation med flere bruger områder.</li>
<li>Kun én enhed Registreringsindstillinger konfigurationsobjekt kan føjes til lokale Active Directory-området. Denne funktion er ikke kompatibel med en topologi, hvor lokale Active Directory er synkroniseret til flere Azure AD-mapper.</li>

## <a name="part-1-install-azure-ad-connect"></a>Del 1: Installer Azure AD forbindelse
1. Installer Azure AD-forbindelse ved hjælp af brugerdefinerede eller Express indstillinger. Microsoft anbefaler skal starte med at alle brugere og grupper blevet synkroniseret før du aktiverer enhed tilbageførsel.

## <a name="part-2-prepare-active-directory"></a>Del 2: Forberedelse af Active Directory
Brug følgende trin til at forberede brugen af enhed tilbageførsel.

1.  Start PowerShell fra den computer, hvor Azure AD-forbindelse er installeret, i øgede tilstand.

2.  Hvis af Active Directory-PowerShell-modulet ikke er installeret, skal du installere det ved hjælp af følgende kommando:

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Hvis af Azure Active Directory PowerShell-modulet ikke er installeret, derefter kan du hente og installere den fra [Azure Active Directory-modulet til Windows PowerShell (64-bit version)](http://go.microsoft.com/fwlink/p/?linkid=236297). Denne komponent har en afhængighed på Logonassistent, som er installeret med Azure AD-forbindelse.

4.  Kør følgende kommandoer med enterprise Administratoroplysninger, og afslut derefter PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Enterprise legitimationsoplysninger er påkrævet, da navneområdet konfiguration ændres. En domæneadministrator har ikke tilstrækkelige tilladelser.

![PowerShell for aktivering af enhed tilbageførsel](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Beskrivelse:

- Hvis de ikke allerede findes, opretter og konfigurerer ny og ubrugt og objekter under CN = enhed registrering konfiguration, CN = tjenesterne, CN = konfiguration, [skov-dn].
- Hvis de ikke allerede findes, opretter og konfigurerer ny og ubrugt og objekter under CN = RegisteredDevices, [domæne-dn]. Enhedsobjekter oprettes i denne beholder.
- Angiver de nødvendige tilladelser på kontoen Azure AD-forbindelse til at administrere enheder på din Active Directory.
- Kun skal køre i et område, selvom Azure AD-forbindelse installeres på flere områder.

Parametre:

- Domænenavn: Active Directory-domæne hvor enhedsobjekter skal oprettes. Bemærk: Oprettes alle enheder for en given Active Directory-område i et enkelt domæne.
- AdConnectorAccount: Active Directory-konto, der skal bruges ved Azure AD-forbindelse til at administrere objekter i kataloget. Dette er den konto, der bruges af synkronisering af Azure AD-forbindelse til at oprette forbindelse til AD. Hvis du har installeret ved hjælp af hurtig indstillinger, er det kontoen præfikset MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Del 3: Aktivér enhed tilbageførsel i Azure AD-forbindelse
Benyt følgende fremgangsmåde for at aktivere enhed tilbageførsel i Azure AD-forbindelse.

1.  Køre installationsguiden igen. Vælg **Tilpas indstillinger for synkronisering** fra siden opgaver, og klik på **Næste**.
![Brugerdefineret installation tilpasse indstillinger for synkronisering](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  På siden valgfrie funktioner vil enhed tilbageførsel ikke længere være nedtonet. Skal du vil note, hvis Azure AD Connect denne trin ikke er færdige enhed tilbageførsel være nedtonet ud på siden valgfrie funktioner. Markér afkrydsningsfeltet ud for enhed tilbageførsel, og klik på **Næste**. Hvis afkrydsningsfeltet stadig er deaktiveret, skal du se [fejlfinding sektion](#the-writeback-checkbox-is-still-disabled).
![Brugerdefineret installation enhed tilbageførsel valgfrie funktioner](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  På siden tilbageførsel vises medfølgende domænet som standard enhed tilbageførsel skov.
![Brugerdefineret installation enhed tilbageførsel target skov](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Fuldføre installationen af guiden uden yderligere konfiguration ændringer. Hvis det er nødvendigt, kan du referere til [brugerdefineret installation af Azure AD-forbindelse.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Aktivere betinget adgang
Detaljerede instruktioner til at aktivere dette scenario er tilgængelige i [konfigurationen af lokale betinget Access ved hjælp af Azure Active Directory enhed registrering](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Bekræfte enheder synkroniseres til Active Directory
Enhed tilbageførsel skulle nu fungerer korrekt. Vær opmærksom på, at det kan tage op til 3 timer for enhedsobjekter, der skal skrives tilbage til AD.  Gør følgende for at bekræfte, at dine enheder der synkroniseres korrekt, når Synkroniser reglerne fuldført:

1.  Start Active Directory Administrative Center.
2.  Udvid RegisteredDevices inden for det domæne, er der knyttet.
![Active Directory-administration registreret enheder](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Aktuelle registrerede enheder, vises der.
![Active Directory-administration registreret listen enheder](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="the-writeback-checkbox-is-still-disabled"></a>Afkrydsningsfeltet tilbageførsel er stadig deaktiveret
Hvis afkrydsningsfeltet for enhed tilbageførsel ikke er aktiveret, selvom du har fulgt vejledningen ovenfor, følgende trin fører dig gennem hvad installationsguiden kontrollerer før feltet er aktiveret.

Første ting første:

- Kontrollér, at mindst én skov har Windows Server 2012R2. Objekttype enhed skal være til stede.
- Hvis installationsguiden allerede kører, registreres den ændringerne ikke. I dette tilfælde skal fuldføre installationsguiden, og kør den igen.
- Sørg for den konto, du giver i scriptet initialisering er faktisk den korrekte bruger, der bruges af Active Directory Connector. For at bekræfte, at følge disse trin:
    - Åbn **Tjenesten Brugerprofilsynkronisering**fra startmenuen.
    - Åbn fanen **forbindelser** .
    - Find forbindelsen med type Active Directory Domain Services, og vælg den.
    - Under **Handlinger**skal du vælge **Egenskaber**.
    - Gå til at **oprette forbindelse til Active Directory-område**. Kontrollér, at domænet og bruger angivet på dette skærmbillede Sammenlign den konto, der er angivet til scriptet.
![Connector-konto i Synkroniser Service Manager](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Kontrollere konfigurationen i Active Directory:
- Kontrollér, at tjenesten enhed registrering er placeret i nedenstående placering (CN = DeviceRegistrationService, CN = enhed registrering tjenesterne, CN = enhed registrering konfiguration, CN = tjenesterne, CN = konfiguration) under konfiguration naming kontekst.

![Fejlfinding af DeviceRegistrationService i konfigurationen navneområde](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Kontroller, der er kun ét konfigurationsobjekt ved at søge navneområdet konfiguration. Hvis der er mere end ét skal du slette duplikatet.

![Fejlfinding i forbindelse med, søge efter de dublerede objekter](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- Sørg for attributten msDS-DeviceLocation er til stede og har en værdi for objektet enhed Registration Service. Opslag denne placering, og Sørg for, at det er til stede med objekttype msDS-DeviceContainer.

![Fejlfinding af msDS DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Fejlfinding af RegisteredDevices objektklasse](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Kontrollere den konto, der bruges af Active Directory Connector har nødvendige tilladelser på objektbeholderen registreret enheder, der er fundet af det forrige trin. Dette er de forventede tilladelser på denne beholder:

![Fejlfinding i forbindelse med, skal du kontrollere tilladelser på objektbeholder](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Kontrollere Active Directory-konto, har tilladelser til CN = enhed registrering konfiguration, CN = tjenesterne, CN = konfigurationsobjekt.

![Fejlfinding i forbindelse med, skal du kontrollere tilladelser på enheden registrering konfiguration](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Yderligere oplysninger
- [Administrere risiko med betinget adgang](active-directory-conditional-access.md)
- [Konfiguration af lokale betinget Access ved hjælp af Azure Active Directory enhed registrering](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
