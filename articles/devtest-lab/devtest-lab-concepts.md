<properties
    pageTitle="DevTest øvelser begreber | Microsoft Azure"
    description="Lær de grundlæggende begreber i DevTest øvelser, og hvordan det kan gøre det nemmere at oprette, administrere og overvåge Azure virtuelle maskiner"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

#<a name="devtest-labs-concepts"></a>DevTest øvelser begreber

> [AZURE.NOTE]
> I denne artikel er en del 3 af en serie på 3 dele:
> 
> 1. [Hvad er DevTest øvelser?](devtest-lab-overview.md)
> 1. [Hvorfor DevTest øvelser?](devtest-lab-why.md)
> 1. **[DevTest øvelser begreber](devtest-lab-concepts.md)**

##<a name="overview"></a>Oversigt

Følgende liste indeholder vigtige DevTest øvelser begreber og definitioner:

##<a name="artifacts"></a>Elementer
Elementer, der bruges til at installere og konfigurere dit program, når en VM er klargjort. Elementer kan være:

- Værktøjer, du vil installere på VM - som supportmedarbejdere, Fiddler og Visual Studio.
- Handlinger, du vil køre på VM - såsom klone en repo.
- Programmer, du vil teste.

Elementer er [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) JSON filer, der indeholder instruktioner til at udføre installation og anvende konfiguration. 

##<a name="artifact-repositories"></a>Typer lagre genstand
Genstand typer lagre er ciffer typer lagre hvor elementer er tjekket ind. Samme typer lagre genstand kan tilføjes til flere øvelser i din organisation aktivere genbrug og deling.

## <a name="base-images"></a>Grundlæggende billeder
Grundlæggende billeder er VM billeder med alle de funktioner og indstillinger forudinstalleret og konfigureret til hurtigt at oprette en VM. Du kan klargøre en VM ved at vælge en eksisterende base og tilføje en genstand for at installere deres test agent. Du kan derefter gemme den klargjorte VM som udgangspunkt, så grundtallet kan bruges uden at geninstallere test agent for hver klargøring af VM.

##<a name="formulas"></a>Formler 
Formler, ud over grundlæggende billeder med en funktion til hurtig klargøring af VM. En formel i DevTest øvelser er en liste over standardværdier egenskaben bruges til at oprette en øvelse VM. Kan oprettes med formler, FOS med det samme sæt af egenskaber – som grundlæggende afbildning, VM størrelse, virtuelt netværk og elementer – uden at skulle angive disse egenskaber, hver gang. Når du opretter en VM fra en formel, standardværdierne kan bruges som-er eller blevet ændret.

##<a name="caps"></a>Caps
Caps er en funktion for at minimere affald i din øvelse. For eksempel kan du angive en knop til at begrænse antallet VM'er, der kan oprettes per bruger eller i en øvelse.

##<a name="policies"></a>Politikker
Politikker hjælpe med at styre omkostninger i din øvelse. For eksempel kan du oprette en politik til lukkes automatisk FOS baseret på en defineret tidsplan.

##<a name="security-levels"></a>Sikkerhedsniveauer
Sikkerhed access bestemmes af Azure Role-Based Access kontrolelement (RBAC). For at forstå, hvordan access virker hjælper med at forstå forskellene mellem et tilladelsesniveau, en rolle og et omfang, som defineret af RBAC. 

- Tilladelsen - et tilladelsesniveau er en defineret adgang til en bestemt handling (fx læse-adgang til alle virtuelle maskiner). 
- Rolle - en rolle er et sæt af tilladelser, der kan grupperes og tildelt til en bruger. For eksempel har rollen *abonnement ejeren* adgang til alle ressourcer inden for et abonnement. 
- Område - et område er et niveau i hierarkiet for en Azure ressource - som en ressourcegruppe, en enkelt øvelse eller hele abonnementet).
 
Der findes to typer roller for at definere brugertilladelser er omfattet af DevTest øvelser: øvelse ejer og øvelse bruger.

- Øvelse ejer - en øvelse ejer har adgang til alle ressourcer inden for øvelse. Derfor kan en øvelse ejer ændre politikker, læse og skrive en hvilken som helst FOS, ændre det virtuelle netværk og så videre. 
- Øvelse bruger - i en øvelse bruger kan få vist alle øvelse de ressourcer, som FOS, politikker og virtuelle netværk, men kan ikke ændre politikker eller en hvilken som helst FOS oprettet af andre brugere. 


For at se, hvordan du kan oprette brugerdefinerede roller i DevTest øvelser skal referere til artiklen, [give brugertilladelser til bestemte øvelse politikker](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Da områder hierarkiske, når en bruger har tilladelser på et bestemt område, er de automatisk tildelt disse tilladelser på hver underniveau omfang encompassed. Eksempelvis hvis en bruger er tildelt rollen som abonnement ejer, har derefter de adgang til alle ressourcer i et abonnement, som omfatter alle virtuelle maskiner, alle virtuelle netværk, og alle øvelser. Derfor arver ejer af et abonnement automatisk øvelse ejer rolle. Modsat er dog ikke sand. En øvelse ejer har adgang til en øvelse, som er et mindre område end niveauet abonnement. Derfor kan en øvelse ejer ikke se virtuelle maskiner eller virtuelle netværk eller en hvilken som helst ressourcer, der er uden for øvelse.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

##<a name="next-steps"></a>Næste trin

[Oprette en øvelse i DevTest øvelser](devtest-lab-create-lab.md)