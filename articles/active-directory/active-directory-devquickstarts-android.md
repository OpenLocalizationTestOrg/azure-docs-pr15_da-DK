<properties
    pageTitle="Azure AD-Android Introduktion | Microsoft Azure"
    description="Sådan oprettes et Android-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Integrere Azure AD til en Android-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Hvis du udvikler en på computeren, gør det Azure AD, enkle og nemt for dig at godkende dine brugere ved hjælp af deres Active Directory-konti.  Det kan også programmet sikkert bruge en hvilken som helst web API, der er beskyttet af Azure AD, som Office 365 API'er eller Azure API.

Azure AD indeholder til Android-klienter, der skal have adgang til beskyttede ressourcer, Active Directory Authentication Library eller ADAL.  ADALS eneste formål i liv er at gøre det lettere for din app til at få adgangstokens.  For at vise lige hvor let det er skal her vi oprettes et Android opgaveliste-program, der:

-   Henter få adgang til tokens for opkald til en opgave liste API, ved hjælp af [OAuth 2.0 godkendelse protocol](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Henter en brugers opgaveliste
-   Fortegn brugere ud.

For at komme i gang, skal du bruge en Azure AD-lejer, hvor du kan oprette brugere og registrere et program.  Hvis du ikke allerede har en lejer, [se, hvordan du få en](active-directory-howto-tenant.md).

> [AZURE.TIP] Prøv eksemplet på vores nye [udvikler portal](https://identity.microsoft.com/Docs/Android) , der kan hjælpe dig med at blive oppe at køre med Azure Active Directory på blot nogle få minutter!  Portalen udvikler fører dig gennem processen med at registrere en app og integrere Azure AD i din kode.  Når du er færdig, får du et enkelt program, der kan godkende brugere i din lejer og en back-end, der kan acceptere tokens og udføre datavalidering. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Trin 1: Hente og køre Node.js RESTEN API Opgaveliste eksempel Server

Dette eksempel er skrevet specifikt til at fungere med vores eksisterende eksempel opbygning af en enkelt lejer opgavepanel REST-API til Microsoft Azure Active Directory. Dette er en foreløbig nødvendige til Hurtig Start.

Oplysninger om, hvordan du konfigurerer det, at besøge vores eksisterende eksempler:

* [Microsoft Azure Active Directory eksempel RESTEN API-tjenesten for Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Trin 2: Registrere dit Web API med din Microsoft Azure AD-lejer

**Hvad jeg gør?**

*Microsoft Active Directory understøtter tilføjelse af to typer af programmer. Web API'er, der tilbyder tjenester til brugere og programmer (enten på internettet eller et program, der kører på en enhed), få adgang til disse Web API'er. I dette trin registrerer du Web API du kører lokalt til test i dette eksempel. Denne Web API vil normalt være en REST-webtjeneste, der tilbyder funktioner, du ønsker en app til at få adgang til. Microsoft Azure Active Directory kan beskytte et slutpunkt!*

*Vi her Hvis du registrerer for den Opgaveliste REST-API, der er nævnt ovenfor, men det fungerer for en hvilken som helst Web API, du vil have Azure Active Directory til at beskytte.*

Trin til at registrere en Web API med Microsoft Azure AD

1. Log på [Azure management portal](https://manage.windowsazure.com).
2. Klik på Active Directory i den venstre navigation fra.
3. Klik på den directory lejer, hvor du vil registrere eksempelprogrammet.
4. Klik på fanen programmer.
5. Kassen, klik på Tilføj.
6. Klik på "Tilføj et program, udvikling af min organisation".
7. Angiv et fuldt navn til programmet, for eksempel "TodoListService," Vælg "Web Application og/eller Web API", og klik på Næste.
8. Angiv grundlæggende URL-adressen for stikprøven, der er som standard for enkeltlogon URL-adressen, `https://localhost:8080`.
9. App-ID URI, Angiv `https://<your_tenant_name>/TodoListService`, erstatte `<your_tenant_name>` med navnet på din Azure AD-lejer.  Klik på OK for at fuldføre registreringen.
10. Klik på fanen Konfigurer af dit program, mens du stadig er i Azure-portalen.
11. **Finde værdien klient-ID og kopiere det Afsæt**, du skal bruge dette senere når du konfigurerer dit program.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Trin 3: Registrere programmet eksempel Android Native Client

Registrere dit webprogram er det første trin. Derefter skal du fortælle Azure Active Directory om dit program samt. Dette giver mulighed for dit program til at kommunikere med lige registrerede Web API

**Hvad jeg gør?**  

*Som anført ovenfor, understøtter Microsoft Azure Active Directory tilføje to typer af programmer. Web API'er, der tilbyder tjenester til brugere og programmer (enten på internettet eller et program, der kører på en enhed), få adgang til disse Web API'er. I dette trin registrerer du programmet i dette eksempel. Du skal gøre, hvis dette program kunne anmodning om adgang til Web API, du lige har registreret. Azure Active Directory kan nægte lige så dit program til at spørge om logon, medmindre det er registreret! Det er en del af sikkerheden for modellen.*

*Vi her Hvis du registrerer dette eksempelprogram, der er nævnt ovenfor, men det fungerer for en app, du udvikler.*

**Hvorfor jeg lægge både et program og en Web API i én lejer?**

*Som du muligvis har gættet, kan du oprette en app, der har adgang til en ekstern API, der er registreret i Azure Active Directory fra en anden lejer. Hvis du gør dette, bliver dine kunder bliver bedt om at samtykke til brug af API i programmet. Delen godt er, Active Directory Authentication Library til iOS tager sig af denne samtykke for dig! Som vi få i til mere avancerede funktioner, kan du se dette er en vigtig del af det arbejde, der er behov for at få adgang til pakken med Microsoft APIs fra Azure og Office samt andre tjenesteudbyder. Nu, fordi du har registreret både din Web API og dit program under samme lejer kan ikke du se meddelelser om samtykke. Dette er som regel sag, hvis du udvikler et program lige til din egen virksomhed til brug.*

1. Log på [Azure management portal](https://manage.windowsazure.com).
2. Klik på Active Directory i den venstre navigation fra.
3. Klik på den directory lejer, hvor du vil registrere eksempelprogrammet.
4. Klik på fanen programmer.
5. Kassen, klik på Tilføj.
6. Klik på "Tilføj et program, udvikling af min organisation".
7. Angiv et fuldt navn til programmet, for eksempel "TodoListClient-Android" skal du vælge "oprindelige klientprogrammet", og klik på Næste.
8. Angiv for URI omdirigere `http://TodoListClient`.  Klik på Udfør.
9. Klik på fanen Konfigurer af programmet.
10. Finde værdien klient-ID og kopiere det Afsæt, du skal bruge dette senere når du konfigurerer dit program.
11. Klik på "Tilladelser til andre programmer", "Tilføj programmet".  Vælg "Andet" i rullemenuen "Vis", og klikke på den øverste markering.  Find og klik på TodoListService, og klikke på den nederste markering for at tilføje programmet.  Vælg "Access TodoListService" på rullelisten "Delegerede tilladelser", og Gem konfigurationen.



For at opbygge med Maven, kan du bruge pom.xml på øverste niveau

  * Klone denne repo i til en mappe efter eget valg:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Følg trinnene på [Prerequests afsnit for at konfigurere din maven til android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Konfiguration af emulator med SDK 19
  * Gå til rodmappen, hvor du klonet på repo
  * Kør kommandoen: mvn ren installere
  * Ændre mappen til eksemplet Hurtig Start: cd samples\hello
  * Kør kommandoen: mvn android: installere android: Kør
  * Skal du se afsnittet app starter
  * Angiv test brugerlegitimationsoplysninger til at prøve!

Glas pakker sendes også ud for pakken aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Trin 4: Hente Android ADAL og tildele den til arbejdsområdet Eklipse

Vi har gjort det nemmere for dig at er flere måder at bruge dette bibliotek i projektet Android:

* Du kan bruge koden til at importere dette bibliotek til Eklipse og link til dit program.
* Hvis bruger Android Studio, kan du bruge *aar* pakke format og henviser til de binære filer.

####<a name="option-1-source-zip"></a>Mulighed 1: Kilde Zip

Hvis du vil hente en kopi af koden, klikke på "Hent ZIP" i højre side af siden, eller klik på [her](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Mulighed 2: Kilde via ciffer

Hvis du vil have kildekode af SDK via ciffer blot skrive:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Mulighed 3: Binære filer via Gradle

Du kan få de binære filer fra Maven central repo. AAR pakke kan medtages på følgende måde i dit projekt i AndroidStudio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Indstillingen 4: aar via Maven

Hvis du bruger m2e plug-in'et i Eklipse, kan du angive afhængigheden i pom.xml filen:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Indstillingen 5: glas pakke i libs mappe
Du kan hente filen glas fra maven repo og slip til mappen *libs* i dit projekt. Skal du kopiere de påkrævede ressourcer til projektet samt, da glas pakker ikke omfatter dem.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Trin 5: Føje referencer til Android ADAL til projektet


2. Føje en reference til projektet og angive den som en Android-bibliotek. Hvis du er usikker på, hvordan du gør dette, [Klik her for at få mere at vide] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Tilføje afhængigheden til fejlfinding i indstillingerne for projekt

4. Opdater dit projekts AndroidManifest.xml filen indeholder:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Oprette en forekomst af AuthenticationContext på din primære aktivitet. Oplysninger om opkaldet er ud over omfanget af denne vigtige oplysninger om, men du kan få en god start ved at kigge på [Android Native Client eksempel](https://github.com/AzureADSamples/NativeClient-Android). Nedenfor vises et eksempel:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Bemærk: mContext er et felt i din aktivitet

8. Kopiér denne kode Bloker for at håndtere slutningen af AuthenticationActivity, når brugeren angiver legitimationsoplysninger og modtager godkendelseskode:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Hvis du vil bede om en token, definerer du et tilbagekald

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Til sidst skal du bede om en token ved hjælp af, at tilbagekald:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Forklaring af parametre:

  * Ressource er påkrævet og er den ressource, du forsøger at få adgang til.
  * Clientid er påkrævet og kommer fra portalen AzureAD.
  * Du kan konfigurere redirectUri som din pakkenavn. Det er ikke påkrævet for at være knyttet til acquireToken opkaldet.
  * PromptBehavior hjælper med at bede om legitimationsoplysninger for at springe cache og cookie.
  * Tilbagekald kaldes, efter at godkendelseskode er udvekslet for et token.

  Tilbagekaldet får et objekt i AuthenticationResult, som har accesstoken, dato udløbet, og idtoken oplysninger.

Valgfrit: **acquireTokenSilent**

Du kan ringe til **acquireTokenSilent** til at håndtere cachelagring og token opdatering. Kan også synkronisere version. Den accepterer bruger-id som parameter.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Broker**: Microsoft Intune virksomhed portalen app får komponenten broker. ADAL Brug broker-konto, hvis der er en brugerkonto er oprettet på denne godkenderen og udvikler vælger ikke at springe den. Udvikler kan springe broker brugeren med:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Udvikler behov at registrere speciel redirectUri for broker brugen. RedirectUri er i formatet af msauth://packagename/Base64UrlencodedSignature. Du kan få din redirecturi til din app, ved hjælp af script "brokerRedirectPrint.ps1" eller bruge API opkald mContext.getBrokerRedirectUri. Signatur er relateret til dine signerende certifikater.

 Der er aktuelle broker model for én bruger. AuthenticationContext indeholder API metode for at få broker brugeren.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Broker bruger returneres, hvis konto er gyldig.

 Din app Manifestet skal have tilladelse til at bruge AccountManager konti: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Ved hjælp af denne gennemgang, bør du have hvad du skal integreres korrekt med Azure Active Directory. Du kan finde flere eksempler på denne arbejde, på AzureADSamples / lager på GitHub.

## <a name="important-information"></a>Vigtige oplysninger

### <a name="customization"></a>Tilpasning

Bibliotek projektressourcer kan overskrives af ressourcerne programmet på computeren. Dette sker, når opbygning af din app. Derfor kan du tilpasse godkendelse aktivitet layout, som du ønsker. Du har brug at sikre, at beholde id'et for kontrolelementerne, ADAL uses(Webview).

### <a name="broker"></a>Broker

Broker komponent vil blive leveret med Microsoft Intune virksomhed portalen app. Konto oprettes i Account Manager. Kontotype er "com.microsoft.workaccount". Det kan kun enkelt SSO-konto. Det opretter SSO cookie for denne bruger, når du er færdig enhed udfordring for en App.

### <a name="authority-url-and-adfs"></a>Nøglecenter URL-adresse og ADFS

ADFS blev ikke genkendt som STS, så du behøver at slå af forekomst registrering og sendes falsk med AuthenticationContext parametre.

Nøglecenter URL-adressen skal STS forekomst og lejer navn: https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>Forespørgsler cache elementer

ADAL indeholder standard cachen i SharedPreferences med nogle enkle cache forespørgsel funktioner. Du kan få den aktuelle cache AuthenticationContext med:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Du kan også angive implementeringen cachen, hvis du vil tilpasse den.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL giver mulighed for at Angiv en Spørg funktionsmåde. PromptBehavior.Auto pop op-brugergrænseflade, hvis Opdater token er ugyldige og brugerlegitimationsoplysninger er påkrævet. PromptBehavior.Always Spring cache brugen, og Vis altid brugergrænseflade.

### <a name="silent-token-request-from-cache-and-refresh"></a>Uovervåget token anmodning fra cachen, og Opdater

Denne metode ikke bruge Brugergrænsefladen i pop op og ikke kræver en aktivitet. Det vil returnere token fra cachen, hvis det er muligt. Hvis token er udløbet, vil det, så prøv at opdatere dem. Hvis Opdater token er udløbet eller mislykkedes, returneres AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Du kan også gøre synkronisering opkald ved hjælp af denne metode. Du kan angive null til tilbagekald eller bruge acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnosticering

Følgende er de primære kilder til oplysninger til diagnosticering problemer:

+ Undtagelser
+ Logfiler
+ Netværk sporinger

Bemærk også, at korrelations-id'er er central til diagnosticering i biblioteket. Du kan angive din korrelations id'er på grundlag per anmodning, hvis du vil koordinere et ADAL anmode om med andre handlinger i din kode. Hvis du ikke angiver et korrelationer-id, og derefter ADAL genererer en tilfældig en and alle være meddelelser og netværk opkald forsynet med korrelations-id'et. Selvsigneret genereres id ændringer på hver enkelt anmodning.

#### <a name="exceptions"></a>Undtagelser

Dette er tydeligt første diagnostic. Vi kan du prøve at give praktisk fejlmeddelelser. Hvis du finder en, der ikke er praktisk Rapportér et problem, og fortæl os. Angiv enhedsoplysninger som model og SDK # også.

#### <a name="logs"></a>Logfiler

Du kan konfigurere biblioteket for at generere logmeddelelser, du kan bruge til at diagnosticere problemer. Du kan konfigurere logføring ved at gøre følgende opkaldet til at konfigurere et tilbagekald, ADAL vil bruge til hånden fra alle de meddelelser, log, som den er oprettet.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Meddelelser kan skrives til en brugerdefineret logfil, som vist nedenfor. Der er desværre ikke standard muligt for at få logfiler fra en enhed. Der er nogle af tjenesterne, der kan hjælpe dig med dette. Du kan også for lager dine egne, som sender filen til en server.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Af logføringsniveauer

+ Error(Exceptions)
+ Warn(Warning)
+ Oplysninger (orientering)
+ Detaljeret (flere detaljer)

Du kan angive logføringsniveau således:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Alle meddelelser sendes til logcat ud over eventuelle brugerdefinerede log tilbagekald.
Du kan få log til en fil formular logcat som vist belog:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Flere eksempler om adb kommandoer: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Netværk sporinger

Du kan bruge forskellige værktøjer til at registrere den HTTP-trafik, der ADAL genererer.  Dette er mest effektiv, hvis du kender med OAuth-protokollen, eller hvis du har brug at levere diagnostiske oplysninger til Microsoft eller andre support kanaler.

Fiddler er værktøjet til sporing af nemmeste HTTP.  Brug følgende links til at konfigurere op til korrekt post ADAL netværkstrafik.  Det er nødvendigt at konfigurere fiddler eller et andet værktøj som Christian til at registrere ikke-krypteret SSL-trafik for at være nyttige.  Bemærk: Sporinger, der er oprettet på denne måde kan indeholde meget følsomme oplysninger som access tokens, brugernavne og adgangskoder.  Hvis du bruger fremstilling konti, kan du ikke dele disse spor med 3 parter.  Hvis du vil angive en sporing til en person for at få support, skal du Genskab problemet med en midlertidig konto med brugernavne og adgangskoder, som du ikke har noget imod deling.

+ [Konfiguration af Fiddler til Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Konfigurere Fiddler regler For ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Dialogboksen tilstand
acquireToken metode uden aktivitet understøtter dialogboks.

### <a name="encryption"></a>Kryptering

ADAL krypterer tokens og store i SharedPreferences som standard. Du kan se på klassen StorageHelper kan se detaljerne. Android introduceret AndroidKeyStore for 4.3(API18) sikker lagring af private nøgler. ADAL bruger, til API18 og derover. Hvis du vil bruge ADAL til nederste SDK versioner, skal du angive hemmeligt tast ad AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>Oauth2 bæreren udfordring

AuthenticationParameters klasse giver funktionalitet for at få authorization_uri fra Oauth2 bæreren udfordring.

### <a name="session-cookies-in-webview"></a>Sessionscookies i webvisning

Android webvisning fjerne ikke sessionscookies, når app er lukket. Du kan håndtere dette med eksempelkode nedenfor:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Flere oplysninger om cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Ressource tilsidesætter

Biblioteket ADAL omfatter engelsk strenge for følgende to ProgressDialog meddelelser.

Dit program skal overskrive dem eventuelt af lokaliserede strenge.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>NTLM-dialogboksen
Adal version 1.1.0 understøtter NTLM dialogboks, der er behandlet via onReceivedHttpAuthRequest begivenhed fra WebViewClient. Dialogboksen layout og strenge kan tilpasses.

### <a name="cross-app-sso"></a>SSO i tværs-app
Lær, [hvordan du aktiverer kryds-app SSO på Android-enhed ved hjælp af ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
