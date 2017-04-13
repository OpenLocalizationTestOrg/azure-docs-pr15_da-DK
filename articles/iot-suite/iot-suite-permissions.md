<properties
  pageTitle="Azure IoT pakke og Azure Active Directory | Microsoft Azure"
  description="I denne artikel beskrives, hvordan Azure IoT pakke anvender Azure Active Directory til at administrere tilladelser."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Tilladelser på webstedet azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Hvad sker der, når du logger på

Når du logger på første gang på [azureiotsuite.com][lnk-azureiotsuite], webstedet afgør de tilladelsesniveauer, som du har baseret på den aktuelt markerede Azure Active Directory (AAD) lejer og Azure-abonnement.

1.  Webstedet først finder fra Azure hvilke AAD lejere, du tilhører for at udfylde listen over lejere set ud for dit logget på brugernavn. Webstedet kan kun få bruger tokens for én lejer ad gangen på nuværende tidspunkt. Som et resultat, når du skifter til en anden lejer ved hjælp af rullemenuen i øverste højre hjørne, logger webstedet igen dig i til den pågældende lejer til at hente tokens for lejeren.

2.  Webstedet finder derefter fra Azure abonnementer, du har knyttet til den valgte lejer. Du kan se hvilke abonnementer der er tilgængelige, når du opretter en ny forudkonfigurerede løsning.

3.  Til sidst skal henter webstedet alle ressourcerne, der i de abonnementer og ressourcegrupper, mærket som forudkonfigurerede løsninger og udfylder felter på startsiden.

I nedenstående afsnit beskrives de roller, der kan kontrollere adgang til de forudkonfigurerede løsninger.

## <a name="aad-roles"></a>AAD roller

Rollerne AAD styre mulighed for klargøring forudkonfigureret løsninger og administrere brugere i en forudkonfigureret løsning.

Du kan finde flere oplysninger om administratorroller i AAD i [tildele administratorroller i Azure AD][lnk-aad-admin], men i denne artikel fokuserer primært på **Global Administrator** og rollerne **Bruger/medlem af domæne** , som bruges af de forudkonfigurerede løsninger.

**Global Administrator:** Der kan være mange globale administratorer per AAD lejer. Når du opretter en AAD lejer, er du som standard den globale administrator af lejeren. Den globale administrator kan tildele en forudkonfigureret løsning og er tildelt **en administratorrolle for programmet inde i deres AAD lejer** . Hvis en anden bruger i samme lejer AAD opretter et program, er den globale administrator er tildelt standardrollen dog **Kun IMPLICIT læse**. Globale administratorer kan tildele roller for programmer, der bruger [Azure klassisk portal][lnk-classic-portal].

**Bruger/medlem af domæne:** Der kan være mange domæne brugere/medlemmer per AAD lejer. En domænebruger kan klargøre en forudkonfigureret løsning ved hjælp af [azureiotsuite.com] [ lnk-azureiotsuite] websted. Standardrollen de er tildelt til programmet de Klargør er **ADMINISTRATOR**. De kan oprette et program med build.cmd scriptet i [azure-iot-remote-overvågning] [ lnk-rm-github-repo] eller [azure-iot-skønnet-vedligeholdelse] [ lnk-pm-github-repo] lager, men de er tildelt standardrollen er **IMPLICIT SKRIVEBESKYTTET**, som de ikke har tilladelse til at tildele roller. Hvis en anden bruger i AAD lejeren opretter et program, er de som standard for det pågældende program tildelt rollen **IMPLICIT SKRIVEBESKYTTET** . De har ikke mulighed for at tildele roller for programmer. Derfor kan ikke tilføje brugere eller roller til brugere til et program, selvom de klargjort den.

**Gæst bruger/gæst:** Der kan være mange gæst brugere/gæster per AAD lejer. Gæstebrugere har et begrænset antal rights i AAD lejeren. Som et resultat, kan ikke gæstebrugere klargøres en forudkonfigureret løsning i AAD lejeren.

Yderligere oplysninger finder du se følgende ressourcer:

- [Oprette eller redigere brugere i Azure AD][lnk-create-edit-users]
- [Tildele App roller i AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure abonnement administratorroller

Azure-administratorroller styre muligheden for at knytte et Azure-abonnement til en AD-lejer.

Du kan få mere at vide om Azure samtidig Administrator, tjenesteadministratoren og -kontoadministratoren rollerne i artiklen [Sådan tilføje eller ændre Azure samtidig Administrator, tjenesteadministratoren og kontoadministrator][lnk-admin-roles].

## <a name="application-roles"></a>Programmet roller

Rollerne programmet styre adgangen til enheder i din forudkonfigurerede løsning.

Der er to defineret og én implicit rolle, der er defineret i det program, som oprettes, når du klargør en forudkonfigureret løsning.

-   **ADMINISTRATOR:** Har fuld kontrol til at tilføje, administrere og fjerne enheder

-   **SKRIVEBESKYTTET:** Har mulighed for at få vist enheder

-   **IMPLICIT Læs kun:** Dette er den samme som skrivebeskyttet, men er tildelt til alle brugere af din AAD lejer. Dette skete for nemmere under udviklingen. Du kan fjerne denne rolle ved at ændre [RolePermissions.cs] [ lnk-resource-cs] kildefilen.

### <a name="changing-application-roles-for-a-user"></a>Ændre programmet roller for en bruger

Du kan bruge følgende procedure til at en bruger i din Active Directory administrator af din forudkonfigurerede løsning.

Du skal være en global administrator AAD til at ændre roller for en bruger:

1. Gå til [Azure klassisk portal][lnk-classic-portal].

2. Vælg **Active Directory**.

3. Klik på navnet på din AAD lejer (dette er den mappe, du vælger på azureiotsuite.com, når du har klargjort din løsning).

4. Klik på **programmer**.

5. Klik på navnet på det program, der svarer til løsningsnavnet på din forudkonfigurerede. Hvis du ikke kan se dit program på listen, skifte **Vis** slip ned til **programmer firmaet ejer** og skal du markere afkrydsningsfeltet.

7. Klik på **brugere**.

8. Vælg den bruger, du vil skifte roller.

9. Klik på **Tildel** , og Vælg rollen (som **administrator**) du gerne vil tildele brugeren, skal du markere afkrydsningsfeltet.

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Jeg er tjenesteadministrator af en, og jeg vil gerne ændre directory tilknytningen mellem mit abonnement og en bestemt AAD lejer. Hvordan gør jeg det?

1. Gå til [Azure klassisk portal][lnk-classic-portal], klik på **Indstillinger** på listen over tjenester i den venstre side.

2. Vælg det abonnement, du vil ændre directory tilknytningen til.

3. Klik på **Rediger Directory**.

4. Vælg den **mappe** , du vil bruge i rullemenuen. Klik på pilen frem.

5. Bekræft directory tilknytningen og påvirkes medadministratorer. Bemærk, at hvis du vil flytte fra en anden mappe, fjernes alle medadministratorer fra den oprindelige mappe.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Jeg bruger/medlem af et domæne på AAD lejer, og jeg har oprettet en forudkonfigureret løsning. Hvordan jeg få tildelt en rolle til mit program?

Bed global administrator for at tildele dig som global administrator på AAD lejeren til at få tilladelse til at tildele roller til brugere dig selv, eller Bed global administrator for at tildele dig en rolle. Hvis du vil ændre AAD lejeren din forudkonfigurerede løsning er blevet installeret for at, få vist næste spørgsmål.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Hvordan skifter jeg min remote overvågning forudkonfigurerede løsning og programmet er tildelt til AAD lejeren?

Du kan køre en skyen installation fra <https://github.com/Azure/azure-iot-remote-monitoring> og geninstaller med en nyoprettede lejer AAD. Da du er som standard en global administrator, når du opretter en ny AAD lejer, har du adgang til at tilføje brugere og tildele roller til disse brugere.

1. Oprette en ny mappe med AAD i [Azure Management portal][lnk-classic-portal].

2. Gå til <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Køre `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (For eksempel `build.cmd cloud debug myRMSolution`)

4. Når du bliver bedt om det, kan du angive **tenantid** skal være din nyoprettede lejer i stedet for din forrige lejer.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Jeg vil ændre en tjenesteadministratoren eller samtidig Administrator når logget på med en organisatoriske konto

Se supportartiklen [ændring af tjenesteadministratoren og samtidig Administrator når logget på med en organisatoriske konto][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Hvorfor får jeg vist denne fejl? "Din konto har ikke de nødvendige tilladelser til at oprette en løsning. Kontroller din kontoadministrator eller prøv med en anden konto."

Se nærmere på diagrammet nedenfor:

![][img-flowchart]

> [AZURE.NOTE] Hvis du fortsat får fejl efter validering du er en global administrator på AAD lejeren og en samtidig administrator på abonnementet, har din kontoadministrator om fjerne brugeren og tildele igen nødvendige tilladelser i denne rækkefølge: føje brugeren som global administrator og derefter tilføje bruger som administrator af en samtidig på Azure abonnementet. Hvis problemer fortsætter, skal du kontakte [Hjælp og Support][lnk-help-support].

**Hvorfor får jeg vist denne fejl, når jeg har et Azure abonnement?** *Et Azure-abonnement er påkrævet til at oprette forudkonfigurerede løsninger. Du kan oprette en gratis prøveversion konto på blot et par minutter.*

Hvis du er sikker på, at du har et Azure abonnement Valider lejeren tilknytning for dit abonnement og sikre, at den korrekte lejer er markeret i rullemenuen. Hvis du har valideret ønskede lejeren er korrekte, Følg diagrammet ovenfor og validere tilknytningen af dit abonnement og denne AAD lejer.

## <a name="next-steps"></a>Næste trin

Hvis du vil fortsætte med at lære om IoT pakke, se, hvordan du kan [tilpasse en forudkonfigureret løsning][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
