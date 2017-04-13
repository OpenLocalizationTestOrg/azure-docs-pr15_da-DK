<properties
   pageTitle="Ikke-teknisk forudsætninger for oprettelse af et tilbud på Azure Marketplace | Microsoft Azure"
   description="Forstå kravene til oprettelse og implementere et tilbud til Azure Marketplace for andre at købe."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Generelle forudsætninger for oprettelse af et tilbud på Azure Marketplace
Forstå de generelle, business-proces centreret forudsætninger, der er nødvendige for at gå videre med processen til oprettelse af tilbud.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Sørg for, at du er registreret som en sælger med Microsoft
Mere detaljerede oplysninger om registrering af en sælger konto med Microsoft, gå til [oprettelse af en konto og registrering](marketplace-publishing-accounts-creation-registration.md).

- **Hvis din virksomhed allerede er registreret som en sælger i den Udviklercenter og du vil oprette et nyt tilbud,** og derefter logge på udgivelse portalen med samme e-mail-id med hvilken Udviklercenter registrering er færdig. Dette trin er påkrævet, så portalen Udviklercenter og publicering er sammenkædet med hinanden.
- **Hvis din virksomhed allerede er registreret som en sælger i den Udviklercenter, og du vil redigere et eksisterende tilbud,** og klik derefter enten at logge på udgivelse portalen med administratorkonto eller med en konto, som er tilføjet som en co-administrator i udgivelse portal. Trin til at tilføje en co-administrator er angivet nedenfor.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Trin til at tilføje en co-administrator i portalen publicering
Administratorer af udgivelse portal kan tilføje de andre medlemmer på firmaet, der arbejder på programmet, som en co-administrator i udgivelse portal. **Hvis resultatet af, at du er administrator,** nedenstående er trinnene for at tilføje en co-administrator.

>[AZURE.NOTE] For nye brugere, før du tilføjer en co-administrator i udgivelse portal, sikre, at du har oprettet mindst ét program i udgivelse portal. Dette er påkrævet, som fanen **UDGIVERE** , der vises kun efter oprettelse af mindst ét program i udgivelse portal.

1. Sikre, at co-administrator e-mail-id'et er en Microsoft-account(MSA). Hvis ikke, registrere den som en MSA ved hjælp af dette [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Sørg for, at der er mindst ét program under kontoen Administrator, før du forsøger at tilføje en co-administrator.
3. Når ovenstående trin er færdigt, skal logge på udgivelse portalen med co-administrator e-mail-id og derefter på log af.
4. Nu logon til udgivelse portalen med administrator e-mail-id.
5. Gå til udgivere -> Vælg din konto -> administratorer -> Tilføj co-administratoren (skærmbillede givet nedenfor)

    ![tegning](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Sørg for, at der skal overvåges mail-id'er, der er angivet i de forskellige faser i en udgivelsesproces (fx Udviklercenter, publicere portal) for al kommunikation fra Microsoft.
7. Undgå at bruge en konto, der er knyttet til en enkelt person til Udviklercenter registrering. Dette er forslag til fjernelse af afhængighed fra én person.
8. Hvis du står over for eventuelle problemer med Udviklercenter registrering og derefter skal du hæve en brugertilladelse ved hjælp af dette [link](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Trin til at slette en co-administrator i udgivelse portal
**Hvis resultatet af, at du er administrator,** nedenstående er trinnene for at slette en co-administrator.

1. Logge på udgivelse portalen med administrator e-mail-id.
2. Gå til **Udgivere** -> Vælg din konto -> **Administratorer** -> **Flere administratorer**.
3. Klik på knappen **X** ud for den co-administrator, du vil Forsikringssummer i alt Slet (skærmbillede givet nedenfor).

    ![tegning](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] Du behøver ikke at udføre firmaoplysninger moms og bank, hvis du planlægger at udgive kun gratis tilbud (eller tage dine egne licens).

> Firmaets registrering skal udføres for at komme i gang. Dog mens din virksomhed arbejder på moms og bank oplysningerne i kontoen Microsoft Developer, udviklerne kan begynde at arbejde på at oprette virtuelt billedet i [Udgivelsesportal](https://publish.windowsazure.com)få den certificeret, og teste det i det Azure midlertidige miljø. Du skal fuldført sælgeren konto godkendelse kun for det sidste trin at publicere dit tilbud til Azure Marketplace.

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Få fat på en Azure "overkommelige" abonnement
Dette er det abonnement, du vil bruge til at oprette billederne VM og dele dem over billederne til [Azure Marketplace](https://azure.microsoft.com/marketplace/). Hvis du ikke har et eksisterende abonnement, derefter skal du tilmelde dig på https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Sælge fra" lande
> [AZURE.WARNING]
For at sælge dine tjenester på Azure Marketplace, skal du sikre dig, at din registrerede enhed stammer fra én af landene, der er godkendt "sælge fra". Denne begrænsning gælder for udbetaling og afgifter årsager. Vi leder aktivt til at udvide denne liste over lande inden for kort tid, så Vær klar. Du kan finde den komplette liste sektion 1 b med [Azure Marketplace deltagelse politikker](http://go.microsoft.com/fwlink/?LinkID=526833).

## <a name="next-steps"></a>Næste trin
Når de ikke-teknisk forudsætninger er opfyldt, du næste er tilbuddet bestemte tekniske forudsætninger. Klik på linket til artiklen respektive tilbud type, du vil oprette på Azure Marketplace.

- [VM tekniske forudsætninger](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Løsning skabelon tekniske forudsætninger](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Se også
- [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
