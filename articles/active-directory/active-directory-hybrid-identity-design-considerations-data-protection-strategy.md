<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - definere strategi til databeskyttelse | Microsoft Azure"
    description="Du skal angive strategi for dit hybride identitet løsning at imødekomme virksomhedens behov, som du angav databeskyttelse."
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


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Angive strategi til databeskyttelse for dit hybride identitet løsning

I denne opgave, skal du definere databeskyttelse strategi for dit hybride identitet løsning at imødekomme virksomhedens behov, som du angav i:

- [Fastlægge krav til beskyttelse af data](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Fastlægge krav til styring af webindhold](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Finde ud af krav til access](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Fastlægge krav til hændelsen svar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Angive indstillinger for beskyttelse af data
Som det er beskrevet i [fastslå directory-synkronisering krav](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), placeret Microsoft Azure AD kan synkronisere med din Active Directory-domænetjenester (AD DS) i det lokale miljø. Denne integration giver organisationer mulighed at udnytte Azure AD for at bekræfte brugerens legitimationsoplysninger, når de forsøger at få adgang til virksomhedens ressourcer. Dette kan gøres for begge scenarier: data på resten lokalt og i skyen.  Adgang til data i Azure AD kræver brugergodkendelse via en tjenesten for sikkerhedstoken (STS).

Når godkendt, læse brugerens hovednavn (UPN) fra tokenet godkendelse og den replikerede partition og beholder, der svarer til brugerens domæne bestemmes. Oplysninger om brugerens eksistensen, aktiverede tilstand og rolle bruges af systemet tilladelse til at afgøre, om den nødvendige adgang til mållejeren er godkendt til denne bruger i denne session. Visse autoriserede handlinger (nærmere betegnet kan oprette bruger, nulstilling af adgangskode) oprette et revisionsspor, der kan bruges til at administrere overholdelse af regler eller undersøgelser af lejeradministrator.

Flytte data fra dit lokale datacenter til Azure-lager over en internetforbindelse altid muligvis ikke muligt på grund af data lydstyrken, båndbredde tilgængelighed eller andre overvejelser i forbindelse med. [Azure lagerplads Importér/Eksportér Service](../storage/storage-import-export-service.md) indeholder en hardware-baserede indstilling til at placere/hente store mængder data i blob-lager. Gør det muligt at sende [BitLocker-krypterede](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) harddiskdrev direkte til en Azure datacenter, hvor skyen operatorer overfører indholdet til kontoen lagerplads, eller de kan hente dine Azure data til dine drev at vende tilbage til dig. Kun krypterede diske accepteres for denne proces (ved hjælp af en BitLocker-nøgle, der er oprettet af tjenesten selve under opsætningen af). Tasten BitLocker er angivet til Azure separat, hvilket giver af band vigtige dele.

Da data undervejs kan foregå i forskellige scenarier, der er også relevant at vide, at Microsoft Azure bruger [virtuelle netværk](https://azure.microsoft.com/documentation/services/virtual-network/) til at isolere lejere trafik fra hinanden, anvender foranstaltninger såsom host og Gæst niveau firewalls, filtrering af IP-pakker, blokering af port og HTTPS slutpunkter. De fleste af Azures interne kommunikation, herunder infrastruktur til infrastruktur og infrastruktur-til-kunde (lokalt), er dog også krypteret. En anden vigtigt scenarie er kommunikation i Azure datacentre; Microsoft administrerer netværk for at sikre, at ingen VM kan repræsentere eller mobiltelefonkommandoer til IP-adressen på en anden. TLS/SSL bruges, når du åbner Azure-lager eller SQL-databaser, eller når du opretter forbindelse til Skytjenester. I dette tilfælde er kunden administratoren ansvarlig for at opnå en TLS/SSL-certifikat og installere den på deres lejer infrastruktur. Data trafik flytte mellem virtuelle maskiner i den samme installation eller mellem lejere i en enkelt installation via Microsoft Azure virtuelt netværk kan være beskyttet gennem krypteret protokoller som HTTPS, SSL/TLS eller andre.

Afhængigt af hvordan du svar på spørgsmålene i [fastslå krav til beskyttelse af data](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), bør du kunne finde ud af, hvordan du vil beskytte dine data, og hvordan løsningen hybrid identitet kan hjælpe dig på. Tabellen viser de indstillinger, der understøttes af Azure, der er tilgængelige for hvert scenario til beskyttelse af data.


| Indstillinger for beskyttelse af data         | I resten i skyen | I resten lokalt | Undervejs |
|---------------------------------|----------------------|---------------------|------------|
| BitLocker drev kryptering      | X                    | X                   |            |
| SQL Server til at kryptere databaser | X                    | X                   |            |
| VM-VM kryptering             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Læs [overholdelse af funktionen til](https://azure.microsoft.com/support/trust-center/services/) [Microsoft Azure Sikkerhedscenter](https://azure.microsoft.com/support/trust-center/) vil vide mere om de certificering, der er kompatible med hver Azure tjeneste.
Siden Indstillinger for databeskyttelse bruger en med flere lag tilgang, er sammenligning mellem disse indstillinger er ikke tilgængelige for denne opgave. Sørg for, at du udnytte alle tilgængelige indstillinger for hver enkelt tilstand, bliver dataene.

## <a name="define-content-management-options"></a>Angiv indstillinger til styring af webindhold
Fordelene ved at bruge Azure AD til at administrere en hybrid identitet infrastruktur er, at processen er helt gennemsigtig fra slutbrugerens perspektiv. Brugeren forsøger at få adgang til en delt ressource, ressourcen kræver godkendelse, får brugeren til at sende en anmodning om godkendelse til Azure AD for at få tokenet og få adgang til ressourcen. Det hele sker i baggrunden, uden brugerinput. Du kan også give tilladelser til en [gruppe](active-directory-manage-groups.md#getting-started-with-access-management) af brugere for at give dem tilladelse til at udføre bestemte almindelige handlinger.

Organisationer, som har mistanke om, at dataene beskyttelse af personlige oplysninger normalt kræver data klassifikation efter deres løsning. Hvis deres aktuelle lokale infrastruktur er allerede bruger data klassifikation, er det muligt at udnytte Azure AD som den primære lager for brugerens identitet. Et almindelige værktøj, at det er anvendt på det lokale miljø til data klassifikation kaldes [Data klassifikation værktøjskassen](https://msdn.microsoft.com/library/Hh204743.aspx) for Windows Server 2012 R2. Dette værktøj kan hjælpe med at identificere, klassificere og beskytte data på filservere i din private skyen. Det er også muligt at udnytte [Automatiske fil klassificering](https://technet.microsoft.com/library/hh831672.aspx) i Windows Server 2012 til at udføre dette.

Hvis din organisation har ikke data klassifikation på plads, men skal bruge til at beskytte følsomme filer uden at føje nye servere i det lokale miljø, kan de bruge Microsoft [Azure Rights Management-tjenesten](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS bruger kryptering, identitet og godkendelse politikker til at beskytte dine filer og mail, og det fungerer på tværs af flere enheder – telefoner, tablets og computere. Da Azure RMS er en skybaseret tjeneste, er der ingen grund til at konfigurere eksplicit tillidsforhold med andre organisationer, før du kan dele beskyttet indhold med dem. Samarbejde på tværs af organisationer understøttes automatisk, hvis de allerede har en Office 365 eller en Azure AD-mappe. Du kan også synkronisere kun de directory attributter, Azure RMS skal understøtte en fælles identitet for din lokale Active Directory-konti ved hjælp af Azure Active Directory-Synkroniseringstjenester (AAD Sync) eller Azure AD-forbindelse.

En vigtig del af indholdsstyring er at forstå, hvem der har adgang til hvilken ressource, en omfattende logføringsegenskab er derfor vigtigt for identitet management-løsning. Azure AD indeholder log over 30 dage, herunder:

- Ændringer i rollemedlemskab (ex: bruger er føjet til globale administratorrolle)
- Opdateringer af legitimationsoplysninger (ex: ændringer af adgangskode)
- Domæneadministration (ex: bekræftelse af et brugerdefineret domæne, fjerne et domæne)
- Tilføje eller fjerne programmer
- Brugeradministration (ex: tilføje, fjerne, ved at opdatere en bruger)
- Tilføje eller fjerne licenser

>[AZURE.NOTE]
Du kan finde [Microsoft Azure sikkerhed og administration af overvågningslog](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) vil vide mere om logføringsfunktioner i Azure.
Afhængigt af hvordan du svar på spørgsmålene i [fastslå Indholdsstyring krav](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), skal du ikke kunne finde ud af, hvordan du vil have indhold der skal administreres i din hybrid identitet løsning. Mens alle indstillinger vises i tabel 6 er kan integreres med Azure AD, er det vigtigt at definere, hvilket er mere passende for virksomhedens behov.

| Indstillinger for styring af webindhold                                                               | Fordele                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Ulemper                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralt lokalt (Active Directory Rights Management Server)                      | Fuld kontrol over server-infrastrukturen ansvarlige for klassificering af dataene <br> Indbyggede funktion i Windows Server, ingen grund til ekstra licens eller et andet abonnement <br> Kan integreres med Azure AD i et scenarie med hybrid <br> Understøtter oplysninger rights management (IRM) funktioner i Microsoft Online services som Exchange Online og SharePoint Online, samt Office 365 <br> Understøtter lokale Microsoft-serverprodukter, som Exchange Server, SharePoint Server og filservere, der kører Windows Server og fil klassifikation infrastruktur (FCI). | Højere, vedligeholdelse (Hold op med opdateringer, konfiguration og potentielle opgraderinger), siden IT ejer serveren <br> Kræv en server-infrastruktur lokalt<br> Doesn'tleverage Azure funktioner indbygget                                     |
| Centraliserede i skyen (Azure RMS)                                                     | Nemmere at administrere sammenlignet med den lokale løsning <br> Kan integreres med AD DS i et scenarie med hybrid <br>  Fuldt integreret med Azure AD <br> Kræver ikke en server i det lokale miljø for at kunne installere tjenesten <br> Understøtter lokale Microsoft serverprodukter som Exchange Server, SharePoint, Server og filservere, der kører Windows Server og fil klassificering, infrastruktur (FCI) <br> IT, kan have fuld kontrol over deres lejer nøgle med BYOK egenskab.                                                                                    | Organisationen skal have et cloud-abonnement, der understøtter RMS <br> Organisationen skal have en Azure AD-mappe til at understøtte brugergodkendelse for RMS                                                                                  |
| Hybrid (Azure RMS integreret med, lokale Active Directory Rights Management Server) | Dette scenarie indsamles fordelene ved begge, centralt on-premises og i skyen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Organisationen skal have et cloud-abonnement, der understøtter RMS <br> Organisationen skal have en Azure AD-mappe til at understøtte brugergodkendelse for RMS, <br> Kræver en forbindelse mellem Azure skybaseret tjeneste og on-premises-infrastruktur |

## <a name="define-access-control-options"></a>Angive indstillinger til styring af access
Styre funktioner, der er tilgængelige i Azure AD, du vil kunne aktivere din virksomhed om at bruge en central identitet lager samtidig med brugere og partnere til at bruge enkeltlogon (SSO), som vist i nedenstående illustration ved at udnytte godkendelsen, autorisation og adgang:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Central administration og fuldt integration med andre mapper

Azure Active Directory indeholder single sign-on til tusindvis af SaaS programmer og lokale webprogrammer. Læs de [Azure Active Directory federation kompatibilitet liste: tredjepartsidentitetsudbydere udbydere, der kan bruges til at implementere enkeltlogon](https://msdn.microsoft.com/library/azure/jj679342.aspx) artikel for at få flere oplysninger om SSO-tredjepart, der blev testet af Microsoft. Denne funktion gør det muligt for organisationen at implementere en række forskellige B2B scenarier mens kontrollen over for identitets- og administration. Under B2B er designe proces dog vigtigt at forstå den godkendelsesmetode, der anvendes af partner og validere Hvis denne metode understøttes af Azure. I øjeblikket er disse metoder, der understøttes af Azure AD:

- Sikkerhed program Markup Language (SAML)
- OAuth
- Kerberos
- Tokens
- Certifikater


>[AZURE.NOTE] Læs [Azure Active Directory Authentication protokoller](https://msdn.microsoft.com/library/azure/dn151124.aspx) for at vide flere oplysninger om hver protokol og dens egenskaber i Azure.

Ved hjælp af fra Azure AD-support, mobile business programmer kan bruge den samme nemt Mobile-tjenester godkendelse oplevelse tillade medarbejdere at logge på deres mobilprogrammer med deres virksomhedens Active Directory-legitimationsoplysninger. Med denne funktion understøttes Azure AD som identitetsudbyder i Mobile-tjenester sammen med med de øvrige identitet vi allerede understøtter (som omfatter Microsoft-Accounts, Facebook-ID, Google-ID og Twitter-ID). Adgang fra partnere og brugere, der kommer fra skyen skal være gennemsigtigt, hvis de lokale apps bruger brugerens legitimationsoplysninger, der findes i virksomhedens Active Directory-Domænetjenester. Du kan administrere brugerens betinget adgangskontrol til (skybaseret) webprogrammer, web API, Microsofts skytjenester, 3 part SaaS programmer og oprindelige (mobil) klientprogrammer og har fordelene ved sikkerhed, overvågning, rapportering samlet på ét sted. Det anbefales, at validere det i et ikke-produktionsmiljø eller med et begrænset antal brugere.


>[AZURE.TIP] det er vigtigt omtale, Azure AD ikke har Gruppepolitik som AD DS har. Du skal bruge en mobilenhed management-løsning som [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)for at gennemtvinge politik for enheder.

Når brugeren er godkendt ved hjælp af Azure AD, er det vigtigt at evaluere adgangsniveauet, brugeren kan bruge den. Adgangsniveauet, får brugeren over en ressource kan variere, mens Azure AD kan tilføje en ekstra sikkerhedslag ved at styre adgangen til nogle ressourcer, skal du også huske på, at ressourcen sig selv kan også have sin egen access kontrolelementet liste separat, som adgangskontrol for filer, der er placeret i en filserver. Figuren nedenfor opsummerer niveauerne af adgangskontrol, du kan have i et scenarie med hybrid:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Hver interaktion i diagrammet blev vist i figur X repræsenterer et access kontrolelement scenarie, der kan være underlagt Azure AD. Nedenfor har du en beskrivelse af hvert scenarie:

1. betinget adgang til programmer, der er hostet lokalt: Du kan bruge registrerede enheder med access politikker for programmer, der er konfigureret til at bruge AD FS med Windows Server 2012 R2. Du kan finde flere oplysninger om konfiguration af betinget adgang til lokale se [konfiguration af lokale betinget adgang ved hjælp af Azure Active Directory enhed registrering](active-directory-conditional-access-on-premises-setup.md).
2. adgangskontrol Azure Management-portalen: Azure har også muligheden for at styre adgang til portalen administration ved hjælp af RBAC (rolle baseret adgangskontrol). Denne metode kan virksomheden til at begrænse antallet handlinger, som kan gøre en enkelt bruger, når hun har adgang til Azure Management Portal. Ved hjælp af RBAC til at styre adgang til portalen, IT-administratorer ca stedfortræderadgang, ved hjælp af følgende access management metoder:

 - Gruppen-baserede rolletildeling: Du kan tildele adgang til Azure AD-grupper, der kan blive synkroniseret fra din lokale Active Directory. Dette gør det muligt at udnytte de eksisterende investeringer, din organisation har foretaget i værktøj og processer til administration af grupper. Du kan også bruge funktionen til styring af delegeret gruppe af Azure AD Premium.
 - Udnyttelse, der er indbygget i roller i Azure: Du kan bruge tre roller – ejer, bidragyder og læser, for at sikre dig, at brugere og grupper har tilladelse til at gøre kun de opgaver, de skal udføre deres arbejde.
 - Detaljeret adgang til ressourcer: Du kan tildele roller til brugere og grupper for et bestemt abonnement, ressourcegruppe eller en enkelt Azure ressource som et websted eller en database. På denne måde, kan du sikre dig, at brugere har adgang til alle de ressourcer, de skal bruge og ikke adgang til ressourcer, som de ikke er nødvendigt at administrere.

>[AZURE.NOTE] Læs [Rollebaseret adgangskontrol i Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) for at vide flere oplysninger om denne egenskab. For udviklere, der bygger programmer og vil tilpasse kontrolelementet adgang til dem, er det også muligt at bruge Azure AD-programroller til godkendelse. Gennemse denne [WebApp-RoleClaims-DotNet eksempel](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) om, hvordan du opretter din app for at bruge denne egenskab.

3. betinget adgang til Office 365-programmer med Microsoft Intune: IT-administratorer kan tildele betinget access enhedspolitikker for at beskytte virksomhedsressourcer, mens samtidig tillade informationsmedarbejdere på kompatible enheder for at få adgang til tjenester. Du kan finde yderligere oplysninger finder [Betinget Access enhedspolitikker for Office 365-tjenester](active-directory-conditional-access-device-policies.md).

4. betinget adgang til Saas apps: [denne funktion](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) kan du konfigurere per programmet Multi-Factor authentication adgangsregler og muligheden for at blokere adgangen for brugere ikke på et netværk, der er tillid til. Du kan anvende Multi-Factor authentication reglerne for alle brugere, der er tildelt til programmet eller kun til brugere inden for angivne sikkerhedsgrupper. Brugere kan udelukkes fra kravet om godkendelse i flere niveauer, hvis de har adgang til programmet fra en IP-adresse, der i i organisationens netværk.

Siden Indstillinger for at kontrollere adgangen bruger en med flere lag tilgang, er sammenligning mellem disse indstillinger er ikke tilgængelige for denne opgave. Sørg for, at du udnytte alle indstillinger, der er tilgængelige for hvert scenarie, der kræver, at du at styre adgangen til dine ressourcer.

## <a name="define-incident-response-options"></a>Definere hændelsen svarindstillinger
Azure AD kan hjælpe IT for at identitet potentielle sikkerhedsrisici i ved at overvåge aktivitet i brugerens, kan IT-miljøet udnytte Azure AD Access og brugsrapporter muligheden for at få indsigt i integritet og sikkerheden for organisationens adressekartotek. Disse oplysninger, kan en IT-administrator bedre se hvor mulige sikkerhedsrisici kan ligge, så de kan planlægge en dækkende måde at reducere disse risici.  [Azure AD Premium-abonnement](active-directory-get-started-premium.md) har et sæt af sikkerhedsrapporter, der kan aktivere IT for at få disse oplysninger. [Azure AD-rapporter](active-directory-view-access-usage-reports.md) er kategoriseret som vist nedenfor:

- **Anomali rapporter**: indeholder logon begivenheder, vi fandt være uoverensstemmende. Vores mål er at gøre dig opmærksom på sådanne aktiviteter og giver dig mulighed at kunne foretages en bestemmelse om, hvorvidt en begivenhed er mistænkelige.
- **Programmet Integrated rapport**: giver indsigt i hvordan skyen programmer bliver brugt i organisationen. Azure Active Directory tilbyder integration med tusindvis af skyen programmer.
- **Fejlrapporter**: angive fejl, der kan opstå, når konti til eksterne programmer.
- **Brugerdefinerede rapporter**: få vist enhed/logonside i aktivitetsdata for en bestemt bruger.
- **Logfiler over aktivitet**: indeholder en oversigt over alle reviderede begivenheder i seneste 24 timer, sidste 7 dage, eller de seneste 30 dage, samt gruppe aktivitet ændringer og adgangskode Nulstil og registrering aktivitet.

>[AZURE.TIP]
En anden rapport, der kan også hjælpe hændelse svar teamet arbejder på en sag er rapporten [med lækket legitimationsoplysninger](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Denne rapport overflader potentielle forekomster mellem listerne lækket legitimationsoplysninger og din lejer.

Andre vigtige indbygget i rapporter i Azure AD, der kan bruges under hændelsen svar undersøgelse og er:

- **Aktivitet til nulstilling af adgangskode**: giver administratoren indsigt i hvordan aktivt nulstilling af adgangskode der anvendes i organisationen.
- **Registrering aktivitet til nulstilling af adgangskode**: giver indsigt, hvor brugere har registreret deres metoder til nulstilling af adgangskode, og hvilke metoder, de har markeret.
- **Gruppeaktivitet**: giver en oversigt over ændringer i gruppen (ex: brugere, der er tilføjet eller fjernet), der er blevet startet i panelet adgang.

Ud over core reporting muligheden tilgængelig i Azure AD Premium, som kan være opgraderede under en hændelse svar undersøgelse proces, kan IT udnytte overvågningslog rapport for at hente oplysninger som f.eks.:

- Ændringer i rollemedlemskab (ex: bruger er føjet til globale administratorrolle)
- Opdateringer af legitimationsoplysninger (ex: ændringer af adgangskode)
- Domæneadministration (ex: bekræftelse af et brugerdefineret domæne, fjerne et domæne)
- Tilføje eller fjerne programmer
- Brugeradministration (ex: tilføje, fjerne, ved at opdatere en bruger)
- Tilføje eller fjerne licenser

Siden Indstillinger for hændelsen svar bruger en med flere lag tilgang, er sammenligning mellem disse indstillinger er ikke tilgængelige for denne opgave. Sørg for, at du udnytte alle indstillinger, der er tilgængelige for hvert scenarie, der kræver, at du kan bruge Azure AD reporting egenskab som en del af din virksomheds hændelsen svar proces.

## <a name="next-steps"></a>Næste trin
[Bestemme hybrid identitet administrationsopgaver](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
