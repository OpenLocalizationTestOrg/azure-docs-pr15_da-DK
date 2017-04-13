<properties
    pageTitle="Infrastruktur navngive retningslinjer | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for navngivning af i Azure infrastrukturtjenester."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Retningslinjer for navngivning infrastruktur

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på forstå, hvordan du kan løse navngivningskonventioner for alle dine forskellige Azure ressourcer til at opbygge en logisk og let kan identificeres samling af ressourcer på tværs af dit miljø.

## <a name="implementation-guidelines-for-naming-conventions"></a>Implementering retningslinjer for navngivning

Beslutninger:

- Hvad er din navngivningskonventioner for Azure ressourcer?

Opgaver:

- Definere foranstillede til brug på tværs af dine ressourcer til at vedligeholde konsistens.
- Definere lagerplads kontonavne givet kravet om dem til at være globalt entydige.
- Dokument Navnekonventionen for at blive brugt og distribuere til alle parter at sikre konsistens på tværs af installationer.

## <a name="naming-conventions"></a>Navngivningskonventioner

Før du opretter noget i Azure, bør du have en god navngivningsregel på plads. En navngivningsregel sikrer, at alle ressourcerne, der har et mere forudsigelige navn, hvilket medvirker til at reducere det administrative arbejde, der er knyttet til administration af disse ressourcer.

Du kan vælge at følge bestemte navngivningskonventioner, der er defineret for hele organisationen eller til en bestemt Azure abonnement eller en konto. Selvom det er nemt for personer i organisationer kan oprette implicitte regler, når du arbejder med Azure ressourcer, skal du kunne skalere til teams arbejde sammen i Azure.

Blive enige om et sæt af navngivningskonventioner på forhånd. Der er nogle overvejelser vedrørende navngivningskonventioner, klippe på tværs af, der angiver af regler.

## <a name="affixes"></a>Foranstillede

Når du ser for at definere en navngivningsregel, er én beslutning om affiks, der er på:

- Starten af navnet (præfiks)
- Slutningen af navnet (suffiks)

For eksempel her er to mulige navne for en ressourcegruppe ved hjælp af den `rg` anbringer:

- Indbyggede RG-WebApp (præfiks)
- WebApp-indbyggede Rg (suffiks)

Foranstillede kan referere til forskellige aspekter, der beskriver de specifikke ressourcer. I følgende tabel vises nogle eksempler, der typisk bruges.

| Højde                               | Eksempler                                                               | Noter                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Miljø                          | Udviklingscenter, stg, prod                                                         | Afhængigt af formålet med og navnet på de enkelte miljøer.                                                     |
| Placering                             | usw (vest USA), brug (af USA 2)                                         | Afhængigt af området af datacenteret eller området af organisationen.                               |
| Azure-komponent, tjenester eller produkter | Indbyggede RG til ressourcegruppe, VNet for virtuelt netværk                        | Afhængigt af det produkt, hvor ressourcen understøtter.                                          |
| Rolle                                 | DB, web app                                                           | Afhængigt af den virtuelle maskine rolle.                                                              |
| Forekomst                             | 01, 02, 03, osv.                                                       | For de ressourcer, der har mere end én forekomst. For eksempel indlæses ikke-opgjorte-servere i en skybaseret tjeneste. |


Når du opretter din navngivningskonventioner, Sørg for, at de klart hvilke foranstillede skal bruges for hver type af ressource og i hvilken position (præfiks eller-suffiks).

## <a name="dates"></a>Datoer

Ofte er det vigtigt at finde ud af datoen for oprettelse af navnet på en ressource. Vi anbefaler ÅÅÅÅMMDD datoformat. Dette format sikrer, at det ikke kun er fuldt registreres dato, men også, at to ressourcer, hvis navne, der er forskellige kun på datoen, der er sorteret alfabetisk og kronologisk.

## <a name="naming-resources"></a>Navngive ressourcer

Definere hver type af ressource i navnekonventionen, som skal have regler, der definerer, hvordan du kan tildele navne til hver ressource, der er oprettet. Disse regler skal gælde for alle de typer af ressourcer, f.eks.:

- Abonnementer
- Konti
- Lagerplads konti
- Virtuelt netværk
- Undernet
- Tilgængelighed sæt
- Ressourcegrupper
- Virtuelle maskiner
- Slutpunkter
- Netværk sikkerhedsgrupper
- Roller

For at sikre, at navnet indeholder nok oplysninger for at finde ud af, hvilken ressource, det henviser, skal du bruge beskrivende navne.

## <a name="computer-names"></a>Computernavne

Når du opretter en virtuel maskine (VM), kræver Azure et VM navn på op til 64 tegn, der bruges til ressourcenavnet. Azure bruger det samme navn for det operativsystem, der er installeret i VM. Men disse navne muligvis ikke den samme altid.

Hvis en VM er oprettet ud fra en .vhd billedfil, som allerede indeholder et operativsystem, kan VM navnet i Azure forskellig fra den VM operativsystem computernavn. Denne situation kan føje en grad af problemer med at til VM management, som vi anbefaler derfor. Tildele Azure VM ressourcen det samme navn som navnet på den computer, som du tildeler til operativsystem, VM.

Vi anbefaler, at Azure VM navnet er den samme som navnet på den underliggende operativsystem computer.

## <a name="storage-account-names"></a>Oversigt over navne på lager

Lagerplads konti har særlige regler for deres navne. Du kan kun bruge små bogstaver og tal. Du kan få flere oplysninger i [oprette en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) . Desuden skal kontonavn lager med core.windows.net, være et globalt gyldigt, entydigt DNS-navn. Hvis kontoen lagerplads kaldes mystorageaccount, skal følgende resulterende DNS-navne for eksempel være forskellige:

- mystorageaccount.BLOB.Core.Windows.NET
- mystorageaccount.Table.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 