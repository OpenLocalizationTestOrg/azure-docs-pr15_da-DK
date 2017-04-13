<properties
    pageTitle="Sende sikker Pushmeddelelser med Azure meddelelse Hubs"
    description="Lær at sende sikker pushmeddelelser til en Android-app fra Azure. Kodeeksempler, der er skrevet i Java og C#."
    documentationCenter="android"
    keywords="Push-meddelelse, pushmeddelelser, push-meddelelser, android pushmeddelelser"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Sende sikker Pushmeddelelser med Azure meddelelse Hubs

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Oversigt

> [AZURE.IMPORTANT] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Understøttelse af push meddelelse i Microsoft Azure gør muligt at få adgang til en nemt Hvis du vil bruge, flere platforme, skaleret opslagsnål meddelelse infrastruktur, hvilket gør det meget forenkler implementeringen af pushmeddelelser for både forbruger- og enterprise-programmer til mobile platforme.

På grund af lovmæssige eller sikkerhedsbegrænsninger, nogle gange et program kan du medtage noget i meddelelsen, som ikke kan overføres via standard opslagsnål meddelelse infrastruktur. Dette selvstudium beskrives, hvordan du opnå den samme oplevelse ved at sende følsomme oplysninger via en sikker, godkendt forbindelse mellem Android klientenhed og app back end.

På et højt niveau som strømmen på følgende måde:

1. App-back-end:
    - Gemmer sikker data i back end-databasen.
    - Sender ID'ET for denne meddelelse til den Android-enhed (der sendes ingen sikker oplysninger).
2. Appen på enheden, når du modtager meddelelsen:
    - Android-enhed kontakter den back end-anmoder om sikker data.
    - Appen kan vise data som en meddelelse på enheden.

Det er vigtigt at være opmærksom på, i det foregående flow (og i dette selvstudium), antager vi, at enheden gemmer et godkendelse token i lokale lager, når brugeren logger på. Dette garanterer en helt problemfri oplevelse, som enheden kan hente på beskeden sikker data ved hjælp af dette symbol. Hvis dit program ikke gemme godkendelsestokens på enheden, eller hvis disse tokens kan være udløbet, appen enhed modtager en opslagsnål besked skal vise en generisk meddelelse spørge brugeren til at starte appen. Appen derefter godkender brugeren og viser besked om overførslen.

Dette selvstudium viser, hvordan du sender en sikker pushmeddelelser. Den bygger på selvstudium [Giv brugerne besked](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , så du skal fuldføre trinnene i dette selvstudium først Hvis du ikke allerede har.

> [AZURE.NOTE] Dette selvstudium antages det, at du har oprettet og konfigureret din meddelelse-hub, som beskrevet i [Komme i gang med besked om Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Ændre Android projektet

Nu hvor du har ændret din app back end-hvis du vil sende lige *id* for en push-meddelelse, er det nødvendigt at ændre din Android-app for at håndtere denne meddelelse og ringe tilbage til din back-end for at hente den sikre meddelelse skal vises.
For at opnå dette mål, har du Sørg for, at din Android-app ved, hvordan at godkende sig selv med din back end-, når den modtager pushbeskeder.

Vi vil nu ændre *login* strømmen for at gemme godkendelse sidehoved værdi i de delte indstillinger af din app. Tilsvarende mekanismer kan bruges til at gemme en hvilken som helst godkendelse token (fx OAuth tokens), som app nødt til at bruge uden kræver brugerlegitimationsoplysninger.

1. Tilføj følgende konstanter øverst i klassen **MainActivity** i dit projekt for Android-appen:

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Stadig i klassen **MainActivity** opdatere den `getAuthorizationHeader()` metode til at indeholde følgende kode:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Tilføj følgende `import` sætninger øverst i filen **MainActivity** :

        import android.content.SharedPreferences;

Nu vil vi ændre den handler, der kaldes, når meddelelsen modtages.

4. I **MyHandler** klasse Skift den `OnReceive()` metode til at indeholde:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. Tilføj derefter den `retrieveNotification()` metode, erstatte pladsholderen `{back-end endpoint}` med back end-slutpunktet opnået under installation af din back end-:

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Denne metode ringer din app back-end for at hente den ved hjælp af de legitimationsoplysninger, der er gemt i indstillingerne for delt indhold i meddelelsen og viser den som en almindelig meddelelse. Meddelelsen ser ud til app brugeren samme måde som andre push-meddelelse.

Bemærk, at det er bedst at håndtere tilfælde af manglende sidehoved egenskaben til godkendelse eller afvisning af back end. Den specifikke håndtering af disse tilfælde afhænger hovedsageligt brugeroplevelsen dine mål. En af mulighederne er at få vist en meddelelse med en meddelelse om generisk til at godkende brugeren til at hente den faktiske meddelelse.

## <a name="run-the-application"></a>Køre programmet

Hvis du vil køre programmet, skal du gøre følgende:

1. Kontrollér, at **AppBackend** er implementeret i Azure. Hvis bruger Visual Studio, skal du køre programmet **AppBackend** Web API. Der vises en ASP.NET-webside.

2. Køre appen på en fysisk Android-enhed eller emulatoren i Eklipse.

3. Angive et brugernavn og din adgangskode i Android app brugergrænseflade. Det kan være en streng, men de skal være den samme værdi.

4. Klik på **Log på**i Android app brugergrænseflade. Klik derefter på **Send opslagsnål**.
