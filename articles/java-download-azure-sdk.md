<properties 
    pageTitle="Hente Azure SDK til Java" 
    description="Lær, hvordan du henter Azure SDK til Java, med eksempelkode fastsat for Maven projekter og grundlæggende installationstrin Azure Tookit for Eklipse." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="multiple" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="download-the-azure-sdk-for-java"></a>Hente Azure SDK til Java

I denne artikel indeholder anvisninger for at hente og installere Azure-biblioteker til Java.

**Note:** Azure-biblioteker til Java fordeles under [Apache licens, Version 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Azure-biblioteker til Java - manuel overførsel

Du kan installere Azure-biblioteker til Java manuelt, skal du klikke på <http://go.microsoft.com/fwlink/?LinkId=690320> for at hente en ZIP-fil, som indeholder alle bibliotekerne og alle afhængigheder.

Når du har hentet zip-filen til din computer, udtrække indholdet og bruge en af følgende indstillinger til at føje glas filer til dit projekt:

* Importere glas filer til projektet Java i Eklipse.

* Konfigurere **Opbygge sti** for projektet Java i Eklipse medtage stien til glas-filer.

Du kan finde detaljerede oplysninger om oprettelse af build stier i Eklipse, i artiklen [Java opbygge sti] på webstedet Eklipse.

**Note:** Se.txt og ThirdPartyNotices.txt fil i POSTNUMMERET for licens og andre oplysninger.

## <a name="azure-libraries-for-java---building-with-maven"></a>Azure biblioteker til Java - opbygning af med Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Trin 1 - konfigurere dit projekt til at bruge Maven for build

Oprette Maven projekter i Eklipse som bruge Azure-biblioteker til Java, følge vejledningen i [Introduktion til Azure Management biblioteker til Java] [ maven-getting-started] artikel. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Trin 2 – konfigurere indstillingerne for Maven med de påkrævede afhængigheder

Når projektet er konfigureret for at bruge Maven til build, kan du tilføje den påkrævede afhængigheder til filen pom.xml ved hjælp af syntaksen som i følgende eksempel. Bemærk, at du ikke behøver at tilføje alle afhængighed, der er angivet i følgende eksempel Du skal kun tilføje de bestemte afhængigheder, der kræver, at dit projekt.

> [AZURE.NOTE] Inden for hver `<version>` element i det følgende eksempel erstatter pladsholderne "n.n.n" i dette eksempel med gyldige version tal, som kan fås fra [Azure biblioteker lager på Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Installation af Azure værktøjerne til Eklipse

Dette afsnit indeholder grundlæggende instruktioner til installation af Azure-værktøjskassen til Eklipse; finde detaljerede oplysninger, kan du se [installere Azure-værktøjskassen til Eklipse].

### <a name="prerequisites"></a>Forudsætninger

1. Windows operting systemer, der er angivet i artiklen [Nyheder i Azure-værktøjskassen til Eklipse] .
1. Macintosh eller Linux operting systemer, der er angivet i artiklen [Nyheder i Azure-værktøjskassen til Eklipse] .
1. Eklipse IDE for Java EE udviklere småt eller nyere. Dette kan hentes fra <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Grundlæggende installationstrin

1. Vælg **Installer nye programmer**i menuen **Hjælp** i Eklipse.
1. Websted placering <http://dl.microsoft.com/eclipse> og tryk på **Enter**.
1. Vælg elementerne, der skal være installeret, og klik på **Udfør**.

Azure-værktøjskassen til Eklipse bruger den nyeste version af Azure SDK. Dette kan hentes ved hjælp af Web Platform installationsprogrammet (WebPI) på <http://go.microsoft.com/fwlink/?LinkID=252838>. Men hvis du ikke har installeret den, når du opretter dit første Azure-installationsprojekt, Azure-værktøjskassen til Eklipse installerer automatisk den relevante version af Azure SDK.

## <a name="see-also"></a>Se også

[Azure-værktøjskassen til Eklipse]

[Installation af Azure værktøjerne til Eklipse] 

[Oprette et Hej verden program til Azure i Eklipse]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure biblioteker lager på Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java Build sti]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Hvad er nyt i Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=690333
