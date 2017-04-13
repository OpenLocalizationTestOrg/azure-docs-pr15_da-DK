
Det forrige eksempel viste en standard-logon, som kræver klienten til at kontakte både identitetsudbyder og back end-Azure service, hver gang appen startes. Ikke alene er denne metode ineffektiv, du kan støde på brugen-relaterede problemer skal mange kunder forsøger at starte app på samme tid. En bedre fremgangsmåde er at cachen godkendelse tokenet Azure tjenesten returnerede og forsøger at bruge dette første inden du bruger en udbyder-baserede logon. 

>[AZURE.NOTE]Du kan gemme i cachen tokenet udstedt af back-end Azure service uanset om du bruger klient-administreret eller service-administreret godkendelse. Dette selvstudium bruger service-administreret godkendelse.


1. Åbn filen ToDoActivity.java og tilføje følgende udtalelser import:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Tilføje den følgende medlemmer til den `ToDoActivity` klasse.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. I feltet ToDoActivity.java fil skal du tilføje den følgende definition af den `cacheUserToken` metode.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Denne metode gemmer bruger-id og token i en indstillingsfil, der er markeret som privat. Dette bør beskytte adgang til cachen, så andre apps på enheden ikke har adgang til tokenet, fordi indstillingen er i sandkassetilstand for appen. Men hvis en person får adgang til enheden, det er muligt, at de kan få adgang til den token cache på anden måde. 

    >[AZURE.NOTE]Du kan beskytte token med kryptering yderligere, hvis token adgang til dine data betragtes som meget følsomme og andre kan få adgang til enheden. En helt sikker løsning er dog ud over omfanget af dette selvstudium og afhænger af dine krav til sikkerhed.


4. I feltet ToDoActivity.java fil skal du tilføje den følgende definition af den `loadUserTokenCache` metode.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. Erstat i filen *ToDoActivity.java* på `authenticate` metode med følgende metode som bruger en token cache. Ændre logon-udbyder, hvis du vil bruge en anden konto end Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Opbygge app og test godkendelsen ved hjælp af en gyldig konto. Køre den mindst to gange. Under den første gang modtager du en meddelelse om at logge på og oprette token cachen. Hver Kør forsøger at indlæse token cachen til godkendelse, og du bør ikke kræves til logon.



