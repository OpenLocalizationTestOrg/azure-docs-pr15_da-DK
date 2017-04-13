<properties
   pageTitle="Oversigt over internettet ting sikkerhed | Microsoft Azure"
   description=" Azure internet af ting (IoT) services indeholder et bredt udvalg af funktioner. I denne artikel hjælper dig med at forstå, hvordan du sikrer dine IoT løsninger i Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Oversigt over internettet ting sikkerhed

Azure internet af ting (IoT) services indeholder et bredt udvalg af funktioner. Bruge tjenesterne enterprise gør det muligt at:

- Indsaml data fra enheder
- Analysere data streams i-bevægelse
- Gemme og forespørgsel store datasæt
- Visualisere både realtid og historiske data
- Integrere med back office-systemer

Til at levere disse funktioner, Azure IoT pakke pakker sammen Azure tjenesterne med brugerdefinerede udvidelser som forudkonfigurerede løsninger. Disse forudkonfigurerede løsninger er grundlæggende almindelige IoT løsning mønstre, der hjælper med at reducere den tid, du bruger til at fremvise din IoT løsninger-installationer. Brug af IoT software development Kit, kan du tilpasse og udvide disse løsninger, så det opfylder dine egne krav. Du kan også bruge disse løsninger som eksempler eller skabeloner, når du udvikler nye IoT løsninger.

Azure IoT pakke er en effektiv løsning til dine IoT behov. Det er dog af upmost betydning, at dine IoT løsninger er designet med fokus fra starten på sikkerhed. På grund af alene antallet af IoT enheder, kan enhver sikkerhedshændelse, hurtigt blive en omfattende begivenhed med betydelige konsekvenser.

For at hjælpe dig med at forstå, hvordan du sikrer dine IoT løsninger, har vi følgende oplysninger.

## <a name="security-architecture"></a>Sikkerhedsarkitekturen

Når du udformer et system, er det vigtigt at forstå de potentielle risici til det pågældende system og tilføje relevante beskyttelse i overensstemmelse hermed, som er udviklet og udformet systemet. Det er særligt vigtig til at designe produktet fra starten med fokus på sikkerhed, fordi forstå, hvordan en hacker muligvis forringer et system gør sikker relevante afhjælpninger er på plads fra begyndelsen.

Du kan få mere at vide om IoT sikkerhedsarkitekturen ved at læse [Internet af sikkerhedsarkitekturen ting](../iot-suite/iot-security-architecture.md).

I denne artikel beskrives følgende emner:

- [Sikkerhed starter med en truslen Model](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Sikkerhed i IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [Truslen Modeling Azure IoT Reference arkitektur](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Sikkerhed fra bunden

IoT udgør entydige sikkerhed, beskyttelse af personlige oplysninger og overholdelse udfordringer til virksomheder i hele verden. I modsætning til traditionelle cyber teknologi, hvor problemerne drejer sig om software, og hvordan den implementeres, vedrører IoT Hvad sker der, når cyber og fysisk verdens konvergerer. Beskytte IoT løsninger kræver sikring af sikker klargøring af enheder, sikker forbindelse mellem disse enheder og skyen og sikker databeskyttelse i skyen under behandling og opbevaring. Arbejde med denne funktionalitet er dog begrænsede ressourcer enheder, geografiske fordeling af installationer og et stort antal enheder i en løsning.

Du kan lære at håndtere sikkerhed i disse områder ved at læse [Internet ting sikkerhed op fra grunden](../iot-suite/securing-iot-ground-up.md).

I artiklen beskrives følgende emner:

- [Sikker infrastruktur fra bunden](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure-sikker IoT infrastruktur til din virksomhed](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Bedste fremgangsmåder

Sikring af en IoT infrastruktur kræver en strenge strategi for sikkerhed i dybde. Start fra sikring af data i skyen, for at beskytte dataintegritet undervejs via offentlige internettet, og gør muligheden at klargøre sikkert enheder, opbygger hvert lag større sikkerhed assurance i den overordnede infrastruktur.

Du kan få at vide om Internet ting Sikkerhed bedste fremgangsmåder ved at læse [bedste fremgangsmåder for Internet ting sikkerhed](../iot-suite/iot-security-best-practices.md).

I artiklen beskrives følgende emner:

- [IoT hardware-producenten/integrator](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [IoT løsning udvikler](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [IoT løsning deployer](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [IoT løsning operator](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
