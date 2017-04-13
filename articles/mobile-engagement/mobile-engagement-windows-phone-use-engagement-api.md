<properties 
    pageTitle="Hvordan du bruger aftale API på Windows Phone Silverlight" 
    description="Hvordan du bruger aftale API på Windows Phone Silverlight"    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" /> 

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Hvordan du bruger aftale API på Windows Phone Silverlight

Dette dokument er et tilføjelsesprogram til dokumentet, [hvordan du integrerer Mobile aftale i din Windows Phone Silverlight-app](mobile-engagement-windows-phone-integrate-engagement.md). Den indeholder dybde oplysninger om, hvordan du bruger aftale API til at rapportere dit programstatistik.

Hvis du kun vil aftale til at rapportere dit program sessioner, aktiviteter, går ned og tekniske oplysninger, er den nemmeste måde at gøre alle dine `PhoneApplicationPage` underordnede klasser nedarver fra den `EngagementPage` klasse.

Hvis du vil udføre mere, for eksempel, hvis du vil rapportere bestemte programhændelser, fejl og job, eller hvis du har til at rapportere dit program aktiviteter i en anden måde, end det, implementeret i den `EngagementPage` klasser, og derefter skal du bruge API'EN aftale.

Aftale API leveres af den `EngagementAgent` klasse. Du kan få adgang til disse metoder gennem `EngagementAgent.Instance`.

Selvom modulet agent ikke er blevet initialiseret, hver opkald til API er udskudt og udføres igen, når agenten er tilgængelig.

##<a name="engagement-concepts"></a>Aftale begreber

Følgende dele Afgræns Mobile aftale begreber til Windows Phone-platformen.

### <a name="session-and-activity"></a>`Session`og`Activity`

En *aktivitet* er som regel tilknyttet én side af programmet, det vil sige *aktivitet* starter, når siden vises og stopper, når siden er lukket: det er tilfældet, når aftale SDK er integreret ved hjælp af den `EngagementPage` klasse.

Men *aktiviteter* kan også styres manuelt ved hjælp af API'EN aftale. Dette giver mulighed for at dele en given side i flere underordnede dele til at få flere oplysninger om brugen af denne side (for eksempel til kendte hvor ofte og hvor lang tid der bruges dialogbokse i denne side).

##<a name="reporting-activities"></a>Rapportering aktiviteter

### <a name="user-starts-a-new-activity"></a>Bruger starter en ny aktivitet

#### <a name="reference"></a>Reference

            void StartActivity(string name, Dictionary<object, object> extras = null)

Du skal ringe til `StartActivity()` hver gang brugeren aktivitet ændres. Det første opkald til denne funktion starter en ny brugersession.

> [AZURE.IMPORTANT] SDK kalde metoden EndActivity automatisk, når programmet er lukket. Dermed, det anbefales at kalde metoden StartActivity, når aktiviteten i bruger ændres, og til aldrig kalde metoden EndActivity, da denne metode tvinger den aktuelle session skal afsluttes.

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Bruger slutter sin nuværende aktivitet

#### <a name="reference"></a>Reference

            void EndActivity()

Du skal ringe til `EndActivity()` mindst én gang, når brugeren afsluttes sin sidste aktivitet. Aftale SDK oplyser, at brugeren er i øjeblikket inaktiv, og, brugersessionen skal afsluttes en gang timeout for session udløber (Hvis du kalder `StartActivity()` før timeout for session udløber, sessionen blot fortsat).

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Rapportering job

### <a name="start-a-job"></a>Starte et job

#### <a name="reference"></a>Reference

            void StartJob(string name, Dictionary<object, object> extras = null)

Du kan bruge jobbet til at registrere visse opgaver over en periode.

#### <a name="example"></a>Eksempel

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Afslutte et job

#### <a name="reference"></a>Reference

            void EndJob(string name)

Så snart en opgave, registreres i en sag er afsluttet, skal du ringe metoden EndJob for denne sag ved angivelse af navnet på jobbet.

#### <a name="example"></a>Eksempel

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Rapportering begivenheder

Der findes tre typer hændelser:

-   Enkeltstående begivenheder
-   Session begivenheder
-   Jobbet begivenheder

### <a name="standalone-events"></a>Enkeltstående begivenheder

#### <a name="reference"></a>Reference

            void SendEvent(string name, Dictionary<object, object> extras = null)

Enkeltstående begivenheder kan opstå uden for konteksten af en session.

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Session begivenheder

#### <a name="reference"></a>Reference

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Sessionshændelser er som regel anvendes til at rapportere de handlinger, der er udført af en bruger i sin session.

#### <a name="example"></a>Eksempel

**Uden data:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Med data:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Jobbet begivenheder

#### <a name="reference"></a>Reference

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Jobstatus hændelser er som regel anvendes til at rapportere de handlinger, der er udført af en bruger under et Job.

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Rapportering af fejl

Der findes tre typer fejl:

-   Enkeltstående fejl
-   Session fejl
-   Jobstatus fejl

### <a name="standalone-errors"></a>Enkeltstående fejl

#### <a name="reference"></a>Reference

            void SendError(string name, Dictionary<object, object> extras = null)

I modsætning til session fejl kan opstå enkeltstående fejl uden for konteksten af en session.

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Session fejl

#### <a name="reference"></a>Reference

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Session fejl bruges normalt til at rapportere de fejl, som påvirker beskyttelsen brugeren i sin session.

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Jobstatus fejl

#### <a name="reference"></a>Reference

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Fejl kan være relateret til et job, der kører i stedet for at være knyttet til den aktuelle brugersession.

#### <a name="example"></a>Eksempel

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Rapportering går ned

Denne tilbyder to metoder til at håndtere går ned.

### <a name="send-an-exception"></a>Sende en undtagelse

#### <a name="reference"></a>Reference

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Eksempel

Du kan sende en undtagelse til enhver tid ved at ringe til:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Du kan også bruge en valgfri parameter aftale session på samme tid end at sende et nedbrud skal afsluttes. Ringe til for at gøre det:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Hvis du gør dette, lukkes den session og -job lige efter du har sendt i går ned.

### <a name="send-an-unhandled-exception"></a>Sende en ikke-afviklet undtagelse

#### <a name="reference"></a>Reference

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Aftale indeholder også en metode til at sende ikke-afviklet undtagelser. Dette er især nyttig, når den bruges i silverlight UnhandledException hændelseshandler.

Denne metode er **altid** Afslut aftale session og job efter bliver kaldt.

#### <a name="example"></a>Eksempel

Du kan bruge det til at implementere din egen UnhandledException handler (især hvis du har deaktiveret den automatiske nedbrud rapportering funktion i aftale). For eksempel i den `Application_UnhandledException` metode til at den `App.xaml.cs` fil:

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Reference

            void OnActivated(ActivatedEventArgs e)

Når brugeren navigerer Videresend, væk fra et program, når hændelsen deaktiveret opløftes, forsøger operativsystemet at sætte programmet i tilstanden passive. Programmet er derefter Tombstoning. I denne proces et program er afsluttet, men nogle data om tilstanden for programmet og de enkelte sider i programmet bevares.

Du skal indsætte `EngagementAgent.Instance.OnActivated(e)` i den `Application_Activated` metode fra filen App.xaml.cs til at nulstille den aftale Agent, når programmet er blevet Tombstoned.

### <a name="example"></a>Eksempel

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Enheds-Id

            String GetDeviceId()

Ved at ringe til denne metode kan du få aftale enheds-id.

##<a name="extras-parameters"></a>Ekstra parametre

Vilkårlig data kan knyttes til en begivenhed, en fejl, en aktivitet eller et job. Disse data kan struktureres ved hjælp af en ordbog. Nøgler og værdier kan være af enhver type.

Ekstra data er serialiseret, så hvis du vil indsætte din egen type i extras du behøver at tilføje en datakontrakt for denne type.

### <a name="example"></a>Eksempel

Vi oprette en ny klasse "Person".

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Derefter vi tilføjer en `Person` forekomst til en ekstra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Hvis du placerer andre typer objekter, skal du kontrollere deres ToString() metode er implementeret for at returnere en human læsbare streng.

### <a name="limits"></a>Begrænsninger

#### <a name="keys"></a>Taster

Hver nøgle i objektet skal svare til det følgende regulære udtryk:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Ekstra er begrænset til **1024** tegn per opkald.

##<a name="reporting-application-information"></a>Rapportering programoplysninger

### <a name="reference"></a>Reference

            void SendAppInfo(Dictionary<object, object> appInfos)

Du kan manuelt rapportere registrering oplysninger (eller andre programmet bestemte oplysninger) ved hjælp af SendAppInfo() funktionen.

Bemærk, at disse oplysninger sendes trinvist: kun den sidste værdi til en bestemt nøgle bevares for en given enhed. Bruge en ordbog som begivenhed extras\<objekt, objekt\> vedhæfte oplysninger.

### <a name="example"></a>Eksempel

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Begrænsninger

#### <a name="keys"></a>Taster

Hver nøgle i objektet skal svare til det følgende regulære udtryk:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Det betyder, at nøgler skal starte med mindst én bogstav, efterfulgt af bogstaver, tal eller understregningstegn (\_).

#### <a name="size"></a>Størrelse

Programoplysninger er begrænset til **1024** tegn per opkald.

I det forrige eksempel er sendt til serveren JSON 44 tegn:

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Logføring
###<a name="enable-logging"></a>Aktivere logføring

SDK kan konfigureres for at frembringe test logge i ide-konsollen.
Disse logfiler er ikke aktiveret som standard. Hvis du vil tilpasse, skal du opdatere egenskaben `EngagementAgent.Instance.TestLogEnabled` til en af den værdi, der er tilgængelige fra den `EngagementTestLogLevel` optælling, for eksempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
