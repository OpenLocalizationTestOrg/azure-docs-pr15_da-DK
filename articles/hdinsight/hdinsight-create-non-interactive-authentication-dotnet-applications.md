<properties
    pageTitle="Oprette ikke-interaktiv godkendelse .NET HDInsight ligger | Microsoft Azure"
    description="Lær at oprette ikke-interaktiv godkendelse .NET HDInsight programmer."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Oprette ikke-interaktiv godkendelse .NET HDInsight programmer

Du kan udføre dit .NET Azure HDInsight program under programs egen identitet (ikke-interaktiv) eller under på identiteten for den bruger, er logget på af programmet (interaktive). Du kan finde en stikprøve af programmet interaktive [sende Hive/gris/Sqoop job ved hjælp af HDInsight .NET SDK](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). I denne artikel viser, hvordan du opretter ikke-interaktiv godkendelse .NET program tilladelse til at oprette forbindelse til Azure HDInsight og sende et Hive-job.

Fra dit .NET-program skal du:

- dit Azure abonnement lejer-ID
- Azure Directory programmet klient-ID
- Azure Directory hemmeligt programtasten.  

Primære processen omfatter følgende trin:

2. Oprette et Azure Directory-program.
2. Tildele roller til programmet AD.
3. Udvikle klientprogrammet.


##<a name="prerequisites"></a>Forudsætninger

- HDInsight klynge. Du kan oprette en ved hjælp af vejledningen, findes i [Introduktion – selvstudium](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Oprette Azure Directory program 
Når du opretter en Active Directory-program, opretter det faktisk både programmet og en tjeneste vigtigste. Du kan køre programmet under programmets identitet.

I øjeblikket, skal du bruge Azure klassisk portalen til at oprette et nyt Active Directory-program. Denne mulighed, føjes til Azure-portalen i en nyere version. Du kan også udføre disse trin til Azure PowerShell eller Azure CLI. Du kan finde yderligere oplysninger om brug af PowerShell eller CLI med tjenesten ydelsen, [Godkend service vigtigste med Azure ressourcestyring](../resource-group-authenticate-service-principal.md).

**Oprette et Azure Directory program**

1.  Log på [Azure klassisk portal]( https://manage.windowsazure.com/).
2.  Vælg **Active Directory** i venstre rude.

    ![Azure klassisk portalen active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Vælg den mappe, du vil bruge til at oprette det nye program. Det skal være den eksisterende kalender.
4.  Klik på **programmer** fra øverst til at få vist de eksisterende programmer.
5.  Klik på **Tilføj** fra bunden til at tilføje et nyt program.
6.  Skriv **navn**, Vælg **webprogram og/eller Web API**, og klik derefter på **Næste**.

    ![nyt azure active directory-program](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Angiv **Sign-on URL-adresse** og **App ID URI**. **Log på URL-adressen**til at give URI til et websted, der beskriver dit program. Eksistensen af webstedet, er ikke valideres. Angiv den URI, der identificerer dit program til APP-ID URI. Og klik derefter på **udført**.
Det tager et øjeblik på at oprette programmet.  Når programmet er oprettet, vises på portalen siden hurtig Glace i det nye program. Luk ikke på portalen. 

    ![Egenskaber for ny azure active directory-program](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**At få programmet klient-ID og tasten hemmeligt**

1.  Den nyoprettede AD programmets side, klik på **Konfigurer** fra den øverste menu.
2.  Oprette en kopi af **Klient-ID**. Du skal bruge den i .NET programmet.
3.  Klik på rullelisten **Vælg varighed** under **taster**, og vælg enten **1 år** eller **2 år**. Den nøgleværdi vises ikke, før du gemmer konfigurationen.
4.  Klik på **Gem** nederst på siden. Når tasten hemmeligt vises, skal du oprette en kopi af nøglen. Du skal bruge den i .NET programmet.

##<a name="assign-ad-application-to-role"></a>Tildele AD program til rolle

Du skal tildele programmet til en [rolle](../active-directory/role-based-access-built-in-roles.md) til at give den tilladelser for at udføre handlinger. Du kan angive omfanget på niveauet i det abonnement, ressourcegruppe eller ressource. Tilladelserne nedarves til lavere niveauer af omfang (for eksempel at tilføje et program til rollen Læser for en ressourcegruppe betyder, at det kan læse ressourcegruppen og de ressourcer, den indeholder). I dette selvstudium skal du angive omfanget på ressourcegruppeniveau.  Da portalen Azure klassisk ikke understøtter ressourcegrupper, har denne del udføres fra Azure-portalen. 

**Tilføje rollen ejer AD-program**

1.  Log på [Azure-portalen](https://portal.azure.com).
2.  Klik på **Ressourcen gruppe** i venstre rude.
3.  Klik på den ressourcegruppe, der indeholder den HDInsight klynge, hvor du vil køre forespørgslen Hive senere i dette selvstudium. Hvis der er for mange grupper, kan du bruge filteret.
4.  Klik på **Access** fra bladet klynge.

    ![ikonet skyen og thunderbolt = Hurtig start](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  Klik på **Tilføj** fra bladet **brugere** .
6.  Følg instruktionerne til at tilføje rollen som **ejer** til programmet AD du oprettede i den seneste procedure. Når du udfører den korrekt, skal du se det program, der er angivet i bladet brugere med rollen ejer.


##<a name="develop-hdinsight-client-application"></a>Udvikle HDInsight-klientprogrammet

Oprette et C# .net console-program, følge vejledningen findes i [sende Hadoop job i HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Erstat derefter metoden GetTokenCloudCredentials med følgende:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Til at hente-ID-lejer via PowerShell:

    Get-AzureRmSubscription

Eller Azure CLI:

    azure account show --json

      
## <a name="see-also"></a>Se også

- [Sende Hadoop-job i HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Oprette Active Directory-program og service hovedstolen ved hjælp af portalen](../resource-group-create-service-principal-portal.md)
- [Godkende service hovedstolen med Azure ressourcestyring](../resource-group-authenticate-service-principal.md)
- [Azure rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md)
