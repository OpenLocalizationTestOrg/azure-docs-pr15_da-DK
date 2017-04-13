<properties
    pageTitle="Azure AD-forbindelse: Næste trin og hvordan du administrerer Azure AD-forbindelse | Microsoft Azure"
    description="Lær, hvordan du udvider standardkonfiguration og driftsopgaver til Azure AD-forbindelse."
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
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Næste trin og hvordan du administrerer Azure AD-forbindelse
Følgende er Avanceret funktionsdygtige emner, der gør det muligt at tilpasse Azure Active Directory oprette forbindelse til opfylder organisationens behov og krav.  

## <a name="add-additional-sync-administrators"></a>Tilføje yderligere Synkroniser administratorer
Som standard vil kun brugere, der gjorde installation og lokale administratorer kunne administrere installerede Synkroniser-program. Find gruppen med navnet ADSyncAdmins på den lokale server til flere personer skal kunne få adgang til og administrere program synkronisering og føje dem til denne gruppe.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Tildeling af licenser til Azure AD Premium og mobilitet Enterprise brugere

Nu, hvor brugerne er blevet synkroniseret til skyen, skal du tildele dem en licens, så de kan komme i gang med skyen apps som Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Tildele en Azure AD Premium- eller Enterprise mobilitet-programpakken
--------------------------------------------------------------------------------
1. Log på portalen Azure som Administrator.
2. Vælg **Active Directory**i venstre side.
3. Siden Active Directory, skal du dobbeltklikke på den mappe, der indeholder de brugere, du vil aktivere.
4. Vælg **licenser**øverst på siden med mappen.
5. Vælg Active Directory Premium- eller Enterprise mobilitet pakke på siden licenser, og klik derefter på **Tildel**.
6. Vælg de brugere, du vil tildele licenser til i dialogboksen, og klik derefter på ikonet markering for at gemme ændringerne.


## <a name="verifying-the-scheduled-synchronization-task"></a>Ved at bekræfte den planlagte synkroniseringsopgave
Hvis du vil kontrollere status for en synkronisering kan du gøre dette ved at markere i portalen Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>At bekræfte opgaven planlagt synkronisering
--------------------------------------------------------------------------------
1. Log på portalen Azure som Administrator.
2. Vælg **Active Directory**i venstre side.
3. Siden Active Directory, skal du dobbeltklikke på den mappe, der indeholder de brugere, du vil aktivere.
4. Vælg **Katalogintegration**øverst på siden med mappen.
5. Under integration med lokale active directory note sidst synkronisering.

<center>![Skyen](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Starte en af planlagt synkroniseringsopgave
Hvis du har brug at køre en synkroniseringsopgave kan du gøre dette ved at køre med guiden Azure AD-forbindelse igen.  Du skal angive dine Azure AD-legitimationsoplysninger.  Vælg opgaven, **Tilpas indstillinger for synkronisering** i guiden, og klik på Næste i guiden. Til sidst skal du sikre, at feltet **starte synkroniseringen, så snart den oprindelige konfiguration er fuldført** , er markeret.

<center>![Skyen](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Yderligere oplysninger om Azure AD-forbindelse Synkroniser: Scheduler, se [Azure AD forbinde Scheduler](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Flere opgaver, der er tilgængelige i Azure AD-forbindelse
Når din indledende installation af Azure AD-forbindelse, kan du altid starte guiden igen fra Azure AD-forbindelse start side eller skrivebord genvej.  Du vil bemærke, at igen ved at følge guiden indeholder nogle nye indstillinger i form af andre opgaver.  

Den følgende tabel indeholder en oversigt over disse opgaver og en kort beskrivelse på hver af dem.

![Deltage i regel](./media/active-directory-aadconnect-whats-next/addtasks.png)


Yderligere opgave | Beskrivelse
------------- | ------------- |
Få vist den valgte situation  |Kan du få vist din aktuelle Azure AD-forbindelse løsning.  Dette omfatter generelle indstillinger, synkroniserede mapper, indstillinger for synkronisering osv.
Tilpasse indstillinger for synkronisering | Du kan ændre den aktuelle konfiguration, herunder tilføje yderligere Active Directory-områder til konfiguration eller aktivering synkroniseringsindstillinger som bruger, gruppe, enhed eller adgangskode tilbageskrivning.
Aktivere midlertidige tilstand |  Dette kan du faseoplysninger, der senere skal synkroniseres, men der eksporteres til Azure AD eller Active Directory.  Dette kan du få vist eksempler på synkroniseringen, inden de opstår.

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
