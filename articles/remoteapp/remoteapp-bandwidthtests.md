<properties 
    pageTitle="Azure RemoteApp - test dit netværk båndbredden med nogle almindelige scenarier | Microsoft Azure"
    description="Få mere at vide, hvad med almindelige brugsscenarier, der kan hjælpe dig med at finde ud af dine netværksbåndbredde behov for Azure RemoteApp."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - test dit netværk båndbredden med nogle almindelige scenarier

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Som beskrevet i [vurdering Azure RemoteApp netværk båndbredden](remoteapp-bandwidth.md), tester den bedste måde at finde ud af, hvad effekten af Azure RemoteApp på dit netværk er at køre nogle brugen. Kør disse test for en angivet periode og måle den båndbredde, der er behov for hvert scenario. Hvis du har mulighed for, kan du også måle den pakke tab og netværk netværksrysten for at forstå de netværk mønstre, der oprettes i dit miljø.

    
At evaluere båndbredden, Husk, at brugen varierer mellem forskellige brugere i virksomheden. For eksempel forbruge tekst læsere og forfattere normalt mindre båndbredde end brugere, der arbejder med video. De bedste resultater ved læse din egen brugernes behov og oprette en blanding af de følgende scenarier, der bedst repræsenterer brugere i virksomheden. Husk at [Gennemgå de faktorer, der oplevelsen virkning båndbredden og bruger](remoteapp-bandwidthexperience.md) -, der kan hjælpe dig med at identificere de ideelle test.

Første Læs om testene, Vælg din blanding, og derefter køre dem. Du kan bruge tabellen nedenfor til at registrere ydeevne.

>[AZURE.NOTE] Hvis du ikke kan gøre din egen Netværkstest af, eller du ikke har tid til at gøre det, kan du se vores [standardnetværk båndbredde beregner/anbefalinger](remoteapp-bandwidthguidelines.md). Din kørte kilometer variere, så hvis du *kan* køre din egen test, skal du.


## <a name="the-usage-tests"></a>Testene, der brugen
Hver af disse prøver kører for forskellige mængde tid og teste forskellige funktioner/funktioner, der forbruger netværksbåndbredde. Husk at vælge blanding af test, som passer bedst til dine individuelle virksomhed brugere.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>Administrerende/komplekst PowerPoint – Kør i 900-1000 sekunder

En bruger præsenterer mellem 45 50 høj pålidelighed slides ved hjælp af Microsoft Office PowerPoint i fuldskærmstilstand. Slides skal indeholde billeder, overgange (med animationer) og baggrunde med farve graduering, der er typisk for din virksomhed. Brugeren skal bruge mindst 20 sekunder på de enkelte dias.
    
Dette scenarie opretter mange burst af trafik, når en slide overgange til det næste dias i præsentationen.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>Enkel PowerPoint – Kør i ~ 620 sekunder

En bruger præsenteres en enkel PowerPoint-fil med cirka 30 dias ved hjælp af Microsoft Office PowerPoint i fuldskærmstilstand. Slides er flere tekst-intensivt end i scenariet administrerende/komplekst PowerPoint og har enklere baggrunde og billeder (sort diagrammer). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - køre i ~ 250 sekunder

En bruger gennemser internettet ved hjælp af Internet Explorer. Brugeren gennemser og ruller gennem en blanding af tekst, neutralt billeder og nogle skematiske diagrammer. De websider, der er gemt på det lokale diskdrev på serveren, Remote Desktop sessionsværten (RD sessionsværten) som en. MHT-fil. Brugeren ruller ved hjælp af Page Up, Page Down, op og ned taster med skiftende intervaller for hver nøgle/Rul:
    
    - Ned - 250 tastetryk meget 500 ms
    - Page Up - 36 tastetryk hver 1000 ms
    - Ned - 75 tastetryk hver 100 ms
    - Page Down - 20 tastetryk hver 500 ms
    - Op - 120 tastetryk hver 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>PDF-dokument - simpelt – Kør i ~ 610 sekunder
En bruger læser og søger et PDF-dokument på forskellige måder ved hjælp af Adobe Acrobat Reader. Dokumentet skal bestå af tabeller, enkle grafer og flere skrifttyper. Dokumentet er 35-40 sider lang. Brugeren ruller gennem på to forskellige satser, bagudkompatibilitet og videresender i fire forskellige zoom størrelser (Tilpas til siden, Tilpas til bredde, 100%, og den anden efter eget valg). Zoome sikrer, at teksten (skrifttype) gengiver i forskellige størrelser. Rulle er nede ved hjælp af Page Up, Page Down, op og ned taster med skiftende intervaller for hver rullepanel.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>PDF dokument - blandede - Kør i ~ 320 sekunder
En bruger læser og søger et PDF-dokument på forskellige måder ved hjælp af Adobe Acrobat Reader. Dokumentet består af høj kvalitet billeder (herunder fotografier), tabeller, enkle grafer og flere skrifttyper. Brugeren ruller gennem på to forskellige satser, bagudkompatibilitet og videresender i fire forskellige zoom størrelser (Tilpas til siden, Tilpas til bredde, 100%, og den anden efter eget valg). Zoome sikrer, at teksten (skrifttype) gengiver i forskellige størrelser. Rulle er nede ved hjælp af Page Up, Page Down, op og ned taster med skiftende intervaller for hver rullepanel.

### <a name="flash-video-playback---run-for-180-seconds"></a>Flash videoafspilning - køre i ~ 180 sekunder
En bruger får en Adobe Flash-kodet video integreret i en webside. Websiden er gemt i den lokale harddisk på serveren, RD sessionsværten. Videoen skal afspilles i Internet Explorer ved en integreret afspiller plug-in'en.

Dette scenarie emulerer brugere, der viser omfattende indhold websider, der indeholder multimedier. De fleste af dataene, der skal bo gennem VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Remote at skrive i Word - køre i ~ 1800 sekunder
En bruger skriver et dokument via en RDP-session. Tastetryk der sendes fra klientsiden via RDP-session i et dokument i Microsoft Word kører i den fjernsession. At skrive rente er ét tegn hver 250 ms (samlede 7050 tegn). 

Dette er en af de mest almindelige scenarier for it-medarbejdere. Dette scenarie tester en bruger skriver til en moderne arbejde processor svartid. Dette scenario er følsomme lige mindre ændringer i brugen af båndbredde.

## <a name="tracking-the-test-results"></a>Sporing af resultaterne

Du kan bruge tabellen nedenfor til at vurdere scenarier i dit miljø. De data, der er angivet herunder er lige til illustration – det kan være hvilket adskiller sig fra hvad du ser. 

For enkel antager vi, at alle scenarier er testet ved brug af den samme skærmopløsning 1920 x 1080 pixel og TCP-transporter på et netværk med ventetid (forsinkelse) under 200 ms og netværk jitter mærket 120 ms + om 1%.

Om tabellen:
- **Gennemsnittet opleve** indeholder netværksbåndbredde, hvor brugernes produktivitet påvirkes ikke betydeligt, men ikke udelade lejlighedsvise video eller lyd fejl. Systemet kan gendanne hurtigt ved at drage fordel af dynamiske logik. Netværksbåndbredde beregner forsøget på at garantere kvaliteten af brugeroplevelsen.
 - **Noticeable problemer (sideskift punkt)** indeholder den netværksbåndbredde, hvor brugere oplever måske betydeligt problemer i brugernes oplevelse, og deres produktiviteten påvirkes for nøjagtighed tidsperioder. På dette tidspunkt RDP algoritmer er kæmper og kan ikke garantere brugerens kvaliteten af oplevelsen på grund af utilstrækkelig netværksbåndbredde.
 - **Anbefalede** indeholder den netværksbåndbredde anbefales til god eller fremragende oplevelse. Det er normalt ét trin, der er højere end værdien i kolonnen tilsvarende **gennemsnittet oplevelse** .
 - **Bemærkninger** indeholder bemærkninger og kommentarer.
 
| Test                  | Gennemsnitlig oplevelse | Chatanmodning problemer (sideskift punkt) | Anbefalede netværksbåndbredde | Noter                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| Administrerende/komplekst PPT | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s foretrukne    | På 1 MB/s går mange animationer tabt                                   |
| Enkel PPT            | 5 MB/s              | 256 KB/s                         | 10 MB/s                        | Med en chatanmodning forsinkelse indlæses slides på 256 KB/s                   |
| Internet Explorer     | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s foretrukne    | Web-videoer er Uskarp på 1 MB/s og usammenhængende, hurtigt rulle har problemer |
| Enkelt PDF-fil            | 1 MB/s              | 256 KB/s                         | 5 MB/s                         | På 256 KB/s tager det lang tid at indlæse siden                       |
| Blandede PDF-fil             | 1 MB/s             | 256 KB/s                         | 5 MB/s                         | På 256 KB/s tager siden en stor mængde tid at indlæse    |
| Afspilning af video i Flash  | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s foretrukne    | På 1 MB/s videoen er kornet og nogle rammer udelades           |
| Remote at skrive i Word    | 256 KB/s            | 128 KB/s                         | 1 MB/s                         | På 256 KB/s kan bruger være tiden mellem tastetryk             |

For at evaluere netværksbåndbredden per bruger, skal du oprette en blanding af ovennævnte situationer og den tilsvarende del af nødvendige netværksbåndbredde. Vælg det højeste tal, der er behov for din scenarier. Da brugerne bruge stort set aldrig alene systemet, bør du overveje nogle reserver for brugere, som arbejde samtidigt på det samme netværk.
     
## <a name="learn-more"></a>Lær mere
- [Anslå Azure RemoteApp netværk båndbredden](remoteapp-bandwidth.md)

- [Azure RemoteApp - hvordan netværksbåndbredde og kvaliteten af oplever arbejde sammen?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp netværksbåndbredde - generelle retningslinjer (Hvis du ikke kan teste din egen)](remoteapp-bandwidthguidelines.md)