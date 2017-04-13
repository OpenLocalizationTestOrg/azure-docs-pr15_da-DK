<properties
    pageTitle="Azure AD-forbindelse synkronisering: tekniske koncepter | Microsoft Azure"
    description="Beskriver de tekniske begreber af Azure AD-forbindelse synkronisering."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD-forbindelse synkronisering: tekniske koncepter
I denne artikel er en oversigt over emnet [forstå arkitektur](active-directory-aadconnectsync-technical-concepts.md).

Azure AD-forbindelse Synkroniser forespørgselsformlen bygger på en dækkende metadirectory synkronisering platform.
I følgende afsnit introduceres begreberne til metadirectory synkronisering.
Azure Active Directory Sync Services indeholder bygger videre MIIS, ILM og FIM, den næste platform til at oprette forbindelse til datakilder, synkronisering af data mellem datakilder samt klargøringen og ophævelse af klargøring af identitet.

![Tekniske koncepter](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

De følgende afsnit indeholder flere oplysninger om følgende aspekter af tjenesten FIM synkronisering:

- Forbindelse
- Attributten flow
- Connector-plads
- Metaverse
- Klargøring

## <a name="connector"></a>Forbindelse

Kodemoduler, der bruges til at kommunikere med en forbundne directory kaldes forbindelser (tidligere kendt som management supportmedarbejdere (MAs)).

Disse er installeret på den computer, der kører Azure AD-forbindelse synkronisering.
Forbindelserne giver uafhængig muligheden for at kommunikere ved hjælp af eksternt system protokoller i stedet for installation af specialiserede supportmedarbejdere. Det betyder, at risikoen mindre og installation klokkeslæt, især hvis håndtere kritiske programmer og systemer.

Forbindelsen er synonym med connector-plads på billedet ovenfor men omfatter al kommunikation med det eksterne system.

Forbindelsen er ansvarlig for alle importere og eksportere funktionalitet til systemet og frigør udviklere fra behøver at forstå, hvordan du knytter til hvert system indbygget, når du bruger deklarativ klargøring til at tilpasse datatransformationer.

Import og eksport kun forekomme, når planlagt, giver mulighed for yderligere isolation fra forandringer i systemet, da ændringer ikke overføre automatisk til den forbundne datakilde. Desuden kan udviklere også oprette deres egne forbindelser til at oprette forbindelse til stort set alle datakilder.

## <a name="attribute-flow"></a>Attributten flow

Metaverse er den konsoliderede visning af alle joinforbundne identiteter fra tilstødende forbindelse mellemrum. Figuren ovenfor er afbildet attribut flow af linjer med pilespidser for både indgående og udgående. Attributten flow er processen med at kopiere eller at transformere data fra et system til en anden og alle attribut flyder (indgående eller udgående).

Attributten flow forekommer mellem connector-plads og metaverse bi directionally når synkronisering (fuld eller delta) handlinger er planlagt til at køre.

Attributten flow sker kun, når der køres synkroniseringen. Attributten flyder er defineret i synkronisering regler. Det kan være indgående (ISR på billedet ovenfor) eller udgående (OSR på billedet ovenfor).

## <a name="connected-system"></a>Forbundne system

Tilsluttede system (også kendt forbundne bibliotek) der refererer til det eksterne system Azure AD-forbindelse synkronisering er tilsluttet og læse og skrive identitetsdata til og fra.

## <a name="connector-space"></a>Connector-plads

Hver forbundne datakilde repræsenteres som et filtrerede undersæt af objekter og attributter i connector-plads.
Dette gør tjenesten sync til at betjene lokalt uden at skulle kontakte det eksterne system, når du synkroniserer objekterne og begrænser interaktion for at importerer og eksporterer kun.

Når først datakilden og forbindelsen har mulighed for at angive en liste over ændringer (en delta importere), derefter øges funktionsdygtige effektivitet dramatisk som kun de ændringer, da den sidste afstemning cyklus er udvekslet. Connector-plads insulates ændringer ud automatisk ved at kræve, at forbindelsen tidsplanen importerer og eksporterer forbundne datakilde. Denne tilføjede forsikring giver ro i huske under test, få vist eller bekræfte den næste opdatering.

## <a name="metaverse"></a>Metaverse

Metaverse er den konsoliderede visning af alle joinforbundne identiteter fra tilstødende forbindelse mellemrum.

Efterhånden som identiteter er kædet sammen og nøglecenter er tildelt til forskellige attributter gennem Importér flow tilknytninger, begynder objektet central metaverse til samling oplysninger fra flere systemer. Fra dette objekt attribut flow udføre tilknytninger oplysninger til udgående systemer.

Objekter oprettes, når et autoritative system projekter dem til metaverse. Så snart alle forbindelser er fjernet, slettes objektet metaverse.

Objekter i metaverse kan ikke redigeres direkte. Alle data i objektet skal være bidraget gennem attribut forløb. Metaverse fører fast forbindelser med hver forbindelse mellemrum. Disse forbindelser kræver ikke nyvurdering for hver synkroniseringen. Det betyder, at Azure AD-forbindelse Synkroniser ikke behøver at finde det tilsvarende remote objekt, hver gang. Derved undgår behovet for dyrt supportmedarbejdere forhindre ændringer i attributter, der normalt er ansvarlig for korrelere objekterne.

Når opdager nye datakilder, der kan være nødvendigt eksisterende objekter, der skal administreres, bruger Azure AD-forbindelse synkronisere en proces, der kaldes en joinforbindelse regel til at vurdere potentielle kandidater, som du opretter et link.
Når der er oprettet linket denne evaluering opstår igen ikke, og normal attribut flow kan forekomme mellem remote forbundne datakilden og metaverse.

## <a name="provisioning"></a>Klargøring

Når en autoritative kildeprojekter kan der oprettes et nyt objekt i metaverse et nyt forbindelse mellemrum objekt i en anden forbindelse, der repræsenterer en efterfølgende forbundne datakilde.

Dette uden videre opretter et link, og attributten flow kan fortsætte bi directionally.

Når en regel angiver, at en ny forbindelse mellemrum objekt skal oprettes, kaldes klargøring. Dog da denne handling kun finder sted inden for den forbindelse plads, fører den ikke til den forbundne datakilde, indtil en eksporten er udført.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Azure Active Directory forbinde-synkronisering: Tilpasse indstillinger for synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
