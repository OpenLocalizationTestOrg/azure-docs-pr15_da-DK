<properties
    pageTitle="Azure Active Directory Domain Services: Installationsscenarier | Microsoft Azure"
    description="Installationsscenarier, hvor Azure AD-domænetjenester"
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


# <a name="deployment-scenarios-and-use-cases"></a>Installationsscenarier og use cases
I dette afsnit skal se vi på nogle scenarier og brug af sager, der få glæde af Azure Active Directory (AD) Domain Services.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Sikre nem administration af Azure virtuelle maskiner
Du kan bruge Azure Active Directory Domain Services til at administrere din Azure virtuelle maskiner i en strømlinet måde. Azure virtuelle maskiner kan være knyttet til administrerede domænet, hvorved du kan bruge din virksomhedens AD legitimationsoplysninger til at logge på. Denne fremgangsmåde hjælper med at undgå legitimationsoplysninger management problemer som vedligeholde lokale administratorkonti på hver af dine Azure virtuelle computere.

Server virtuelle maskiner, der er knyttet til administrerede domænet kan også administreres og sikret ved hjælp af gruppepolitik. Du kan anvende påkrævede grundlinjer din Azure virtuelle maskiner og låse dem, i overensstemmelse med retningslinjer for virksomhedens sikkerhed. Du kan for eksempel bruge funktioner til administration af gruppepolitik til at begrænse typerne af programmer, der kan startes på disse virtuelle maskiner.

![Strømlinet administration af Azure virtuelle maskiner](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Som servere og andre infrastruktur du når slutningen af livscyklussen, som Contoso flytter mange programmer, der aktuelt er hostet lokalt til skyen. Deres aktuelle IT-standard sikrer, at servere, der indeholder virksomhedens programmer skal være medlem af et domæne og administrerede ved hjælp af gruppepolitik. Contosos IT-administratoren foretrækker at domæne joinforbindelse virtuelle maskiner implementeret i Azure, at gøre det nemmere at administration. Administratorer og brugere kan derfor log på med deres virksomhedens legitimationsoplysninger. På samme tid, kan computere være konfigureret til at overholde påkrævede oprindelige planer ved hjælp af gruppepolitik. Contoso foretrækker ikke at skal installere, overvåge og administrere domænecontrollere i Azure til at sikre Azure virtuelle computere. Azure AD-domænetjenester er derfor et godt egnet til denne use case.

**Installation noter**

Overvej følgende vigtige punkter i dette scenarie installation:

- Administrerede domæner, der leveres af Azure AD-domænetjenester udgør en enkelt flad OU (organisationsenhed) struktur som standard. Alle computere, der er medlem af et domæne, der er placeret i en enkelt flad OU. Du kan dog vælge at oprette brugerdefinerede afdelinger.

- Azure AD-domænetjenester understøtter simple Gruppepolitik i form af et indbygget GPO hver til siden brugere og computere beholdere. Du kan ikke målrette generel efter OU/afdeling, udføre WMI-filtrering, eller Opret brugerdefinerede gruppepolitikobjekter.

- Azure AD-domænetjenester understøtter grundlæggende AD computer objekt skemaet. Du kan udvide computerobjektet skema.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Elevatoren-og-Skift et lokalt program, der anvender LDAP bind godkendelse til Azure infrastrukturtjenester

![LDAP-binding](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso er et lokalt program, der blev købt fra en ISV mange år siden. Programmet er i øjeblikket i vedligeholdelsestilstand ved ISV og anmoder om ændringer til programmet er uforholdsmæssigt høje for Contoso. Dette program er en webbaseret frontend, som indsamler brugerlegitimationsoplysninger ved hjælp af en webformular og derefter godkender brugere ved at udføre en LDAP-binding til virksomhedens Active Directory. Contoso vil gerne overføre dette program til Azure infrastrukturtjenester. Det er hensigtsmæssigt, at programmet fungerer som er, uden at eventuelle ændringer. Desuden kan brugere skal ikke kunne godkendes ved hjælp af deres eksisterende virksomhedens legitimationsoplysninger og uden at skulle omskole brugerne for at udføre ting anderledes. Med andre ord slutbrugere skal være oblivious af hvor programmet kører og overførslen skal være gennemsigtigt hertil.

**Installation noter**

Overvej følgende vigtige punkter i dette scenarie installation:

- Sørg for, at programmet ikke nødvendigt at ændre-og skriveadgang til adressekartoteket. LDAP-skriveadgang til administrerede domæner, der leveres af Azure AD-domænetjenester understøttes ikke.

- Du kan ikke ændre adgangskoder direkte forhold til administrerede domænet. Slutbrugere kan ændre deres adgangskode enten ved hjælp af Azure AD selvbetjening Skift ordning eller mod lokal adresseliste. Disse ændringer er automatisk synkroniseret og i administrerede domænet.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Elevatoren og Skift et lokalt program, der bruger LDAP læse, for at få adgang til biblioteket til Azure infrastrukturtjenester
Contoso er et lokalt line of business (LOB)-program, der blev udviklet et årti siden. Dette program er directory opmærksom på, og er udviklet til at arbejde med Windows Server AD. Programmet bruger LDAP (Lightweight Directory Access Protocol) til at læse oplysninger/attributter om brugere fra Active Directory. Programmet ikke ændre attributter eller skrive ellers til mappen. Contoso vil overføre dette program til Azure infrastrukturtjenester og går på pension aldersfordelt lokale hardware i øjeblikket er vært dette program. Programmet kan ikke omskrives for at bruge moderne directory API'er som RESTEN-baserede Azure AD Graph API. Valgmuligheden elevatoren og Skift ønskes derfor, så programmet kan overføres for at køre i skyen, uden at ændre kode eller omskrivning af programmet.

**Installation noter**

Overvej følgende vigtige punkter i dette scenarie installation:

- Sørg for, at programmet ikke nødvendigt at ændre-og skriveadgang til adressekartoteket. LDAP-skriveadgang til administrerede domæner, der leveres af Azure AD-domænetjenester understøttes ikke.

- Sørg for, at programmet ikke skal bruge en brugerdefineret udvidet Active Directory-skemaet. Skema filtypenavne understøttes ikke i Azure AD-domænetjenester.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Flytte et lokalt eller daemon program til Azure infrastrukturtjenester
Nogle programmer består af flere lag, hvor en af lag skal udføre godkendte opkald til en back end-niveau som et trin i databasen. Active Directory-tjenestekonti bruges typisk til disse Brug-tilfælde. Du kan elevatoren og Skift sådanne programmer til Azure infrastrukturtjenester og bruge Azure AD-domænetjenester til identitet behov af disse programmer. Du kan vælge at bruge den samme tjenestekonto, som er synkroniseret fra din lokale mappe til Azure AD. Du kan også oprette en brugerdefineret OU først og derefter oprette en separat tjenestekonto i den pågældende afdeling at implementere disse programmer.

![Tjenestekonto ved hjælp af WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso er et specialtilpasset softwareprogram til samling af legitimationsoplysninger, der indeholder en web front-end, en SQL server og en back end-FTP-serveren. Windows-integreret godkendelse af tjenestekonti bruges til at godkende web front end FTP-serveren. Web front end er konfigureret til at køre som en tjenestekonto. Back end-serveren er konfigureret til at tillade adgang fra servicekonto for web front end. Contoso foretrækker ikke at installere en domænecontroller virtuel maskine i skyen for at flytte dette program til Azure infrastrukturtjenester. Contosos IT-administrator kan installere de servere, der er vært for web front end, SQL server og FTP-serveren Azure virtuelle maskiner. Disse computere er derefter føjet til en administreret Azure AD Domain Services-domæne. De kan derefter bruge den samme servicekonto i deres lokale directory henblik på app-godkendelse. Denne servicekonto synkroniseres til Azure AD-domænetjenester administrerede domænet og er tilgængelig til brug.

**Installation noter**

Overvej følgende vigtige punkter i dette scenarie installation:

- Sørg for, at programmet bruger brugernavn og adgangskode til godkendelse. Certifikat/chipkort baseret godkendelse understøttes ikke af Azure AD-domænetjenester.

- Du kan ikke ændre adgangskoder direkte forhold til administrerede domænet. Slutbrugere kan ændre deres adgangskode enten ved hjælp af Azure AD selvbetjening Skift ordning eller mod lokal adresseliste. Disse ændringer er automatisk synkroniseret og i administrerede domænet.


## <a name="azure-remoteapp"></a>Azure RemoteApp
Azure RemoteApp aktiverer Contosos administrator for at oprette en samling, der er medlem af et domæne. Denne funktion kan remote programmer served ved Azure RemoteApp til at køre på computere, der er medlem af et domæne og få adgang til andre ressourcer ved hjælp af Windows-integreret godkendelse. Contoso kan bruge Azure AD Domain Services til at give et administreret domæne, der bruges af Azure RemoteApp medlem af et domæne af websteder.

![Azure RemoteApp](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Du kan finde flere oplysninger om denne installation scenarie artiklen Remote Desktop Services Blog med titlen [elevatoren og Skift din arbejdsmængder med Azure RemoteApp og Azure AD-domænetjenester](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
