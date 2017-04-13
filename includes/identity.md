Administrere identitet er lige så vigtigt i offentlige skyen som i det lokale miljø. For at hjælpe med dette understøtter Azure flere forskellige skyen identitet teknologier. De omfatter følgende:

- Du kan køre Windows Server Active Directory (også kaldet lige AD) i skyen ved hjælp af virtuelle maskiner, der er oprettet med virtuelle Azure-computere. Denne fremgangsmåde giver mening, når du bruger Azure til at udvide dit lokale datacenter til skyen.


- Du kan bruge Azure Active Directory til at give dine brugere enkeltlogon til programmer [som en tjeneste (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) . Microsofts Office 365 bruger denne teknologi, for eksempel og programmer, der kører på Azure eller andre skyen platforme kan også bruge den.


- Programmer, der kører i skyen eller lokalt kan bruge Azure Active Directory-adgangskontrol til at lade brugerne skal logge på ved hjælp af identiteter fra Facebook, Google, Microsoft og andre id-udbydere.


I denne artikel beskrives alle tre af disse indstillinger.

## <a name="table-of-contents"></a>Indholdsfortegnelse

- [Kører Windows Server Active Directory i virtuelle maskiner](#adinvm)

- [Brug af Azure Active Directory](#ad)

- [Brug af Azure Active Directory adgangskontrol](#ac)


## <a name="adinvm"></a>Kører Windows Server Active Directory i virtuelle maskiner

Kører Windows Server AD i Azure virtuelle maskiner minder meget om at køre den lokalt. [Figur 1](#fig1) viser et typisk eksempel på hvordan det ser ud.

![Azure Active Directory i virtuelt](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figur 1: Windows Server Active Directory kan køre på Azure virtuelle computere, der har forbindelse til en organisations lokale datacenter ved hjælp af Azure virtuelt netværk.

I eksemplet herunder, kører Windows Server AD i FOS, der er oprettet ved hjælp af virtuelle Azure-computere, den platform IaaS teknologi. Disse FOS og et par andre er inddelt i et virtuelt netværk, der er knyttet til en lokal datacenter, ved hjælp af Azure virtuelt netværk. Det virtuelle netværk skærer ud af en gruppe af skyen virtuelle maskiner, der arbejder sammen med det lokale netværk via en virtuel privat netværksforbindelse (VPN). At gøre på denne måde kan ligne disse Azure virtuelle maskiner bare en anden undernet til det lokale datacenter. Som i figur vises, kører to af disse FOS Windows Server AD domænecontrollere. De andre virtuelle maskiner i det virtuelle netværk kører muligvis programmer, som SharePoint eller bruges i en anden måde, som til udvikling og test. Det lokale datacenter kører også to Windows Server AD domæne enheder.

Der er adskillige muligheder for at oprette forbindelse domænecontrollerne i skyen med dem, der kører lokalt:

- Gøre alle dem en del af et enkelt Active Directory-domæne.

- Oprette separate AD domæner i det lokale miljø og i skyen, der er en del af den samme skov.

- Oprette separate AD områder i skyen og i det lokale miljø, og forbind områder ved hjælp af tillidsforhold i tværs af områder eller Windows Server Active Directory Federation Services (AD FS), som kan også køre i virtuelle maskiner på Azure.

Uanset valg er foretaget, skal en administrator skal sikre, at anmodninger om godkendelse fra lokale brugere går til skyen domænecontrollere kun, når det er nødvendigt, da linket til skyen der sandsynligvis er langsommere end lokale netværk. En anden faktor skal du tænke på i forbindende skyen og lokale domæne enheder er trafikken genereret gentagelse. I skyen der typisk i deres egen AD-websted, som gør det muligt for en administrator til at planlægge, hvor ofte replikering er færdig. Azure gebyrer for trafik, der sendes af en Azure datacenter, selvom ikke sendt i, som kan påvirke administratorens gentagelse valgmuligheder for byte. Det er også værd fremhæver, mens Azure indeholder understøttelse sin egen Domain Name System (DNS), er denne tjeneste mangler funktioner, der kræves af Active Directory (såsom understøttelse af dynamisk DNS- og SRV-poster). På grund af dette kræver kører Windows Server AD på Azure opsætning af dine egne DNS-servere i skyen.

Kører Windows Server AD i Azure FOS kan giver mening i flere forskellige situationer. Her er nogle eksempler:

- Hvis du bruger virtuelle Azure-computere som en udvidelse af din egen datacenter, kan du køre programmer i skyen, der skal lokale domæne enheder til at håndtere ting som Windows-integreret godkendelse af anmodninger om eller LDAP-forespørgsler. SharePoint, for eksempel interagerer ofte med Active Directory, og så det er muligt at køre en SharePoint-farm på Azure ved hjælp af en lokal adresseliste, Opsætning af domæne enheder i skyen betydeligt forbedrer ydeevnen. (Det er vigtigt at huske, at det ikke er nødvendigvis påkrævet, men; masser af programmer, kan køre i skyen ved hjælp af kun lokale domæne enheder).

- Antag, at en fjerntliggende afdelingen mangler ressourcer for at køre sit eget domæne enheder. I dag, dens brugerne skal godkendes domænecontrollere på anden siden i verden - logon er langsom. Køre Active Directory på Azure i en nærmere Microsoft datacenter kan fremskynde dette uden at kræve flere servere i afdelingen.

- En organisation, som bruger Azure til nedbrud ønsker at opretholde et lille sæt aktive FOS i skyen, herunder et domænenavn fra domænecontrolleren. Vedkommende kan derefter parat til at udvide dette websted efter behov for at tage over for at finde fejl et andet sted.

Der er også andre muligheder. For eksempel er du ikke påkrævet for at oprette forbindelse mellem Windows Server AD i skyen til en lokal datacenter. Hvis du vil køre en SharePoint-farm, der fungeret et bestemt antal brugere, for eksempel alle hvem vil logge på udelukkende med skybaseret identiteter, kan du oprette en enkeltstående skov på Azure. Hvordan du bruger denne teknologi, afhænger af, hvad dine mål er. (For at få mere detaljerede vejledning til brug af Windows Server AD med Azure, [Se her](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Brug af Azure Active Directory

Efterhånden som SaaS programmer bliver mere og mere almindelige, de hæve et indlysende spørgsmål: hvilken type af katalogtjenesten skal programmerne skybaseret bruge? Microsoft-svar på spørgsmålet er Azure Active Directory.

Der er to primære muligheder for at bruge denne Internetadresseliste i skyen:

- Personer og organisationer, der bruger kun SaaS programmer kan stole på Azure Active Directory, som deres eneste katalogtjenesten.

- Organisationer, der kører Windows Server Active Directory kan forbinde deres lokale mappe til Azure Active Directory og derefter bruge det til at give deres brugere enkeltlogon til SaaS programmer.


[Figur 2](#fig2) illustrerer først af disse to muligheder, hvor Azure Active Directory er alle, der kræves.

![Azure Active Directory i virtuelt](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figur 2: Azure Active Directory giver en organisations brugere enkeltlogon til SaaS programmer, herunder Office 365.

Som i figur vises, er Azure AD en med flere lejer tjeneste. Det betyder, at det samtidigt kan understøtte mange forskellige organisationer, lagring af directory oplysninger om brugere på hver af dem. I dette eksempel en bruger på organisation A forsøger at få adgang til et SaaS program. Dette program være en del af Office 365, som SharePoint Online, eller det kan være noget andet – ikke Microsoft-programmer kan også bruge denne teknologi. Fordi Azure AD understøtter SAML 2.0-protokollen, er det eneste, der kræves fra et program muligheden for at interagere med denne branchestandard. (Faktisk programmer, der bruger Azure AD kan køre i et datacenter, ikke kun en Azure datacenter).

Processen starter, når brugeren har adgang til et SaaS program (trin 1). Hvis du vil bruge dette program, skal brugeren præsentere et udstedt af Azure AD-id.

Dette token indeholder oplysninger, der identificerer brugeren, og det er digitalt signeret af Azure AD. For at få tokenet, brugeren skal godkende sig selv til Azure AD ved at angive et brugernavn og adgangskode (trin 2). Azure AD derefter returnerer tokenet han skal (trin 3).

Dette token sendes til SaaS programmet (trin 4), som validerer den token signatur og dens indhold (trin 5) bruges. Programmet bruger typisk identitet oplysningerne tokenet indeholder for at afgøre, hvilke oplysninger, som brugeren har tilladelse til access og måske på andre måder.

Hvis programmet skal flere oplysninger om brugeren, end hvad er indeholdt i tokenet, kan det anmode om dette direkte fra Azure AD ved hjælp af Azure AD Graph API (trin 6). I den oprindelige version af Azure AD directory skemaet er helt enkelt: den indeholder kun brugere og grupper og relationerne mellem dem. Programmer kan bruge disse oplysninger til at få mere for at vide om forbindelser mellem brugere. Lad os antage, at et program skal vide, hvem denne brugers manager er beslutte, om han har tilladt adgang til nogle portion data. Det kan du læse ved at forespørge Azure AD gennem Graph API.

Graph API bruger en almindelig RESTful protokol, hvilket gør det nemt at bruge fra de fleste klienter, herunder mobilenheder. API understøtter også de filtypenavne, der er defineret af OData, tilføje ting som et forespørgselssprog til at lade klienter access-data i flere nyttige måder. (Få mere at vide om OData, under [Introduktion til OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf)). Fordi Graph API kan bruges til at få mere at vide om relationer mellem brugere, kan programmer forstå sociale grafen, der er integreret i Azure AD-skemaet for en bestemt organisation (som er grunden til, at den hedder Graph API). Og for at godkende sig selv over Azure AD Graph API anmodninger om, et program bruger OAuth 2.0.

Hvis en organisation ikke bruger Windows Server Active Directory - det har ingen lokale servere eller domæner - og afhænger udelukkende af skyen programmer, der bruger Azure AD, vil ved hjælp af lige adresselisten skyen give virksomhedens brugere enkeltlogon til dem alle. Endnu mens dette scenario bliver mere almindelige hver dag, de fleste organisationer stadig bruge lokale domæner, der er oprettet med Windows Server Active Directory. Azure AD har en nyttig rolle her samt, som viser [figur 3](#fig3) .

![Azure Active Directory i virtuelt](./media/identity/identity_03_AD.png)
<a id="fig3"></a>figur 3: en organisation kan oprette Windows Server Active Directory med Azure Active Directory give dens brugere enkeltlogon til SaaS programmer.

I dette scenarie skal ønsker en bruger på organisation B at få adgang til et SaaS program. Før hun gør dette, skal organisationens directory-Administratorer opretter en relation, sammenslutning med Azure AD med AD FS, som viser figur. Disse administratorer skal også konfigurere synkronisering af data mellem organisationens lokale Windows Server AD og Azure AD. Dette kopierer automatisk bruger og gruppeoplysninger fra mappen lokale til Azure AD. Bemærk, at det giver mulighed for: I kraft organisationen udvide dets lokal adresseliste til skyen. Kombinerer Windows Server AD og Azure AD på denne måde giver organisationen en katalogtjeneste, der kan administreres som et enkelt objekt, mens du stadig har et miljøet både lokalt og i skyen.

Hvis du vil bruge Azure AD, logger brugeren først på sin lokale Active Directory-domæne som normalt (trin 1). Når hun forsøger at få adgang til SaaS programmet (trin 2), giver sammenslutning processen Azure AD udstedelse hende et token for dette program (trin 3). (Få mere at vide om, hvordan sammenslutning fungerer, se [kravbaseret identitet til Windows:-teknologier og scenarier](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) Som før, dette token indeholder oplysninger, der identificerer brugeren, og det er digitalt signeret af Azure AD. Dette token sendes til SaaS programmet (trin 4), som validerer den token signatur og dens indhold (trin 5) bruges. Og er i det forrige scenario, SaaS programmet kan bruge Graph API til at få flere oplysninger om denne bruger, hvis behov (trin 6).

I dag, Azure AD er ikke en komplet erstatning for lokale Windows Server AD. Mappen skyen har en meget nemmere skema som allerede nævnt, og der mangler også ting som Gruppepolitik, muligheden for at gemme oplysninger om computere og understøttelse af LDAP. (Faktisk en Windows-computer kan ikke konfigureres til at lade brugere logger på den ved hjælp af noget men Azure AD - det er ikke et understøttet scenarie). I stedet Azure AD indledende mål omfatter at lade enterprise brugere få adgang til programmer i skyen uden at opretholde et separat logon og frigøre on-premises directory-administratorer fra manuelt synkronisere deres lokale mappe med alle SaaS programmerne deres organisation bruger. Over tid, men forvente denne skyen Internetadresseliste til en adresse til en bred vifte af scenarier.

## <a name="ac"></a>Brug af Azure Active Directory adgangskontrol

Skybaseret identitet teknologier kan bruges til at løse en række problemer. Azure Active Directory kan give en organisations brugere enkeltlogon til flere SaaS programmer, f.eks. Men identitet teknologier i skyen kan også bruges på andre måder.

Forestil dig f.eks, at et program ønsker at lade brugerne logge på ved hjælp af tokens, der er udstedt af flere *identitetsudbydere (IdPs)*. Mange forskellige identitetsudbydere findes i dag, herunder Facebook, Google, Microsoft og andre, og programmer så ofte brugerne kan logge på ved hjælp af en af disse identiteter. Hvorfor skal et program ikke tænke på at vedligeholde sin egen liste over brugere og adgangskoder, når den i stedet kan stole på identiteter, der allerede findes? Acceptere eksisterende identiteter gør liv enklere både for brugere, som har en mindre brugernavn og din adgangskode til at huske, og de personer, oprette programmet, der ikke længere har brug for til at vedligeholde deres egne lister over brugernavne og adgangskoder.

Men mens hver identitetsudbyder problemer er en slags token disse tokens ikke er standard - hver IdP har sit eget format. Desuden oplysningerne i disse tokens er heller ikke standard. Et program, som ønsker at acceptere tokens, der er udstedt af, sig, Facebook, Google og Microsoft står med udfordringen ved at skrive entydig kode til at håndtere hver af disse forskellige formater.

Men hvorfor gør dette? Hvorfor ikke i stedet oprette mellemled, der kan generere et enkelt token format med et almindelige repræsentation af identitetsoplysninger? Denne metode ville gør livet lettere for udviklere, der opretter programmer, da de nu skal bruge til at håndtere kun én type token. Azure Active Directory-adgangskontrol understøtter præcis det, give mellemled i skyen til at arbejde med forskellige tokens. [Figur 4](#fig4) viser, hvordan det fungerer

![Azure Active Directory i virtuelt](./media/identity/identity_04_IdentityProviders.png)
<a id="fig4"></a>figur 4: Azure Active Directory-adgangskontrol gør det nemmere for programmer for at acceptere identitet tokens udstedt af forskellige identitetsudbydere.

Processen starter, når en bruger forsøger at få adgang til programmet fra en browser. Programmet omdirigerer hun kan en IdP vilkårlig (og, programmet også har tillid til). Hun godkendes af sig selv IdP, f.eks ved at angive et brugernavn og adgangskode (trin 1), og IdP returneres et token, der indeholder oplysninger om sin (trin 2).

Som i figur vises, understøtter adgangskontrol til en række forskellige skybaseret IdPs, herunder konti, der er oprettet af Google, Yahoo, Facebook, Microsoft, (tidligere kaldet Windows Live ID) og enhver OpenID udbyder. Understøtter også identiteter, der er oprettet ved hjælp af Azure Active Directory og gennem sammenslutning med AD FS, Windows Server Active Directory. Målet er dækker de mest almindeligt anvendte identiteter i dag, om de er udstedt af IdPs i skyen eller i det lokale miljø.

Når brugerens browser har et IdP token fra hendes valgte IdP, sender programmet dette token til adgangskontrol (trin 3). Adgangskontrol valideret tokenet, og Sørg for, at det virkelig er udstedt af denne IdP, og derefter opretter et nyt id ifølge de regler, der er defineret for dette program. Adgangskontrol er en med flere lejer tjeneste som Azure Active Directory, men lejere er programmer i stedet for kunde organisationer. Hvert program kan få sin egen navneområde, som viser de figur, og kan angive forskellige regler om godkendelse og meget mere.

Disse regler lade hvert program administrator definere, hvordan tokens ud fra forskellige IdPs skal omdannes til et adgangskontrol token. Eksempelvis hvis forskellige IdPs bruger forskellige typer til at repræsentere brugernavne, kan adgangskontrol regler forvandle alle af følgende i en almindelig brugernavn type. Adgangskontrol sender derefter denne nye token tilbage til browseren (trin 4), som sender den til programmet (trin 5). Når det er adgangskontrol tokenet, kontrollerer programmet, at dette token virkelig er udstedt af adgangskontrol, og derefter bruger de oplysninger, den indeholder (trin 6).

Mens denne proces kan virke kompliceret en smule, gør det faktisk liv betydeligt enklere til oprettelse af programmet. I stedet for håndtere forskelligartet tokens, der indeholder de oplysninger, kan programmet acceptere identiteter udstedt af flere identitetsudbydere, mens du stadig modtager kun en enkelt token med velkendte oplysninger. Desuden i stedet for kræver hvert program for at være konfigureret til at have tillid til forskellige IdPs, disse tillidsforhold vedligeholdes i stedet ved adgangskontrol – et program skal kun har tillid til den.

Det er værd fremhæver, noget om adgangskontrol er bundet til Windows - lige så godt skal bruges af en Linux-program, der har accepteret kun Google- og Facebook identiteter. Og selvom adgangskontrol er en del af Azure Active Directory-serien, kan du betragte det som en helt særskilt tjeneste end det er beskrevet i forrige afsnit. Mens begge teknologier arbejder med identitet, omhandler de helt forskellige problemer (selvom Microsoft har sagt, at den forventer at integrere to på et tidspunkt).

Arbejde med identitet er vigtige i næsten alle programmet på computeren. Formålet med adgangskontrol er at gøre det nemmere for udviklere til at oprette programmer, der accepterer identiteter fra forskellige identitetsudbydere. Når du indsætter denne tjeneste i skyen, har Microsoft gjort det tilgængeligt for alle programmer, der kører på en hvilken som helst platform.

##<a name="about-the-author"></a>Om forfatteren

David Chappell er vigtigste Chappell og kolleger [www.davidchappell.com](http://www.davidchappell.com) i San Francisco, California.
