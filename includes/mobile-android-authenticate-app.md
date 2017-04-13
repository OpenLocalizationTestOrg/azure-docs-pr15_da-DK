
1. Åbn filen ToDoActivity.java i **Projektstifinder** i Android Studio, og Tilføj følgende import-sætninger.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Føje følgende metode til klassen **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Dette opretter en ny metode til at håndtere godkendelsesprocessen. Brugeren er godkendt ved hjælp af et Google-logon. Vises en dialogboks, der viser ID'ET for den godkendte bruger. Du kan ikke fortsætte uden en positiv godkendelse.

    > [AZURE.NOTE] Hvis du bruger en identitetsudbyder end Google, ændre den værdi, der overføres til **logon** metoden herover til et af følgende: _MicrosoftAccount_, _Facebook_, _Twitter_eller _windowsazureactivedirectory_.

3. Tilføj følgende linje af kode i metoden **onCreate** efter den kode, der starter den `MobileServiceClient` objekt.

        authenticate();

    Dette opkald starter godkendelsesprocessen.

4. Flytte den resterende kode efter `authenticate();` i metoden **onCreate** til en ny **createTable** metode, som ser sådan ud:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. Fra menuen **Kør** klik derefter på **Kør program** for at starte appen og logge på med din valgte identitetsudbyder. 

    Når du er logget på korrekt, APP'en skal køre uden fejl, og du bør kunne forespørge back end-tjenesten og foretage opdateringer til data.