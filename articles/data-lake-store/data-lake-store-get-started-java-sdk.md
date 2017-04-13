<properties
   pageTitle="Bruge Data sø Store Java SDK til at udvikle programmer | Microsoft Azure"
   description="Bruge Azure Data sø Store Java SDK til at udvikle programmer"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introduktion til Azure Data sø Store ved hjælp af Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lær at bruge Azure Data sø Store Java SDK til at udføre grundlæggende handlinger, sådan som oprette mapper, upload og download datafiler osv. Du kan finde flere oplysninger om Data sø, [Azure sø datalager](data-lake-store-overview.md).

Du kan få adgang til Java SDK API-dokumenter til Azure sø datalager på [Azure Data sø Store Java API-dokumenter](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Forudsætninger

* Java Development Kit (JDK 7 eller nyere ved hjælp af Java version 1,7 eller nyere)
* Azure datalager sø konto. Følg vejledningen på [Introduktion til Azure Data sø Store ved hjælp af portalen Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Dette selvstudium bruger Maven for build og project afhængigheder. Selvom det er muligt at opbygge uden at bruge et build system som Maven eller Gradle, er disse systemer foretage meget nemmere at administrere afhængigheder.
* (Valgfrit) Og IDE som [IntelliJ ide](https://www.jetbrains.com/idea/download/) eller [Eklipse](https://www.eclipse.org/downloads/) eller lignende.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hvordan kan jeg godkende ved hjælp af Azure Active Directory?

I dette selvstudium bruger vi hemmeligt klienten en Azure AD-program til at hente en Azure Active Directory-token (service-tjenesten godkendelse). Vi bruger dette token til at oprette et datalager sø klientobjekt for at udføre handlinger filer og mapper handlinger. For oplysninger om, hvordan du vil godkende med Azure Data sø Store ved hjælp af klienten hemmeligt, foretager vi de følgende overordnede trin:

1. Oprette et Azure AD-webprogram
2. Hent den klient-ID, klienten hemmeligt og token slutpunkt for Azure AD-webprogrammet.
3. Konfigurere adgang til Azure AD-webprogrammet sø datalager fil/mappe, du vil have adgang til fra den Java-program, du opretter.

Du kan finde oplysninger om, hvordan du kan udføre disse trin, [Opret et Active Directory-program](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory indeholder andre indstillinger samt til at hente et token. Du kan vælge fra en række forskellige godkendelsesmetoder, så det passer til det pågældende scenarie, for eksempel køres i en browser, et program, der er distribueret som en på computeren eller en serverprogram, der kører lokalt eller i en Azure virtuelt et program. Du kan også vælge mellem forskellige typer legitimationsoplysninger som adgangskoder, certifikater, 2 faktor-godkendelse osv. Desuden Azure Active Directory giver dig mulighed at synkronisere dine lokale Active Directory-brugere med skyen. Yderligere oplysninger finder du [Godkendelse scenarier, hvor Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Oprette en Java-program

Koden eksempel tilgængelig [på GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) beskrives dig gennem processen med at oprette filer i store, sammenkæde filer, hente en fil og slette nogle filer i store. Dette afsnit i denne artikel hjælper dig med de vigtigste dele af koden.

1. Oprette et Maven projekt ved hjælp af [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) fra kommandolinjen eller ved hjælp af en IDE. Finde oplysninger om, hvordan du opretter en Java-projekt, ved hjælp af IntelliJ, [her](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Finde oplysninger om, hvordan du opretter et projekt ved hjælp af Eklipse, [her](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Tilføj følgende afhængigheder til filen Maven **pom.xml** . Tilføj følgende kodestykke teksts mellem den ** \</version >** mærke og ** \</projekt >** mærke:

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    Den første afhængighed er at bruge Data sø Store SDK (`azure-datalake-store`) fra maven lager. Den anden afhængighed (`slf4j-nop`) er at angive, hvilke logføring framework skal bruges til dette program. Data sø Store SDK bruger [slf4j](http://www.slf4j.org/) logføring facaden, hvor du kan vælge mellem en række populære logføring strukturer, som log4j, Java logføring, logback osv., eller ingen logføring. I dette eksempel skal vi deaktiverer logføring, derfor vi bruge **slf4j nop** binding. Hvis du vil bruge andre indstillinger for logføring i din app, skal du se [her](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Tilføje programkoden

Der findes tre hoveddele til koden.

1. Få Azure Active Directory-tokenet

2. Brug tokenet til at oprette en sø datalager klient.

3. Bruge sø datalager-klienten til at udføre handlinger.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Trin 1: Få et Azure Active Directory-token.

Data sø Store SDK indeholder praktisk metoder, som du kan få sikkerhedstokens det er nødvendigt at tale kontoen sø datalager. Dog kræver SDK ikke, at der benyttes kun disse metoder. Du kan bruge en hvilken som helst anden måde for at opnå token samt, som om brugen af [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java)eller din egen brugerdefinerede kode.

Hvis du vil bruge Data sø Store SDK til at hente token for Active Directory-webprogrammet du oprettede tidligere, bruge statisk metoderne i `AzureADAuthenticator` klasse. Erstat **UDFYLD-her** med de faktiske værdier for Azure Active Directory-webprogrammet.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Trin 2: Oprette et Azure datalager sø klienten (ADLStoreClient) objekt

Oprette et [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objekt, skal du angive sø datalager kontonavn og det Azure Active Directory-token, du har oprettet i det sidste trin. Bemærk, at kontonavnet sø datalager skal være et fuldt domænenavn. For eksempel Erstat **UDFYLD-her** med noget i retning af **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Trin 3: Brug ADLStoreClient til at udføre handlinger filer og mapper

Nedenstående kode indeholder eksempel kodestykker af nogle almindelige handlinger. Du kan se den fulde [Data sø Store Java SDK API dokumenter](https://azure.github.io/azure-data-lake-store-java/javadoc/) for objektet **ADLStoreClient** at få vist andre operationer.
 
Bemærk, at filer læses fra og skrives til ved hjælp af standard Java-stream. Det betyder, at du kan lag nogen af Java streams oven på sø datalager streams kan få glæde af Java standardfunktioner (f.eks., Udskriv streams for formateret output eller komprimering eller kryptering streams for flere funktioner på toppen osv.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Trin 4: Oprette og køre programmet

1. Find for at køre fra en IDE, og tryk på knappen **Kør** . For at køre fra Maven, skal du bruge [eksekverbar: eksekverbar](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. For at frembringe opbygge et enkeltstående glas, som du kan køre fra kommandolinjen krukken med alle afhængigheder, der er inkluderet, ved hjælp af [Maven samling plug-in'en](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Pom.xml i [eksempel kildekode på github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) er et eksempel på, hvordan du gør dette.


## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
