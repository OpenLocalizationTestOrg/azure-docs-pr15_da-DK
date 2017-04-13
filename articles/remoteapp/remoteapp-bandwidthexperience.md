<properties 
    pageTitle="Azure RemoteApp - hvordan netværksbåndbredde og kvaliteten af oplever arbejde sammen? | Microsoft Azure"
    description="Få mere at vide, hvordan netværksbåndbredde i Azure RemoteApp kan påvirke din bruger kvaliteten af oplevelsen."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp - hvordan netværksbåndbredde og kvaliteten af oplever arbejde sammen?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Når du kigger på den [overordnede netværksbåndbredde](remoteapp-bandwidth.md) , der kræves til Azure RemoteApp, skal du huske følgende faktorer – dette er en del af et dynamisk system, der påvirker den overordnede brugeroplevelse. 

- **Tilgængelige netværksbåndbredde og aktuelle netværk betingelser** - et sæt af parametre (tab, ventetid, jitter) på det samme netværk på et bestemt tidspunkt kan påvirke programmet streaming oplevelse, hvilket betyder, at en sænket overordnede brugeroplevelse. Den båndbredde, der er tilgængelige i dit netværk er en funktion af overbelastning, tilfældige tab, ventetid, fordi alle disse parametre påvirker overbelastning kontrolelement ordning, som i slår kontrolelementer overførselshastighed for at undgå kollisioner.  For eksempel vil en uden tab netværk eller et netværk med stor forsinkelse gøre for brugeren forkert endda på et netværk med 1000 MB båndbredde. Tab og ventetid varierer afhængigt af antallet af brugere, der er på samme netværket, og hvad disse brugere foretager sig (for eksempel, videoer, downloade eller uploade store filer, udskrivning).
- **Brugen scenarie** - oplevelsen, afhænger af, hvad brugerne laver som enkeltpersoner og som en gruppe på det samme netværk. For eksempel kræver læse ét dias kun en enkelt ramme skal opdateres. Hvis brugeren skims og ruller over indholdet af et dokument, skal de et stort antal rammer skal opdateres sekundet. Kommunikation frem og tilbage til serveren i dette scenarie vil til sidst forbruge mere netværksbåndbredde. Også overveje et yderste eksempel: flere brugere med HD-videoer (som 4K opløsning), holder HD telefonmøder, 3D-video spil eller arbejde på CAD-systemer. Alle disse kan gøre endnu et virkelig stor båndbredde netværk næsten ubrugelig.
- **Skærmopløsning og antallet af skærmbilleder** - mere netværksbåndbredde er påkrævet til fuld opdatering større skærmbilleder end mindre skærme. Den underliggende teknologi er en god til kodningen og overføre kun områderne af de skærmbilleder, der er opdateret, men en gang imellem, hele skærmbilledet skal opdateres. Når brugeren har en højere opløsning skærm (for eksempel 4K opløsning), kræver opdateringen mere netværksbåndbredde end en skærm med lavere opløsning (som 1024x768px). Denne samme logik gælder, hvis du bruger mere end ét skærmbillede til omdirigering. Båndbredde skal forøges med antallet af skærme.
- **Omdirigering af Udklipsholder og enhed** – dette er et problem, der ikke meget indlysende, men i mange tilfælde Hvis en bruger gemmer en stor bunke data til Udklipsholder, det tager lidt tid for disse oplysninger til at overføre fra klienten Fjernskrivebord til serveren. Den efterfølgende oplevelse kan påvirkes af oplevelsen på afsendelse Udklipsholder indholdet af strømmen. Det samme gælder for enhedsomdirigering -, hvis en scanner eller et webcam giver en stor mængde data, der skal sendes oven på serveren, eller en printer skal modtage et stort dokument, eller lokalt lager skal være tilgængelige for en app, der kører i skyen for at kopiere en stor fil, brugere oplever måske billeder udelades eller midlertidigt "frosset" video fordi de data, der kræves for enhedsomdirigering forøges netværksbåndbredden skal. 

Når du evaluerer dine netværksbåndbredde behov, skal du kontrollere skal du tænke på alle disse faktorer, der fungerer som et system.

Nu, gå tilbage til den [primære netværksbåndbredde artikel](remoteapp-bandwidth.md), eller gå videre til test af [netværksbåndbredde](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Lær mere
- [Anslå Azure RemoteApp netværk båndbredden](remoteapp-bandwidth.md)

- [Azure RemoteApp - test dit netværk båndbredden med nogle almindelige scenarier](remoteapp-bandwidthtests.md)

- [Azure RemoteApp netværksbåndbredde - generelle retningslinjer (Hvis du ikke kan teste din egen)](remoteapp-bandwidthguidelines.md)