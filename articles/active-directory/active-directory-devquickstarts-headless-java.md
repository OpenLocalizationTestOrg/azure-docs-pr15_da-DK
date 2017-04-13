<properties
    pageTitle="Azure AD-Java Introduktion | Microsoft Azure"
    description="Sådan oprettes en Java kommandolinjen app, der logger brugere i at få adgang til en API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Brug af Java kommandolinjen App til at få adgang til en API med Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD gør det enkle og nemt at udlicitere din online identitet management, give enkelt logon og hvor med kun nogle få linjer af kode.  I Java webapps, kan du udføre dette med Microsofts implementering af den community-driven ADAL4J.

  Her skal vi bruge ADAL4J til:
- Log brugeren på appen med Azure AD som identitetsudbyder.
- Få vist nogle oplysninger om brugeren.
- Log brugeren af app'en.

Før du gør dette, skal du:

1. Registrere et program med Azure AD
2. Konfigurere din app til at bruge biblioteket ADAL4J.
3. Brug biblioteket ADAL4J til at udstede logon og hvor anmodninger til Azure AD.
4. Udskrive data om brugeren.

At komme i gang skal [hente app skelet](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  Du skal også en Azure AD-lejer som til at registrere dit program.  Hvis du ikke allerede har et, [se, hvordan du få en](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. registrere et program med Azure AD
Hvis du vil aktivere din app til at godkende brugere, skal du først registrere et nyt program i din lejer.

- Log på administrationsportalen Azure.
- Klik på **Active Directory**i til venstre i navigationen.
- Vælg den lejer, hvor du vil registrere programmet.
- Klik på fanen **programmer** , og klik på Tilføj i den nederste skuffe.
- Følg anvisningerne, og oprette et nyt **webprogram og/eller WebAPI**.
    - **Navnet** på programmet, som beskriver dit program for slutbrugere
    - **Sign-On URL-adresse** er den grundlæggende URL-adressen for din app.  Det skelet standard er `http://localhost:8080/adal4jsample/`.
    - **App ID URI** er et entydigt id for dit program.  Konferencen er at bruge `https://<tenant-domain>/<app-name>`, f.eks.`http://localhost:8080/adal4jsample/`
- Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen Konfigurer.

Oprette et **Program hemmeligt** for dit program én gang i portalen for din app og kopiere den ned.  Du skal bruge den kort.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. konfigurere din app til at bruge ADAL4J bibliotek og forudsætninger, der bruger Maven
Her skal vi konfigurere ADAL4J for at bruge OpenID forbinde authentication protocol.  ADAL4J bruges til at udstede logon og hvor anmodninger, administrere brugerens session og få oplysninger om brugeren, bl.a.

-   I rodmappen på dit projekt, Åbn/Opret `pom.xml` og Find den `// TODO: provide dependencies for Maven` og Erstat med følgende:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Opret filen java PublicClient

Som nævnt ovenfor skal skal vi bruge Graph API til at hente data om den bruger logget på. Dette skal være let for os skal vi oprette både en fil til at repræsentere en **Directory-objekt** og individuelle filer til at repræsentere **brugeren** , så OO anvendelse af Java kan bruges.

1. Oprette en fil kaldet `DirectoryObject.java` som vi skal bruge til at gemme grundlæggende data om en hvilken som helst DirectoryObject (du kan Velkommen til senere brug dette til andre Graph-forespørgsler, kan du gøre). Du kan klip og Indsæt dette nedefra:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Samle og køre eksemplet

Ændre ud igen til din rodmappe og køre følgende kommando for at oprette eksemplet, du lige har lagt sammen ved hjælp af `maven`. Dette vil bruge den `pom.xml` fil, du har skrevet for afhængigheder.

`$ mvn package`

Du bør nu har en `adal4jsample.war` filer i din `/targets` directory. Du kan installere, i din Tomcat objektbeholder og gå til URL-adressen 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
Det er meget nem at installere en KRIG med de nyeste Tomcat-servere. Bare gå til `http://localhost:8080/manager/` og følge instruktionerne på overførsel af dine "adal4jsample.war' fil. Det vil autodeploy for dig med det korrekte slutpunkt.

##<a name="next-steps"></a>Næste trin

Tillykke! Du har nu en arbejde Java-program, der har mulighed for at godkende brugere, sikkert opkald Web API'er ved hjælp af OAuth 2.0, og få grundlæggende oplysninger om brugeren.  Hvis du ikke allerede har gjort det, nu er det tid til at udfylde din lejer med nogle brugere.

Til reference kan på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip her](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), eller du klone den fra GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

