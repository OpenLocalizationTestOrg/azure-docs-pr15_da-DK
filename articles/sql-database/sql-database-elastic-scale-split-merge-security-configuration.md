<properties 
    pageTitle="Konfiguration af delt Flet sikkerhed | Microsoft Azure" 
    description="Konfigurere x409 certifikater til kryptering" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Konfiguration af delt Flet sikkerhed  

Hvis du vil bruge tjenesten opdelt/fletningen, skal du konfigurere sikkerhed korrekt. Tjenesten er en del af funktionen elastiske skala i Microsoft Azure SQL-Database. Se [elastiske skala opdelt og flette Service selvstudium](sql-database-elastic-scale-configure-deploy-split-and-merge.md)kan finde flere oplysninger.

## <a name="configuring-certificates"></a>Konfiguration af certifikater

Certifikater er konfigureret på to måder. 

1. [Konfigurere SSL-certifikat](#To-Configure-the-SSL#Certificate)
2. [Sådan konfigureres klient certifikater](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Til at hente certifikater

Certifikater kan fås fra offentlige nøglecentre (nøglecentre) eller fra [Windows certifikat-tjenesten](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Dette er den foretrukne metode til at hente certifikater.

Hvis disse indstillinger ikke er tilgængelig, kan du oprette **selvsignerede certifikater**.
 
## <a name="tools-to-generate-certificates"></a>Værktøjer til at oprette certifikater

* [MakeCert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Til at køre værktøjerne

* Fra en udvikler kommandoprompt for visuelle Studios se [Visual Studio kommandoprompt](http://msdn.microsoft.com/library/ms229859.aspx) 

    Hvis installeret, skal du gå til:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Få WDK fra [Windows 8.1: hente værktøjer og pakker](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Konfigurere SSL-certifikat
Et SSL-certifikat er påkrævet til at kryptere kommunikationen og godkende serveren. Vælg de mest relevante af de tre scenarier nedenfor, og udføre alle dens trin:

### <a name="create-a-new-self-signed-certificate"></a>Oprette et nyt selvsigneret certifikat

1.    [Oprette et selvsigneret certifikat](#Create-a-Self-Signed-Certificate)
2.    [Oprette PFX-fil til Self-Signed SSL-certifikat](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Overføre et SSL-certifikat for at tjeneste i skyen](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Opdatere SSL-certifikat i tjenesten konfigurationsfil](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importere SSL nøglecenter](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Bruge et eksisterende certifikat fra store certifikat
1. [Eksportere SSL-certifikat fra lager](#Export-SSL-Certificate-From-Certificate-Store)
2. [Overføre et SSL-certifikat for at tjeneste i skyen](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Opdatere SSL-certifikat i tjenesten konfigurationsfil](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Bruge et eksisterende certifikat i en PFX-fil

1. [Overføre et SSL-certifikat for at tjeneste i skyen](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Opdatere SSL-certifikat i tjenesten konfigurationsfil](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Sådan konfigureres klient certifikater
Klient certifikaterne er påkrævet for at godkende forespørgsler til webtjenesten. Vælg de mest relevante tre eksemplerne nedenfor, og udføre alle dens trin:

### <a name="turn-off-client-certificates"></a>Deaktivere klientcertifikater
1.    [Deaktivere klienten certifikat-baseret godkendelse](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Problem med nye selvsigneret klientcertifikater
1.    [Oprette et selvsigneret nøglecenter](#Create-a-Self-Signed-Certification-Authority)
2.    [Overføre CA certifikat for at tjeneste i skyen](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Opdatere certifikat i tjenesten konfigurationsfil](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Udstede klientcertifikater](#Issue-Client-Certificates)
5.    [Oprette PFX-filer til klientcertifikater](#Create-PFX-files-for-Client-Certificates)
6.    [Importér klientcertifikat](#Import-Client-Certificate)
7.    [Kopiere klient certifikat Thumbprints](#Copy-Client-Certificate-Thumbprints)
8.    [Konfigurere tilladte klienter i tjenesten konfigurationsfil](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Bruge eksisterende klientcertifikater
1.    [Finde CA offentlig nøgle](#Find-CA-Public Key)
2.    [Overføre CA certifikat for at tjeneste i skyen](#Upload-CA-certificate-to-cloud-service)
3.    [Opdatere certifikat i tjenesten konfigurationsfil](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Kopiere klient certifikat Thumbprints](#Copy-Client-Certificate-Thumbprints)
5.    [Konfigurere tilladte klienter i tjenesten konfigurationsfil](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Konfigurere kontrol af tilbagekaldelse klient certifikat](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Tilladte IP-adresser

Adgang til tjenesten slutpunkterne kan være begrænset til bestemte områder af IP-adresser.

## <a name="to-configure-encryption-for-the-store"></a>Sådan konfigureres kryptering for store

Et certifikat er påkrævet til at kryptere legitimationsoplysninger, der er gemt i metadatalageret. Vælg de mest relevante af de tre scenarier nedenfor, og udføre alle dens trin:

### <a name="use-a-new-self-signed-certificate"></a>Brug et nyt selvsigneret certifikat

1.     [Oprette et selvsigneret certifikat](#Create-a-Self-Signed-Certificate)
2.     [Oprette PFX-fil til Self-Signed krypteringscertifikat](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Overføre krypteringscertifikat for at tjeneste i skyen](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Opdatere krypteringscertifikat i tjenesten konfigurationsfil](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Bruge et eksisterende certifikat fra store certifikat

1.     [Eksportere krypteringscertifikat fra lager](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Overføre krypteringscertifikat for at tjeneste i skyen](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Opdatere krypteringscertifikat i tjenesten konfigurationsfil](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Bruge et eksisterende certifikat i en PFX-fil

1.     [Overføre krypteringscertifikat for at tjeneste i skyen](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Opdatere krypteringscertifikat i tjenesten konfigurationsfil](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>Standardkonfiguration

Standardkonfigurationen afviser alle adgang til http-slutpunkt. Dette er den anbefalede indstilling, da anmodninger om til disse slutpunkter kan foretage følsomme oplysninger som databaselegitimationsoplysninger.
Standardkonfiguration giver alle adgang til HTTPS-slutpunkt. Denne indstilling kan være begrænset yderligere.

### <a name="changing-the-configuration"></a>Ændre konfigurationen

Gruppen af kontrolelementet adgangsregler, der gælder for- og slutpunkt er konfigureret i den **<EndpointAcls>** sektion i **tjenesten konfigurationsfil**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Regler i en access-kontrolelementet gruppe er konfigureret i en <AccessControl name=""> sektion af konfigurationsfil tjeneste. 

Formatet, der er beskrevet i netværk adgangskontrollister dokumentation.
For eksempel for at tillade kun IP'er i området 100.100.0.0 til 100.100.255.255 for at få adgang til HTTPS slutpunktet, ville reglerne, der se således ud:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Manglende adgang til tjenesten forebyggelse

Der er to forskellige mekanismer understøttes for at registrere og forhindre nægtelse af Service-angreb:

*    Begrænse antallet af samtidige anmodninger om eksterne vært (fravalgt som standard)
*    Begrænse rente access per eksterne vært (på som standard)

Disse er baseret på de funktioner, der er beskrevet yderligere i dynamiske IP-sikkerhed i IIS. Når ændring af denne konfiguration Vær opmærksom på følgende faktorer:

* Funktionsmåden for proxyer og NAT enheder over eksterne værtsoplysninger
* Hver anmodning om en ressource i web rollen betragtes som (fx indlæsning scripts, billeder osv.)

## <a name="restricting-number-of-concurrent-accesses"></a>Begrænse antallet af samtidige adgang

De indstillinger, konfigurere denne funktionalitet er:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Ændre DynamicIpRestrictionDenyByConcurrentRequests til Sand for at aktivere denne beskyttelse.

## <a name="restricting-rate-of-access"></a>Begrænse rente i access

De indstillinger, konfigurere denne funktionalitet er:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Konfiguration af svar på en nægtet

Følgende indstilling konfigurerer svar på en nægtet:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Se dokumentationen til dynamisk IP-sikkerhed i IIS til andre understøttede værdier.

## <a name="operations-for-configuring-service-certificates"></a>Handlinger til at konfigurere tjenesten certifikater
Dette emne er kun til reference. Følg de trin til konfiguration, der er beskrevet i:

* Konfigurere SSL-certifikat
* Konfigurere klientcertifikater

## <a name="create-a-self-signed-certificate"></a>Oprette et selvsigneret certifikat
Udføre:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Sådan tilpasser du:

*    -n med URL-adressen. Jokertegn ("CN = * .cloudapp .net") og alternative navne ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") understøttes.
*    e - med udløbsdatoen certifikat Opret en stærk adgangskode, og Angiv det, når du bliver bedt om.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Oprette PFX-fil for selvsigneret SSL-certifikat

Udføre:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Angiv din adgangskode og derefter eksportere certifikat med disse indstillinger:
* Ja, eksport privat nøgle
* Eksport alle udvidede egenskaber

## <a name="export-ssl-certificate-from-certificate-store"></a>Eksportere SSL-certifikat fra lager

* Finde certifikat
* Klik på Handlinger -> alle opgaver -> Eksportér...
* Eksportere certifikat til en. PFX-fil med disse indstillinger:
    * Ja, eksport privat nøgle
    * Hvis det er muligt at medtage alle certifikater i certificering stien * eksportere alle udvidede egenskaber

## <a name="upload-ssl-certificate-to-cloud-service"></a>Overfør et SSL-certifikat til skybaseret tjeneste

Overførsel af certifikat med den nuværende eller genereres. PFX-filen med vigtige parret SSL:

* Skriv den adgangskode, beskytte oplysninger om den privat nøgle

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Opdatere SSL-certifikat i tjenesten konfigurationsfil

Opdatere miniatureværdien af følgende indstilling i tjenesten konfigurationsfil med miniature for det certifikat, der er overført til skytjenesten:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importere SSL nøglecenter

Følg disse trin i alle konto/computer, der kan kommunikere med tjenesten:

* Dobbeltklik på den. Virksomheden fil i Windows Stifinder
* Klik på Installer certifikat i dialogboksen certifikat...
* Importere certifikat på butikken, der er tillid til rodnøglecentre

## <a name="turn-off-client-certificate-based-authentication"></a>Deaktivere klienten certifikat-baseret godkendelse

Understøttes kun certifikat-baseret klientgodkendelse og deaktivere det vil tage højde for offentlig adgang til slutpunkter service, medmindre anden metode er på plads (fx Microsoft Azure Virtual Network).

Ændre indstillingerne til falsk i tjenesten konfigurationsfil at slå funktionen fra:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Derefter skal du kopiere den samme miniature som SSL-certifikatet i indstillingen CA certifikat:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Oprette et selvsigneret nøglecenter
Udfør følgende trin for at oprette et selvsigneret certifikat skal fungere som et nøglecenter:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

At tilpasse den

*    e - med udløbsdatoen certificering


## <a name="find-ca-public-key"></a>Finde CA offentlig nøgle

Alle klientcertifikater skal være udstedt af et nøglecenter, der er tillid til af tjenesten. Find den offentlige nøgle til det nøglecenter, der udstedes af klienten certifikater, der skal bruges til godkendelse for at overføre den til skybaseret tjeneste.

Hvis filen med offentlig nøgle ikke er tilgængelig, kan du eksportere den fra certifikat store:

* Finde certifikat
    * Søge efter et klientcertifikat udstedt af det samme nøglecenter
* Dobbeltklik på certifikatet.
* Vælg fanen certificering stien i dialogboksen certifikat.
* Dobbeltklik på posten CA i stien.
* Tage noter af egenskaberne certifikat.
* Luk dialogboksen **certifikat** .
* Finde certifikat
    * Søg efter det Nøglecenter, der er nævnt ovenfor.
* Klik på Handlinger -> alle opgaver -> Eksportér...
* Eksportere certifikat til en. Virksomheden med disse indstillinger:
    * **Nej, eksport ikke den private nøgle**
    * Medtage alle certifikater i certificering stien, hvis det er muligt.
    * Eksportere alle udvidede egenskaber.

## <a name="upload-ca-certificate-to-cloud-service"></a>Overfør certifikat til skybaseret tjeneste

Overførsel af certifikat med den nuværende eller genereres. Virksomheden-fil med den offentlige nøgle CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Opdater certifikat i tjenesten konfigurationsfil

Opdatere miniatureværdien af følgende indstilling i tjenesten konfigurationsfil med miniature for det certifikat, der er overført til skytjenesten:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Opdatere værdien af følgende indstilling med det samme miniature:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Udstede klientcertifikater

Hver person, der er autoriseret til at få adgang til tjenesten skal have et klientcertifikat, der er udstedt til his/hers eksklusivt brug og skal vælge his/hers ejer stærk adgangskode for at beskytte tilhørende privat nøgle. 

Følgende trin skal udføres i den samme computer, hvor et selvsigneret certifikat CA blev oprettet og gemt:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Tilpasning:

* -n med et ID for at den klient, der skal godkendes med dette certifikat
* e - med udløbsdatoen certifikat
* MyID.pvk og MyID.cer med entydige filnavne for denne klientcertifikat

Denne kommando bliver bedt om en adgangskode til oprettes og bruges én gang. Brug en stærk adgangskode.

## <a name="create-pfx-files-for-client-certificates"></a>Oprette PFX-filer for klienten certifikater

For hvert enkelt genererede klientcertifikat udføre:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Tilpasning:

    MyID.pvk and MyID.cer with the filename for the client certificate

Angiv din adgangskode og derefter eksportere certifikat med disse indstillinger:

* Ja, eksport privat nøgle
* Eksport alle udvidede egenskaber
* Den person, som er der udstedt dette certifikat skal vælge Eksportér adgangskoden

## <a name="import-client-certificate"></a>Importér klientcertifikat

Hver enkelt bruger, der er udstedt et klientcertifikat skal importere nøglerne på de computere, han/hun vil bruge til at kommunikere med tjenesten:

* Dobbeltklik på den. PFX-fil i Windows Stifinder
* Importér certifikatet i personlige Gem med mindst denne indstilling:
    * Medtag alle udvidede egenskaber, der er markeret

## <a name="copy-client-certificate-thumbprints"></a>Kopiere klient certifikat thumbprints
Hver enkelt bruger, der er udstedt et klientcertifikat skal du følge disse trin for at få miniature af his/hers certifikat, som vil blive føjet til konfigurationsfil service:
* Køre certmgr.exe
* Vælg fanen personlige
* Dobbeltklik på klientcertifikat skal bruges til godkendelse
* Vælg fanen Detaljer i dialogboksen certifikat, der åbnes
* Sørg for, at Vis viser alle
* Vælg feltet med navnet miniature i listen
* Kopiér værdien af miniature **slette synlige Unicode-tegn foran det første ciffer** Slet alle mellemrum

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurere tilladte klienter i tjenesten konfigurationsfil

Opdater værdien af følgende indstilling i filen service konfiguration med en kommasepareret liste over thumbprints af klientcertifikaterne har adgang til tjenesten:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Konfigurere kontrol af tilbagekaldelse klient certifikat

Standardindstillingen kontrollerer ikke med nøglecenter for klient certifikatet. Hvis du vil slå Kontroller, hvis det nøglecenter, der har udstedt af klienten certifikater understøtter kontrollen, skal du ændre indstillingen følgende hos en af de værdier, der er defineret i X509RevocationMode optælling:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Oprette PFX-fil til selvsigneret krypteringscertifikater

For et krypteringscertifikat udføre:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Tilpasning:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Angiv din adgangskode og derefter eksportere certifikat med disse indstillinger:
*    Ja, eksport privat nøgle
*    Eksport alle udvidede egenskaber
*    Du skal bruge adgangskoden, når du overfører certifikatet til skybaseret tjeneste.

## <a name="export-encryption-certificate-from-certificate-store"></a>Eksportere krypteringscertifikat fra lager

*    Finde certifikat
*    Klik på Handlinger -> alle opgaver -> Eksportér...
*    Eksportere certifikat til en. PFX-fil med disse indstillinger: 
  *    Ja, eksport privat nøgle
  *    Hvis det er muligt at medtage alle certifikater i certificering stien 
*    Eksport alle udvidede egenskaber

## <a name="upload-encryption-certificate-to-cloud-service"></a>Overfør krypteringscertifikat til skybaseret tjeneste

Overførsel af certifikat med den nuværende eller genereres. PFX-filen med vigtige parret kryptering:

* Skriv den adgangskode, beskytte oplysninger om den privat nøgle

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Opdatere krypteringscertifikat i tjenesten konfigurationsfil

Opdatere miniatureværdien af følgende indstillinger i tjenesten konfigurationsfil med miniature for det certifikat, der er overført til skytjenesten:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Almindelige certifikathandlinger

* Konfigurere SSL-certifikat
* Konfigurere klientcertifikater

## <a name="find-certificate"></a>Finde certifikat

Følg disse trin:

1. Køre mmc.exe.
2. Filer -> Tilføj/fjern Snap-in...
3. Vælg **certifikater**.
4. Klik på **Tilføj**.
5. Vælg certifikat store sted.
6. Klik på **Udfør**.
7. Klik på **OK**.
8. Udvid **certifikater**.
9. Udvid noden certifikat store.
10. Udvid noden certifikat underordnede.
11. Vælg et certifikat på listen.

## <a name="export-certificate"></a>Eksportere certifikat
I **eksportguiden af certifikat**:

1. Klik på **Næste**.
2. Vælg **Ja**, derefter **eksportere privat nøgle**.
3. Klik på **Næste**.
4. Vælg det ønskede output-filformat.
5. Markér de ønskede indstillinger.
6. Markér afkrydsningsfeltet **adgangskode**.
7. Indtast en stærk adgangskode og bekræft den.
8. Klik på **Næste**.
9. Skriv eller gennemse et filnavn, hvor du vil gemme certifikatet (Brug en. PFX lokalnummer).
10. Klik på **Næste**.
11. Klik på **Udfør**.
12. Klik på **OK**.

## <a name="import-certificate"></a>Importer certifikat

I importguiden:

1. Vælg placeringen, store.

    * Vælg **Aktuel bruger** , hvis der kun processer, der kører under aktuelle bruger skal have adgang til tjenesten
    * Vælg **Lokal computer** , hvis andre processer på computeren skal have adgang til tjenesten
2. Klik på **Næste**.
3. Hvis du importerer fra en fil, du bekræfte stien til filen.
4. Hvis du importerer en. PFX-filen:
    1.     Skriv den adgangskode, beskytte privat nøgle
    2.     Vælg importindstillinger
5.     Vælg "Placering" certifikater i følgende store
6.     Klik på **Gennemse**.
7.     Vælg den ønskede butik.
8.     Klik på **Udfør**.
       
    * Hvis der er tillid til rodnøglecenter store blev valgt, skal du klikke på **Ja**.
9.     Klik på **OK** i alle vinduer i dialogboksen.

## <a name="upload-certificate"></a>Overføre certifikat

[Azure-portalen](https://portal.azure.com/)

1. Vælg **Cloud Services**.
2. Vælg tjenesten i skyen.
3. Klik på den øverste menu **certifikater**.
4. Klik på **Send**på den nederste linje.
5. Vælg certifikatfil.
6. Hvis det er en. PFX filer, Skriv adgangskoden til den private nøgle.
7. Når færdig, skal du kopiere certifikataftrykket fra den nye post på listen.

## <a name="other-security-considerations"></a>Andre overvejelser om sikkerheden
 
SSL-indstillingerne, der er beskrevet i dette dokument kryptere kommunikationen mellem tjenesten og dens klienter, når HTTPS slutpunktet bruges. Dette er vigtigt siden legitimationsoplysninger for adgang til databaser og potentielt andre følsomme oplysninger er indeholdt i meddelelsen. Bemærk, at tjenesten fortsætter interne status, herunder legitimationsoplysninger, i dets interne tabeller i Microsoft Azure SQL-database, du har angivet for metadata-lageret i dit Microsoft Azure-abonnement. Databasen er defineret som en del af følgende indstilling i din konfigurationsfil service (. CSCFG-fil): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Legitimationsoplysninger, der er gemt i denne database er krypteret. Dog sikre, at både internettet og arbejder roller service installation af holdes opdaterede og sikker som de begge har adgang til metadatadatabasen og certifikatet, der bruges til kryptering og dekryptering af gemte legitimationsoplysninger som en bedste fremgangsmåde. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
