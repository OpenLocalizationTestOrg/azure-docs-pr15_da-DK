<properties 
    pageTitle="Oprette forbindelse til en DocumentDB konto med protocol-understøttelse af MongoDB | Microsoft Azure" 
    description="Lær at oprette forbindelse til en DocumentDB konto med protocol-understøttelse af MongoDB, nu tilgængeligt til preview. Oprette forbindelse ved hjælp af din MongoDB forbindelsesstreng." 
    keywords="mongodb forbindelsesstreng"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Sådan oprettes forbindelse til en DocumentDB konto med protocol-understøttelse af MongoDB

Lær at oprette forbindelse til en Azure DocumentDB konto hos protocol-understøttelse af MongoDB ved hjælp af de almindelige MongoDB URI formatet af forbindelsesstrengen.  

## <a name="get-the-accounts-connection-string-information"></a>Få firmaets oplysningerne om forbindelsesstrengen

1. Log på [Azure Portal](https://portal.azure.com)i et nyt vindue.
2. Klik på **Forbindelsesstreng**i **Venstre** navigationslinje på bladet konto. For at gå til **Konto Blade**, på Jumpbar skal du klikke på **Flere tjenester**, skal du klikke på **DocumentDB (NoSQL)** og derefter vælge DocumentDB kontoen med protocol-understøttelse af MongoDB.

    ![Skærmbillede af bladet alle indstillinger](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. Bladet **Oplysningerne om forbindelsesstrengen** åbnes og har alle de oplysninger, der er nødvendige for at oprette forbindelse til kontoen ved hjælp af en driver til MongoDB, herunder en udfyldt bygget forbindelsesstreng.

    ![Skærmbillede af bladet forbindelse streng](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Forbindelse streng krav

Det er vigtigt at være opmærksom på, at DocumentDB understøtter almindelige MongoDB forbindelse streng URI-formatet, med nogle specifikke krav: DocumentDB konti kræver godkendelse og sikker kommunikation via SSL.  Formatet af forbindelsesstrengen er således:

    mongodb://username:password@host:port/[database]?ssl=true

Hvor værdierne for denne streng er tilgængelige i bladet forbindelsesstreng ovenstående.

- Brugernavn (påkrævet)
    - DocumentDB kontonavn
- Adgangskode (påkrævet)
    - DocumentDB kontoadgangskode
- Host (påkrævet)
    - FULDE DocumentDB konto
- Port (påkrævet)
    - 10250
- Database (valgfrit)
    - Den standarddatabase, der bruges af forbindelsen
- SSL = true (påkrævet)

For eksempel bør du overveje den konto, vises i oplysningerne om forbindelsesstrengen ovenfor.  Der er en gyldig forbindelsesstreng:
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>Oprette forbindelse med C#-driveren til MongoDB
Som allerede nævnt kræver alle DocumentDB konti både godkendelse og sikker kommunikation via SSL. Mens MongoDB forbindelse streng URI-format understøtter en ssl = SAND streng forespørgselsparameter, arbejde med MongoDB C# driver kræver brug af objektet MongoClientSettings, når du opretter en MongoClient.  Givet kontooplysninger ovenfor, viser følgende kodestykke, hvordan du opretter forbindelse til kontoen og arbejde med "Opgaver" databasen.

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Næste trin


- Få mere at vide hvordan [bruge MongoChef](documentdb-mongodb-mongochef.md) med en DocumentDB konto med protocol support til MongoDB.
- Udforsk DocumentDB med protocol-understøttelse af MongoDB [eksempler](documentdb-mongodb-samples.md).

 
