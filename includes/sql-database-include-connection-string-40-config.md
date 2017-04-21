
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>Eksempel konfigurationsfil for forbindelse streng sikkerhed


Det er unsound placere forbindelsesstrengen som konstanter i C#-kode. Det er bedre til at sætte forbindelsesstrengen på en konfigurationsfil. Der kan du redigere strengen helst uden at skulle genkompilere.

Lad os antage dit kompileret C#-program har navnet **ConsoleApplication1.exe**og, som denne .exe er placeret i en **bin\debug\* * directory.

I dette eksempel er de fleste dele af din forbindelsesstreng gemt i en konfigurationsfil med navnet nøjagtigt **ConsoleApplication1.exe.config**. Denne konfigurationsfil skal også være i **bin\debug\**.

I XML for følgende konfigurationsfil vist du en forbindelsesstreng med navnet **ConnectionString4NoUserIDNoPassword**. C#-kode søger efter denne streng.

Du skal redigere reelle navne i pladsholderne:

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Vi valgte udelade to parametre for denne illustration:

- Bruger-ID = {your_userName_here};
- Adgangskode = {your_password_here};


Du kan medtage dem, men nogle gange er det bedre at have programmet få disse værdier fra tastaturinput af brugeren. Det afhænger af.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
