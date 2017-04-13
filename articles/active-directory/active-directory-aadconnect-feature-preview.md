<properties
   pageTitle="Azure AD-forbindelse: Funktioner i preview | Microsoft Azure"
   description="Dette emne beskrives i flere detaljer-funktioner der er placeret i Vis udskrift i Azure AD-forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>Flere oplysninger om funktioner i forhåndsvisning
Dette emne beskrives, hvordan du bruger funktioner i øjeblikket i Vis udskrift.

## <a name="group-writeback"></a>Gruppen tilbageførsel
Indstillingen for gruppen tilbageførsel i valgfrie funktioner, så du til tilbageførsel **Office 365-grupper** til et område med Exchange installeret. Dette er en gruppe, som er altid styr på i skyen. Hvis du har Exchange lokalt, og du kan skrive tilbage disse grupper til en lokal installation så brugere med en lokal Exchange-postkasse kan sende og modtage mails fra disse grupper.

Du kan finde flere oplysninger om Office 365-grupper og hvordan du bruger dem [her](http://aka.ms/O365g).

Denne gruppe, der repræsenteres som en distributionsgruppe på lokale Active Directory-Domænetjenester. Din lokale Exchange-server skal være på Exchange 2013 – kumulativ opdatering 8 (udgivet i marts 2015) eller Exchange 2016 at genkende denne nye Gruppetypen.

**Noter under Vis udskrift**

- Attributten address book der i øjeblikket ikke udfyldes i eksemplet. Uden denne attribut, vil gruppen ikke være synlig i den globale Adresseliste. Den nemmeste måde at udfylde denne attribut er at bruge Exchange PowerShell-cmdlet'en `update-recipient`.
- Kun områder med Exchange-skema er gyldig mål for grupper. Hvis ingen Exchange ikke blev fundet, derefter gruppe tilbageførsel ikke mulighed for at aktivere.
- Kun enkelt skov Exchange organisation installationer understøttes i øjeblikket. Hvis du har mere end ét Exchange organisation i det lokale miljø, derefter skal du en lokal GALSync løsning til disse grupper, der skal vises i dine andre områder.
- Funktionen gruppe tilbageførsel håndterer ikke i øjeblikket, sikkerhedsgrupper eller distributionsgrupper.

>[AZURE.NOTE] Et abonnement på Azure AD Premium er påkrævet for gruppen tilbageførsel.

## <a name="user-writeback"></a>Bruger tilbageførsel
> [AZURE.IMPORTANT] Funktionen bruger tilbageførsel Vis er blevet fjernet i August 2015 Opdater til Azure AD-forbindelse. Hvis du har aktiveret det, skal du deaktivere denne funktion.

## <a name="next-steps"></a>Næste trin
Fortsæt med [brugerdefineret installation af Azure AD-forbindelse](./connect/active-directory-aadconnect-get-started-custom.md).

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
