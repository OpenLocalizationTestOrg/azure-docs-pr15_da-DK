<properties
    pageTitle="Administrere adgang til Log Analytics | Microsoft Azure"
    description="Administrere adgang til Log analyser ved hjælp af en række administrative opgaver på brugere, konti, OMS arbejdsområder og Azure konti."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>

# <a name="manage-access-to-log-analytics"></a>Administrere adgang til Log Analytics

Du skal bruge en lang række administrative opgaver på brugere, konti, OMS arbejdsområder og Azure konti for at administrere adgang til Log Analytics skal. Hvis du vil oprette et nyt arbejdsområde i handlinger Management pakke (OMS), skal du vælge et navn på arbejdsområde, knytte den til din konto, og du vælger en geografisk placering. Et arbejdsområde er grundlæggende en objektbeholder, der indeholder kontooplysninger og enkel konfigurationsoplysninger for kontoen. Du eller andre medlemmer af organisationen kan bruge flere OMS arbejdsområder til at administrere forskellige typer data, der indsamles fra alle eller dele af din IT-infrastruktur.

[Komme i gang med Log Analytics](log-analytics-get-started.md) -artikel viser, hvordan du hurtigt komme i gang og kører, og resten af denne artikel beskrives mere detaljeret nogle af de handlinger, skal du administrere adgangen til OMS.

Selvom du ikke kan behøver at udføre alle management opgave i første omgang, kan vi vil gennemgå de ofte anvendte opgaver, som du kan bruge i de følgende afsnit:

- Finde ud af antallet arbejdsområder, du har brug for
- Administrere konti og -brugere
- Føje en gruppe til et eksisterende arbejdsområde
- Sammenkæde et eksisterende arbejdsområde til et Azure-abonnement
- Opgradere et arbejdsområde til et betalt dataabonnement
- Ændre en plan datatype
- Føje en Azure Active Directory organisation til et eksisterende arbejdsområde
- Luk arbejdsområdet OMS

## <a name="determine-the-number-of-workspaces-you-need"></a>Finde ud af antallet arbejdsområder, du har brug for

Et arbejdsområde er en Azure ressource og en objektbeholder, hvor data er der indsamles, sammenlagt, analysere og præsenteres på portalen OMS.

Det er muligt at oprette flere OMS Log Analytics-arbejdsområder og for brugere skal have adgang til en eller flere arbejdsområder. Generelt vil du minimere antallet af arbejdsområder, som dette gør det muligt at forespørge og koordinere på tværs af de fleste data. I dette afsnit beskrives, hvornår det kan være praktisk at oprette mere end én arbejdsområde.

I dag, indeholder et Log Analytics-arbejdsområde:

- En geografisk placering til lagring af data
- Granulariteten for fakturering
- Data isolationsniveauet

Baseret på de ovenstående egenskaber, eventuelt du til at oprette flere arbejdsområder, hvis:

- Du er en global virksomhed, og du skal bruge data, der er gemt i bestemte områder data højhedsområde eller overholdelse årsager.
- Du bruger Azure, og du vil undgå at udgående data filoverførsel gebyrer ved at få et Log Analytics-arbejdsområde i samme område som Azure ressourcerne den administrerer.
- Du vil fordele tillæg til forskellige afdelinger eller business-grupper, der er baseret på deres brugen. Når du opretter et arbejdsområde for hver afdeling eller business-gruppe, viser dine Azure faktura for og brugen sætningen gebyrerne for de enkelte arbejdsområder separat.
- Du er en udbyder af administrerede og har brug for at holde log analytics-data for hver kunde du administrere adskilt fra andre kundedata.
- Du administrerer flere kunder, og du vil hver kunde eller afdeling eller virksomheds til at se deres egne data, men ikke data for andre kunder eller afdelinger eller business-grupper.

Når du bruger supportmedarbejdere til at indsamle data, kan du konfigurere hver agent for at rapportere til arbejdsområdet påkrævet.

Hvis du bruger System Center Operations manager, kan hver Operations Manager management gruppe forbindes med kun én arbejdsområde. Du kan installere Microsoft overvågning Agent på computere, der administreres af Operations Manager og have rapporten agent både Operations Manager og et andet Log Analytics-arbejdsområde.

### <a name="workspace-information"></a>Oplysninger om arbejdsområde

I portalen OMS, kan du se arbejdsområdeoplysninger om din og vælge, om du vil modtage oplysninger fra Microsoft.

#### <a name="view-workspace-information"></a>Få vist oplysninger om arbejdsområde

1. Klik på feltet **Indstillinger** i OMS.
2. Klik på fanen **firmaer** .
3. Klik på fanen **Oplysninger om arbejdsområde** .  
  ![Oplysninger om arbejdsområde](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Administrere konti og -brugere

De enkelte arbejdsområder kan have flere brugerkonti, der er knyttet til den, og hver brugerkonto (Microsoft-konto eller organisationskonto) kan få adgang til flere OMS arbejdsområder.

Som standard bliver den Microsoft-konto eller organisationskonto, der bruges til at oprette arbejdsområdet administratoren af arbejdsområdet. Administratoren kan derefter invitere flere Microsoft-konti eller Vælg brugere fra Azure Active Directory.

Give personer adgang til arbejdsområdet OMS styres på 2 steder:

- Du kan bruge rollebaseret adgangskontrol i Azure, giver adgang til Azure abonnementet og de tilknyttede Azure ressourcer. Det bruges også til PowerShell og REST-API-adgang.
- Få adgang til kun OMS portalen - ikke den tilknyttede Azure abonnement på portalen OMS.

Hvis give personer adgang til portalen OMS, men ikke til Azure abonnementet, som det er tilknyttet, derefter viser automatisering, sikkerhedskopiering og gendannelse af websteder løsning felter ikke data til brugere når de logon portalen OMS.

Tillade alle brugere kan få vist alle dataene i disse løsninger, skal du sikre, at de har mindst **læser** få adgang til for den automatisering konto, samling sikkerhedskopiering og gendannelse af websteder samling af legitimationsoplysninger, der er knyttet til arbejdsområdet OMS.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Adgangskontrol til Log analyser ved hjælp af portalen Azure

Hvis du give andre adgang til arbejdsområdet Log analyser ved hjælp af Azure tilladelser, i portalen Azure eksempelvis derefter de samme brugere kan få adgang til portalen Log analyser. Hvis brugerne er i portalen Azure, kan de gå til portalen OMS ved at klikke på opgaven **OMS Portal** , når du får vist Log Analytics arbejdsområde ressourcen.

Nogle punkter du være opmærksom på angående Azure portalen:

- Dette er ikke *Rollebaseret adgangskontrol*. Hvis du har *læser* adgangstilladelser i portalen Azure for Log Analytics-arbejdsområde, kan du foretage ændringer ved hjælp af portalen OMS. Portalen OMS har en begrebet Administrator, bidragyder og skrivebeskyttet bruger. Hvis du er logget på med kontoen i Azure Active Directory knyttet til arbejdsområdet får skal du være Administrator i portalen OMS, ellers du en bidragyder.

- Når du logger på portalen OMS ved hjælp af http://mms.microsoft.com, derefter som standard vises på listen **Vælg et arbejdsområde** . Den indeholder kun arbejdsområder, som blev tilføjet ved hjælp af portalen OMS. Hvis du vil se arbejdsområderne, du har adgang til med Azure-abonnementer, og derefter skal du angive en lejer som en del af URL-adressen. Eksempel:

  `mms.microsoft.com/?tenant=contoso.com`Lejer id'et er ofte den sidste del af den mailadresse, du logge på med.

- Hvis kontoen du logge på med en konto i lejeren Azure Active Directory, der er som regel tilfældet medmindre du er logge på som en CSP, vil du være en *Administrator* i portalen OMS. Hvis din konto ikke er i lejeren Azure Active Directory, vil du være en *bruger* i portalen OMS.

- Hvis du vil gå direkte til en portal, at du har adgang til at bruge Azure tilladelser, skal du angive ressourcen som en del af URL-adressen. Det er muligt at få denne URL-adresse ved hjælp af PowerShell.

  For eksempel `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  URL-adressen ser sådan ud:`https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### <a name="managing-users-in-the-oms-portal"></a>Administrere brugere i portalen OMS

Du kan administrere brugere og gruppe under fanen **Administrer brugere** under fanen **konti** på siden Indstillinger. Der, kan du udføre opgaver i de følgende afsnit.  

![administrere brugere](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Tilføje en bruger til et eksisterende arbejdsområde

Brug følgende trin til at tilføje en bruger eller gruppe til en OMS arbejdsområde. Brugeren eller gruppen skal kunne få vist og reagere på alle vigtige beskeder, der er knyttet til dette arbejdsområde.

>[AZURE.NOTE] Hvis du vil tilføje en bruger eller gruppe fra din organisations Azure Active Directory-konto, skal du kontrollere, at du har knyttet kontoen OMS til Active Directory-domænet. Se [tilføje en Azure Active Directory organisation med et eksisterende arbejdsområde](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Klik på feltet **Indstillinger** i OMS.
2. Klik på fanen **konti** , og klik derefter på fanen **Administrer brugere** .
3. Vælg kontotype tilføje i afsnittet **Administrere brugere** : **Organisationskonto**, **Microsoft-konto**, **Microsoft Support**.
    - Hvis du vælger Microsoft-Account, skal du skrive mailadressen på den bruger, der er knyttet til den Microsoft-Account.
    - Hvis du vælger Organisationskonto, kan du angive en del af brugeren eller gruppens navn eller alias, og der vises en liste over brugere og grupper. Vælg en bruger eller gruppe.
    - Bruge Microsoft Support til at give en Microsoft-Support engineering midlertidig adgang til dit arbejdsområde med hjælp til fejlfinding.

    >[AZURE.NOTE] Begrænse antallet af Active Directory-grupper, der er knyttet til en enkelt OMS konto til tre for de bedste resultater – én for administratorer, én for bidragydere og én i skrivebeskyttet tilstand brugere. Brug af flere grupper kan påvirke ydeevnen for Log analyser.

5. Vælg typen af brugeren eller gruppen at tilføje: **Administrator**, **bidragyder**eller **Skrivebeskyttet bruger** .  
6. Klik på **Tilføj**.

  Hvis du føjer en Microsoft-konto, sendes en invitation til at deltage i arbejdsområdet til den mail, du har angivet. Når brugeren følger vejledningen i invitationen for at deltage i OMS, kan brugeren se beskeder og kontooplysninger for denne OMS-konto, og du vil kunne få vist brugeroplysninger under fanen **konti** på siden **Indstillinger** .
  Hvis du føjer en organisationskonto, bliver brugeren få adgang til Log Analytics med det samme.  
  ![invitation via mail](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>Redigere en eksisterende bruger tekst

Du kan ændre konto rollen for en bruger, der er knyttet til kontoen OMS. Har du følgende rolle indstillinger:

 - *Administrator*: kan administrere brugere, få vist og handle på alle vigtige beskeder, og tilføje og fjerne servere

 - *Bidragyder*: kan få vist og handle på alle vigtige beskeder, og tilføje og fjerne servere

 - *Skrivebeskyttet bruger*: brugere, der er markeret som skrivebeskyttet vil ikke kunne:
   1. Tilføj/fjern løsninger. Løsningsgalleriet er skjult.
   2. Tilføje/redigere/Fjern felter i **Mit Dashboard**.
   3. Få vist siderne **Indstillinger** . Siderne er skjult.
   4. Opgaver er skjult i Søg visning, PowerBI konfiguration, gemmes søgninger og beskeder.


#### <a name="to-edit-an-account"></a>Redigere en konto

1. Klik på feltet **Indstillinger** i OMS.
2. Klik på fanen **konti** , og klik derefter på fanen **Administrer brugere** .
3. Vælg rollen, for den bruger, du vil ændre.
2. Klik på **Ja**i bekræftelsesdialogboksen.

### <a name="remove-a-user-from-a-oms-workspace"></a>Fjerne en bruger fra et arbejdsområde, OMS

Brug følgende trin til at fjerne en bruger fra et OMS arbejdsområde. Bemærk, at dette ikke lukke brugerens arbejdsområde. I stedet fjernes tilknytningen mellem brugeren og arbejdsområdet. Hvis en bruger er knyttet til flere arbejdsområder, vil pågældende bruger stadig kunne logge på OMS og se andre arbejdsområderne.

1. Klik på feltet **Indstillinger** i OMS.
2. Klik på fanen **konti** , og klik derefter på fanen **Administrer brugere** .
3. Klik på **Fjern** ud for navnet på den bruger, du vil fjerne.
4. Klik på **Ja**i bekræftelsesdialogboksen.


### <a name="add-a-group-to-an-existing-workspace"></a>Føje en gruppe til et eksisterende arbejdsområde

1.  Følg trin 1 -4 i "Hvis du vil du tilføje en bruger til et eksisterende arbejdsområde" ovenfor.
2.  Vælg **gruppe**under **Vælg bruger/gruppe**.
    ![føje en gruppe til et eksisterende arbejdsområde](./media/log-analytics-manage-access/add-group.png)
3.  Angiv det viste navn eller mailadressen for den gruppe, du gerne vil tilføje.
4.  Vælg gruppen på listen i søgeresultaterne, og klik derefter på **Tilføj**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Sammenkæde et eksisterende arbejdsområde til et Azure-abonnement

Det er muligt at oprette et arbejdsområde fra webstedet [microsoft.com/oms](https://microsoft.com/oms) .  Dog findes visse grænser for disse arbejdsområder mest bemærkelsesværdige er en begrænsning på 500MB/ugedag data overførsler, hvis du bruger en gratis konto. Hvis du vil foretage ændringer i arbejdsområdet skal du sammenkæde *din eksisterende arbejdsområde til et Azure-abonnement*.

>[AZURE.IMPORTANT] Hvis du vil sammenkæde et arbejdsområde, har kontoen Azure allerede adgang til det arbejdsområde, du vil oprette et link.  Det vil sige, skal den konto, du kan bruge til at få adgang til portalen Azure være **den samme** som den konto, du kan bruge til at få adgang til arbejdsområdet OMS. Hvis dette ikke er tilfældet, kan du se [tilføje en bruger til et eksisterende arbejdsområde](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Oprette et link til et Azure abonnement på portalen OMS et arbejdsområde

For at oprette et arbejdsområde et link til et Azure abonnement på portalen OMS, skal den bruger, der er logget på allerede har et betalt Azure-konto. Det arbejdsområde, som du bruger aktivt bliver knyttet til Azure-konto.

1. Klik på feltet **Indstillinger** i OMS.
2. Klik på fanen **firmaer** , og klik derefter på fanen **Azure-abonnement og planlægge Data** .
3. Klik på den dataplan, du vil bruge.
4. Klik på **Gem**.  
  ![abonnement og data-planer](./media/log-analytics-manage-access/subscription-tab.png)

Din nye dataabonnement vises i OMS portalen båndet øverst på websiden.

![OMS båndet](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Sammenkæde et arbejdsområde til en Azure abonnement på portalen Azure

1.  Logge på [Azure-portalen](http://portal.azure.com).
2.  Søge efter **Log Analytics (OMS)** , og vælg derefter den.
3.  Du kan se en liste over eksisterende arbejdsområder. Klik på **Tilføj**.  
    ![liste over arbejdsområder](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  Klik på **eller sammenkæde eksisterende**under **OMS arbejdsområde**.  
    ![sammenkæde eksisterende](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Klik på **Konfigurer påkrævet indstillinger**.  
    ![konfigurere indstillinger for påkrævet](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Du får vist på listen over arbejdsområder, der ikke er endnu er knyttet til kontoen Azure. Vælg et arbejdsområde.  
    ![Vælg arbejdsområder](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Hvis det er nødvendigt, kan du ændre værdier for følgende elementer:
    - Abonnement
    - Ressourcegruppe
    - Placering
    - Priser niveau  
        ![ændre værdier](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Klik på **Opret**. Arbejdsområdet er nu sammenkædet med din Azure-konto.

>[AZURE.NOTE] Hvis du ikke kan se det arbejdsområde, du vil oprette et link, derefter har abonnementet Azure ikke adgang til arbejdsområdet OMS, du har oprettet ved hjælp af webstedet OMS.  Du skal give adgang til denne konto fra i arbejdsområdet OMS ved hjælp af webstedet OMS. Gør du ved at se [tilføje en bruger til et eksisterende arbejdsområde](#add-a-user-to-an-existing-workspace).



## <a name="upgrade-a-workspace-to-a-paid-data-plan"></a>Opgradere et arbejdsområde til et betalt dataabonnement

Der er tre arbejdsområdedata forudse typer OMS: **ledig**, **Standard**og **Premium**.  Hvis du er på en *gratis* plan, har du muligvis rammer dine data knop 500 MB.  Du skal opgradere dit arbejdsområde til en ***overkommelige plan*** for at indsamle data ud over denne grænse. Du kan konvertere din plantype når som helst.  Du kan finde flere oplysninger om OMS priser, [Priser detaljer](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Arbejdsområde planer kan kun ændres, hvis de er *sammenkædet* med et Azure-abonnement.  Hvis du har oprettet dit arbejdsområde i Azure, eller hvis du har sammenkædet *allerede* dit arbejdsområde, kan du ignorere denne meddelelse.  Hvis du har oprettet dit arbejdsområde med [OMS websted](http://www.microsoft.com/oms), skal du følge trinnene [Link et eksisterende arbejdsområde til et Azure-abonnement](#link-an-existing-workspace-to-an-azure-subscription).

### <a name="using-entitlements-from-the-oms-add-on-for-system-center"></a>Brug af rettigheder fra OMS tilføjelsesprogrammet til System Center

Tilføjelsesprogrammet OMS til System Center indeholder en ret til Premium planen for OMS Log Analytics, beskrevet på [OMS priser](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

Når du køber OMS tilføjelsesprogrammet til System Center, tilføjes tilføjelsesprogrammet OMS som en ret på din accept af System Center. En hvilken som helst Azure-abonnement, der oprettes under denne aftale kan gøre brug af ret. Dette kan du eksempelvis har flere OMS arbejdsområder, der bruger ret fra OMS tilføjelsesprogrammet.

For at sikre, at brugen af et OMS arbejdsområde anvendes på dine rettigheder fra OMS tilføjelsesprogrammet, skal du:

1. Oprette et link arbejdsområdet OMS til et Azure abonnement, som er en del af Enterprise Agreement, der indeholder både OMS tilføjelsesprogram køb og Azure abonnement brugen
2. Vælg planen, Premium til arbejdsområdet

Når du har gennemgået din brugen i portalen Azure eller OMS, kan du ikke se OMS tilføjelsesprogram rettigheder. Dog kan du se rettigheder i Enterprise Portal.  

Hvis du vil ændre det Azure abonnement, arbejdsområdet OMS er sammenkædet med, kan du bruge Azure PowerShell [Flyt AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) cmdlet.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Brug af Azure anvendelsen fra en Enterprise Agreement

Hvis du vælger at bruge enkeltstående priser for OMS komponenter, du vil betale for hver komponent af OMS separat, og brug vises på din Azure faktura.

Hvis du har en Azure pengeværdier bekræftelse på enterprise for tilmelding, dine Azure abonnementer er sammenkædet, debiteres en hvilken som helst brugen af Log Analytics automatisk mod eventuelle resterende pengeværdier bekræftelse.

Hvis du vil ændre kan Azure abonnementet, OMS arbejdsområdet er sammenkædet med du bruge cmdlet'en Azure PowerShell [Flyt AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) til.  



### <a name="to-change-a-workspace-to-a-paid-data-plan"></a>Ændre et arbejdsområde til et betalt dataabonnement

1.  Logge på [Azure-portalen](http://portal.azure.com).
2.  Søge efter **Log Analytics (OMS)** , og vælg derefter den.
3.  Du kan se en liste over eksisterende arbejdsområder. Vælg et arbejdsområde.  
    ![liste over arbejdsområder](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  Klik på **priser niveau**under **Indstillinger**.  
    ![priser niveau](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  Vælg en et dataabonnement under **priser niveau**, og klik derefter på **Vælg**.  
    ![Vælg plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Når du opdaterer din visning i portalen Azure, får du vist **priser niveau** opdateret til den plan, du har valgt.  
    ![opdatere priser niveau](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Nu kan du indsamle data ud over kappen "gratis" data.


## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Føje en Azure Active Directory organisation til et eksisterende arbejdsområde

Du kan knytte arbejdsområdet Log Analytics (OMS) med en Azure Active Directory-domæne. Dette gør det muligt at føje brugere fra Active Directory direkte til arbejdsområdet OMS uden at en separat Microsoft-konto.

Når du opretter arbejdsområdet fra Azure-portalen, eller link dit arbejdsområde til et Azure abonnement sammenkædes din Azure Active Directory som din virksomhedskonto.

Når du opretter arbejdsområdet fra portalen OMS, bliver du bedt om at oprette et link til et Azure-abonnement og en organisationskonto.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Føje en Azure Active Directory organisation til et eksisterende arbejdsområde

1. Klik på **konti** på siden Indstillinger i OMS, og klik derefter på fanen **Oplysninger om arbejdsområde** .  
2. Gennemse oplysningerne om organisationskonti, og klik derefter på **Tilføj organisation**.  
    ![tilføje organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Angiv identitetsoplysninger til administratoren af Azure Active Directory-domænet. Bagefter, får du vist en bekræftelse om, at dit arbejdsområde er sammenkædet med Azure Active Directory-domænet.
    ![sammenkædede arbejdsområde bekræftelse](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] Når kontoen er sammenkædet med en Organisationskonto, kan ikke sammenkædning være fjernet eller ændret.

## <a name="close-your-oms-workspace"></a>Luk arbejdsområdet OMS

Når du lukker en OMS arbejdsområde, slettes alle de data, der er relateret til dit arbejdsområde fra tjenesten OMS inden for 30 dage for at afslutte arbejdsområdet.

Hvis du er administrator, og der er flere brugere, der er knyttet til arbejdsområdet, brydes tilknytningen mellem disse brugere og arbejdsområdet. Hvis brugerne, der er knyttet til andre arbejdsområder, kan derefter de fortsætte med at bruge OMS med disse andre arbejdsområder. Men hvis de ikke er knyttet til andre arbejdsområder derefter de skal oprette et nyt arbejdsområde for at bruge OMS.

### <a name="to-close-an-oms-workspace"></a>Lukke et OMS arbejdsområde

1. Klik på feltet **Indstillinger** i OMS.
2. Klik på fanen **konti** , og klik derefter på fanen **Oplysninger om arbejdsområde** .
3. Klik på **Luk arbejdsområde**.
4. Vælg en af årsager til lukning af arbejdsområdet, eller angive en anden årsag i tekstfeltet.
5. Klik på **Luk arbejdsområde**.

## <a name="next-steps"></a>Næste trin

- Se [oprette forbindelse Windows-computere at Log Analytics](log-analytics-windows-agents.md) for at tilføje supportmedarbejdere og indsamle data.
- [Tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til at tilføje funktionalitet og indsamle data.
- [Konfigurer proxy og firewall-indstillinger i Log analyser](log-analytics-proxy-firewall.md) , hvis din organisation bruger en proxyserver eller firewall, så supportmedarbejdere kan kommunikere med Log Analytics-tjenesten.
