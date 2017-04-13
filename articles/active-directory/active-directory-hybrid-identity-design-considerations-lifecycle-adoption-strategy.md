
<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - fastlægge hybrid identitet livscyklus indføring strategi for | Microsoft Azure"
    description="Hjælper med at definere hybrid identitet administrationsopgaver ifølge de tilgængelige indstillinger for hver livscyklus fase."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Fastlægge strategi for indføring hybrid identitet livscyklus
I denne opgave, skal du definere identitet management strategi for dit hybride identitet løsning at imødekomme virksomhedens behov, som du angav i [fastslå hybrid identitet administrationsopgaver](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).


For at definere hybrid identitet administrationsopgaver ifølge livscyklus til slut identitet præsenteres tidligere i dette trin, skal du overveje de tilgængelige indstillinger for hver livscyklus fase.

## <a name="access-management-and-provisioning"></a>Access administration og klargøring
Med en god konto access management-løsning, kan din organisation spore nøjagtigt der har adgang til hvilke oplysninger på tværs af organisationen.

Adgangskontrol er en vigtig funktion af et centralt, enkelt klargøringssystem. Ud over at beskytte følsomme oplysninger, adgang til kontrolelementer for få vist eksisterende konti, der har ikke-godkendte tilladelser eller er ikke længere det er nødvendigt. Til at styre forældede konti, klargøring system links sammen kontooplysninger med autoritative oplysninger om de brugere, der ejer kontiene. Autoritative identitet brugeroplysninger er typisk vedligeholdes i databaser og kataloger af hr-afdeling.

Konti i store virksomheder avancerede IT omfatter hundredvis af parametre, der definerer myndigheder, og disse oplysninger kan styres af dit klargøringssystem. Nye brugere kan blive identificeret med de data, du angiver fra autoritative kilden. Muligheden for access anmodning om godkendelse starter processer, godkende (eller afvise) ressource klargøring til dem.


| Livscyklus management fase          | Lokalt                                                                                                                                                                                                                                                       | Skyen                                                                                                                                                                                                                                                                                                                     | Hybrid                                                                                   |
|-------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| Kontoadministration og klargøring | Ved hjælp af rollen Active Directory®-domænetjenester (AD DS) server, kan du oprette en SVG, sikre og administrere infrastruktur til bruger- og ressourceadministration og understøttelse af directory-aktiverede programmer som Microsoft® Exchange Server. <br><br> [Du kan klargøre grupper i AD DS via en identitet manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[Du kan tildele brugere i AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratorer kan bruge adgangskontrol til at administrere brugeradgang til delte ressourcer af sikkerhedsmæssige årsager. I Active Directory, adgangskontrol administreres på objektniveau ved at angive forskellige niveauer af access eller tilladelser til objekter, som fuld kontrol, Skriv, læst eller ingen adgang. Adgangskontrol i Active Directory definerer, hvordan forskellige brugere kan bruge Active Directory-objekter. Tilladelser til objekter i Active Directory er som standard angivet til den mest sikre indstilling. | Du skal oprette en konto for alle brugere, der skal have adgang til en skybaseret Microsoft-tjeneste. Du kan også ændre brugerkonti eller slette dem, når de er ikke længere bruges. Brugere har ikke administratorrettigheder, men du kan også tildele dem som standard. Se [Administrere brugere i Azure AD](active-directory-create-users.md)kan finde flere oplysninger. <br><br> I Azure Active Directory er en af de vigtigste funktioner muligheden for at administrere adgang til ressourcer. Disse ressourcer kan være en del af mappen, som er tilfældet med tilladelser til at administrere objekter gennem roller i den mappe eller ressourcer, som er ekstern i forhold til mappen, som SaaS programmer, Azure services og SharePoint-websteder eller på en lokal ressourcer. <br><br> I midten af Azure Active Directory's access er management-løsning sikkerhedsgruppen. Ejeren af ressource (eller administratoren af mappen) kan tildele en gruppe for at angive en bestemt adgang direkte til de ressourcer, de ejer. Medlemmer af gruppen får adgang til, og ejeren af ressource kan uddelegere rettigheder til at administrere listen medlemmer af en gruppe til en anden – som en afdeling chef eller en administrator for helpdesk<br> <br> Administrere grupper i Azure AD emne indeholder flere oplysninger om administration af adgang via grupper.| Udvide Active Directory-identiteter til skyen via synkronisering og sammenslutning |

## <a name="role-based-access-control"></a>Rollebaseret adgangskontrol
Rollebaseret access styre (RBAC) bruger roller og klargøring af politikker, der skal evalueres, teste og gennemtvinge dine forretningsprocesser og regler for at give adgang til brugere. Vigtige Administratorer opretter klargøring politikker og tildele brugere roller og, der definerer sæt af rettigheder til ressourcer for disse roller. RBAC udvider identitet management-løsning for at bruge software-baserede processer og reducere manuel brugerinput i klargøring processen.
Azure AD-RBAC gør det muligt for firmaet til at begrænse antallet handlinger, som kan gøre en enkelt bruger, når hun har adgang til Azure Management Portal. Ved hjælp af RBAC til at styre adgang til portalen, IT-administratorer ca stedfortræderadgang, ved hjælp af følgende access management metoder:

- **Gruppen-baserede rolletildeling**: Du kan tildele adgang til Azure AD-grupper, der kan blive synkroniseret fra din lokale Active Directory. Dette gør det muligt at udnytte de eksisterende investeringer, din organisation har foretaget i værktøj og processer til administration af grupper. Du kan også bruge funktionen til styring af delegeret gruppe af Azure AD Premium.
- **Udnyttelse indbygget i roller i Azure**: Du kan bruge tre roller – ejer, bidragyder og læser, for at sikre dig, at brugere og grupper har tilladelse til at gøre kun de opgaver, de skal udføre deres arbejde.
- **Granular adgang til ressourcer**: Du kan tildele roller til brugere og grupper for et bestemt abonnement, ressourcegruppe eller en enkelt Azure ressource som et websted eller en database. På denne måde, kan du sikre dig, at brugere har adgang til alle de ressourcer, de skal bruge og ikke adgang til ressourcer, som de ikke er nødvendigt at administrere.

## <a name="provisioning-and-other-customization-options"></a>Klargøring og andre indstillinger for tilpasning
Dit team kan bruge forretningsplaner og krav beslutte, hvor meget du vil tilpasse identitet løsning. For eksempel kan en stor virksomhed kræve en faseinddelt udrulning plan til arbejdsprocesser og brugerdefinerede kort, der er baseret på en tidslinje til trinvist klargøring af programmer, der bruges på tværs af lande. En anden tilpasning plan kan indeholde to eller flere programmer til at blive klargjort på tværs af hele organisationen, efter vellykket testen. Bruger-programmet interaktion kan tilpasses, og procedurer til klargøring af ressourcer kan blive ændret, hvis du vil tage højde for automatiseret klargøring.

Du kan deprovision Hvis du vil fjerne en tjeneste eller komponent. For eksempel betyder ophævelse af klargøring en konto, at kontoen slettes fra en ressource.

Hybrid modellen klargøringen ressourcer kombinerer anmodningen og rollebaseret fremgangsmåder, som er begge understøttes af Azure AD. For et undersæt af medarbejdere eller administrerede systemer måske en virksomhed til at automatisere access med rollebaseret tildeling. En virksomhed kan også håndtere alle andre anmodninger om adgang eller undtagelser gennem en anmodning om-baserede model. Nogle virksomheder kan starte med manuel tildeling og udvikle mod en hybrid-model, med en hensigten med en fuldt rollebaseret installation på et senere tidspunkt.

Andre firmaer kan finde den upraktisk til business grunde til at opnå fuldstændig rollebaseret klargøring og målrette en hybrid fremgangsmåde som ønskede mål. Stadig andre firmaer kan være tilfreds med kun anmodning-baserede klargøring, og vil ikke investere yderligere forsøg på at definere og administrere rollebaseret, automatiseret klargøring politikker.

## <a name="license-management"></a>Håndtering af licenser
Administration af gruppe-baserede licenser i Azure AD kan administratorer tildele brugere til en sikkerhedsgruppe og Azure AD tildeles automatisk licenser til alle medlemmer af gruppen. Hvis en bruger er efterfølgende tilføjet eller fjernet fra gruppen, bliver en licens automatisk tildelt eller fjernet efter behov.

Du kan bruge grupper, du synkroniserer fra lokale AD eller administrere i Azure AD. Du kan nemt parring dette med Azure AD premium selvbetjening Gruppeadministration til at uddelegere licensaftalen til den relevante beslutning virksomhed. Du kan være sikker på, at problemer som licenskonflikter og manglende placeringsdata sorteres automatisk.

## <a name="self-regulating-user-administration"></a>Selv regulerende bruger administration
Når organisationen begynder at klargøre ressourcer på tværs af alle interne organisationer, kan du implementere muligheden for selv regulerende bruger administration. Du kan opdager fordele og fordele ved klargøring brugere på tværs af organisatoriske grænser. I dette miljø kan afspejles en ændring i en brugers status automatisk i adgangsrettigheder på tværs af organisationen grænser og geografisk placering. Du kan reducere klargøring omkostninger og strømline processerne adgang og godkendelse. Implementeringen udløser det fulde potentiale til implementering af rollebaseret adgangskontrol for administration af adgang til slut i din organisation. Du kan reducere de administrative omkostningerne gennem automatiseret procedurer for gælder for brugerklargøring af. Du kan forbedre sikkerheden ved at automatisere sikkerhed politik aktivering og strømline og samle livscyklus brugeradministration og ressource klargøring til store bruger populationer.

>[AZURE.NOTE]
Kan finde flere oplysninger under installationen af Azure AD for selv access administration af tjenesteprogram

Licens-baserede (ret-baseret) Azure AD tjenester arbejde ved at aktivere et abonnement i din Azure AD katalogtjenesten/lejer. Når abonnementet er aktivt kan tjenesten funktionerne administreres af katalogtjenesten/administratorer og bruges af licenserede brugere. Se hvordan påvirker Azure AD-licensering arbejde få mere at vide?
Integration med andre 3 producenter

Azure Active Directory indeholder enkelt Log på og forbedret programmet access-sikkerhed til tusindvis af SaaS programmer og lokale webprogrammer. En detaljeret liste over Azure Active Directory-program galleriet til understøttede SaaS programmer, se Azure Active Directory federation compatibility list: tredjepartsidentitetsudbydere udbydere, der kan bruges til at implementere enkeltlogon

## <a name="define-synchronization-management"></a>Definere synkronisering administration
Integrere dine lokale mapper med Azure AD gør brugerne mere produktiv ved at give en fælles identitet for at få adgang til både skyen og lokale ressourcer. Med denne integration kan brugere og organisationer drage fordel af følgende:

- Organisationer kan give brugere med en fælles hybrid identitet på tværs af lokalt eller skybaseret tjenester udnytte Windows Server Active Directory og opretter derefter forbindelse til Azure Active Directory.
- Administratorer kan give betinget adgang, der er baseret på programmet ressource, enhed og bruger-id, netværksplacering og multi-Factor authentication.
- Brugere kan udnytte deres fælles identitet via konti i Azure AD til Office 365, Intune, SaaS apps og tredjepartsprogrammer.
- Udviklere kan oprette programmer, der udnytter almindelige identitet modellen, integrere programmer i Active Directory lokalt eller Azure til skyen-baserede programmer

I følgende figur er et eksempel på en overordnet oversigt over processen til synkronisering af identitet.

![](./media/hybrid-id-design-considerations/identitysync.png)

Processen til synkronisering af identitet

Gennemse den følgende tabel for at sammenligne synkroniseringsindstillinger:

| Indstilling for administration af synkronisering          | Fordele                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Ulemper                                                                                                                                                                                                                                                                                  |
|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Synkroniser baseret (via DirSync eller AADConnect) | Brugere og grupper, der er synkroniseret fra i det lokale miljø og skyen <br>  **Politik for kontrolelement**: kontopolitikker kan angives via Active Directory, der giver administratoren mulighed for at administrere adgangskodepolitikker arbejdsstation begrænsninger, og Lås op-kontrolelementer, og mere, uden at de skal udføre flere opgaver i skyen.  <br>  **Adgangskontrol**: kan begrænse adgangen til skytjenesten, således at tjenesterne kan få adgang til via virksomhedsmiljø via online-serverne eller begge dele. <br>  Reduceret supportopkald: Hvis brugere har færre adgangskoder til at huske, de er mindre sandsynligt til at glemme dem. <br>  Sikkerhed: Bruger-id'er og oplysninger er beskyttet, fordi alle servere og tjenester, der bruges i single sign-on –, administreres og kontrolleres i det lokale miljø. <br>  Understøttelse af stærk godkendelse: Du kan bruge stærk godkendelse (også kaldet to faktor-godkendelse) med skytjenesten. Men hvis du bruger stærke godkendelse, skal du bruge enkeltlogon. |                                                                                                                                                                                                                                                                                                |
| Sammenslutning-baserede (via AD FS)           | Aktiveret af tjenesten for sikkerhedstoken (STS). Når du konfigurerer en STS til at levere enkelt sign-on – access med en Microsoft-skytjeneste, opretter du et medlem af organisationsnetværket tillidsforhold mellem dit lokale STS og det medlem af organisationsnetværket domæne, du har specificeret i din Azure AD-lejer. <br> Gør det muligt for slutbrugere mulighed for at bruge det samme sæt legitimationsoplysninger til at få adgang til flere ressourcer <br>slutbrugere behøver ikke at vedligeholde flere sæt af legitimationsoplysninger. Endnu, brugerne skal levere deres legitimationsoplysninger til hver enkelt af de deltagende ressourcer., B2B og B2C scenarier, der understøttes.                                                                                                                                                                                                                                                                                                                                                                                                             | Kræver særlig personale til installation og vedligeholdelse af dedikeret lokalt AD FS-servere. Der er begrænsninger for brug af stærk godkendelse, hvis du planlægger at bruge AD FS til din STS. Se [Konfigurere avancerede indstillinger for AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649)kan finde flere oplysninger. |

>[AZURE.NOTE]
Yderligere oplysninger finder [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).


## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
