<properties 
    pageTitle="Konfigurere SSL til en skybaseret tjeneste | Microsoft Azure" 
    description="Lære at angive et HTTPS slutpunkt for en web rolle og hvordan du overfører et SSL-certifikat for at sikre dit program. Disse eksempler bruger Azure portalen." 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016"
    ms.author="adegeo"/>




# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurere SSL for et program i Azure

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-configure-ssl-certificate-portal.md)
- [Azure klassisk portal](cloud-services-configure-ssl-certificate.md)

Secure Socket Layer (SSL) kryptering er den mest almindeligt anvendte metode til sikring af data, der sendes på tværs af internettet. Denne almindelige opgave beskrives, hvordan du kan angive et HTTPS slutpunkt for en web rolle og hvordan du overfører et SSL-certifikat for at sikre dit program.

> [AZURE.NOTE] Anvende procedurerne i denne opgave til Azure Skytjenester; få vist [denne](../app-service-web/web-sites-configure-ssl-certificate.md)for App-tjenester.

Denne opgave bruger et produktionsmiljø; oplysninger om brug af en midlertidig installation er angivet i slutningen af dette emne.

Læs [denne](cloud-services-how-to-create-deploy-portal.md) først Hvis du ikke har, men oprettet en skybaseret tjeneste.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## <a name="step-1-get-an-ssl-certificate"></a>Trin 1: Hente et SSL-certifikat

Hvis du vil konfigurere SSL for et program, skal du først få et SSL-certifikat, der er blevet signeret af et certifikat nøglecenter (CA), der er tillid til tredjepart der udsteder certifikater til dette formål. Hvis du ikke allerede har en, skal du oprette en hos en virksomhed, der sælger SSL-certifikater.

Certifikatet, der skal opfylde følgende krav til SSL-certifikater i Azure:

-   Certifikatet, der skal indeholde en privat nøgle.
-   Certifikatet, der skal oprettes til vigtige exchange, eksporteres til en Personal Information Exchange (.pfx)-fil.
-   Certifikatets emnenavn skal svare til det domæne, der bruges til at få adgang til tjenesten skyen. Du kan ikke hente et SSL-certifikat fra et nøglecenter (CA) for domænet, cloudapp.net. Du skal have et brugerdefineret domænenavn til bruges til at få adgang til tjenesten. Når du anmoder om et certifikat fra et Nøglecenter skal certifikatets emnenavn til det brugerdefinerede domænenavn, der bruges til at få adgang til dit program. Eksempelvis hvis dit eget domænenavn er **contoso.com** du vil anmode om et certifikat fra dit Nøglecenter for * **. contoso.com** eller * *www.contoso.com**.
-   Certifikatet, der skal bruge et minimum af 2048-bit kryptering.

Til testformål, kan du [oprette](cloud-services-certs-create.md) og bruge et selvsigneret certifikat. Et selvsigneret certifikat er ikke godkendt gennem et Nøglecenter og kan bruge cloudapp.net domænet som URL-adressen til websted. For eksempel bruger opgaven nedenfor et selvsigneret certifikat, hvor det almindelige navn (CN) bruges i certifikatet, der er **sslexample.cloudapp.net**.

Derefter skal du medtage oplysninger om certifikatet i dine tjenestedefinitionen og tjenesten konfigurationsfiler.

<a name="modify"> </a>
## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Trin 2: Ændre tjenesten definition og konfiguration af filer

Dit program skal være konfigureret til at bruge certifikatet, og et HTTPS slutpunkt skal tilføjes. Som et resultat skal tjenestedefinitionen og tjenesten konfigurationsfiler opdateres.

1.  I dit udviklingsmiljø åbne definitionsfil service (CSDEF), Tilføj en **certifikater** sektion i sektionen **WebRole** og indeholder følgende oplysninger om certifikatet (og mellemliggende certifikater):

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="My"
                             permissionLevel="limitedOrElevated" />
                <!-- IMPORTANT! Unless your certificate is either
                self-signed or signed directly by the CA root, you
                must include all the intermediate certificates
                here. You must list them here, even if they are
                not bound to any endpoints. Failing to list any of
                the intermediate certificates may cause hard-to-reproduce
                interoperability problems on some clients.-->
                <Certificate name="CAForSampleCertificate"
                             storeLocation="LocalMachine"
                             storeName="CA"
                             permissionLevel="limitedOrElevated" />
            </Certificates>
        ...
        </WebRole>

    Sektionen **certifikater** definerer navnet på vores certifikat, dens placering og navnet på den butik, hvor det er placeret.
    
    Tilladelser (`permisionLevel` attribut) kan indstilles til et af følgende:

  	| Tilladelse værdi  | Beskrivelse |
  	| ----------------  | ----------- |
  	| limitedOrElevated | **(Standard)** Alle rolle processer kan få adgang til den private nøgle. |
  	| udvidede          | Kun øgede processer kan få adgang til den private nøgle.|

2.  Føje et **InputEndpoint** element i afsnittet **slutpunkter** for at aktivere HTTPS i din tjeneste definitionsfil:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Tilføje en **Binding** element i afsnittet **websteder** i din tjeneste definitionsfil. Dette tilføjer en HTTPS-binding tilknytte slutpunktet til dit websted:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Alle de nødvendige ændringer til tjenesten definitionsfil er fuldført, men du stadig har brug at føje oplysningerne om certifikatet til tjenesten konfigurationsfil.

4.  Tilføje en **certifikater** sektion i sektionen **rolle** , erstatter den eksempel miniature værdi, der er vist nedenfor med adressen på dit certifikat i din tjeneste konfigurationsfil (CSCFG), ServiceConfiguration.Cloud.cscfg:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
                <Certificate name="CAForSampleCertificate"
                    thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(I eksemplet ovenfor bruges **sha1** for algoritmen miniature. Angiv den relevante værdi for certifikatets miniature algoritme).

Nu hvor tjenestedefinitionen og service konfigurationsfilerne er blevet opdateret, Pak din-overførsel til Azure. Hvis du bruger **cspack**, ikke bruge flaget **/generateConfigurationFile** som, overskriver oplysningerne om certifikatet du lige har indsat.

## <a name="step-3-upload-a-certificate"></a>Trin 3: Overføre et certifikat

Oprette forbindelse til portalen og...

1. Vælg din skybaseret tjeneste i portalen skal du vælge din **Skybaseret tjeneste**. (Som er i afsnittet **alle ressourcer** ). 
    
    ![Publicere dit skytjeneste](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klik på **certifikater**.

    ![Klik på ikonet certifikater](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Angiv **fil**, **adgangskode**, og klik derefter på **Overfør**.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Trin 4: Oprette forbindelse til den rolle forekomst ved hjælp af HTTPS

Nu, hvor din installation er oppe at køre i Azure, kan du oprette forbindelse til den ved hjælp af HTTPS.
    
1.  Klik på **Webstedets URL-adresse** til at åbne webbrowseren.

    ![Klik på webstedets URL-adresse](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2.  Rediger på linket for at bruge **https** i stedet for **http**i din webbrowser, og derefter gå til siden.

    >[AZURE.NOTE] Hvis du bruger et selvsigneret certifikat, når du navigerer til en HTTPS-slutpunkt, der er knyttet til et selvsigneret certifikat vises muligvis en fejl ved certifikat i browseren. Ved hjælp af et certifikat, der er signeret af et nøglecenter løser problemet. i mellemtiden kan du ignorere fejlen. (En anden mulighed er at føje et selvsigneret certifikat til brugerens der er tillid til certifikatet nøglecenter lager).

    ![Websted preview](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

    >[AZURE.TIP] Hvis du vil bruge SSL til en midlertidig installation i stedet for et produktionsmiljø, skal du først at finde ud af den URL-adresse bruges til den midlertidige installation. Når din skybaseret tjeneste er blevet installeret, bestemmes URL-adressen til det midlertidige miljø efter **Installation ID** GUID i dette format:`https://deployment-id.cloudapp.net/`  
      
    >Opret et certifikat med det almindelige navn (CN) lig med GUID-baserede URL-adressen (for eksempel **328187776e774ceda8fc57609d404462.cloudapp.net**). Brug portalen til at tilføje certifikatet til din faseinddelt skybaseret tjeneste. Tilføj derefter oplysningerne om certifikatet CSDEF og CSCFG filen, pakke dit program og opdatere faseinddelt installationen for at bruge den nye pakke.

## <a name="next-steps"></a>Næste trin

* [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure-portal.md).
* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy-portal.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name-portal.md).
* [Administrer din skybaseret tjeneste](cloud-services-how-to-manage-portal.md).
