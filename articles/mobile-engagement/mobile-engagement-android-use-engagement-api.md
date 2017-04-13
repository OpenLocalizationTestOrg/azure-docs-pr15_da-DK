<properties
    pageTitle="Sådan bruger du aftale API på Android-enhed"
    description="Seneste Android SDK - hvordan du bruger aftale API på Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>Sådan bruger du aftale API på Android-enhed

Dette dokument er et tilføjelsesprogram til [rapportering af avancerede indstillinger for Android Mobile aftale SDK](mobile-engagement-android-advanced-reporting.md)-dokumentet. Den indeholder dybde oplysninger om, hvordan du bruger aftale API til at rapportere dit programstatistik.

Husk på, at hvis du kun vil aftale til at rapportere dit program sessioner, aktiviteter, går ned og tekniske oplysninger, derefter den nemmeste måde er at få alle dine `Activity` underordnede klasser nedarver fra det tilsvarende `EngagementActivity` klasse.

Hvis du vil udføre mere, for eksempel, hvis du vil rapportere bestemte programhændelser, fejl og job, eller hvis du har til at rapportere dit program aktiviteter i en anden måde, end det, implementeret i den `EngagementActivity` klasser, og derefter skal du bruge API'EN aftale.

Aftale API leveres af den `EngagementAgent` klasse. En forekomst af denne klasse kan hente den ved at ringe på `EngagementAgent.getInstance(Context)` statisk metode (Bemærk, at den `EngagementAgent` objekt, der returneres er en separat).

##<a name="engagement-concepts"></a>Aftale begreber

Følgende dele afgræns de almindelige [Mobile aftale begreber](mobile-engagement-concepts.md), til Android-platformen.

### <a name="session-and-activity"></a>`Session`og`Activity`

Hvis brugeren bliver mere end et par sekunder inaktiv mellem to *aktiviteter*, er sin sekvens af *aktiviteter* opdelt i to forskellige *sessioner*. Disse par sekunder kaldes "Sessionstimeout".

En *aktivitet* er som regel tilknyttet et skærmbillede af programmet, det vil sige *aktivitet* starter, når skærmen vises og stopper, når skærmen er lukket: det er tilfældet, når aftale SDK er integreret ved hjælp af den `EngagementActivity` klasser.

Men *aktiviteter* kan også styres manuelt ved hjælp af API'EN aftale. Dette giver mulighed for at dele en given skærm i flere underordnede dele til at få flere oplysninger om brugen af dette skærmbillede (for eksempel til kendte hvor ofte og hvor lang tid der bruges dialogbokse i dette skærmbillede).

##<a name="reporting-activities"></a>Rapportering aktiviteter

> [AZURE.IMPORTANT] Du behøver ikke at rapport aktiviteter som beskrevet i dette afsnit, hvis du bruger den `EngagementActivity` klasse og dens varianter som beskrevet i afsnittet Sådan integrere aftale på Android dokument.

### <a name="user-starts-a-new-activity"></a>Bruger starter en ny aktivitet

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Du skal ringe til `startActivity()` hver gang brugeren aktivitet ændres. Det første opkald til denne funktion starter en ny brugersession.

Det bedste sted til at ringe til denne funktion er på hver aktivitet `onResume` tilbagekald.

### <a name="user-ends-his-current-activity"></a>Bruger slutter sin nuværende aktivitet

            EngagementAgent.getInstance(this).endActivity();

Du skal ringe til `endActivity()` mindst én gang, når brugeren afsluttes sin sidste aktivitet. Aftale SDK oplyser, at brugeren er i øjeblikket inaktiv, og, brugersessionen skal afsluttes en gang timeout for session udløber (Hvis du kalder `startActivity()` før timeout for session udløber, sessionen genoptages blot).

Det bedste sted til at ringe til denne funktion er på hver aktivitet `onPause` tilbagekald.

##<a name="reporting-events"></a>Rapportering begivenheder

### <a name="session-events"></a>Session begivenheder

Sessionshændelser er som regel anvendes til at rapportere de handlinger, der er udført af en bruger i sin session.

**Eksempel uden ekstra data:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Eksempel med ekstra data:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Enkeltstående begivenheder

I modsætning til session begivenheder kan enkeltstående hændelser indtræffer uden for konteksten af en session.

**Eksempel:**

Antag, at du vil rapport hændelser når en broadcast modtager udløses:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Rapportering af fejl

### <a name="session-errors"></a>Session fejl

Session fejl bruges normalt til at rapportere de fejl, som påvirker beskyttelsen brugeren i sin session.

**Eksempel:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Enkeltstående fejl

I modsætning til session fejl kan opstå enkeltstående fejl uden for konteksten af en session.

**Eksempel:**

I følgende eksempel viser, hvordan til at rapportere en fejl, når hukommelsen bliver lav på telefonen, mens din ansøgningsprocedure kører.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Rapportering job

### <a name="example"></a>Eksempel

Antag, at du gerne vil rapportere varigheden af dit login-processen:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Rapportfejl under et Job

Fejl kan være relateret til et job, der kører i stedet for at være knyttet til den aktuelle brugersession.

**Eksempel:**

Antag, at du vil rapportere en fejl under du logger på processen:

[...] offentlige annullere logon (kontekst kontekst,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Rapportering begivenheder under et job

Begivenheder kan være relateret til et job, der kører i stedet for at være knyttet til den aktuelle brugersession.

**Eksempel:**

Antag, at vi har et socialt netværk, og vi bruge et job til rapport den samlede tid, hvor brugeren har forbindelse til serveren. Brugeren kan holde forbindelsen i baggrunden lige når hun bruger et andet program, eller når telefonen sovende, så der er ingen session.

Brugeren kan modtage meddelelser fra sin venner, er det en sag begivenhed.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Ekstra parametre

Vilkårlig data kan knyttes til begivenheder, fejl, aktiviteter og job.

Disse data kan struktureres, den anvender Android-enheds samlet klasse (faktisk det fungerer ligesom ekstra parametre i Android gengivelser). Bemærk, at en samlet kan indeholde, matrixer eller en anden samlet forekomster.

> [AZURE.IMPORTANT] Hvis du placerer i parcelable eller serialiserbar parametre, skal du kontrollere deres `toString()` metode er implementeret for at returnere en læsbar streng. Serialiserbar klasser, der indeholder ikke midlertidige felter, der ikke er serialiserbar får Android går ned, når du ringer`bundle.putSerializable("key",value);`

> [AZURE.WARNING] Sparse matrixer i ekstra parametre understøttes ikke, det vil sige, den serialiseret ikke som en matrix. Du skal konvertere dem til standard matrixer inden du bruger det i ekstra parametre.

### <a name="example"></a>Eksempel

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Begrænsninger

#### <a name="keys"></a>Taster

Hver nøgle i den `Bundle` skal svare til det følgende regulære udtryk:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Ekstra er begrænset til **1024** tegn per opkald (én gang kodet i JSON af tjenesten aftale).

I det forrige eksempel er sendt til serveren JSON 58 tegn:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Rapportering programoplysninger

Du kan manuelt rapport registrere oplysninger (eller andre programmet bestemte oplysninger) ved hjælp af den `sendAppInfo()` funktionen.

Bemærk, at disse oplysninger sendes trinvist: kun den sidste værdi til en bestemt nøgle bevares for en given enhed.

Sådan begivenhed extras bruges klassen samlet abstrakt programoplysninger, skal du bemærke, matrixer eller underordnede pakker bliver behandlet som flad strenge (ved hjælp af JSON serialisering).

### <a name="example"></a>Eksempel

Her er et eksempel på-kode til at sende bruger køn og Fødselsdato:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Begrænsninger

#### <a name="keys"></a>Taster

Hver nøgle i den `Bundle` skal svare til det følgende regulære udtryk:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Programoplysninger er begrænset til **1024** tegn per opkald (én gang kodet i JSON af tjenesten aftale).

I det forrige eksempel er sendt til serveren JSON 44 tegn:

            {"expiration":"2016-12-07","status":"premium"}
