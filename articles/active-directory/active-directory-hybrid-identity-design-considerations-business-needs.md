<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - identitet mht | Microsoft Azure"
    description="Identificere virksomhedens forretningsbehov, der fører dig til at definere kravene til hybrid identitet designet."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Bestemme identitet krav til din hybrid identitet løsning
Det første trin i design en hybrid identitet løsning er at finde ud af kravene til den business-organisation, der vil udnytte denne løsning.  Hybrid identitet starter som en supplerende rolle (det understøtter alle andre skyen løsninger ved at indsende godkendelse) og går videre for at angive nye og interessante funktioner, der låse op for nye arbejdsmængder for brugere.  Disse arbejdsbelastninger eller tjenester, du vil indføre for dine brugere vil bestemmer kravene til hybrid identitet designet.  Disse tjenester og arbejdsbelastninger, som skal udnytte hybrid identitet både lokalt og i skyen.  

Du skal gennemgå disse vigtige aspekter af virksomheden at forstå, hvad det er nu et krav, og hvad virksomheden planlægger i fremtiden. Hvis du ikke har synligheden af langsigtede strategi for hybride identitet design, sandsynligvis det, at din løsning ikke SVG som virksomheden behov vokser og ændre.   T han diagramvisning nedenfor viser et eksempel på en hybrid identitet arkitektur og arbejdsbelastninger, som er der åbnes ved angivelse for brugere. Dette er blot et eksempel på de nye muligheder, som kan åbnes ved angivelse og leveret med en dækkende hybrid identitet strategi. 
 
Visse komponenter, der er en del af hybrid identitet arkitektur![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Bestemme forretningsbehov
Hver virksomhed har forskellige krav, selvom disse virksomheder er en del af den samme branche-, reelle virksomheden krav kan variere. Du kan stadig udnytte bedste fremgangsmåder fra branchen, men i sidste ende er det virksomhedens forretningsbehov, der fører dig til at definere kravene til hybrid identitet designet. 

Sørg for at besvare følgende spørgsmål for at identificere virksomhedens behov:

- Er din virksomhed leder til at klippe IT funktionsdygtige omkostninger?
- Er din virksomhed leder til at sikre skyen Aktiver (SaaS apps, infrastruktur)?
- Er din virksomhed leder til at modernisere din IT?
  - Er dine brugere mere mobil- og krævende IT for at oprette undtagelser til din DMZ tillade forskellige typer trafik til at få adgang til forskellige ressourcer?
  - Har din virksomhed ældre apps, der skal bruges til at blive udgivet på disse moderne brugere, men kan ikke nemt at omskrive?
  - Din virksomhed skal udføre alle disse opgaver og finde den under kontrol på samme tid?
- Er din virksomhed leder til at sikre brugernes identitet og reducere risikoen ved at tage nye værktøjer, som udnytte ekspertise Microsofts Azure sikkerhed ekspertise lokale?
- Din virksomhed forsøger at slippe af med de frygtede "eksterne" konti lokalt og flytte dem i skyen, hvor de ikke længere er en passive truslen i dit lokale miljø?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analysere lokale identitet infrastruktur
Nu hvor du har en ide om virksomhed virksomhedens behov, skal du evaluere infrastrukturen lokale identitet. Denne evaluering er vigtige for definerer de tekniske krav til at integrere din aktuelle identitet løsning til skyen identitet management system. Sørg for at besvare spørgsmål, der er følgende:

- Hvilke godkendelse og autorisation løsning betyder din virksomhed bruger i det lokale miljø? 
- Har din virksomhed i øjeblikket lokal synkroniseringstjenester?
- Anvender din virksomhed en hvilken som helst fra tredjepart identitetsudbydere (IdP)?

Du skal også være opmærksom på skytjenesterne, der måske har din virksomhed. Udføre en vurdering for at forstå de aktuelle integration med SaaS, IaaS eller PaaS modeller i dit miljø er meget vigtigt. Sørg for at besvare følgende spørgsmål under denne vurdering:
- Har din virksomhed integration med en udbyder af skyen?
- Hvis det er tilfældet, hvilke tjenester der bruges?
- Denne integration i fremstilling eller er det et pilotprojekt?


>[AZURE.NOTE]
Hvis du ikke har en nøjagtig tilknytningen af alle dine apps og tjenester i skyen, kan du bruge værktøjet skyen App registrering. Dette værktøj kan give din IT-afdeling indsigt i alle organisationens business og forbrugere skyen apps. Det gør det nemmere end nogensinde at opdage skygge IT i organisationen, herunder oplysninger om brugsmønstre og alle brugere, få adgang til dine cloud-programmer. Få adgang til dette værktøj skal du gå til [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Vurder krav til integration af identitet
Derefter skal du evaluere identitet integrationskrav. Denne evaluering er vigtigt at definere de tekniske krav til hvordan brugere kan godkende, hvordan organisationens tilstedeværelse ser ud i skyen, hvordan organisationen har mulighed for godkendelse og hvad brugeroplevelsen der skal fungere. Sørg for at besvare spørgsmål, der er følgende:

- Din organisation anvender sammenslutning, standard godkendelse eller begge dele?
- Er organisationsnetværk et krav?  På grund af følgende:
 - Kerberos-baserede SSO
 - Dit firma har en lokal-programmer (enten indbygget internt eller 3), der bruger SAML eller lignende sammenslutning funktioner.
 - MFA via chipkort. RSA SecurID osv.
 - Klient adgangsregler, der adresserer spørgsmålene nedenfor:
     1. Kan jeg blokere alle ekstern adgang til Office 365, der er baseret på IP-adressen på klienten?
     1. Kan jeg blokere alle ekstern adgang til Office 365, bortset fra Exchange ActiveSync?
     1. Jeg kan blokere alle ekstern adgang til Office 365, med undtagelse af browserbaserede apps (OWA, SPO)
     1. Jeg kan blokere alle ekstern adgang til Office 365 til medlemmer af udpegede AD-grupper
- Sikkerhed/overvågning problemstillinger
- Eksisterende investering i organisationsnetværk godkendelse
- Hvilket navn anvender vores organisation for vores domæne i skyen?
- Har organisationen et brugerdefineret domæne?
    1. Er domænet, offentlige og nemt kan bekræftes via DNS?
    1. Hvis det ikke er, derefter har du et offentligt domæne, der kan bruges til at registrere et alternativt UPN i AD?
- Er i overensstemmelse til skyen repræsentation bruger-id? 
- Har organisationen apps, der kræver integration med skytjenester?
- Har organisationen flere domæner, og de alle anvender standard eller samlet godkendelse?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Evaluere programmer, der kører i dit miljø
Nu, hvor du har en ide om dine lokale og infrastruktur i skyen, skal du evaluere de programmer, der kører i disse miljøer. Denne evaluering er vigtigt at definere de tekniske krav til at integrere disse programmer til skyen identitet management system. Sørg for at besvare spørgsmål, der er følgende:

- Hvor vil vores programmer live?
- Får brugerne adgang til lokale programmer?  I skyen? Eller begge dele?
- Er der planer om at få de eksisterende arbejdsbelastninger og flytte dem til skyen?
- Er der planer om at udvikle nye programmer, der er placeret enten lokalt eller i skyen, som skal bruges i skyen godkendelse?

## <a name="evaluate-user-requirements"></a>Vurder brugerkrav til
Du kan også nødt til at vurdere bruger kravene. Denne evaluering er vigtigt at definerer de trin, der er behov for afvises og hjælpe brugere, som de overgang til skyen. Sørg for at besvare spørgsmål, der er følgende:

- Får brugerne adgang til lokale programmer?
- Får brugerne adgang til programmer i skyen?
- Hvordan gør brugere typisk logge på deres lokale miljø?
- Hvordan kan brugere-logon til skyen?

>[AZURE.NOTE]
Sørg for at tage noter på hvert svar og forstå begrundelse bag svaret. [Fastslå hændelsen svar krav](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) vil gennemgå de tilgængelige indstillinger og medarbejdere/ulemper ved hver indstilling.  Skal have ved har besvaret de spørgsmål, du vil vælge, hvilken indstilling, der bedst passer til din virksomhed.

## <a name="next-steps"></a>Næste trin
[Fastlægge krav til directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
