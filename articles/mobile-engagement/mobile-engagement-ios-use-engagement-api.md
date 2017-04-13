<properties
    pageTitle="Hvordan du bruger API'EN aftale på iOS"
    description="Seneste iOS SDK - hvordan du bruger API'EN aftale på iOS"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>Hvordan du bruger API'EN aftale på iOS

Dette dokument er et tilføjelsesprogram til dokumentet, hvordan du kan integrere aftale på iOS: kan dybde oplysninger om, hvordan du bruger aftale API til at rapportere dit programstatistik.

Husk på, at hvis du kun vil aftale til at rapportere dit program sessioner, aktiviteter, går ned og tekniske oplysninger, derefter den nemmeste måde er at lave din egen `UIViewController` objekter, der nedarver fra det tilsvarende `EngagementViewController` klasse.

Hvis du vil udføre mere, for eksempel, hvis du vil rapportere bestemte programhændelser, fejl og job, eller hvis du har til at rapportere dit program aktiviteter i en anden måde, end det, implementeret i den `EngagementViewController` klasser, og derefter skal du bruge API'EN aftale.

Aftale API leveres af den `EngagementAgent` klasse. En forekomst af denne klasse kan hente den ved at ringe på `[EngagementAgent shared]` statisk metode (Bemærk, at den `EngagementAgent` objekt, der returneres er en separat).

Før API-kald den `EngagementAgent` objekt skal være initialiseret ved at ringe til metoden`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>Aftale begreber

Følgende dele afgræns de almindelige [Mobile aftale begreber](mobile-engagement-concepts.md) til iOS-platformen.

### <a name="session-and-activity"></a>`Session`og`Activity`

En *aktivitet* er som regel tilknyttet et skærmbillede af programmet, det vil sige *aktivitet* starter, når skærmen vises og stopper, når skærmen er lukket: det er tilfældet, når aftale SDK er integreret ved hjælp af den `EngagementViewController` klasser.

Men *aktiviteter* kan også styres manuelt ved hjælp af API'EN aftale. Dette giver mulighed for at dele en given skærm i flere underordnede dele til at få flere oplysninger om brugen af dette skærmbillede (for eksempel til kendte hvor ofte og hvor lang tid der bruges dialogbokse i dette skærmbillede).

##<a name="reporting-activities"></a>Rapportering aktiviteter

### <a name="user-starts-a-new-activity"></a>Bruger starter en ny aktivitet

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Du skal ringe til `startActivity()` hver gang brugeren aktivitet ændres. Det første opkald til denne funktion starter en ny brugersession.

### <a name="user-ends-his-current-activity"></a>Bruger slutter sin nuværende aktivitet

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Du skal ringe **aldrig** denne funktion ved dig selv, undtagen hvis du vil opdele en anvendelse af dit program til flere sessioner: et opkald til denne funktion vil afslutte den aktuelle session med det samme, så et efterfølgende opkald til `startActivity()` vil starte en ny session. Denne funktion kaldes automatisk af SDK, når dit program er lukket.

##<a name="reporting-events"></a>Rapportering begivenheder

### <a name="session-events"></a>Session begivenheder

Sessionshændelser er som regel anvendes til at rapportere de handlinger, der er udført af en bruger i sin session.

**Eksempel uden ekstra data:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Eksempel med ekstra data:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Enkeltstående begivenheder

I modsætning til session begivenheder kan enkeltstående begivenheder bruges uden for konteksten af en session.

**Eksempel:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>Rapportering af fejl

### <a name="session-errors"></a>Session fejl

Session fejl bruges normalt til at rapportere de fejl, som påvirker beskyttelsen brugeren i sin session.

**Eksempel:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Enkeltstående fejl

I modsætning til session fejl, kan bruges enkeltstående fejl uden for konteksten af en session.

**Eksempel:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>Rapportering job

**Eksempel:**

Antag, at du gerne vil rapportere varigheden af dit login-processen:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Rapportfejl under et Job

Fejl kan være relateret til et job, der kører i stedet for at være knyttet til den aktuelle brugersession.

**Eksempel:**

Antag, at du gerne vil rapportere en fejl under dit login-proces:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Hændelser under et job

Begivenheder kan være relateret til et job, der kører i stedet for at være knyttet til den aktuelle brugersession.

**Eksempel:**

Antag, at vi har et socialt netværk, og vi bruge et job til rapport den samlede tid, hvor brugeren har forbindelse til serveren. Brugeren kan modtage meddelelser fra sin venner, er det en sag begivenhed.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>Ekstra parametre

Vilkårlig data kan knyttes til begivenheder, fejl, aktiviteter og job.

Disse data kan struktureres, den anvender Ios's NSDictionary klasse.

Bemærk, at extras kan indeholde `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` eller andre `NSDictionary` forekomster.

> [AZURE.NOTE] Den ekstra parameter er serialiseres i JSON. Hvis du vil overføre forskellige objekter end dem, der er beskrevet ovenfor, skal du implementere følgende metode i klasse:
>
             -(NSString*)JSONRepresentation;
>
> Metoden skal returnere en JSON repræsentation af objektet.

### <a name="example"></a>Eksempel

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Begrænsninger

#### <a name="keys"></a>Taster

Hver nøgle i den `NSDictionary` skal svare til det følgende regulære udtryk:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Ekstra er begrænset til **1024** tegn per opkald (én gang kodet i JSON ved aftale agent).

I det forrige eksempel er sendt til serveren JSON 58 tegn:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Rapportering programoplysninger

Du kan manuelt rapport registrere oplysninger (eller andre programmet bestemte oplysninger) ved hjælp af den `sendAppInfo:` funktionen.

Bemærk, at disse oplysninger sendes trinvist: kun den sidste værdi til en bestemt nøgle bevares for en given enhed.

Sådan begivenhed extras, den `NSDictionary` klasse bruges til at abstrakt programoplysninger, noter, matrixer eller underordnede ordbøger bliver behandlet som flad strenge (ved hjælp af JSON serialisering).

**Eksempel:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Begrænsninger

#### <a name="keys"></a>Taster

Hver nøgle i den `NSDictionary` skal svare til det følgende regulære udtryk:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Programoplysninger er begrænset til **1024** tegn per opkald (én gang kodet i JSON ved aftale agent).

I det forrige eksempel er sendt til serveren JSON 44 tegn:

    {"birthdate":"1983-12-07","gender":"female"}
