<properties
   pageTitle="Programmet eller brugerdefinerede Marathon tjenesten | Microsoft Azure"
   description="Oprette et program eller brugerdefinerede Marathon service"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Beholdere, Marathon, Micro-tjenester, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Oprette et program eller brugerdefinerede Marathon service

Azure objektbeholder tjenesten indeholder et sæt master servere, som vi forudkonfigurere Apache Mesos og Marathon. Disse kan bruges til at dirigerer dine programmer på klyngen, men det er bedst ikke at bruge de overordnede servere hertil. For eksempel tilpasse konfigurationen af Marathon kræver Sådan logger du ind på master-serverne og foretage ændringer – dette tilskynder til entydige master servere, der er en smule anderledes end standarden og skal behandles og administreres uafhængigt. Konfigurationen kræves, før et team kan desuden ikke være optimal konfigurationen til et andet team.

I denne artikel gennemgår vi Sådan føjer du et program eller en bruger-specifikke Marathon tjeneste.

Da denne tjeneste skal høre til en enkelt bruger eller gruppe, er de gratis at konfigurere den på nogen måde, som de ønsker. Azure objektbeholder tjenesten sikrer også, at tjenesten fortsætter med at køre. Hvis den tjeneste, genstart Azure objektbeholder tjenesten det for dig. De fleste tilfælde kan du ikke selv se den havde nedetid.

## <a name="prerequisites"></a>Forudsætninger

[Implementer en forekomst af Azure objektbeholder tjenesten](container-service-deployment.md) med orchestrator type DC/OS, og [Sørg for, at din klient kan oprette forbindelse til din klynge](container-service-connect.md). Benyt følgende fremgangsmåde.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Oprette et program eller brugerdefinerede Marathon service

Starte med at oprette en JSON-konfigurationsfil, der definerer navnet på tjenesten programmet, som du vil oprette. Her vi bruge `marathon-alice` som framework navnet. Gem filen som noget i retning af `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Derefter Brug DC/OS CLI til at installere Marathon forekomsten med de indstillinger, der er angivet i din konfigurationsfil:

```bash
dcos package install --options=marathon-alice.json marathon
```

Du bør nu se din `marathon-alice` tjeneste, der kører under fanen tjenester i din DC/OS brugergrænseflade. Brugergrænsefladen bliver `http://<hostname>/service/marathon-alice/` Hvis du vil have adgang til den direkte.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Angive DC/OS CLI til at få adgang til tjenesten

Du kan vælge at konfigurere din DC/OS CLI for at få adgang til denne nye tjeneste ved at angive den `marathon.url` egenskab til at pege på den `marathon-alice` forekomst på følgende måde:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Du kan kontrollere, hvilken forekomst af Marathon, som din CLI arbejder mod med den `dcos config show` kommandoen. Du kan vende tilbage til at bruge din masterside Marathon tjeneste med kommandoen `dcos config unset marathon.url`.
