<properties
    pageTitle="Mobile aftale Eksportér API oversigt"
    description="Få grundlæggende oplysninger om at eksportere dine rækkedata, der genereres af din brugerens enheder til at udnytte det i dine egne værktøjer"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Mobile aftale Eksportér API oversigt

## <a name="introduction"></a>Introduktion

I dette dokument, skal du lære grundlæggende om at eksportere dine rækkedata, der genereres af din brugerens enheder til at udnytte det i dine egne værktøjer.

## <a name="pre-requisites"></a>Forudsætninger

Eksportere den rækkedata fra Mobile aftale kræver:

- API godkendelse konfiguration for at kunne bruge de API'er (se [godkendelse manuel konfiguration](mobile-engagement-api-authentication-manual.md)),
- Brug enten REST API'er eller [.net SDK](mobile-engagement-dotnet-sdk-service-api.md)
- En konto, Azure-lager.

>[AZURE.NOTE] Vi også oplyse fremragende [Microsoft Azure lagerplads Explorer](http://storageexplorer.com/)mindst i udviklingsfasen som den indeholder en brugervenlig brugergrænseflade til arbejde med Azure-lager.

## <a name="what-can-be-exported"></a>Hvad kan eksporteres?

Mobile aftale gør det muligt for brugerne at indsamle mange typer data, og derfor har flere typer Eksportér er tilpasset til disse forskellige datatyper.
Der er 2 væsentlige typer Eksportér:

- Øjebliksbillede: bruges typisk for at eksportere data, der repræsenterer en tilstand og Mobile aftale, ikke har en oversigt. Dette omfatter tokens eller push kampagnens feedback mærker (app-info), f.eks. Derfor er disse typer Eksportér ikke relateret til en dato.
- historiske: denne Eksportér type bruges til data, der indsamles over tid som begivenheder eller aktiviteter f.eks.

Tabellen nedenfor beskrives omfattende alle de mulige Eksporter:

| Eksportere Type | Datatype | Beskrivelse                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Øjebliksbillede    | Push      | Genererer en eksport af Push kampagner feedback på grundlag af per deviceid/bruger-id                                                              |
| Øjebliksbillede    | Mærke       | Genererer en eksport af mærker (app-info), der er knyttet til hver enheder                                                                       |
| Øjebliksbillede    | Enhed    | Genererer en eksport af de fleste af data om enheder som technicals (model, landestandard, tidszone,...), mærker, vist første gang... |
| Øjebliksbillede    | Token     | Genererer en eksport af alle gyldige tokens                                                                                                 |
| Historiske  | Aktivitet  | Genererer en eksport af alle aktiviteterne for hver enheder på en given periode                                                           |
| Historiske  | Begivenhed     | Genererer en eksport af alle aktiviteterne for hver enheder på en given periode                                                           |
| Historiske  | Job       | Genererer en eksport af alle job for hver enheder på en given periode                                                                 |
| Historiske  | Fejl     | Genererer en eksport af alle fejl for hver enheder på en given periode                                                               |

## <a name="how-does-it-work"></a>Hvordan fungerer det?

Eksporter er lange kører opgaver, som kan give store datafiler. Derfor kan de ikke aktiveres for at vende tilbage med det samme en fil til overførsel.
For at eksportere data fra Mobile aftale, nødt du til at oprette et **Eksportere Job** via API hvor du kan angive Generelt:

- Typen Eksportér (snapshot eller historiske)
- Datatypen
- **Azure-objektbeholder til lagring** (herunder en gyldig SAS med skriveadgang) hvor resultatet af eksporten bliver skrevet.

Bemærk, at det kan tage et par minutter til jobbet skal startes, og derefter den kan køre fra et par sekunder til lille apps til flere timer til apps med mange brugere eller aktivitet.

Når jobbet er oprettet, er det muligt at kontrollere dens status for at se, om det stadig er aktiv, eller hvis den er fuldført.

Når jobbet er fuldført, er den resulterende datafil tilgængelig på objektbeholderen medfølgende lagerplads.
