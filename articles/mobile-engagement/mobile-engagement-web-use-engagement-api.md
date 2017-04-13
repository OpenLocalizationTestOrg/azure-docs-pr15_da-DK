<properties
    pageTitle="Azure Mobile aftale Web SDK API'er | Microsoft Azure"
    description="De seneste opdateringer og procedurer for Web SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Brug Azure Mobile aftale API i et webprogram

Dette dokument er en tilføjelse til det dokument, kan du se, hvordan du [integrerer Mobile aftale i et webprogram](mobile-engagement-web-integrate-engagement.md). Den indeholder detaljerede oplysninger om, hvordan du bruger Azure Mobile aftale API til at rapportere dit programstatistik.

Mobile aftale API leveres af den `engagement.agent` objekt. Standard Azure Mobile aftale Web SDK alias er `engagement`. Du kan omdefinere dette alias fra SDK konfigurationen.

## <a name="mobile-engagement-concepts"></a>Mobile aftale begreber

Følgende dele Afgræns almindelige [Mobile aftale begreber](mobile-engagement-concepts.md) til web-platformen.

### <a name="session-and-activity"></a>`Session`og`Activity`

Hvis brugeren forbliver inaktiv i mere end et par sekunder mellem to aktiviteter, er brugerens sekvens af aktiviteter opdelt i to forskellige sessioner. Disse par sekunder kaldes timeout for session.

Hvis dit program ikke erklære slutningen af brugeraktiviteter alene (ved at ringe på `engagement.agent.endActivity` funktionen), Mobile aftale serveren automatisk udløber brugersessionen i tre minutter, efter at programmets side er lukket. Dette kaldes Sessionstimeout server.

### `Crash`

Automatiseret rapporter over en ikke-opfanget JavaScript undtagelser oprettes ikke som standard. Dog kan du rapportere nedbrud manuelt ved hjælp af den `sendCrash` funktionen (se afsnittet på rapportering går ned).

## <a name="reporting-activities"></a>Rapportering aktiviteter

Rapportering om brugeraktivitet omfatter, når en bruger starter en ny aktivitet, og når brugeren stopper den aktuelle aktivitet.

### <a name="user-starts-a-new-activity"></a>Bruger starter en ny aktivitet

    engagement.agent.startActivity("MyUserActivity");

Du skal ringe til `startActivity()` hver gang brugeraktivitet ændres. Det første opkald til denne funktion starter en ny brugersession.

### <a name="user-ends-the-current-activity"></a>Bruger afslutter den aktuelle aktivitet

    engagement.agent.endActivity();

Du skal ringe til `endActivity()` mindst én gang, når brugeren afslutter deres sidste aktivitet. Mobile aftale Web SDK oplyser, at brugeren er i øjeblikket er inaktiv, og, brugersessionen skal lukkes, når Sessionstimeout udløber. Hvis du kalder `startActivity()` før timeout for session udløber, sessionen genoptages blot.

Fordi der ikke er nogen pålidelig opkaldet for når navigator-vinduet lukkes, er det ofte, svært eller umuligt til at opfange slutningen af brugeraktiviteter i et internetbaseret miljø. Det er grunden Mobile aftale serveren automatisk udløber brugersessionen i tre minutter, efter at programmets side er lukket.

## <a name="reporting-events"></a>Rapportering begivenheder

Rapportering om begivenheder dækker session begivenheder og enkeltstående begivenheder.

### <a name="session-events"></a>Session begivenheder

Session begivenheder bruges normalt til at rapportere de handlinger, der er udført af en bruger i brugerens session.

**Eksempel uden ekstra data:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Eksempel med ekstra data:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Enkeltstående begivenheder

I modsætning til session begivenheder kan enkeltstående hændelser indtræffer uden for konteksten af en session.

I dette tilfælde skal du bruge ``engagement.agent.sendEvent`` i stedet for ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Rapportering af fejl

Rapportering af fejl dækker session fejl og enkeltstående fejl.

### <a name="session-errors"></a>Session fejl

Session fejl bruges normalt til at rapportere de fejl, der har indflydelse på brugeren i brugerens session.

**Eksempel uden ekstra data:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Eksempel med ekstra data:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Enkeltstående fejl

I modsætning til session fejl kan opstå enkeltstående fejl uden for konteksten af en session.

I dette tilfælde skal du bruge `engagement.agent.sendError` i stedet for `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Rapportering job

Rapportering om job omslag rapportering af fejl og hændelser, der opstår under et job, og -rapportering går ned.

**Eksempel:**

Hvis du vil overvåge anmodning om en AJAX, skal bruge du følgende:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Rapportering af fejl under et job

Fejl kan være relateret til et job, der kører i stedet for i den aktuelle brugersession.

**Eksempel:**

Hvis du vil rapportere en fejl, hvis en anmodning om en AJAX mislykkes:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Rapportering begivenheder under et job

Begivenheder kan være relateret til et job, der kører i stedet for i den aktuelle brugersession takket den `engagement.agent.sendJobEvent` funktionen.

Denne funktion fungerer præcis ønsker `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Rapportering går ned

Brug den `sendCrash` funktionen til rapport går ned manuelt.

Den `crashid` argument er en streng, der identificerer typen gå ned.
Den `crash` argument er som regel Stakspor nedbrud som en streng.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Ekstra parametre

Du kan vedhæfte vilkårlig data til en begivenhed, fejl, aktivitet eller job.

Dataene kan være JSON objekter (men ikke en matrix eller enkle type).

**Eksempel:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Begrænsninger

Begrænsninger, der gælder for ekstra parametre er i områderne af regulære udtryk for taster, værdityper og størrelse.

#### <a name="keys"></a>Taster

Hver nøgle i objektet skal svare til det følgende regulære udtryk:

    ^[a-zA-Z][a-zA-Z_0-9]*

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="values"></a>Værdier

Værdier er begrænset til streng, tal og booleske typer.

#### <a name="size"></a>Størrelse

Ekstra er begrænset til 1.024 tegn per opkald (når Mobile aftale Web SDK koder den i JSON).

## <a name="reporting-application-information"></a>Rapportering programoplysninger

Du kan manuelt rapportere registrering oplysninger (eller andre programmer-specifikke oplysninger) ved hjælp af den `sendAppInfo()` funktionen.

Bemærk, at disse oplysninger sendes trinvist. Kun den sidste værdi for en bestemt nøgle bevares for en bestemt enhed.

Du kan bruge JSON objekter til abstrakt programoplysninger som begivenhed extras. Bemærk, at matrixer eller underordnede objekter behandles som flad strenge (ved hjælp af JSON serialisering).

**Eksempel:**

Her er en kodeeksempel for at sende brugerens køn og Fødselsdato:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Begrænsninger

Begrænsninger, der gælder for følgende oplysninger om er i områderne af regulære udtryk for nøgler og størrelse.

#### <a name="keys"></a>Taster

Hver nøgle i objektet skal svare til det følgende regulære udtryk:

    ^[a-zA-Z][a-zA-Z_0-9]*

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Programoplysninger er begrænset til 1.024 tegn per opkald (når Mobile aftale Web SDK koder den i JSON).

I det foregående eksempel er sendt til serveren JSON 44 tegn:

    {"birthdate":"1983-12-07","gender":"female"}
