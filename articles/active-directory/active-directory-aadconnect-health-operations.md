<properties
    pageTitle="Azure AD Connect sundhed handlinger."
    description="I denne artikel beskrives flere handlinger, der kan udføres, når du har installeret Azure AD forbinde tilstand."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect sundhed handlinger

I følgende emne beskrives de forskellige handlinger, der kan udføres ved hjælp af Azure AD forbinde tilstand.

## <a name="enable-email-notifications"></a>Aktivere besked via mail
Du kan konfigurere Azure AD forbinde sundhed Service for at sende e-mail-beskeder, når der genereres påmindelser, der angiver infrastrukturen identitet ikke er sund. Dette sker, når der genereres en meddelelse, samt når den er markeret som løst. Følg vejledningen nedenfor for at konfigurere besked via mail.

![Azure AD Connect sundhed mail meddelelse opdage](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Mailbeskeder er deaktiveret som standard.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aktivere Azure AD forbinde sundhed besked via mail

1. Åbn bladet beskeder for den tjeneste, som du ønsker at modtage mailmeddelelse.
2. Klik på knappen "Indstillinger for meddelelser" fra handlingslinjen.
3. Slå parameteren mailmeddelelse til ON.
4. Markér afkrydsningsfeltet for at konfigurere alle globale administratorer for at modtage en mail.
5. Hvis du vil modtage besked via mail på andre mailadresser, kan du angive dem i boksen yderligere e-mail-modtager. Højreklik på den pågældende post for at fjerne en e-mailadresse fra denne liste, og vælg Slet.
6. Hvis du vil færdiggøre Klik ændringerne på "Gem". Alle ændringer træder effekter, når du vælger "Gem".

## <a name="delete-a-server-or-service-instance"></a>Slette en forekomst af server eller tjeneste

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Slette en server fra Azure AD forbinde sundhed Service
I visse tilfælde kan du vil fjerne en server fra overvåges. Følg vejledningen nedenfor for at fjerne en server fra Azure AD forbinde sundhed Service.

Når du sletter en server, skal være opmærksom på følgende:

- Denne handling STOPPER ved at indsamle eventuelle yderligere data fra den pågældende server. Denne server fjernes fra tjenesten overvågning. Efter denne handling, du vil ikke kunne se nye beskeder, overvågning eller brugen analytics-data for denne server.
- Denne handling kan ikke fjerne eller fjerne Health Agent på din server. Hvis du ikke har fjernet Health Agent før udføre dette trin, kan du se fejl begivenheder på den server, der er relateret til den tilstand Agent.
- Denne handling kan ikke slette de data, der allerede har indsamlet fra denne server. Slettes data ud fra Microsoft Azure-Dataopbevaringspolitik.
- Hvis du vil starte overvågning af samme serveren igen, når du udfører denne handling, skal du fjerne og geninstallere sundhed agent på denne server.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Sådan slettes en server fra Azure AD forbinde sundhedstjeneste

Azure AD Connect tilstand for AD FS & Azure AD-forbindelse (Sync):

1. Åbn bladet Server fra listen bladet Server ved at vælge servernavnet for at blive fjernet.
2. Klik på knappen "Slet" fra handlingslinjen i Blade Server.
3. Bekræfte handlingen, hvis du vil slette serveren ved at skrive navnet på server i bekræftelsesdialogboksen.
4. Klik på knappen "Slet".

Azure AD Connect tilstand til AD DS:

1. Åbn dashboardet domæne enheder.
2. Vælg domænecontrolleren for at blive fjernet.
3. Klik på knappen "Slet de markerede" fra handlingslinjen.
4. Bekræfte handlingen, hvis du vil slette serveren.
5. Klik på knappen "Slet".

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Slette en forekomst af tjenesten fra Azure AD forbinde sundhed Service

I visse tilfælde kan du vil fjerne en forekomst af tjenesten. Følg vejledningen nedenfor for at fjerne en forekomst af tjenesten fra Azure AD forbinde sundhed Service.

Når du sletter en forekomst af tjenesten, kan du være opmærksom på følgende:

- Denne handling fjerner den aktuelle forekomst af tjenesten fra tjenesten overvågning.
- Denne handling kan ikke fjerne eller fjerne Health Agent fra enhver af servere, der blev overvåges som en del af denne forekomst af tjenesten. Hvis du ikke har fjernet Health Agent før udføre dette trin, kan du se fejl begivenheder på servere, der er relateret til den tilstand Agent.
- Alle data fra denne forekomst af tjenesten, slettes ud fra Microsoft Azure-Dataopbevaringspolitik.
- Når du har udført handlingen, hvis du vil begynde at overvåge tjenesten, skal du fjerne og geninstaller sundhed agent på alle servere, der vil blive overvåget. Hvis du vil starte overvågning af samme serveren igen, når du udfører denne handling, skal du fjerne og geninstallere sundhed agent på denne server.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Slette en forekomst af tjenesten fra Azure AD forbinde sundhedstjeneste

1. Åbn bladet Service fra bladet Service liste ved at vælge det service identifier (farm navn), som du vil fjerne.
2. Klik på knappen "Slet" fra handlingslinjen i Blade Server.
3. Bekræft navnet på tjenesten ved at skrive den i bekræftelsesdialogboksen. (for eksempel: sts.contoso.com)
4. Klik på knappen "Slet".
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Administrere adgang med rolle baseret adgangskontrol
### <a name="overview"></a>Oversigt
[Rolle baseret adgangskontrol](role-based-access-control-configure.md) til Azure AD forbinde tilstand giver adgang Azure AD forbinde sundhed tjeneste til brugere og/eller grupper uden for globale administratorer. Dette opnås ved at tildele roller til de tilsigtede brugere og/eller grupper og tilbyder en metode til at begrænse de globale administratorer i mappen.

#### <a name="roles"></a>Roller
Azure AD forbinde sundhed understøtter følgende indbyggede roller.

| Rolle | Tilladelser |
| ----------- | ---------- |
| Ejer | Ejere kan ***administrere adgangen*** (fx Tildel rolle til en brugergruppe) ved at ***få vist alle oplysninger*** (fx Vis beskeder) fra portalen og ***ændre indstillinger*** (fx mailbeskeder) i Azure AD forbinde tilstand. <br>Azure AD globale administratorer er tildelt denne rolle som standard, og det kan ikke ændres.  |
|Bidragyder|  Bidragydere kan ***få vist alle oplysninger*** (fx Vis beskeder) fra portalen og ***ændre indstillinger*** (fx mailbeskeder) i Azure AD forbinde tilstand.|
|Reader| Læsere kan ***få vist alle oplysninger*** (fx Vis beskeder) fra portalen i Azure AD forbinde tilstand.|

Alle andre roller (såsom 'Bruger Access administratorer' eller 'DevTest øvelser brugere'), selv hvis de findes i oplevelser med portalen, har ingen indvirkning have adgang til fra Azure AD forbinde tilstand.

#### <a name="access-scope"></a>Access omfang

Azure AD-forbindelse understøtter adgangskontrol på to niveauer:

- ***Alle forekomster af tjenesten***: Dette er det anbefalede sti for de fleste kunder og styrer adgang til alle forekomster af tjenesten (f.eks. en farm ADFS) på tværs af alle rolletyper, der skal overvåges ved Azure AD forbinde tilstand.

- ***Tjenesteforekomst***: I nogle tilfælde kan du muligvis udskille access, der er baseret på rolletyper eller af en forekomst af tjenesten. I dette tilfælde kan du administrere adgangen på niveauet for tjenesten forekomst.  

Er givet tilladelse til, hvis en slutbruger har adgang til enten på niveauet for bibliotek eller den aktuelle forekomst af tjenesten.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Hvordan du kan give brugere eller grupper adgang til Azure AD forbinde tilstand
#### <a name="steps-1-select-the-appropriate-access-scope"></a>Trin 1: Vælg den relevante access omfang
For at tillade en brugeradgang på niveauet for *alle forekomster af tjenesten* i Azure AD forbinde tilstand, skal du åbne bladet primære i Azure AD forbinde tilstand.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Trin 2: Tilføje brugere, grupper og tildele roller
1. Klik på den "Brugere" del fra afsnittet konfiguration.<br>
![Azure AD Connect sundhed RBAC primære Blade](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Vælg Tilføj""
3. Vælg "Rollen" som "Ejer"<br>
![Azure AD Connect sundhed RBAC Tilføj bruger](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Skriv navnet eller id for den målrettede bruger eller gruppe. Du kan vælge en eller flere brugere eller grupper på samme tid. Klik på "Vælg".
![Azure AD Connect sundhed RBAC Vælg bruger](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Vælg "Ok".<br>

6. Når rolletildelingen er fuldført, vises de brugere og/eller grupper på listen.<br>
![Azure AD Connect sundhed RBAC brugerlisten](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Disse trin, så de vises brugere og gruppeadgang ud fra deres tildelte roller.
>[AZURE.NOTE]
- Globale administratorer har altid fuld adgang til alle handlinger, men global administrator-konti kan ikke findes på listen ovenfor.
- "Invitere brugere" funktion understøttes ikke i Azure AD forbinde tilstand.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Trin 3: Dele blade placeringen med brugere eller grupper
1. Når du tildeler tilladelser, kan en bruger få adgang til Azure AD forbinde tilstand ved at gå til [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Én gang på bladet, brugeren kan fastgøre blade eller forskellige dele til dashboard ved blot at klikke på "Fastgør til dashboard"<br>
![Azure AD forbinde sundhed RBAC pinkode blade](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] En bruger med tildelt rollen "Læser" vil ikke kunne udføre handlingen "oprette" for at få Azure AD forbinde sundhed lokalnummer fra Azure Marketplace. Denne bruger kan stadig åbne bladet ved at gå til det ovenstående link. Brugeren kan fastgøre bladet til dashboard til senere brug.

### <a name="remove-users-andor-groups"></a>Fjerne brugere og/eller grupper
Du kan fjerne en bruger eller en gruppe, der er føjet til Azure AD forbinde sundhed rolle baseret adgangskontrol del ved at højreklikke og vælge Fjern.<br>
![Azure AD Connect sundhed RBAC Fjern bruger](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Relaterede links

* [Azure AD Connect tilstand](active-directory-aadconnect-health.md)
* [Azure AD Connect sundhed Agent-Installation](active-directory-aadconnect-health-agent-install.md)
* [Brug af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md)
* [Ved hjælp af Azure AD forbinde tilstand til synkronisering](active-directory-aadconnect-health-sync.md)
* [Brug af Azure AD forbinde tilstand med AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect sundhed ofte stillede spørgsmål](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect sundhed versionshistorik](active-directory-aadconnect-health-version-history.md)
