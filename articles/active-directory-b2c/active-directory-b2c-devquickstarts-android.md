<properties
    pageTitle="Azure Active Directory-B2C: Ringe til en web API fra et Android program | Microsoft Azure"
    description="I denne artikel viser dig, hvordan du opretter en Android-enhed 'opgaveliste' app, der kalder en Node.js web API ved hjælp af OAuth 2.0 bæreren tokens. Brug Azure Active Directory B2C til at administrere bruger-id'er og godkende brugere af både Android-appen og web API."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD-B2C: Ringe til en web API fra en Android-program

> [AZURE.WARNING] Dette selvstudium kræver nogle vigtige opdateringer, specifikt til at fjerne brugen af ADAL Android for B2C.  Vi vil publicere frisk vejledning til brug af Azure AD B2C i Android-apps i den kommende uge, og vi anbefaler, at du holder, indtil tidspunkt.  Men hvis du blot ønsker at prøve ting ud, Velkommen til at fortsætte med den artikel nedenfor.



Ved hjælp af Azure Active Directory (Azure AD) B2C, kan du tilføje effektive selvbetjening identitet administrationsfunktioner til din Android-apps og web API'er i et par korte trin. I denne artikel viser dig, hvordan du opretter en Android "opgaveliste" app, der kalder en Node.js web API ved hjælp af OAuth 2.0 bæreren tokens. Brug Azure AD B2C til at administrere bruger-id'er og godkende brugere af både Android-appen og web API.

Denne Hurtig start kræver, at du har en web API, der er beskyttet af Azure AD med B2C til at arbejde fuldt ud. Vi har oprettet et til både .NET og Node.js for dig at bruge. Denne gennemgang antages, at Node.js web API eksempel er konfigureret. Du kan finde flere oplysninger [Azure AD B2C web API til Node.js selvstudium](active-directory-b2c-devquickstarts-api-node.md).

Azure AD indeholder til Android-klienter, der skal have adgang til beskyttede ressourcer, Active Directory Authentication Library (ADAL). Der er ADAL udelukkende med det formål at gør det nemt for din app til at få adgangstokens. Til at vise, hvor let, det er, i denne vejledning vi kan udvikle et Android opgavepanel listen program, der:

- Får access tokens, der kalder en opgaveliste API ved hjælp af [OAuth 2.0 godkendelse protocol](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Får brugernes opgavelister.
- Fortegn af brugere.

> [AZURE.NOTE] I denne artikel dækker ikke Sådan implementere logon, tilmelding og administration af profil ved hjælp af Azure AD B2C. Det fokuserer på, hvordan ringe web API'er, når brugeren er godkendt. Hvis du ikke allerede har gjort det, skal du starte med den [.NET web app Introduktion – selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md) at lære de grundlæggende regler for Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer. En mappe er en beholder for alle dine brugere, apps, grupper og meget mere. Hvis du ikke allerede har et, [oprette en mappe med B2C](active-directory-b2c-get-started.md) inden du kan fortsætte i denne vejledning.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal kommunikere sikkert med din app. Både app og web API er repræsenteret af et enkelt **Program-ID** i dette tilfælde, fordi de indeholder én logiske app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md). Sørg for at:

- Medtage en **Online**/**web API** i programmet på computeren.
- Angiv `urn:ietf:wg:oauth:2.0:oob` som en **URL-adresse til svar**. Det er standard URL-adressen for denne eksempel-kode.
- Oprette et **program hemmeligt** for dit program og kopiere den. Du skal bruge den senere. Bemærk, at denne værdi skal være [XML escape-tegn](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) , før du bruge den.
- Kopiere **Program-ID** , der er tildelt din app. Du skal også dette senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Denne app indeholder tre identitet oplevelser: tilmelde dig, logge på og logge på ved hjælp af Facebook.  Du skal oprette én politik af hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Når du opretter din tre politikker, skal du:

- Vælg det **viste navn** og andre tilmelding attributter i din tilmelding politik.
- Vælg de **viste navn** og **Objekt-ID** programmet krav i alle politikker. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Det skal have præfikset `b2c_1_`.  Du skal bruge disse politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du opretter de tre politikker, er du klar til at opbygge din app.

Bemærk, at denne artikel ikke dækker Sådan bruges de politikker, du lige har oprettet. For at få mere for at vide om, hvordan fungerer politikker i Azure AD B2C, kan du starte med den [.NET web app Introduktion – selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Hente koden

Koden for dette selvstudium [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). For at oprette eksemplet, mens du skriver, kan du [hente det skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Du behøver at hente det skelet for at fuldføre dette selvstudium.** På grund af kompleksitet til implementering af et fuldt funktionelt Android program, har skelet UX kode, der skal køre, når du har fuldført dette selvstudium. Dette er et tidsbesparende mål for udviklere. UX-kode er ikke germane til emnet, hvordan du tilføjer B2C til Android programmet.

Den færdige app findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) eller på den `complete` gren i den samme lager.

For at opbygge med Maven, kan du bruge `pom.xml` på øverste niveau.

  1. Følg trinnene i [afsnittet forudsætninger for at konfigurere Maven til Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Konfigurere en emulator med SDK 21.
  3. Gå til rodmappen, hvor du klonet på repo.
  4. Kør kommandoen `mvn clean install`.
  5. Ændre mappen til hurtig start stikprøvernes `cd samples\hello`.
  6. Kør kommandoen `mvn android:deploy android:run`.

Du bør se appen Start. Angiv test brugerlegitimationsoplysninger for at prøve det.

Java arkiv (glas) pakker sendes også ud for pakken Android arkiv (AAR).

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Hente den Android ADAL og tildele den til din Android Studio arbejdsområde

Har du muligheder at bruge dette bibliotek i projektet Android:

* Du kan bruge koden til at importere biblioteket til Eklipse og link til dit program.
* Hvis du bruger Android Studio, kan du bruge formatet AAR pakke og henviser til de binære filer.

### <a name="option-1-binaries-via-gradle-recommended"></a>Mulighed 1: Binære filer via Gradle (anbefales)

Du kan få de binære filer fra den Maven central repo. Pakken AAR skal indgå i dit projekt i Android Studio (for eksempel i `build.gradle`) på denne måde:

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Mulighed 2: AAR via Maven

Hvis du bruger den `m2e` -plug-in'en i Eklipse, kan du angive afhængigheden i din `pom.xml` fil:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Mulighed 3: Datakilde via ciffer (sidste Sortér igen)

For at få kildekode af SDK via ciffer skal du angive:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Brug grenen **konvergens.**

## <a name="set-up-your-configuration-file"></a>Konfigurere din konfigurationsfil

Brug den konfiguration, som du har konfigureret tidligere i portalen B2C til at konfigurere Android projektet.

Åbn `helpes/Constants.java` og udfylde værdier for følgende:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: De områder, du sender til den server, du vil anmode om tilbage fra serveren, når en bruger logger på. For eksempel B2C du overfører `client_id`. Men dette forventes at ændre til `read scopes` i fremtiden. Dokumentet opdateres, når det sker.
- `ADDITIONAL_SCOPES`: Flere områder, kan du få brug for dit program. De forventes der skal bruges i fremtiden.
- `CLIENT_ID`: Det program-ID, du har fået fra portalen.
- `REDIRECT_URL`: Omdiriger, hvor du forventer tokenet sendes tilbage.
- `EXTRA_QP`: Noget ekstra du vil overføre til serveren i en URL-kodet format.
- `FB_POLICY`: Politikken du starter. Dette er den vigtigste del til denne gennemgang.
- `EMAIL_SIGNIN_POLICY`: Politikken du starter. Dette er den vigtigste del til denne gennemgang.
- `EMAIL_SIGNUP_POLICY`: Politikken du starter. Dette er den vigtigste del til denne gennemgang.

## <a name="add-references-to-android-adal-to-your-project"></a>Føje referencer til Android ADAL til projektet

> [AZURE.NOTE]  ADAL til Android bruger en formålet-baserede model til at kalde godkendelse. Gengivelser Arranger"over" app til at arbejde. Dette hele eksempel, og alle ADAL til Android, centrum om, hvordan du administrere gengivelser og overføre oplysninger mellem dem.

Først skal se Android om layout i dit program, herunder de gengivelser, du vil bruge. Disse gengivelser vil forklares detaljeret senere i dette selvstudium.

Opdatere dit projekts `AndroidManifest.xml` fil for at medtage alle dine gengivelser:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Som du kan se, kan du angive fem aktiviteter. Du kan bruge alle disse.

- `AuthenticationActivity`: Dette kommer fra ADAL, og den indeholder webvisning logon.
- `LoginActivity`: Dette viser dine logon-politikker og knapperne for hver politik.
- `SettingsActivity`: Du kan bruge dette til at ændre indstillingerne app på kørselstidspunktet.
- `AddTaskActivity`: Du kan bruge dette til at føje opgaver til din REST-API, der er beskyttet af Azure AD.
- `ToDoActivity`: Dette er den primære aktivitet, der viser opgaver.

## <a name="create-the-sign-in-activity"></a>Oprette aktiviteten-logon

Opret en primære aktivitet og kald den `LoginActivity`.

Oprette en fil kaldet `LoginActivity.java`.

Du skal initialiseret aktiviteten og tilføje nogle knapper, der skal styre din brugergrænseflade. Dette er velkendt for dig, hvis du har skrevet Android kode før.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Du har nu oprettet knapper, der kalder din `ToDoActivity` formålet (som kalder ADAL, når du har brug for et token). De kan gøre dette ved hjælp af din aktivitet som en reference og en ekstra parameter. Denne ekstra parameter er overført som den `intent.putExtra()` metode. Du definerer `"thePolicy"` ved hjælp af, hvad du har angivet i `Constants.java`. Det fortæller formålet hvilken politik til at aktivere under godkendelse.

## <a name="create-the-settings-activity"></a>Oprette indstillinger aktivitet

Dette er en aktivitet, der udfylder dine indstillinger for Brugergrænsefladen.

Oprette en fil kaldet `SettingsActivity.java` for enkel oprette, læse, opdatere og slette (CRUD) handlinger.

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Oprette Tilføj-opgaveaktivitet

Du kan bruge denne aktivitet til at føje en opgave til din REST-API slutpunkt.

Oprette en fil kaldet `AddTaskActivity.java` og skrive følgende.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Oprette opgave liste aktivitet

Dette er den mest vigtigt aktivitet. Du kan anvende den for at få et token fra Azure AD for en politik og derefter bruge dette token til at ringe til opgave REST-API-server.

Oprette en fil kaldet `ToDoActivity.java` og skrive følgende. (Opkaldene skal være forklares senere.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Du har måske bemærket, at dette er afhængig af metoder, der ikke er skrevet. De indeholder `updateLoggedInUser()`, `clearSessionCookie()`, og `getTasks()`. Du skal skrive dem senere i denne vejledning. Du kan ignorere fejlene i Android Studio nu.

Forklaring af parametre:

  - `SCOPES`: Obligatorisk, de områder, du forsøger at anmode om adgang til. Dette er den samme som for eksempel B2C `client_id`, men dette forventes at ændre i fremtiden.
  - `POLICY`: Politikken når du vil godkende brugeren.
  - `CLIENT_ID`: Påkrævet, kommer fra Azure AD-portalen.
  - `redirectUri`: Kan konfigureres som navnet på din pakke. Det er ikke påkrævet for at være knyttet til den `acquireToken` opkald.
  - `getUserInfo()`: Den måde til at slå op om en bruger er allerede i cachen. Parameteren også beskrives, hvordan du bede en bruger, hvis brugeren ikke findes, eller hvis brugerens adgangstoken er ugyldig. Denne metode skrives senere i denne vejledning.
  - `PromptBehavior.always`: Hjælper med at bede om legitimationsoplysninger for at springe cache og cookie.
  - `Callback`: Kaldes, når en godkendelseskode er udvekslet for et token. Den får et objekt `AuthenticationResult`, som indeholder adgangstoken, udløbsdato og ID token oplysninger.

> [AZURE.NOTE]  Appen med firmaportalen Microsoft Intune indeholder komponenten broker, og det kan være installeret på brugerens enhed. Appen giver enkelt enkeltlogon (SSO) adgang på tværs af alle programmer på enheden. Udviklere skal være forberedt på at Tillad Intune. ADAL til Android kan bruge kontoen broker, hvis der er en brugerkonto, der er oprettet i godkenderen. Hvis du vil bruge broker, udvikleren har brug for til at registrere en særlig `redirectUri` for broker, der skal bruges. `redirectUri`er i formatet af msauth://packagename/Base64UrlencodedSignature. Du kan få `redirectUri` for din app ved hjælp af scriptet `brokerRedirectPrint.ps1` eller ved hjælp af API opkaldet `mContext.getBrokerRedirectUri()`. Signaturen er relateret til dine signerende certifikater fra Google Play store.

 Du kan springe broker brugeren ved hjælp af:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Vi har valgt for at springe broker i vores eksempel for at reducere kompleksiteten af denne B2C Hurtig start.

Dernæst skal du oprette hjælper metoder, får tokenet alene under godkendelse opkald til opgaven API.

I den samme `ToDoActivity.java` fil ved at skrive følgende.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Også tilføje metoder, der "Angiv" og "Hent" `AuthenticationResult` (som har et token) til den globale `Constants`. Selvom `ToDoActivity.java` bruger `sResult` i dets flyder, skal du tilføje disse metoder. Hvis du ikke dine andre aktiviteter ikke har adgang til tokenet til at arbejde (såsom at tilføje en opgave i `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Oprette en metode til at returnere et bruger-id

ADAL for Android repræsenterer brugeren i form af en `UserIdentifier` objekt. Dette administrerer brugeren. Du kan bruge objektet til at identificere, om den samme bruger bruges i dine opkald. Ved hjælp af disse oplysninger, du kan stole på cachen i stedet for foretager et nyt opkald til serveren. Hvis du vil gøre det nemmere, vi oprettede den `getUserInfo()` metode, der returnerer `UserIdentifier`. Du kan bruge det samme med `acquireToken()`. Vi også har oprettet en `getUniqueId()` metode, der returnerer ID `UserIdentifier` i cachen.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Skrive hjælper metoder

Dernæst skal skrive nogle hjælper metoder til at hjælpe dig med at fjerne markeringen i cookies og give `AuthenticationCallback`. Disse metoder bruges kun til stikprøvernes for at sikre dig, at du er i en ren tilstand, når du ringer til din `ToDo` aktivitet.

I den samme fil kaldet `ToDoActivity.java`, skrive følgende.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Ringe til opgaven API

Når du har din aktivitet, der er klar til at tage tokens, kan du skrive din API for at få adgang til opgave-serveren.

`getTasks`indeholder en matrix, der repræsenterer opgaverne i din server.

Starte med `getTasks`.

I den samme fil kaldet `ToDoActivity.java`, skrive følgende.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Også skrive en metode, starte dine tabeller ved første kørsel.

I den samme fil kaldet `ToDoActivity.java`, skrive følgende.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

Du kan se, at denne kode kræver nogle flere metoder til at gøre arbejdet. Skriv næste.

### <a name="create-an-endpoint-url-generator"></a>Oprette en slutpunkt URL-adressen generator

Du har brug for til at generere slutpunkt URL-adresse, du vil oprette forbindelse til. Du kan gøre det i den samme klassefil.

**I den samme fil** kaldet `ToDoActivity.java`, skrive følgende.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Bemærk, at du føjer adgangstoken til anmodningen i den kode, der er beskrevet i næste afsnit.

## <a name="write-some-ux-methods"></a>Skrive nogle UX metoder

Android kræver, at du skal håndtere nogle tilbagekald for at betjene appen. Dette er `createAndShowDialog` og `onResume()`. Dette er velkendt for dig, hvis du har skrevet Android kode før.

I den samme fil kaldet `ToDoActivity.java`, skrive følgende.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Dernæst skal administrere dine dialogboksen tilbagekald.

I den samme fil kaldet `ToDoActivity.java`, skrive følgende.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Du bør nu har en `ToDoActivity.java` fil, som samler. Hele projektet bør også samle på dette tidspunkt.

## <a name="run-the-sample-app"></a>Køre appen eksempel

Til sidst skal oprette og køre appen Android Studio eller Eklipse. Har tilmeldt dig eller logge på appen. Oprette opgaver for den bruger, der er logget på. Log af og logge på igen som en anden bruger, og derefter oprette opgaver for den pågældende bruger.

Bemærk, at opgaverne, der er lagrede brugerniveau på API, fordi API henter brugerens identitet fra adgangstoken den modtager.

Til reference gennemført prøveoverførsel [leveres som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Du kan også klone fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Vigtige oplysninger


### <a name="encryption"></a>Kryptering

ADAL krypterer tokens og store i `SharedPreferences` som standard. Du kan se den `StorageHelper` klasse kan se detaljerne. Android introduceret **AndroidKeyStore for 4.3(API18)** sikker lagring af private nøgler. ADAL bruger, til API18 og derover. Hvis du vil bruge ADAL til nederste SDK versioner, skal du angive et hemmeligt tast ad `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Sessionscookies i webvisning

Android webvisning fjerne ikke sessionscookies, når appen er lukket. Du kan håndtere sagen med følgende eksempelkode.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Lær mere om cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).
