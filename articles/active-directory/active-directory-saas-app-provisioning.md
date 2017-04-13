<properties
    pageTitle="Automatisk SaaS App bruger klargøring i Azure AD | Microsoft Azure"
    description="En introduktion til, hvordan du kan bruge Azure AD til automatisk for at klargøre, deaktivere Klargør og opdatere løbende brugerkonti på tværs af flere SaaS tredjepartsprogrammer."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatisere bruger klargøring og ophævelse af klargøring til SaaS programmer med Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>Hvad er automatiserede klargøring af brugere til SaaS Apps?

Azure Active Directory (Azure AD) gør det muligt at automatisere oprettelse, vedligeholdelse og fjernelse af bruger-id'er i skyen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) programmer som Dropbox, Salesforce, ServiceNow og meget mere.

**Nedenfor vises nogle eksempler på hvad denne funktion kan du gøre følgende:**

- Automatisk oprette nye konti i højre SaaS apps for nye personer, når de deltager i dit team.
- Deaktivere automatisk konti fra SaaS apps, når brugere lader milepælsdatoer teamet.
- Sørg for, identiteter i dine SaaS apps holdes opdaterede baseret på ændringer i kataloget.
- Klargøring af ikke-brugere objekter, som grupper til SaaS apps, der understøtter dem.

**Brugerklargøring af automatiseret også indeholder følgende funktioner:**

- Muligheden for at matche eksisterende identiteter mellem Azure AD og SaaS apps.
- Indstillinger for tilpasning til Hjælp Azure AD så det passer til de aktuelle konfigurationer SaaS Apps, der bruger din organisation i øjeblikket.
- Valgfri e-mail-beskeder for klargøringsfejl.
- Rapportering og aktivitet logfiler med hjælp til overvågning og fejlfinding.

##<a name="why-use-automated-provisioning"></a>Hvorfor bruge Automatiseret klargøring?

Nogle almindelige motivationer til brug af denne funktion omfatter:

- For at undgå de omkostninger, ineffektiv brug og human fejl i forbindelse med manuel klargøring processer.
- Til at sikre din organisation ved at fjerne brugernes identiteter fra vigtige SaaS apps med det samme, når de forlader organisationen.
- Nemt importere et samlet antal brugere til et bestemt SaaS-program.
- For at drage fordel af har klargøring af din løsning Kør fra de samme app access politikker, som du angav for Azure AD Single Sign-On.

##<a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Hvor ofte kan Azure AD skrive directory ændringer til appen SaaS?**

Azure AD kontrollerer, om ændringer hver fem til ti minutter. Hvis appen SaaS er returnere flere fejl (f.eks som er tilfældet med ugyldige Administratoroplysninger), sænke hyppighed til op til én gang om dagen før fejlene er rettet gradvist i Azure AD.

**Hvor lang tid tager det for at klargøre Mine brugere?**

Trinvise ændringer sker næsten med det samme, men hvis du prøver at klargøre mest muligt ud af dit bibliotek, derefter det afhænger af antallet af brugere og grupper, du har. Lille kataloger tage kun et par minutter, mellemstore mapper kan tage flere minutter, og meget store mapper kan tage flere timer.

**Hvordan kan jeg få vist status for det aktuelle klargøring job?**

Du kan gennemse konto klargøring af rapporten under afsnittet rapporter i mappen. En anden mulighed er at gå til fanen Dashboard for programmet SaaS, du er klargøring til, og se under afsnittet "Integration Status" nær bunden af siden.

**Hvordan ved jeg, hvis brugerne ikke kunne få klargjort korrekt?**

Guiden der er i slutningen af konfigurationen af klargøring mulighed for at abonnere på e-mail-meddelelser til klargøring af mislykkede forsøg. Du kan også kontrollere rapporten klargøring fejl for at få vist, hvilke brugere kunne ikke klargøres og hvorfor.

**Kan Azure AD skrive ændringer fra app SaaS tilbage til mappen?**

For de fleste SaaS apps er klargøring udelukkende er udgående, hvilket betyder, at brugere, der skrives fra mappen til programmet, og ændringer fra programmet kan ikke skrives tilbage til mappen. For [arbejdsdag](https://msdn.microsoft.com/library/azure/dn762434.aspx)er klargøring dog indgående kun, hvilket betyder, at brugerne er importeret til mappen fra arbejdsdag, og på samme måde, ændringer i kataloget ikke få skrevet tilbage til arbejdsdag.

**Hvordan kan jeg sende feedback til det tekniske Hold?**

Skal du kontakte os via [feedback-forum for Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>Hvordan fungerer automatiseret klargøring sammen?

Azure AD Klargør brugere til SaaS apps ved at oprette forbindelse til klargøring slutpunkter, der leveres af hver forhandleren af programmet. Disse slutpunkter Tillad Azure AD til automatisk at oprette, opdatere og fjerne brugere. Nedenfor vises en kort oversigt over de forskellige trin, Azure AD tager for at automatisere klargøring.

1. Når du aktiverer klargøring til et program for første gang, udføres følgende handlinger:
 - Azure AD forsøger så det svarer til en eksisterende brugere i appen SaaS med deres tilsvarende identiteter i kataloget. Når en bruger er matchet, de er *ikke* aktiveret automatisk til single sign-on. Hvis en bruger skal have adgang til programmet, skal skal de eksplicit tildeles til appen i Azure AD, enten direkte eller via gruppemedlemskab.
 - Hvis du allerede har angivet, hvilke brugere skal have tildelt programmet, og hvis Azure AD mislykkes til at finde eksisterende konti for disse brugere, Klargør Azure AD nye konti til dem i programmet på computeren.
2. Når den indledende synkronisering er afsluttet, som beskrevet ovenfor, kontrollere Azure AD hver 10 minutter for følgende ændringer:
 - Hvis nye brugere har fået tildelt til programmet (enten direkte eller via et gruppemedlemskab), vil de være klargjort en ny konto i appen SaaS.
 - Hvis en brugers adgang er blevet fjernet og derefter deres konto i appen SaaS bliver markeret som deaktiveret (brugere aldrig fuldt slettes, som beskytter dig mod datatab i tilfælde af en forkert konfiguration).
 - Hvis en bruger for nylig er blevet tildelt programmet, og de allerede har en konto i appen SaaS, markeres kontoen, som aktiveret, og visse brugeregenskaber kan opdateres, hvis de er forældet, sammenlignet med mappen.
 - Hvis en brugers oplysninger (såsom telefonnummer, Kontoradressen osv) er blevet ændret i kataloget, opdateres oplysningerne også i programmet SaaS.

Yderligere oplysninger om hvordan attributter, som er tilknyttet mellem Azure AD, og din SaaS-app, se artiklen på [Tilpasning af attributtilknytninger](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Listen over Apps, der understøtter automatiseret brugerklargøring

Klik på en app til at se et selvstudium om, hvordan du konfigurerer automatiseret klargøring til det:

- [Feltet](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Tilsammen kan føre](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox til erhverv](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce sandkassetilstand](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Arbejdsdag](http://go.microsoft.com/fwlink/?LinkId=690250) (indgående klargøring)

I rækkefølge for et program til at understøtte brugerklargøring af automatiseret, skal den først angive de nødvendige slutpunkter, som giver mulighed for eksterne programmer til at automatisere oprettelse og vedligeholdelse på fjernelse af brugere. Derfor er ikke alle SaaS apps kompatible med denne funktion. Azure AD engineering-teamet vil derefter kunne oprette en klargøring forbindelse til disse apps til apps, som understøtter dette, og dette arbejde prioriteres ved behovene hos eksisterende og mulige kunder.

Hvis du vil ringe til teknisk Azure AD-team for at anmode om klargøring understøttelse af flere programmer, skal du sende en meddelelse til [Azure Active Directory feedback-forum](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Tilpasse attributtilknytninger til klargøring af bruger](active-directory-saas-customizing-attribute-mappings.md)
- [Skrive udtryk for attributten tilknytninger](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Angivelse af område filtre til klargøring af bruger](active-directory-saas-scoping-filters.md)
- [Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer](active-directory-scim-provisioning.md)
- [Klargøring af beskeder-konto](active-directory-saas-account-provisioning-notifications.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)
