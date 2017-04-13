<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - fastlægge krav til beskyttelse af data | Microsoft Azure"
    description="Når du planlægger dit hybride identitet løsning, identificere databeskyttelse kravene til din virksomhed, og hvilke indstillinger er tilgængelige for bedst opfylder kravene."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Planlægge, hvordan du udvider datasikkerhed gennem stærke identitet løsning

Det første trin til at beskytte data er identificere, hvem der kan få adgang til dataene, og som en del af denne proces, du vil have en identitet løsning, som kan integreres med dit system for at angive godkendelses- og funktioner. Godkendelse og autorisation forveksles ofte med hinanden og deres roller misforståede. I virkeligheden er de meget forskellige, som vist i nedenstående figur:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Mobilenhed management livscyklus faser**

Når du planlægger dit hybride identitet løsning skal du forstå databeskyttelse kravene til din virksomhed, og hvilke indstillinger er tilgængelige for bedst opfylder kravene.
 
>[AZURE.NOTE]
Når du er færdig med at planlægge datasikkerhed, kan du gennemse [bestemme Multi-Factor godkendelseskrav](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) for at sikre, at dine valg angående Multi-Factor godkendelseskrav ikke blev påvirkes af de beslutninger, du har foretaget i dette afsnit.

## <a name="determine-data-protection-requirements"></a>Fastlægge krav til beskyttelse af data
I alderen for mobilitet de fleste virksomheder har et fælles mål: muligt for brugerne at være produktiv på deres mobilenheder, mens du lokalt eller fra en fjernplacering fra et vilkårligt sted for at øge produktiviteten. Mens det kan skyldes et fælles mål, bliver virksomheder, der har disse krav også problem vedrørende mængden af trusler, der skal mindskes for at beskytte virksomhedens data og vedligeholde brugerens personlige oplysninger. Hvert firma kan have forskellige krav herom forskellige overholdelse af regler, der varierer efter hvilken branche virksomheden handler føre til forskellige designbeslutninger. 

Der er dog nogle sikkerhedsaspekter, der skal set og godkendt, uanset branche, som er beskrevet i næste afsnit.

## <a name="data-protection-paths"></a>Databeskyttelse stier

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Databeskyttelse stier**

Komponenten identitet vil være det første, der kontrolleres, før der opnås adgang til data i diagrammet ovenfor. Disse data kan dog være i forskellige tilstande, mens det blev åbnet. Hvert tal på dette diagram repræsenterer en sti, hvor data kan være placeret på et tidspunkt i gang. Tallene er beskrevet nedenfor:

1. Databeskyttelse på niveauet for enhed.
2. Databeskyttelse undervejs.
3. Databeskyttelse samtidig med lokale resten.
4. Databeskyttelse samtidig med resten i skyen.

Selvom tekniske kontrolelementer, der Aktivér IT for at beskytte selve dataene på hver enkelt af disse faser ikke direkte tilbydes af løsningen hybrid identitet, det er nødvendigt, der kan udnytte både i det lokale miljø og skyen hybrid identitet løsningen identitet management ressourcer for at identificere brugeren før give adgang til dataene. Når planlægning din hybrid identitet løsning sikrer, at følgende spørgsmål besvares efter organisationens krav:

## <a name="data-protection-at-rest"></a>Databeskyttelse på resten
Uanset hvor dataene er på resten (enhed, skyen eller lokalt), er det vigtigt at udføre en vurdering for at forstå organisation behovet i denne forbindelse. I dette område, kan du sikre dig, at følgende spørgsmålene:

- Skal din virksomhed at beskytte data på resten?
 - Hvis det er tilfældet, er hybrid identitet løsning kan du integrere systemet med den aktuelle lokale infrastruktur?
 - Hvis det er tilfældet, er hybrid identitet løsning kan du integrere systemet med din arbejdsbelastninger, der er placeret i skyen?
- Er skyen identitet management stand til at beskytte brugerens legitimationsoplysninger og andre data, der er gemt i skyen?

## <a name="data-protection-in-transit"></a>Databeskyttelse undervejs
Data mellem enheden og datacenter eller mellem enheden og skyen undervejs skal være beskyttet. Dog betyder blive i overførsel ikke nødvendigvis et kommunikation proces med en komponent uden for din skytjeneste det flyttes internt, også, f.eks. mellem to virtuelle netværk. I dette område, kan du sikre dig, at følgende spørgsmålene:

- Skal din virksomhed til at beskytte data under overførsel?
 - Hvis det er tilfældet, er hybrid identitet løsning kan du integrere systemet med sikker kontrolelementer som SSL/TLS?
- Bevarer skyen identitet management trafik til og i directory lageret (i og mellem datacentre) logget?


## <a name="compliance"></a>Overholdelse af angivne standarder
Lovgivning, gælder lovgivningen og overholdelse af lovgivningen krav varierer efter branche, der tilhører din virksomhed. Selskaber i høj organiseret virksomheder skal løse identitet-management problemer relateret til problemer med overholdelse af angivne standarder. Bestemmelser som rapportdata iht (SOX), sygeforsikring mobilitet og pålidelighed Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) og den betaling kort branche Data sikkerhed Standard (PCI DSS) er meget strenge om identitet og adgang. Løsningen hybride identitet, der indfører din virksomhed skal have de grundlæggende funktioner, der opfylder kravene i en eller flere af disse regler. I dette område, kan du sikre dig, at følgende spørgsmålene:

- Er løsningen hybrid identitet overholder de lovmæssige krav til din virksomhed?
- Hybrid identitet løsning er indbygget i funktioner, der gør det muligt for din virksomhed skal være kompatibel lovbestemmelser? 
 
>[AZURE.NOTE]
Sørg for at tage noter på hvert svar og forstå begrundelse bag svaret. [Angive strategi til databeskyttelse](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) går hen over de tilgængelige indstillinger og fordele/ulemper ved hver indstilling.  Skal have ved har besvaret de spørgsmål, du vil vælge, hvilken indstilling, der bedst passer til din virksomhed.

## <a name="next-steps"></a>Næste trin
 [Fastlægge krav til styring af webindhold](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
