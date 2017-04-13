<properties 
    pageTitle="Konfigurere SSL til en skybaseret tjeneste (klassisk) | Microsoft Azure" 
    description="Lære at angive et HTTPS slutpunkt for en web rolle og hvordan du overfører et SSL-certifikat for at sikre dit program." 
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

> [AZURE.NOTE] Anvende procedurerne i denne opgave til Azure Skytjenester; Se [denne](../app-service-web/web-sites-configure-ssl-certificate.md) artikel for App-tjenester.

Denne opgave bruger et produktionsmiljø. Oplysninger om brug af en midlertidig installation er angivet i slutningen af dette emne.

Læs [denne](cloud-services-how-to-create-deploy.md) artikel, hvis du ikke har, men oprettet en skybaseret tjeneste.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]


## <a name="step-1-get-an-ssl-certificate"></a>Trin 1: Hente et SSL-certifikat

Hvis du vil konfigurere SSL for et program, skal du først få et SSL-certifikat, der er blevet signeret af et certifikat nøglecenter (CA), der er tillid til tredjepart der udsteder certifikater til dette formål. Hvis du ikke allerede har en, skal du oprette en hos en virksomhed, der sælger SSL-certifikater.

Certifikatet, der skal opfylde følgende krav til SSL-certifikater i Azure:

-   Certifikatet, der skal indeholde en privat nøgle.
-   Certifikatet, der skal oprettes til vigtige exchange, eksporteres til en Personal Information Exchange (.pfx)-fil.
-   Certifikatets emnenavn skal svare til det domæne, der bruges til at få adgang til tjenesten skyen. Du kan ikke hente et SSL-certifikat fra et nøglecenter (CA) for domænet, cloudapp.net. Du skal have et brugerdefineret domænenavn til bruges til at få adgang til tjenesten. Når du anmoder om et certifikat fra et Nøglecenter, skal det certifikat emnenavn svarer til det brugerdefinerede domænenavn, der bruges til at få adgang til dit program. Eksempelvis hvis dit eget domænenavn er **contoso.com** du vil anmode om et certifikat fra dit Nøglecenter for * **. contoso.com** eller * *www.contoso.com**.
-   Certifikatet, der skal bruge et minimum af 2048-bit kryptering.

Til testformål, kan du [oprette](cloud-services-certs-create.md) og bruge et selvsigneret certifikat. Et selvsigneret certifikat er ikke godkendt gennem et Nøglecenter og kan bruge cloudapp.net domænet som URL-adressen til websted. For eksempel bruger følgende opgave et selvsigneret certifikat, hvor det almindelige navn (CN) bruges i certifikatet, der er **sslexample.cloudapp.net**.

Derefter skal du medtage oplysninger om certifikatet i dine tjenestedefinitionen og tjenesten konfigurationsfiler.

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
    
    Tilladelser (`permisionLevel` attribut) kan indstilles til en af følgende værdier:

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

3.  Tilføje en **Binding** element i afsnittet **websteder** i din tjeneste definitionsfil. Dette afsnit tilføjer en HTTPS-binding tilknytte slutpunktet til dit websted:

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

(Det foregående eksempel bruger **sha1** for algoritmen miniature. Angiv den relevante værdi for certifikatets miniature algoritme).

Nu hvor tjenestedefinitionen og service konfigurationsfilerne er blevet opdateret, Pak din-overførsel til Azure. Hvis du bruger **cspack**, ikke bruge flaget **/generateConfigurationFile** som, overskriver oplysningerne om certifikatet du har indsat.

## <a name="step-3-upload-a-certificate"></a>Trin 3: Overføre et certifikat

Din installationspakke er blevet opdateret for at bruge certifikatet, og HTTPS ark er blevet tilføjet. Nu kan du overføre pakke og certifikat til Azure ved hjælp af Azure klassisk portal.

1. Log på [Azure klassisk portal][]. 
2. Klik på **Cloud Services** i den venstre navigationsrude.
3. Klik på den ønskede skybaseret tjeneste.
4. Klik på fanen **certifikater** .

    ![Klik på fanen certifikater](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Klik på knappen **Overfør** .

    ![Overføre](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Angive **fil**, **adgangskode**, og klik derefter på **udført** (markering).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Trin 4: Oprette forbindelse til den rolle forekomst ved hjælp af HTTPS

Nu, hvor din installation er oppe at køre i Azure, kan du oprette forbindelse til den ved hjælp af HTTPS.

1.  Vælg din installation i Azure klassisk portalen, og derefter klikke på linket under **Webstedets URL-adresse**.

    ![Bestemme webstedets URL-adresse][2]

2.  Rediger på linket for at bruge **https** i stedet for **http**i din webbrowser, og derefter gå til siden.

    >[AZURE.NOTE] Hvis du bruger et selvsigneret certifikat, når du navigerer til en HTTPS-slutpunkt, der er knyttet til et selvsigneret certifikat vises muligvis en fejl ved certifikat i browseren. Ved hjælp af et certifikat, der er signeret af et nøglecenter løser problemet. i mellemtiden kan du ignorere fejlen. (En anden mulighed er at føje et selvsigneret certifikat til brugerens der er tillid til certifikatet nøglecenter lager).

    ![SSL eksempel-websted][3]

Hvis du vil bruge SSL til en midlertidig installation i stedet for et produktionsmiljø, skal du først at finde ud af den URL-adresse bruges til den midlertidige installation. Installere skybaseret tjeneste i det midlertidige miljø uden at medtage et certifikat eller en hvilken som helst certifikatoplysninger. Når installeret, kan du bestemme GUID-baserede URL-adressen, som er angivet i den Azure klassisk portal **Webstedets URL-adresse** felt. Opret et certifikat med det almindelige navn (CN) lig med GUID-baserede URL-adressen (for eksempel **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Brug Azure klassisk portalen til at tilføje certifikatet til din faseinddelt skybaseret tjeneste. Tilføj derefter oplysningerne om certifikatet CSDEF og CSCFG filen, pakke dit program og opdatere faseinddelt installationen for at bruge den nye pakke.

## <a name="next-steps"></a>Næste trin

* [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure.md).
* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name.md).
* [Administrer din skybaseret tjeneste](cloud-services-how-to-manage.md).


  [Azure klassisk portal]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  
