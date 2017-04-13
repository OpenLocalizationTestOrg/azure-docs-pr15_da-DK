<properties
   pageTitle="Få indsigt i dine Microsoft Azure Ressourceforbrug | Microsoft Azure"
   description="Indeholder en oversigt over Azure fakturering forbrug og RateCard APIs, som bruges til at give indsigt i Azure Ressourceforbrug og tendenser."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Få indsigt i dine Microsoft Azure Ressourceforbrug

Kræver muligheden for at forudsige og administrere deres Azure omkostninger præcist, kunder og partnere.  Når de flyttes fra en Capex for indkøb til en Opex model, skal de også muligheden for at gøre showback kontra kortejeren analyse samt angive tilstand lydkvalitet i skøn og fakturering, især til store skyen installationer.

Azure Ressourceforbrug og rente kort API'er beskrevet i denne artikel adresse disse behov ved at aktivere ny indsigt i dit forbrug af Azure ressourcer.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Introduktion til Azure Ressourceforbrug og RateCard API'er

Azure Ressourceforbrug og RateCard APIs implementeres som en ressource-udbyder, som en del af familien af API'er vises ved Azure ressourcestyring.  

### <a name="azure-resource-usage-api-preview"></a>Azure Ressourceforbrug API (Preview)
Kunder og partnere kan bruge Azure ressource brugen API til at hente deres anslåede Azure forbrugsdata. Funktionerne omfatter:

- **Azure rollebaseret adgangskontrol** - kunder og partnere kan konfigurere deres access politikker på [Azure portal](https://portal.azure.com) eller via [Azure PowerShell-cmdlet'er](powershell-install-configure.md) til at angive, hvilke brugere eller programmer kan få adgang til data om brug af det abonnement. Ringer skal bruge standard Azure Active Directory-tokens til godkendelse. Hvem skal også føjes til den læser, en ejer eller en bidragyder rolle for at få adgang til Brugsdata for et bestemt Azure abonnement.

- **Hver time eller daglige sammenlægninger** - ringer kan angive, om de vil deres Azure Brugsdata i hver time filsæt eller daglige filsæt. Standard er daglige.

- **Forekomst metadata, der er angivet (inkluderer ressource mærker)** – forekomst niveau oplysninger som fuldt kvalificeret ressource uri (/subscriptions/ {abonnement-id} / …), sammen med ressourcen, der vil blive leveret gruppe oplysninger og ressource mærker i svaret. Dette vil hjælpe kunder deterministically og fra et program fordele brugen af mærkerne, til brug tilfælde som tværs opladning.

- **Ressource-metadata, der er angivet** - ressourcedetaljer som dokumentmåler navn, kategori dokumentmåler, dokumentmåler underkategori, enhed og område vil også blive overført i svaret, at give ringer en bedre forståelse af hvad der blev brugt. Vi arbejder også for at justere ressource metadata terminologi på tværs af portalen Azure Azure brugen CSV, EA fakturering csv- og andre offentligt oplevelser for at aktivere kunder til at koordinere data på tværs af oplevelser.

- **Brugen for alle typer af tilbud** – Brugsdata vil være tilgængelige for alle tilbyder former, herunder Pay-as-you-go, MSDN, pengeværdier anvendelsen, pengeværdier kredit og EA blandt andet.

### <a name="azure-resource-ratecard-api-preview"></a>Azure ressource RateCard API (Preview)
Kunder og partnere kan bruge Azure ressource RateCard API til at hente en liste over tilgængelige Azure ressourcer, sammen med anslået prisoplysninger for hver. Funktionerne omfatter:

- **Azure rollebaseret adgangskontrol** - kunder og partnere kan konfigurere deres access politikker på [Azure portal](https://portal.azure.com) eller via [Azure PowerShell-cmdlet'er](powershell-install-configure.md) til at angive, hvilke brugere eller programmer kan få adgang til RateCard dataene. Ringer skal bruge standard Azure Active Directory-tokens til godkendelse. Hvem skal også føjes til den læser, en ejer eller en bidragyder rolle for at få adgang til Brugsdata for et bestemt Azure abonnement.

- **Understøttelse af Pay-as-you-go, MSDN, pengeværdier anvendelsen og valuta kredit tilbyder (EA ikke understøttes)** - denne API giver oplysninger om Azure tilbud niveau rente, kontra abonnement niveau.  Kalderen af denne API skal overføre tilbud oplysningerne for at få detaljerede oplysninger om ressourcen og satser.  Som EA tilbud har tilpasset satser efter tilmelding, er vi kan ikke angive EA satser på nuværende tidspunkt.

## <a name="scenarios"></a>Scenarier

Her er nogle af de scenarier, der er foretaget mulige med en kombination af RateCard APIs og brug:

- **Azure bruger løbet af måneden** - kunder kan bruge brugen og RateCard APIs sammen til at få bedre indsigt i deres skyen bruger løbet af måneden, ved at analysere hver time og daglige grupperne af brugen og gebyr beregner.

- **Konfigurere beskeder** – kunder og partnere kan konfigurere ressource- eller valuta-baserede beskeder på deres skyen forbrug ved at få de anslåede forbrug og gebyr vurdering ved hjælp af brug og API'EN RateCard.

- **Predict faktura** – kunder og partnere kan få deres anslåede forbrug og skyen bruger og anvende machine learning algoritmer for at forudsige, hvad deres faktura ville være i slutningen af faktureringscyklussen for den.

- **Foreløbig forbrug omkostningsanalyse** -kunder kan også bruge RateCard API til at forudsige, hvor meget deres faktura ville være, hvis det var flytte deres arbejdsbelastninger til Azure, by, forudsat at beskedteksten brugen tal. Hvis kunder har eksisterende arbejdsmængder i andre skyer eller privat skyer, kan de også knytte deres brugen med Azure satser for at få en bedre vurdering af deres anslåede Azure bruger. Dette giver en forbedret visning af hvad kan fås via [Azure priser Lommeregner](https://azure.microsoft.com/pricing/calculator/), som (for eksempel) vores fakturering partnere giver mulighed for at pivotere på tilbud og sammenligne/kontrast mellem forskellige tilbud typer ud over Pay-As-You-Go, herunder valuta anvendelsen og valuta kreditkontrol. API'erne giver også muligheden for at omkostninger skøn ændringer efter område, så typer what if-analyse, der kræves for at gøre installation beslutninger, som implementering ressourcer i forskellige DCs over hele verden kan have en direkte indvirkning på samlede omkostninger.

- **What if-analyse** -

    - Kunder og partnere kan bestemme, om det ville være mere økonomisk at køre deres arbejdsbelastninger, i et andet område eller på en anden konfiguration af Azure ressourcen. Azure ressource kan variere omkostninger baseret på den Azure område, hvor de kører, og dette giver mulighed for kunder og partnere kan få omkostninger optimeringer.

    - Kunder og partnere kan også angive, hvis en anden type Azure tilbud giver en bedre sats på en Azure ressource.

## <a name="partner-solutions"></a>Partnerløsninger

[Microsoft Azure brugen og RateCard API'er aktivere Cloudyn til at give ITFM for kunder, der](billing-usage-rate-card-partner-solution-cloudyn.md) beskriver den integration oplevelse, tilbydes af Azure fakturering API partner [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  I denne artikel indeholder detaljerede beskrivelser af deres oplevelser, herunder en kort video, som viser, hvordan en Azure kunde kan bruge Cloudyn og Azure fakturering API'er gevinst Indsigter fra deres Azure forbrugsdata.

[Skyen Cruiser og Microsoft Azure fakturering API Integration](billing-usage-rate-card-partner-solution-cloudcruiser.md) beskrives, hvordan [skyen Cruiser Express til Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) fungerer direkte fra portalen WAP aktivere kunder til at administrere problemfrit begge funktionsdygtige og finansielle aspekter af deres Microsoft Azure privat eller hostet offentlige skyen fra en enkelt brugergrænseflade.   

## <a name="next-steps"></a>Næste trin
+ Se [Azure fakturering RESTEN API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) til flere oplysninger om både API'er, som er en del af sæt af API'er, der er angivet af Azure ressourcestyring.
+ Hvis du vil have at kaste dig ud direkte i eksempelkoden, se vores Microsoft Azure fakturering API kodeeksempler på [Azure kodeeksempler](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Lær mere
+ Artiklen [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md) for at lære mere om Azure ressourcestyring.
+ Yderligere oplysninger om pakken med værktøjer, der er nødvendige for at hjælpe med at få indsigt i skyen bruger, skal du henvise til Gartner artikel [Marked vejledning til IT finansielle Management (ITFM) værktøjer](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
