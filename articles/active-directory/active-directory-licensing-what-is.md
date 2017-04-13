<properties
    pageTitle="Hvad er Microsoft Azure Active Directory licenser? | Microsoft Azure"
    description="Beskrivelse af Microsoft Azure Active Directory-licensering, hvordan det fungerer, hvordan du kommer i gang og bedste fremgangsmåder, herunder Office 365, Microsoft Intune og Azure Active Directory Premium og grundlæggende udgaver"
    services="active-directory"
      keywords="Azure AD-licensering"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>Hvad er Microsoft Azure Active Directory licenser?

##<a name="description"></a>Beskrivelse
Azure Active Directory (Azure AD) er Microsofts identitet som en tjeneste (IDaaS) løsning og platform. Azure AD tilbydes i en række funktionelle og tekniske versioner, lige fra Azure AD ledig, som er tilgængelige med en Microsoft-tjeneste som Office 365, Dynamics, Microsoft Intune og Azure (Azure AD ikke genererer en hvilken som helst forbrug gebyrer i denne tilstand), Azure AD har betalt versioner såsom Enterprise mobilitet pakke (EMS), Azure AD Premium og Basic samt Azure Multi-Factor Authentication (MFA). Mange af Microsoft online services, som leveres mest Azure AD betalt versioner gennem hver bruger rettigheder, som i Office 365, Microsoft Intune og Azure AD. I disse tilfælde servicekøb repræsenteres med en eller flere abonnementer, og hvert abonnement omfatter en foreløbig køb antallet af licenser i din lejer. Hver bruger rettigheder opnås via licensaftalen, oprette en kæde mellem brugeren og det produkt, aktivere tjenesten komponenterne for brugeren, og bruger en af de forudbetalte licenser.

[Prøv Azure AD premium nu.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Azure AD-administrationsportalen er en del af portalen Azure klassisk. Mens ved hjælp af Azure AD ikke kræver en hvilken som helst Azure køb, kræver adgang til denne portal et aktivt Azure abonnement eller [Azure prøveabonnement](https://azure.microsoft.com/pricing/free-trial/).

Se [Hvad er Azure AD](active-directory-whatis.md)til en bred oversigt over Azure AD-tjenesten funktioner.
[Lær mere om Azure AD-tjenesten niveauer](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Azure betal efterhånden abonnementer er forskellige: mens også repræsenteret i mappen, disse abonnementer, aktivere oprettelse af Azure ressourcer og knytte dem til din betalingsmetode. I dette tilfælde er der ingen licens tæller, der er knyttet til abonnementet. Brugeres tilknytning til abonnementet, brugernes adgang til administration af abonnementsressourcer, der opnås ved at give dem tilladelse til at betjene på Azure ressourcer, der er knyttet til abonnementet.


##<a name="how-does-azure-ad-licensing-work"></a>Hvordan fungerer Azure AD licenser?

Licens-baserede (ret-baseret) Azure AD tjenester arbejde ved at aktivere et abonnement i din Azure AD katalogtjenesten/lejer. Når abonnementet er aktivt kan tjenesten funktionerne administreres af katalogtjenesten/administratorer og bruges af licenserede brugere.

Når du køber eller aktiverer Enterprise mobilitet pakke, Azure AD Premium eller Azure AD grundlæggende, opdateres dit bibliotek til abonnementet, herunder gyldighedsperioden og forudbetalt licenser. Dine abonnementsoplysninger, herunder status, næste livscyklus hændelse og antallet af tildelte eller tilgængelige licenser er tilgængelig via Azure klassisk administrationsportalen under fanen licenser for den specifikke mappe. Dette er også til bedste sted til at administrere din licenstildelinger.

Hvert abonnement består af en eller flere tjenesteplanerne hver tilknytning inkluderet funktionelle niveauet for typen; for eksempel Azure AD Azure MFA, Microsoft Intune, Exchange Online eller SharePoint Online. Azure AD-licens management kræver ikke niveau styring af plan. Dette er forskellig fra Office 365, der er afhængig af denne Avanceret konfigurationstilstand til at administrere adgang til inkluderede tjenester. Azure AD er afhængig af i tjenestekonfiguration aktivere funktioner og administrere separate tilladelser.

Azure AD abonnementsoplysninger administreres generelt via Azure klassisk administrationsportalen, under fanen licenser for den specifikke mappe. Azure AD-abonnementer, med undtagelse af Azure AD Premium, vises ikke i Office-portalen.

> [AZURE.IMPORTANT] Azure AD Premium og Basic samt Enterprise mobilitet pakke abonnementer er begrænset til deres klargjorte mappe/lejer. Abonnementer kan ikke bliver opdelt mellem mapper eller bruges til at giver brugere i andre mapper. Flytte et abonnement mellem mapper er muligt, men det kræver, at sende en supportbilletter eller annullering og købe igen i forbindelse med direkte køb.

> Når du køber Azure AD eller Enterprise mobilitet pakke gennem Volume Licensing abonnement aktivering sker automatisk, når aftalen omfatter andre Microsoft Online services, f.eks. Office 365.

Betalt Azure AD funktioner strækker sig over bredden af mappen. Som eksempler kan nævnes:
- Gruppen-baserede tildelingen til programmer, som er aktiveret under det angivne program, du administrerer.
- Avanceret og selvbetjening gruppen funktioner er tilgængelige under konfigurationen af directory eller inden for den angivne gruppe.
- Premium sikkerhedsrapporter er på fanen rapportering
- Skyen programmet registrering vises i portalen Azure under identitet.

###<a name="assigning-licenses"></a>Tildeling af licenser
Bestille et abonnement er alt, du skal konfigurere betalt funktioner, kræver ved hjælp af din Azure AD, som er betalt funktioner, at distribuere licenser til de rette personer. Alle brugere, der skal have adgang til, eller der administreres via en Azure AD betalt funktion skal generelt skal have tildelt en licens. En licensaftalen er en tilknytning mellem en bruger og en købte tjeneste, såsom Azure AD Premium, Basic eller Enterprise mobilitet pakke.

Det er nemt at administrere, hvilke brugere i mappen skal have en licens. Det kan gøres ved at tildele en gruppe til at oprette regler for rolletildeling via Azure AD-administrationsportalen eller ved at tildele licenser direkte til de rette personer via en portal, PowerShell eller API'er. Når du tildeler licenser til en gruppe, skal alle gruppemedlemmer tildeles en licens. Hvis brugere er tilføjet eller fjernet fra gruppen tildeles eller fjernes den rigtige licens. Gruppetildeling kan bruge en hvilken som helst gruppeadministration, der er tilgængelige for dig og er i overensstemmelse med gruppe-baserede tildeling til programmer. Bruger denne metode, kan du konfigurere regler, så alle brugere i mappen tildeles automatisk, sikre, at alle med de relevante stilling har en licens eller endda uddelegere beslutningen til andre ledere i organisationen.

Alle brugere, der mangler en brugsplaceringen arver med gruppe-baserede licensaftalen mappeplaceringen under tildeling. Denne placering kan ændres af administratoren når som helst. I tilfælde, hvor automatiseret tildelingen mislykkedes på grund af fejl, vil brugeroplysninger under licenstypen afspejle tilstanden.

##<a name="getting-started-with-azure-ad-licensing"></a>Kom godt i gang med Azure AD-licensering

Introduktion til Azure AD er nemt; Du kan altid oprette mappen som en del af tilmelde dig en gratis Azure prøveabonnement. [Få mere at vide om tilmelding som en organisation](sign-up-organization.md). Følgende kan hjælpe dig med at kontrollere, at mappen bedst er justeret med andre Microsoft-tjenester, du kan være forbrug eller planlægger at bruge, og dine mål i at opnå tjenesten.

Her er nogle af bedste fremgangsmåder:
- Hvis du allerede bruger en af Microsofts organisatoriske tjenester, har du allerede en Azure AD-mappe. I dette tilfælde du skal fortsætte med at bruge den samme mappe til andre tjenester, så core identitet management, herunder klargøring og hybrid SSO, kan anvendes på tværs af tjenesterne. Dine brugere har en enkelt logonoplevelsen og vil få glæde af bedre muligheder på tværs af tjenesterne. Som et resultat, hvis du beslutter at købe en Azure AD, som er betalt-tjeneste til dine medarbejdere, anbefaler vi, at du bruger den samme mappe til at gøre dette.
- Hvis du planlægger at bruge Azure AD til et andet sæt brugere (partnere, kunder og osv.), eller hvis du gerne vil evaluere Azure AD-tjenester og gerne vil gøre dette i isolationsniveauet fremstilling-tjenesten, eller hvis du vil konfigurere et lukket miljø for dine tjenester, anbefaler vi, at oprette du først en ny mappe via portalen Azure Azure klassisk. [Yderligere oplysninger om oprettelse af en ny Azure AD-mappe på portalen Azure klassisk](active-directory-licensing-directory-independence.md). Den nye mappe oprettes med din konto som en ekstern bruger med tilladelser som global administrator. Når du logger på Azure klassisk portalen med denne konto, vil du kunne se denne mappe og få adgang til alle directory administrationsopgaver. Vi anbefaler, at du opretter en lokal konto med de rette tilladelser til at administrere andre Microsoft-tjenester (dem ikke tilgængelige via Azure klassisk administrationsportalen). [Få mere at vide om at oprette brugerkonti i Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD understøtter "eksterne brugere", som er brugerkonti i en forekomst af Azure AD, der er oprettet ved hjælp af en Microsoft-konto (MSA) eller en Azure AD-identitet fra en anden mappe. Selvom vi optaget understøttes forlænge denne egenskab til alle Microsofts organisatoriske services, lige nu disse konti ikke i nogle af de tjenester oplevelser; for eksempel understøtter Office 365-administrationsportalen aktuelt ikke disse brugere. Som et resultat kan eksterne brugere med Microsoft-konti ikke få adgang til Office 365-administrationsportalen overhovedet, mens eksterne brugere fra andre Azure AD-kataloger, blive ignoreret. I så fald kun lokale brugerkontoen, Azure AD eller Office 365-mappe, hvor brugeren oprindeligt blev oprettet, vil være tilgængeligt via disse oplevelser.

Som angivet, har Azure AD forskellige betalt versioner. Disse versioner har nogle mindre forskelle i deres køb tilgængelighed:


| Produkt   | EA/VL     | Åbn  |   CSP |   MPN brugsrettigheder  |   Direkte køb | Prøveversion |
|---|---|---|---|---|---|---|
| Enterprise mobilitet pakke |   X | X | X | X |  |      X |
| Azure AD-Premium  | X | X | X |   | X | X |
| Azure AD-Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Vælg en eller flere licens forsøg
 I alle tilfælde, kan du aktivere en Azure AD Premium- eller Enterprise mobilitet pakke prøveabonnement ved at vælge bestemte prøveversionen, du vil have på fanen licenser i mappen. Enten prøveversion indeholder et 30-dages abonnement med 100 licenser.

![Azure Active Directory prøvelicens planer](./media/active-directory-licensing-what-is/trial_plans.png)

![Enterprise mobilitet pakke prøvelicens planer](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Aktive prøvelicens planer](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Tildele licenser
Når abonnementet er aktivt, skal du tildele en licens til dig selv og Opdater browseren for at sikre, at du får vist alle funktioner. Næste trin er at tildele licenser til de brugere, der skal få adgang til eller medtages i betalt Azure AD-funktioner. Som nævnt ovenfor i "Tildele licenser", er den bedste måde at gøre dette til at identificere den gruppe, der repræsenterer den ønskede målgruppe og tildele den til licensen på denne måde, kan brugere, der er tilføjet eller fjernet fra gruppen over sin livscyklus tildelt eller fjernet fra licensen.

Hvis du vil tildele en licens for en gruppe eller individuelle brugere, Vælg licens plan, du vil tildele, og klik på **Tildel** på kommandolinjen.

![Aktive prøvelicens planer](./media/active-directory-licensing-what-is/assign_licenses.png)

Én gang i dialogboksen tildeling for den valgte plan, kan du vælge brugere og føje dem til kolonnen **Tildel** til højre. Du kan bladre gennem listen over brugere eller søge efter bestemte personer ved hjælp af den leder glas i øverste højre hjørne af gitteret bruger. Vælg "Grupper" i menuen **Vis** for at tildele grupper, og klik derefter på knappen Kontrollér til højre for at opdatere de opgaver, der vises.

![Tildeling af licenser til grupper](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Nu kan du søge eller bladre gennem grupper og føje dem til kolonnen **Tildel** på samme måde. Du kan bruge disse til at tildele en kombination af brugere og grupper i en enkelt handling. Klik på knappen Kontrollér i nederste højre hjørne på siden for at fuldføre tildelingsprocessen skal.

![Tildeling fremdrift licensmeddelelse](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Når der er tildelt en gruppe, nedarver medlemmerne licenserne inden for 30 minutter, men normalt inden for 1-2 minutter.

Tildeling fejl kan opstå under Azure AD-licensaftalen, men er relativt sjældne. Potentielle tildeling fejl er begrænset til:
- Tildeling konflikt - når en bruger der tidligere er tildelt en licens, som er ikke kompatibelt med den aktuelle licens. I dette tilfælde kræver tildele den nye licens, at fjerne den forrige opgave.
- Overskredne tilgængelige licenser - når antallet af brugere i tildelte grupper overstige tilgængelige licenser, afspejler brugernes tildeling status en mislykket tildele på grund af manglende licenser.

###<a name="view-assigned-licenses"></a>Se tildelt licenser

En oversigtsvisning af tildelte licenser, herunder tilgængelige, tildelte og næste abonnement livscyklus begivenhed vises under fanen **licenser** .

![Vis antallet af tildelte licenser](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

En detaljeret liste over tildelte brugere og grupper, herunder tildeling status og sti (direkte eller nedarvet fra en eller flere grupper) er tilgængelig, når du navigerer til en licens plan.

![Detaljeret visning af licenser, der er tildelt til en licens plan](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Fjerne licenser er lige så nemt som at tildele dem. Hvis brugeren tildeles direkte, eller du kan fjerne licensen for en tildelt gruppe ved at vælge licenstypen, vælge **fjerne**, tilføjelse af brugeren eller gruppen til Fjern og bekræfte handlingen. Du kan også åbne en licenstype, vælge bestemt bruger eller gruppe og tryk på **Fjern** på kommandolinjen. For at afslutte en brugers nedarvning af en licens fra en gruppe, skal du blot fjerne brugeren fra gruppen.

###<a name="extending-trials"></a>Hvis du vil udvide forsøg

Prøveversion udvidelser for kunder, der er tilgængelige som selvbetjening via Office 365-portalen. En kunde-administrator kan gå til [Office-portal](https://portal.office.com/#Billing) (adgangen afhænger af, om tilladelser for Office-portal) og vælge din prøveversion af Azure AD Premium. Klik på linket **Forlæng prøveversion** , og følg vejledningen. Du skal angive et kreditkort, men den kan ikke betale.

![Hvis du vil udvide en licens prøveversion i Office-portalen](./media/active-directory-licensing-what-is/extend_license_trial.png)

Kunder kan også du anmode om en forlængelse af prøveperioden ved at sende en supportanmodning. En kunde-administrator kan navigere til den Office 365 portal [understøtter side](http://aka.ms/extendAADtrial) (adgangen afhænger af, om tilladelser for siden Office support). Vælg "Abonnementer og forsøg" under funktioner på denne side og "Prøveversion spørgsmål" under Symptom. Til sidst skal angive oplysninger om omstændighederne

![Hvis du vil udvide en licens-prøveversion ved hjælp af en anmodning om support](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Næste trin

Du kan nu være klar til at konfigurere og bruge nogle Azure AD Premium-funktioner.

- [Nulstilling af adgangskode selvbetjening](active-directory-manage-passwords.md)
- [Selvbetjening gruppeadministration](active-directory-accessmanagement-self-service-group-management.md)
- [Azure AD-forbindelse områder](active-directory-aadconnect-health.md)
- [Gruppetildeling til programmer](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Direkte køb af Azure AD Premium licenser](http://aka.ms/buyaadp)
