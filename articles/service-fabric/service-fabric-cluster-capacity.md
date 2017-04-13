<properties
   pageTitle="Planlægning af Service-strukturen klynge kapaciteten | Microsoft Azure"
   description="Tjenesten strukturen klynge kapacitet overvejelser i forbindelse med. Nodetypes, holdbarhed og pålidelighed niveauer"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Tjenesten strukturen klynge kapacitet overvejelser i forbindelse med

For en hvilken som helst produktionsmiljø er kapacitetsplanlægning meget vigtigt. Her er nogle af de elementer, du skal overveje som en del af processen.

- Antallet af node-typer din klynge skal starte med
- Egenskaberne for hver af nodetype (størrelse, primær, internet modstående, antal FOS osv.)
- Pålidelighed og holdbarhed egenskaberne for klyngen

Lad os kortvarigt gennemset hver af disse elementer.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Antallet af node-typer din klynge skal starte med

Først skal du finde ud af, hvad den klynge, du opretter der skal bruges til, og hvilke typer af programmer, du planlægger at installere i denne klynge. Hvis du ikke er Ryd på formålet med klyngen, du sandsynligvis ikke er endnu klar til at angive kapaciteten planlægningsprocessen.

Oprette antallet af node-typer din klynge skal starte med.  Hver nodetype er knyttet til at angive et virtuelt skala. Hver nodetype kan derefter skaleres eller ned uafhængigt af hinanden, har forskellige typer åbne porte og kan have forskellige kapacitet målepunkter. Så leveres beslutning af antallet node-typer grundlæggende til Overvej følgende:

- Har dit program flere tjenester, og enhver af dem skal være offentlig eller adgang til internettet? Typisk programmer indeholder en front end gateway-tjeneste, som modtager input fra en klient og en eller flere back end-tjenester, der kommunikerer med front end-tjenester. Så i dette tilfælde ender du har mindst to nodetyper af.

- Dine tjenester (der udgør dit program) har forskellige infrastruktur behov som større RAM eller højere CPU skifter? For eksempel Lad os antage, at det program, du vil installere indeholder en front-end-tjeneste og en back end-tjeneste. Front end-tjenesten kan køre på mindre VM'er (VM størrelser som D2), der har porte åbne til internettet.  Back end-tjenesten, men beregning intensivt og skal køre på større FOS (med VM størrelser som D4, D6, D15), der ikke er internet vender.

 I dette eksempel, selvom du kan vælge at flytte alle tjenester til en nodetype, anbefaler vi, at du placerer dem i en klynge med to nodetyper.  Dette giver mulighed for hver nodetype har forskellige egenskaber som forbindelse til internettet eller VM størrelse. Antallet af FOS kan skaleres, uafhængigt af hinanden, samt.  

- Da du ikke til at forudsige fremtiden, gå med oplysninger, du kender til og antallet af node-typer, som dine programmer skal starte med at beslutte. Du kan altid tilføje eller fjerne node-typer senere. En tjeneste-strukturen klynge skal have mindst én nodetype.

## <a name="the-properties-of-each-node-type"></a>Egenskaberne for hver nodetype

**Node af type** kan ses som svarer til roller i Cloud Services. Node bruges til at definere VM størrelser, antallet af FOS og deres egenskaber. Hver node af type, der er defineret i en tjeneste-strukturen klynge er konfigureret som en separat virtuelt skala angive. VM skala sæt er en Azure beregne ressource, som du kan bruge til at installere og administrere en samling af virtuelle maskiner som et sæt. Blive defineret som særskilte VM skala angiver, hver nodetype kan derefter skaleres eller ned uafhængigt af hinanden, har forskellige typer åbne porte og kan have forskellige kapacitet målepunkter.

Din klynge kan have mere end én nodetype, men den primære nodetype (den første opgave, som du definerer på portalen) skal have mindst fem FOS for klynger, der bruges til fremstilling arbejdsbelastninger (eller mindst tre FOS til test klynger). Hvis du opretter den klynge ved hjælp af en skabelon til Ressourcestyring, kan du finde en **er primære** attribut under node typedefinition. Den primære nodetype er nodetypen, hvor tjenesten strukturerede system services er placeret.  

### <a name="primary-node-type"></a>Primære nodetype
For en klynge med flere node-typer skal du vælge en af de skal være primære. Her er egenskaberne for typen primære node:

- Minimumstørrelse af FOS for den primære nodetype bestemmes af det holdbarhed niveau, du vælger. Standard for holdbarhed niveau er Bronze. Rul ned for at få at vide om holdbarhed niveau nyheder og de værdier, kan det tage.  

- Det mindste antal FOS for den primære nodetype bestemmes af det pålidelighed niveau, du vælger. Standard for pålidelighed niveau er Silver. Rul ned for at få at vide om pålidelighed niveau nyheder og de værdier, kan det tage.

- Tjenesten strukturen systemets tjenester (for eksempel tjenesten klynge Manager eller billede Store service) er placeret på den primære nodetype, og så pålidelighed og holdbarhed af klyngen bestemmes af pålidelighed niveau værdi og holdbarhed niveau værdi, du vælger for den primære node.

![Skærmbillede af en klynge, der indeholder to typer af Node ][SystemServices]


### <a name="non-primary-node-type"></a>Ikke-primær node af type
Der er én primære nodetype for en klynge med flere node-typer og resten af dem er ikke-primær. Her er egenskaberne for en ikke-primær node af type:

- Minimumstørrelse af FOS for denne nodetype bestemmes af det holdbarhed niveau, du vælger. Standard for holdbarhed niveau er Bronze. Rul ned for at få at vide om holdbarhed niveau nyheder og de værdier, kan det tage.  

- Det mindste antal FOS for denne nodetype kan være en. Dog skal du vælge dette nummer, der er baseret på antallet kopier af de/programtjenester, som du gerne vil køre i denne nodetype. Antallet af FOS i en node af type kan øges, når du har installeret klyngen.


## <a name="the-durability-characteristics-of-the-cluster"></a>Klyngen holdbarhed karakteristika

Holdbarhed niveau bruges til at angive de tilladelser, der har din FOS med den underliggende Azure infrastruktur til systemet. I typen primære node kan denne rettighed Service-strukturen til at afbryde midlertidigt enhver VM niveau infrastruktur anmodning (som en VM genstart, VM reimage eller VM-migrering), der påvirker quorum kravene til systemets tjenester og dine tjenester uden problemer med høj sikkerhed. I ikke-primær node-typer kan denne rettighed Service-strukturen til at afbryde midlertidigt anmodning om en hvilken som helst VM niveau infrastruktur som VM genstart, VM reimage, VM overførsel osv., der påvirker quorum kravene til din med høj sikkerhed services kører i den.

Denne rettighed udtrykkes i følgende værdier:

- Gold - infrastruktur job kan være midlertidigt for en varighed på 2 timer per UD

- Silver - infrastruktur job kan være midlertidigt for en varighed på 30 minutter per UD (dette ikke er aktiveret til brug. Én gang aktiveret denne vil være tilgængelig på alle standard FOS enkelt kerne og derover).

- Bronze - ingen rettigheder. Dette er standardværdien.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Klyngen pålidelighed karakteristika

Pålidelighed niveauet bruges til at angive antallet kopier af systemets tjenester, du vil køre i denne klynge på den primære nodetype. Den flere antallet af replikaer og mere pålidelige er systemets tjenester i din klynge.  

Pålidelighed niveauet kan have følgende værdier.

- Platinum - køre systemets-tjenester med en destination replikasættet antallet af 9

- Gold - køre systemets-tjenester med en destination replikasættet antallet af 7

- Silver - køre systemets-tjenester med en destination replikasættet antallet af 5

- Bronze - køre systemets-tjenester med en destination replikasættet antallet af 3

>[AZURE.NOTE] Det pålidelighed niveau, du vælger bestemmer det mindste antal noder din primære node af typen skal have. Pålidelighed niveauet har ingen indflydelse på den maksimale størrelse af klyngen. Så du kan have en klynge 20 noder, der kører på Bronze pålidelighed.

 Du kan vælge at opdatere pålideligheden af din klynge fra ét niveau til en anden. Dette udløser klynge opgraderinger det er nødvendigt at ændre system services replikaen angive antal. Vent opgraderingen i gang for at fuldføre, før du foretager andre ændringer til klynge, som tilføjelse af noder osv.  Du kan overvåge forløbet af opgraderingen til Service-strukturen Explorer eller ved at køre [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Når du er færdig med din kapacitet, planlægning og konfiguration af en klynge, skal du læse følgende:
- [Tjenesten strukturen klynge sikkerhed](service-fabric-cluster-security.md)
- [Tjenesten strukturen sundhed model Introduktion](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
