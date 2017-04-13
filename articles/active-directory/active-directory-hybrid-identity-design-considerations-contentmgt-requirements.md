<properties
    pageTitle="Azure Active Directory hybride identitet Designovervejelser - fastlægge krav til styring af webindhold | Microsoft Azure"
    description="Giver indblik i, hvordan du kan bestemme Indholdsstyring krav til din virksomhed. Når en bruger har sin egen enhed kan han har normalt også flere legitimationsoplysninger, som kan skiftende efter det program, han bruger. Det er vigtigt at skelne, hvilket indhold blev oprettet med personlige brugeroplysninger kontra dem, der er oprettet ved hjælp af virksomhedens legitimationsoplysninger. Din identitet løsning bør kunne interagere med cloud services for at give en problemfri oplevelse til slutbrugeren under sikre sin beskyttelse af personlige oplysninger og øge beskyttelse mod uautoriseret videregivelse data."
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

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Fastlægge krav til styring af webindhold til din hybrid identitet løsning

Forstå Indholdsstyring kravene til din virksomhed kan påvirke din beslutning på hvilken hybrid identitet løsning at bruge direkte. Virksomhedens skal beskytte sine egne data med udbredelsen af flere enheder og muligheden for brugere for at få deres egne enheder ([BYOD](http://aka.ms/byodcg)), men det også skal bevare brugerens personlige oplysninger. Når en bruger har sin egen enhed kan han har normalt også flere legitimationsoplysninger, som kan skiftende efter det program, han bruger. Det er vigtigt at skelne, hvilket indhold blev oprettet med personlige brugeroplysninger kontra dem, der er oprettet ved hjælp af virksomhedens legitimationsoplysninger. Din identitet løsning bør kunne interagere med cloud services for at give en problemfri oplevelse til slutbrugeren under sikre sin beskyttelse af personlige oplysninger og øge beskyttelse mod uautoriseret videregivelse data. 

Din identitet løsning vil opgraderede ved forskellige tekniske kontrolelementer for at give indholdsstyring, som vist i nedenstående figur:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Sikkerhedskontrolelementer, der vil udnytte din identitet administrationssystem**

Krav til styring af webindhold kan generelt udnytte din identitet administrationssystem i de følgende områder:

- Beskyttelse af personlige oplysninger: identificerer den bruger, der ejer en ressource og anvende de relevante kontrolelementer for at bevare integritet.
- Data klassifikation: identificere den bruger eller gruppe og adgangsniveau til et objekt efter dens klassificering. 
- Uautoriseret videregivelse databeskyttelse: sikkerhedskontrolelementer, der er ansvarlig for beskyttelse af data for at undgå uautoriseret videregivelse skal interagere med identitetssystem til at validere brugerens identitet. Det er også vigtigt for overvågning for formål.

>[AZURE.NOTE]
Læs [data klassifikation til skyen parathed](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) for flere oplysninger om bedste praksis og retningslinjer for data klassificering.

Når planlægning din hybrid identitet løsning sikrer, at følgende spørgsmål besvares efter organisationens krav:

- Har din virksomhed sikkerhedskontrol på plads til at gennemtvinge data beskyttelse af personlige oplysninger?
 - Hvis Ja, disse sikkerhedskontrol kan du integrere systemet med løsningen hybride identitet, du vil indføre?
- Anvender din virksomhed data klassifikation?
 - Hvis det er tilfældet, er den aktuelle løsning kan du integrere systemet med løsningen hybride identitet, du vil indføre?
- Har din virksomhed i øjeblikket en løsning til uautoriseret videregivelse data? 
 - Hvis det er tilfældet, er den aktuelle løsning kan du integrere systemet med løsningen hybrid identitet, du vil indføre?
- Skal din virksomhed skal overvåges adgang til ressourcer?
 - Hvis det er tilfældet, hvilken type ressourcer?
 - Hvis Ja, er det nødvendigt adgangsniveau for at vide?
 - Hvis det er tilfældet, hvor overvågningsloggen skal være placeret? Lokalt eller i skyen?
- Skal din virksomhed til at kryptere alle mails, der indeholder følsomme data (SSNs, kreditkortnumre osv.)?
- Skal din virksomhed til at kryptere alle dokumenter/indholdet delt med eksterne partnere?
- Skal din virksomhed at håndhæve firmapolitikker på visse typer mails (har ingen svar til alle, Viderestil ikke)?
 
>[AZURE.NOTE]
Sørg for at tage noter på hvert svar og forstå begrundelse bag svaret. [Angive strategi til databeskyttelse](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) går hen over de tilgængelige indstillinger og fordele/ulemper ved hver indstilling.  Skal have ved har besvaret de spørgsmål, du vil vælge, hvilken indstilling, der bedst passer til din virksomhed.


## <a name="next-steps"></a>Næste trin
[Finde ud af krav til access](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
