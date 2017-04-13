<properties
    pageTitle="Azure Active Directory hybride identitet Designovervejelser - fastlægge directory-synkronisering krav | Microsoft Azure"
    description="Identificere, hvilke krav skal bruge til at synkronisere alle brugere mellem on = det lokale miljø og skyen til virksomheden."
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

# <a name="determine-directory-synchronization-requirements"></a>Fastlægge krav til directory-synkronisering
Synkronisering er alle om at give brugerne en identitet i skyen baseret på deres lokale identitet. Uanset om de oftest bruger synkroniserede konto til godkendelse eller samlet godkendelse, skal brugerne stadig har en identitet i skyen.  Denne identitet skal vedligeholdes og opdateret med jævne mellemrum.  Opdateringerne kan tage mange formularer fra titelændringer i ændringer af adgangskode.  

Start ved evaluering af organisationer lokale identitet løsning og bruger krav. Denne evaluering er vigtigt at definere de tekniske krav til hvordan bruger-id'er oprettes og vedligeholdes i skyen.  De fleste organisationer, Active Directory er i det lokale miljø og den lokale mappe, som brugere kan ved synkroniseres fra, men i nogle tilfælde dette ikke tilfældet.  

Sørg for at besvare spørgsmål, der er følgende:


- Har du én AD skov, flere eller ingen?
 - Du hvor mange Azure AD-mapper der synkroniserer til?
 
    1. Bruger du filtrering?
    2. Har du flere Azure AD-forbindelse servere, der er planlagt?
  
- I øjeblikket har du en synkronisering værktøjet lokalt?
  - Hvis Ja, du gør dine brugere, hvis brugere har en virtuel mappe/integration af identiteter?
- Har du en hvilken som helst anden katalog lokalt, du vil synkronisere (fx LDAP-adresseliste, HR database osv)?
  - Vil du at udføre en hvilken som helst GALSync?
  - Hvad er den aktuelle tilstand for UPN i din organisation? 
  - Har du en anden mappe, som brugere godkendelse mod?
  - Anvender din virksomhed Microsoft Exchange?
    - De planer for at have en exchange-hybridinstallation?

Nu hvor du har en ide om dine krav til synkronisering, skal du bestemme, hvilket værktøj er den rigtige person, der opfylder kravene.  Microsoft indeholder flere værktøjer for at udføre katalogintegration og synkronisering.  Se [Hybrid identitet directory integration værktøjer sammenligningen](active-directory-hybrid-identity-design-considerations-tools-comparison.md) kan finde flere oplysninger. 
   
Nu hvor du har dine krav til synkronisering og værktøjet, udføre dette for din virksomhed, skal du evaluere de programmer, der bruger disse katalogtjenester. Denne evaluering er vigtigt at definere de tekniske krav til at integrere disse programmer i skyen. Sørg for at besvare spørgsmål, der er følgende:

- Disse programmer flyttes til skyen og bruge mappen?
- Er der specielle attributter, der skal synkroniseres til skyen, så disse programmer kan bruge dem korrekt?
- Disse programmer skal skrives igen for at kunne udnytte skyen auth?
- Fortsætter med at live i det lokale miljø, mens brugere har adgang til dem ved hjælp af skyen identitet disse programmer?

Du skal også bestemme sikkerhed krav og begrænsninger katalogsynkronisering. Denne evaluering er vigtigt at få en liste over de krav, der er behov for at oprette og vedligeholde brugerens identiteter i skyen. Sørg for at besvare spørgsmål, der er følgende:

- Hvor bliver synkronisering serveren placeret?
- Bliver det domæne, der er tilsluttet sig?
- Serveren vil være placeret i en begrænset netværk bag en firewall, som en DMZ?
  - Vil du kunne åbne de nødvendige Firewallportene for at understøtte synkronisering?
- Har du en nedbrud gendannelse plan for synkronisering serveren?
- Har du en konto med de rette tilladelser for alle områder, du vil synkronisere med?
 - Hvis din virksomhed ikke ved svar på spørgsmålet, gennemse afsnittet "Tilladelser for synkronisering af adgangskoder" i artiklen [installere Azure Service til synkronisering af Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) og fastslå, om du allerede har en konto med disse tilladelser, eller hvis du har brug at oprette et.
- Hvis du har er synkronisering af mutli skov serveren synkronisering kunne gå til hvert område?
 
>[AZURE.NOTE]
Sørg for at tage noter på hvert svar og forstå begrundelse bag svaret. [Fastslå hændelsen svar krav](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) vil gennemgå de tilgængelige indstillinger. Skal have ved har besvaret de spørgsmål, du vil vælge, hvilken indstilling, der bedst passer til din virksomhed.

## <a name="next-steps"></a>Næste trin
[Fastlægge krav til multi-Factor authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
