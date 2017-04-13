<properties
  pageTitle="Adgangskontrol til apps ved hjælp af Azure AD |  Microsoft Azure"
  description="I denne artikel beskrives, hvordan Azure Active Directory giver organisationer mulighed at angive de apps, som hver enkelt bruger har adgang til."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Adgangskontrol til apps

Administration af få vedvarende adgang, brugen beregning og rapportering stadig være en udfordring, når en app er integreret i din organisations identitetssystem. I mange situationer nødt IT-administratorer eller helpdesk til at udføre en igangværende aktiv rolle i administration af adgang til dine apps. Nogle gange kan udføres tildeling af en generelle eller afdelingen IT-teamet. Ofte tildeling beslutning er beregnet på at uddelegeres til business beslutning maker, der kræver deres godkendelse, før IT gør tildelingen.  Andre organisationer investere i integration med et eksisterende automatiseret identitets- og -system, som rollebaseret adgangskontrol (RBAC) eller attribut-baserede adgangskontrol (ABAC). Både integration og regel udvikling ofte specialiserede og dyrt. Overvågning eller har rapporteret på enten management tilgang er sin egen separate, kostbar og kompleks investering.

## <a name="how-does-azure-active-directory-help"></a>Hvordan hjælper Azure Active Directory?

 Azure AD understøtter omfattende tilgængelig til konfigurerede programmer, så organisationer til nemt at opnå de rigtige access politikker, lige fra automatisk, attribut-baserede tildeling (scenarier med ABAC eller RBAC) via delegering og herunder administrationen. Du kan nemt opnå komplekse politikker, kombinerer flere management modeller til et enkelt program og kan endda genbruge management regler på tværs af programmer med de samme målgrupper med Azure AD.

 - [Tilføje nye eller eksisterende programmer](active-directory-sso-integrate-saas-apps.md)


 Azure AD-program tildeling fokuserer på to primære tildeling tilstande:

- **Individuel tildeling** En IT-administrator med directory globale administratorrettigheder kan vælge individuelle brugerkonti og give dem adgang til programmet.
- **Gruppen-baserede tildeling (betalt Azure AD)** En IT-administrator med mappetilladelser Global Administrator kan tildele en gruppe til programmet. Bestemte brugere adgang afhænger af, om de er medlem af gruppen på det tidspunkt, de forsøger at få adgang til programmet. En administrator kan med andre ord effektivt oprette en tildelingsregel om "nuværende medlemmer af gruppen tildelte har adgang til programmet". Bruger denne indstilling for tildelingen, administratorer kan få glæde af Azure AD gruppe management indstillinger, herunder [attribut-baserede dynamiske grupper](active-directory-accessmanagement-manage-groups.md), eksternt systemgrupper (for eksempel lokale Active Directory eller arbejdsdag) eller Administrator-administreret eller din egen-service-administreret grupper. En enkelt gruppe kan nemt tildeles til flere apps, sikrer, at programmer med tildeling forbindelse kan dele tildelingsregler for reducere overordnede management kompleksitet. Vær opmærksom på, indlejret gruppe medlemskaber ikke understøttes for gruppe-baserede tildeling til programmer på nuværende tidspunkt.

Ved hjælp af følgende to tildeling tilstande kan administratorer opnå en hvilken som helst bør tildeling management tilgang.

Med Azure AD brugen og rapportering af tildelinger er fuldt integreret, gør det muligt administratorer nemt rapportere om tildeling stat, tildeling fejl og lige brugen.

## <a name="complex-application-assignment-with-azure-ad"></a>Komplekst program tildeling med Azure AD

Overvej at et program som Salesforce. I mange organisationer, der Salesforce primært bruges af salg og marketing organisationer. Medlemmer af teamet marketing har ofte meget følsomme adgang til Salesforce, mens medlemmer af den salgsteam har begrænset adgang. I mange tilfælde har en bred populationen af informationsmedarbejdere begrænset adgang til programmet. Undtagelser til disse regler let gøre spørgsmål. Det er ofte prerogative marketing eller salg førerskab teams til at tildele en brugeradgang eller ændre deres roller uafhængigt af disse generisk regler.

Med Azure AD kan computerprogrammer som Salesforce være forudkonfigurerede til enkeltlogon (SSO) og automatiseret klargøring. Når programmet er konfigureret, kan en Administrator kan overtage handlingen enkeltstående til at oprette og tildele de relevante grupper. I dette eksempel kan en administrator udføre følgende opgaver:

- [Dynamiske grupper](active-directory-accessmanagement-manage-groups.md) kan defineres for at repræsentere automatisk alle medlemmer af de salg og marketing teams med attributter som afdeling eller rolle:

    - Alle medlemmer af marketing grupper ville være tildelt rollen "marketing" i Salesforce

    - Alle medlemmer af salgsteam grupper ville være tildelt rollen "salg" i Salesforce. En yderligere afgrænsning kunne bruge flere grupper, der repræsenterer internationale salg teams, der er tildelt til forskellige Salesforce-roller.

- For at aktivere undtagelse ordning skal kunne en selvbetjening gruppe oprettes for hver rolle. Gruppen "Salesforce marketing undtagelse" kan for eksempel oprettes som en selvbetjening gruppe. Gruppen kan være tildelt rollen Salesforce marketing og marketing ledelsen kan foretages ejere. Medlemmer af marketing ledelsen kan tilføje eller fjerne brugere, angive en joinforbindelse politik, eller endda godkende eller afvise individuelle brugere anmodninger om at deltage i. Dette understøttes via en oplysninger arbejder relevante oplevelse, ikke som kræver særlig uddannelse for ejere eller medlemmer.

I dette tilfælde skal vil alle tildelte brugere automatisk klargøres til Salesforce, når de føjes til forskellige grupper deres rolletildeling vil blive opdateret i Salesforce. Brugere vil ikke kunne finde og få adgang til Salesforce via panelet Microsoft program adgang, Office web-klienter, eller endda ved at navigere til deres organisatoriske Salesforce-logonside. Administratorer vil ikke kunne nemt få vist status for brugen og tildeling ved hjælp af Azure AD-rapportering.

Administratorer kan anvende [Azure AD betinget access](active-directory-conditional-access.md) til at angive access politikker for bestemte roller. Disse politikker kan medtage om adgang er tilladt uden for virksomhedens miljø og endda Multi-Factor Authentication eller enhed krav til at opnå adgang i forskellige tilfælde.

## <a name="how-can-i-get-started"></a>Hvordan kan jeg komme i gang?

Første, hvis du ikke allerede bruger Azure AD, og du er en IT-administrator:

 - [Prøv det!](https://azure.microsoft.com/trial/get-started-active-directory/) – kan du tilmelde dig en gratis 30-dages prøveversion i dag og installere din første skyen løsning under 5 minutter ved hjælp af dette link

Azure AD-funktioner, der aktiverer konto deling omfatter:

- [Gruppetildeling](active-directory-accessmanagement-self-service-group-management.md)
- Tilføje programmer til Azure AD
- Introduktion til tildeling
- Programmet tildeling ofte stillede spørgsmål
- [App dashboard/brugsrapporter](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Hvor kan jeg få mere at vide?

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Beskytte apps med betinget adgang](active-directory-conditional-access.md)
- [Selvbetjening gruppe administration/SSAA](active-directory-accessmanagement-self-service-group-management.md)
