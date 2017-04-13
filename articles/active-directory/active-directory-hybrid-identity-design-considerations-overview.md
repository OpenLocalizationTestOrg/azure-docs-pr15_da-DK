<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - oversigt | Microsoft Azure"
    description="Oversigt og indhold kort over hybride identitet design overvejelser i forbindelse med vejledning"
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

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory Hybrid identitet Designovervejelser

Forbruger-baserede enheder proliferating virksomhedens verden, og skybaserede software som en tjeneste (SaaS) programmer er nemt at indføre. Som et resultat vedligeholdelse kontrol af adgang til brugernes programmer på tværs af interne datacentre og skyen platforme udfordring.  Microsofts identitet løsninger dække lokale og skybaserede funktioner, oprette en enkelt bruger-id for godkendelse og autorisation til alle de ressourcer, uanset placering. Vi kalder denne Hybrid identitet. Der er forskellige design og konfigurationsindstillinger for hybride identitet ved hjælp af Microsoft solutions, og i nogle tilfælde kan det være svært at afgøre, hvilken kombination vil bedste imødekommer behovet hos din organisation. Denne Hybrid identitet Design overvejelser i forbindelse med vejledning hjælper dig med at forstå, hvordan til at designe en hybrid identitet løsning, der passer bedst til virksomheden og teknologi skal for organisationen.  Denne vejledning indeholder oplysninger om en række trin og opgaver, som du kan følge for at hjælpe dig med at designe en hybridløsning, der opfylder organisationens særlige krav. Vejledningen skal i hele trin og opgaver vises de relevante teknologier og funktioner, der er tilgængelig for organisationer til mødes funktionelle og tjenestens kvalitet (såsom tilgængelighed, skalerbarhed, ydeevne, administration og sikkerhed) krav niveau. Nærmere betegnet kan er hybride identitet design overvejelser i forbindelse med vejledning mål at besvare spørgsmål, der er følgende: 

- Hvilke spørgsmål skal jeg stille og besvare for at drive en hybrid identitet-specifikke design for et domæne teknologi eller et problem, der bedst opfylder min krav?
- Hvilke sekvens af aktiviteter skal jeg udføre for at designe en hybrid identitet løsning for domænet, teknologi eller et problem? 
- Hybrid identitet teknologi og konfiguration af indstillinger er tilgængelige kan hjælpe mig opfylder min krav? Hvad er kompromiser mellem disse indstillinger, så jeg kan vælge den bedste løsning til min virksomhed?


## <a name="who-is-this-guide-intended-for"></a>Hvem denne vejledning henvender sig til?
 CIO, CITO, ledende identitet arkitekter, Enterprise Architects og IT arkitekter ansvarlig for at designe en hybrid identitet løsning til mellemstore eller store virksomheder.

## <a name="how-can-this-guide-help-you"></a>Hvordan kan denne vejledning hjælpe dig? 
Du kan bruge denne vejledning til at forstå, hvordan til at designe en hybridløsning, der er i stand til at integrere et skybaseret identitet administrationssystem med din aktuelle lokale identitet løsning. Følgende grafik viser et eksempel en hybridløsning, der gør det muligt for IT-administratorer til at administrere integrere deres aktuelle Windows Server Active Directory-løsning placeret lokalt med Microsoft Azure Active Directory til at aktivere brugere at bruge enkelt enkeltlogon (SSO) på tværs af programmer, der er placeret i skyen og i det lokale miljø.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


Illustrationen ovenfor er et eksempel på en hybridløsning, der udnytte skytjenester for at integrere med lokale funktioner for at give en enkelt oplevelse til slutbrugeren godkendelsesprocessen og for at lette IT administration af disse ressourcer. Selvom det kan være en meget almindelige scenarie, der hver organisations hybride identitet design sandsynligvis er anderledes end det viste i figur 1 på grund af forskellige krav eksempel. Denne vejledning indeholder en række trin og opgaver, som du kan følge for at designe en hybridløsning, der opfylder organisationens særlige krav. I de følgende trin og opgaver præsenterer vejledningen de relevante teknologier og funktioner, der er tilgængelige for dig at imødekomme funktionelle og service kvalitet niveau krav til din organisation.

**Forudsætninger**: du har erfaring med Windows Server, Active Directory-domænetjenester og Azure Active Directory. I dette dokument, vi antager, at du leder efter hvordan disse løsninger kan dine forretningsbehov i deres egen eller i en integreret løsning.

## <a name="design-considerations-overview"></a>Design overvejelser i forbindelse med oversigt
Dette dokument indeholder en række trin og opgaver, som du kan følge for at designe en hybridløsning, der bedst opfylder dine behov. Trinnene vises i et bestemt rækkefølge. Designovervejelser du få mere at vide i senere trin kan kræve, at du ændrer beslutninger, du har oprettet i tidligere trin, men på grund af problemer med uoverensstemmende designvalg. Hver forsøgt at give dig besked om potentielle design konflikter i hele dokumentet. 

Du kan ankommer til designet, der bedst opfylder dine behov, når gentagelse gennem trinnene, så mange gange som det er nødvendigt at inkorporere alle overvejelser i dokumentet. 

| Hybrid identitet fase                                             | Emneliste                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fastlægge krav til identitet                                   | [Bestemme forretningsbehov](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Fastlægge krav til directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Fastlægge krav til multi-Factor authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definere en hybrid identitet indføring strategi](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Planlægge, hvordan du udvider datasikkerhed gennem stærke identitet løsning | [Fastlægge krav til beskyttelse af data](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Fastlægge krav til styring af webindhold](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Finde ud af krav til access](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Fastlægge krav til hændelsen svar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Angive strategi for beskyttelse af data](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Planlægge i forhold til hybrid identitet livscyklus                                | [Bestemme hybrid identitet administrationsopgaver](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Synkronisering af administration](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Fastlægge hybrid identitet management indføring strategi for](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Hent denne vejledning
Du kan hente en PDF-versionen af guiden Hybrid identitet Designovervejelser i [Technet-galleriet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             
