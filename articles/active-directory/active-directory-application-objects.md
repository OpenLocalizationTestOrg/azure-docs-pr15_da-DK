<properties
pageTitle="Azure Active Directory-program og Service hovedstolen objekter | Microsoft Azure"
description="En beskrivelse af forholdet mellem programmet og service vigtigste objekter i Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Programmet og service vigtigste objekter i Azure Active Directory
Når du læser om en Azure Active Directory (AD) "programmet", er det ikke altid Ryd præcis hvad er der refereres til af forfatteren. Formålet med denne artikel er at gøre det mere klart, ved at definere de grundlæggende og cement aspekter af Azure AD-programintegration med et eksempel på registrering og samtykke til et [program med flere lejer](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Oversigt
Et Azure AD-program er bredere end bare en brik af software. Det er et grundlæggende udtryk, der refererer ikke kun til programmer, men også dens registrering (også kendt: identitet konfiguration) med Azure AD, som gør det muligt at deltage i godkendelse og autorisation "samtaler" på kørselstidspunktet. Ved definition fungerer et program i en [klient](active-directory-dev-glossary.md#client-application) rolle (forbrug en ressource), en [ressource server](active-directory-dev-glossary.md#resource-server) rolle (udsætte API'er til klienter) eller endda begge. Samtale-protokollen er defineret af et [OAuth 2.0 tilladelse Giv flow](active-directory-dev-glossary.md#authorization-grant), med et mål for at tillade klient/ressourcen at access/beskytte en ressources data henholdsvis. Nu skal du gå et niveau længere ned, og se, hvordan Azure AD-Programmodellen repræsenterer et program internt. 

## <a name="application-registration"></a>Programmet registrering
Når du registrerer et program i [Azure klassisk portal][AZURE-Classic-Portal], der oprettes to objekter i din Azure AD-lejer: en application-objektet, og en tjeneste primært objekt.

#### <a name="application-object"></a>Application-objektet
Et Azure AD-program er *defineret* af dens én og kun application-objektet, som er placeret i den Azure AD-lejer, hvor programmet blev registreret, kaldes programmets "Privat" lejer. Objektet application identitet-relaterede oplysninger om et program, og er den skabelon, hvorfra dens tilsvarende service vigtigste objekter er *udledt* til brug på kørselstidspunktet. 

Du kan betragte programmer som den *globale* repræsentation af dit program (til brug på tværs af alle lejere) og service hovedstolen som den *lokale* repræsentation (til brug i en bestemt lejer). Azure AD Graph [programmet enhed] [ AAD-Graph-App-Entity] definerer skemaet til et objekt i programmet. Et programobjekt derfor har en 1:1 relation med programmet, og værdien 1:*n* relation med dens tilsvarende *n* service vigtigste objekter.

#### <a name="service-principal-object"></a>Tjenesten primært objekt
Serviceobjektet vigtigste definerer den politik og tilladelser for et program, give grundlaget for en sikkerhedskonto til at repræsentere programmet, når du åbner ressourcer på kørselstidspunktet. Azure AD Graph [ServicePrincipal enhed] [ AAD-Graph-Sp-Entity] definerer skemaet til en tjeneste primært objekt. 

En tjeneste primært objekt er påkrævet i hver lejer som en forekomst af programmets brugen skal repræsenteres, aktivere sikker adgang til ressourcer, der er ejet af brugerkonti fra lejeren. Et enkelt lejer program har kun én tjeneste hovedstolen (i dens privat lejer). En med flere lejer [webprogram](active-directory-dev-glossary.md#web-client) får også en tjeneste hovedstolen i hver lejer, hvor en administrator eller bruger(e) fra lejeren har givet samtykke, give tilladelse til at få adgang til deres ressourcer. Følge samtykke, vil blive hørt serviceobjektet vigtigste fremtidige godkendelse anmodninger om. 

> [AZURE.NOTE] De ændringer, du foretager i programmet på objektet, afspejles også i dens tjeneste primært objekt i programmets privat lejer kun (den lejer, hvor det blev registreret). For flere lejer programmer afspejles ændres til objektet application ikke i en hvilken som helst consumer lejere service vigtigste objekter, indtil consumer lejeren fjerner adgang og giver adgang igen.

## <a name="example"></a>Eksempel
I det følgende diagram viser forholdet mellem et program application-objektet, og tilsvarende service vigtigste objekter i forbindelse med et eksempel med flere lejer program, der hedder **HR app**. Der er tre Azure AD-lejere i dette scenarie: 

- **Adatum** - lejeren bruges af det firma, udviklet **HR app**
- **Contoso** - lejeren bruges af Contoso-organisation, som er en forbruger af **HR app**
- **Fabrikam** - lejeren bruges af Fabrikam-organisationen, der også forbruger **HR app**

![Relation mellem et programobjekt og en tjeneste primært objekt](./media/active-directory-application-objects/application-objects-relationship.png)

I det forrige diagram er trin 1 processen med at oprette programmet og service vigtigste objekter i programmets privat lejer.

I trin 2, når Contoso og Fabrikam Administratorer udfører samtykke, en tjeneste vigtigste objektet er oprettet i deres egen virksomhed Azure AD-lejer og tildelt de tilladelser, administratoren tildelt. Bemærk også, HR app kan være konfigureret/udviklet til at tillade accept af brugere til individuelle brug.

I trin 3 skal have consumer lejere af HR programmet (Contoso og Fabrikam) hver sin egen service primært objekt. Hver repræsenterer på deres brug af en forekomst af programmet på kørselstidspunktet, underlagt tilladelserne accepteret respektive-administratoren.

## <a name="next-steps"></a>Næste trin
Et program application-objektet kan åbnes via API Azure AD-graf, som repræsenteret af dens OData [programmet enhed][AAD-Graph-App-Entity]

Et program service primært objekt kan åbnes via API Azure AD-graf, som repræsenteret af dens OData [ServicePrincipal enhed][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com