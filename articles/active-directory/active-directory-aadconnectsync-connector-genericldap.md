<properties
   pageTitle="Generisk LDAP-forbindelse | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Microsofts generisk LDAP-forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-ldap-connector-technical-reference"></a>Generisk teknisk vejledning til LDAP-forbindelse
I denne artikel beskrives generisk LDAP-forbindelsen. I artiklen gælder for følgende produkter:

- Microsoft identitet Manager 2016 (MIM2016)
- Forefront identitet Manager 2010 R2 (FIM2010R2)
    -   Skal bruge programrettelse 4.1.3671.0 eller nyere [KB3092178](https://support.microsoft.com/kb/3092178).

Forbindelsen er for MIM2016 og FIM2010R2 skal hentes fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Når der henvises til IETF RFC'er, er dette dokument ved hjælp af formatet (RFC [RFC antal] / [afsnit i RFC dokument]), for eksempel (RFC 4512/4.3).
Du kan finde flere oplysninger på http://tools.ietf.org/html/rfc4500 (du skal erstatte 4500 med det korrekte RFC-nummer).

## <a name="overview-of-the-generic-ldap-connector"></a>Oversigt over generisk LDAP-forbindelse
Generisk LDAP-forbindelsen gør det muligt at integrere tjenesten til synkronisering med en LDAP-v3 server.

Visse handlinger og skemaelementer som dem, der kræves for at udføre delta importen, er ikke angivet i IETF RFC'er. Til disse handlinger understøttes kun LDAP-mapper, der eksplicit er angivet.

Følgende funktioner understøttes af den aktuelle udgave af forbindelsen fra et overordnet perspektiv:

Funktion | Support
--- | --- |
Forbundne datakilde | Forbindelsen understøttes med alle servere, LDAP-v3 (RFC 4510 kompatibel). Det er testet med følgende: <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Microsoft Active Directory globalt katalog AD</li><li>389 directory-Server</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Åbn DJ</li><li>Åbn DS</li><li>Åbn LDAP (openldap.org)</li><li>Oracle (tidligere søn) Directory Server Enterprise Edition</li><li>RadiantOne virtuel mappe Server (tjenesten)</li><li>Søn én Directory Server</li>**Væsentlige kataloger, der ikke understøttes:** <li>Microsoft Active Directory-domænetjenester (AD DS) [Brug den indbyggede Active Directory-forbindelse i stedet]</li><li>Oracle Internetadresseliste (objekt-id)</li>
Scenarier   | <li>Objekt Livscyklusadministration</li><li>Gruppeadministration af</li><li>Administration af adgangskoder</li>
Handlinger |Følgende handlinger understøttes for alle LDAP-mapper: <li>Fuldstændig Import</li><li>Eksportere</li>Følgende handlinger understøttes kun på angivne mapper:<li>Deltaimport</li><li>Angive adgangskode, ændre adgangskode</li>
Skema | <li>Skema registreres fra LDAP-skemaet (RFC3673 og RFC4512/4.2)</li><li>Understøtter strukturel klasser, aux klasser og extensibleObject objektklasse (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Understøttelse af delta Importér og din adgangskode dokumentstyring
Understøttede mapper til Delta Importér og administration af adgangskoder:

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskode
- Microsoft Active Directory globalt katalog AD
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskode
- 389 directory-Server
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- Apache Directory Server
    - Understøtter ikke delta Importér, da denne mappe ikke har en fast ændringslog
    - Understøtter angive adgangskode
- IBM Tivoli DS
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- Isode Directory
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- Novell eDirectory og NetIQ eDirectory
    - Understøtter Tilføj, Opdater og Omdøb handlinger til Deltaimport
    - Understøtter ikke sletninger til Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- Åbn DJ
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- Åbn DS
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- Åbn LDAP (openldap.org)
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskode
    - Understøtter ikke Skift adgangskode
- Oracle (tidligere søn) Directory Server Enterprise Edition
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
- RadiantOne virtuel mappe Server (tjenesten)
    - Skal bruge version 7.1.1 eller nyere
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode
-  Søn én Directory Server
    - Understøtter alle handlinger for Deltaimport
    - Understøtter angive adgangskoden og Skift adgangskode

### <a name="prerequisites"></a>Forudsætninger
Før du bruger forbindelsen, skal du kontrollere følgende på serveren synkronisering være:

- Microsoft .NET 4.5.2 Framework eller nyere

### <a name="detecting-the-ldap-server"></a>Registrering af LDAP-serveren
Forbindelsen er afhængig af forskellige teknikker til at registrere og identificere LDAP-serveren. Forbindelsen bruges rod DSE, leverandør navn/version, og det inspicerer skemaet for at finde entydige objekter og attributter, der er blevet konstateret, at der findes i visse LDAP-servere. Disse data, hvis fundet, bruges til at udfylde allerede konfigurationsindstillinger i forbindelsen.

### <a name="connected-data-source-permissions"></a>Forbundne datakilde tilladelser
For at udføre import og eksport på objekterne i mappen forbindelsen, skal connector-kontoen har de nødvendige tilladelser. Forbindelsen skal have skrivetilladelser kunne eksportere, og Læs tilladelser til at kunne importere. Tilladelse konfiguration udføres inden for management erfaringerne fra destinationsmappen sig selv.

### <a name="ports-and-protocols"></a>Porte og protokoller
Forbindelsen bruger det portnummer, der er angivet i konfigurationen, der som standard er 389 til LDAP og 636 for LDAPS.

LDAPS, skal du bruge SSL 3.0 eller TLS. SSL 2.0 understøttes ikke og kan ikke aktiveres.

### <a name="required-controls-and-features"></a>Nødvendige kontrolelementer og funktioner
Følgende LDAP-kontrolelementer/funktioner, der skal være tilgængelige på LDAP-serveren for forbindelsen til at fungere korrekt:  
`1.3.6.1.4.1.4203.1.5.3`Sand/falsk filtre

Filteret sand/falsk ikke rapporteres ofte som understøttes af LDAP-mapper og kan ses på den **Globale side** under **Obligatorisk funktioner, der blev ikke fundet**. Det bruges til at oprette **eller** filtre i LDAP-forespørgsler, for eksempel, når du importerer flere objekttyper. Hvis du kan importere mere end én objekttype, understøtter LDAP-serveren denne funktion.

Hvis du bruger en mappe, hvor et entydigt id er anker være følgende også tilgængelig (se afsnittet [Konfigurere ankre](#configure-anchors) senere i denne artikel for at få mere at vide):  
`1.3.6.1.4.1.4203.1.5.1`Alle funktionsdygtige attributter

Hvis mappen indeholder flere objekter, end hvad kan være i et opkald til mappen, er det anbefales at bruge sideinddeling. Til sideopdeling af arbejde, du skal bruge en af følgende indstillinger:

**Mulighed 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Mulighed 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Hvis begge indstillinger er aktiveret i forbindelse konfiguration, bruges pagedResultsControl.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl bruges kun sammen med USNChanged delta importmetode skal kunne se slettede objekter.

Forbindelsen forsøger at finde de indstillinger, der findes på serveren. Hvis indstillingerne ikke kan registreres, er der en advarsel, der er indsat på siden globale i Egenskaber for forbindelse. Ikke alle LDAP-servere Præsenter alle objekter og-funktioner, de understøtter, og selvom der findes en advarsel, forbindelsen kan fungere uden problemer.

### <a name="delta-import"></a>Deltaimport
Delta Importér er kun tilgængelig, når der blev fundet en support-mappe. Fremgangsmåderne bruges i øjeblikket:

- LDAP-Accesslog. Se [http://www.openldap.org/doc/admin24/overlays.html#Access logføring](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- LDAP-Changelog. Se [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Tidsstempel. For Novell/NetIQ eDirectory forbindelsen bruger sidste dato og klokkeslæt til at få oprettet og opdateret objekter. Novell/NetIQ eDirectory giver ikke en tilsvarende betyder, at hente slettede objekter. Denne indstilling kan også bruges, hvis ingen andre delta importmetode er aktiv på LDAP-serveren. Denne indstilling er ikke kunne importere slettede objekter.
- USNChanged. Se: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Ikke understøttet
Følgende LDAP-funktioner understøttes ikke:

- LDAP-henvisninger mellem servere (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Oprette en ny forbindelse
Vælg **Administration Agent** og **oprette**for at oprette en generisk LDAP-forbindelse, i **Tjenesten Brugerprofilsynkronisering** . Markér forbindelsen **generisk LDAP (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Forbindelse
På siden Connectivity, skal du angive oplysninger om Host, Port og Binding. Afhængigt af hvilket Binding er markeret, yderligere kan oplysninger angives i de følgende afsnit.

![Forbindelse](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- Indstillingen forbindelse timeout bruges kun til den første forbindelse til serveren, når registrering af skemaet.
- Hvis Binding er anonym, derefter hverken brugernavn / adgangskode eller certifikat er brugt.
- For andre bindinger, indtaste oplysninger enten i brugernavn / adgangskode, eller Vælg et certifikat.
- Hvis du bruger Kerberos til at godkende, også give startdomænet/domænet for brugeren.

Tekstfeltet **attribut aliasser** bruges til attributter, der er defineret i skemaet med RFC4522 syntaks. Følgende attributter ikke kan findes under registrering af skemaet og forbindelsen har brug for hjælp til at identificere disse attributter. For eksempel bruges følgende skal angives i feltet attribut aliasser til at identificere korrekt attributten userCertificate som en binær attribut:

`userCertificate;binary`

Følgende er et eksempel på hvordan denne konfiguration kan se ud som:

![Forbindelse](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Markér afkrydsningsfeltet **Medtag funktionsdygtige attributter i skemaet** også medtage attributter, der er oprettet af serveren. Dette omfatter attributter som hvornår objektet er blevet oprettet og opdatere sidste gang.

Vælg **Medtag extensible attributter i skemaet** , hvis der bruges extensible objekter (RFC4512/4.3) og aktivere denne indstilling giver mulighed for hver attribut, der skal bruges i alle objekt. Hvis du vælger denne indstilling gør skemaet meget store så medmindre mappen forbundne bruger denne funktion anbefales det at beholde den indstilling, der er ikke valgt.

### <a name="global-parameters"></a>Globale parametre
På siden globale parametre skal konfigurere du DN ændringslog for delta og yderligere LDAP-funktioner. Siden er udfyldt med de oplysninger, som LDAP-serveren.

![Forbindelse](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

Den øverste del viser oplysninger fra serveren sig selv som navnet på serveren. Forbindelsen kontrollerer også, at de obligatoriske kontrolelementer findes i roden DSE. Hvis disse kontrolelementer ikke er angivet, vises en advarsel. Nogle LDAP-adresselister viser ikke alle funktioner i roden DSE og det er muligt, at forbindelsen fungerer uden problemer, selvom en advarsel er til stede.

Afkrydsningsfelterne **understøttes kontrolelementer** styrer funktionaliteten for visse handlinger:

- Med trævisning Slet er markeret, er blevet slettet et hierarki med én LDAP-opkald. Forbindelsen understøtter med trævisning Slet ikke valgt en rekursiv Slet, hvis det er nødvendigt.
- Med sideinddelt resultater, der er markeret, gør forbindelsen en sideinddelt import med den angivne på Kør trinnene størrelse.
- VLVControl og SortControl er et alternativ til pagedResultsControl til at læse data fra LDAP-adresselisten.
- Hvis alle tre indstillinger (pagedResultsControl, VLVControl og SortControl) er ikke er valgt importerer forbindelsen alle objekter i en handling, som kan mislykkes, hvis det er et stort katalog.
- ShowDeletedControl bruges kun, når importmetode Delta er USNChanged.

Ændringslog DN er konteksten naming bruges af ændringslog delta f.eks **cn = changelog**. Denne værdi skal være angivet skal kunne gøre Deltaimport.

Følgende er en liste over standard ændringslog DNs:

Directory | Ændringslog for delta
--- | ---
Microsoft AD LDS og AD globalt katalog | Fundet automatisk. USNChanged.
Apache Directory Server | Ikke tilgængelig.
Directory 389 | Ændringslog for. Værdi der skal bruges som standard: **cn = changelog**
IBM Tivoli DS | Ændringslog for. Værdi der skal bruges som standard: **cn = changelog**
Isode Directory | Ændringslog for. Værdi der skal bruges som standard: **cn = changelog**
Novell/NetIQ eDirectory | Ikke tilgængelig. Tidsstempel. Forbindelse bruger senest opdateret dato og klokkeslæt for at få tilføjet og de opdaterede poster.
Åbn DJ/DS | Ændringslog for.  Værdi der skal bruges som standard: **cn = changelog**
Åbn LDAP | Log af Access. Værdi der skal bruges som standard: **cn = accesslog**
Oracle DSEE | Ændringslog for. Værdi der skal bruges som standard: **cn = changelog**
RadiantOne tjenesten | Virtuel mappe. Afhænger af den mappe, der er knyttet til tjenesten.
Søn én Directory Server | Ændringslog for. Værdi der skal bruges som standard: **cn = changelog**

Attributten adgangskode er navnet på attributten forbindelsen skal bruge til at angive adgangskoden i adgangskode ændres, og din adgangskode handlinger.
Denne værdi er som standard angivet til **userPassword** , men kan ændres, når det er nødvendigt for et bestemt LDAP-system.

I listen yderligere partitioner er det muligt at føje flere navneområder registreres ikke automatisk. Denne indstilling kan for eksempel bruges, hvis flere servere udgør en logisk klynge, som skal alle kan importeres på samme tid. Ligesom Active Directory kan have flere domæner i et område, men alle domæner dele et skema, kan den samme simuleret ved at angive flere navneområder i dette felt. Hver navneområde kan importere fra forskellige servere og yderligere er konfigureret på siden Konfigurer partitioner og hierarkier. Brug Ctrl + Enter for at få en ny linje.

### <a name="configure-provisioning-hierarchy"></a>Konfigurere klargøring hierarki
Denne side bruges til at knytte komponenten DN, for eksempel OU til objekttype, der skal være klargjort, for eksempel organizationalUnit.

![Klargøring af hierarki](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Ved at konfigurere klargøring hierarki, kan du konfigurere forbindelsen til automatisk at oprette en struktur, når det er nødvendigt. Eksempelvis hvis der er et navneområde dc = contoso, dc = com og et nyt objekt cn = Joe, ou = Seattle, c = DK, dc = contoso, dc = com er klargjort, og klik derefter forbindelsen kan oprette et objekt af typen land for USA og en organizationalUnit for Seattle, hvis dem, ikke der allerede findes i mappen.

### <a name="configure-partitions-and-hierarchies"></a>Konfigurere partitioner og hierarkier
Vælg alle navneområder med objekter, du planlægger at importere og eksportere, på siden partitioner og hierarkier.

![Partitioner](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

For hver navneområde er det også muligt at konfigurere forbindelsesindstillinger, vil tilsidesætte de værdier, der er angivet på skærmbilledet Connectivity. Hvis disse værdier bevares til deres tom standardværdi, bruges oplysningerne fra skærmbilledet Connectivity.

Du kan også vælge, hvilke beholdere og afdelinger forbindelsen skal importere fra og eksportere til.

### <a name="configure-anchors"></a>Konfigurere ankre
Denne side har altid en forudkonfigureret værdi og kan ikke ændres. Hvis der er identificeret server leverandør, kan ankeret udfyldes med en fast attribut, for eksempel GUID for et objekt. Hvis det ikke er registreret eller kendes ikke have en fast attribut, bruger forbindelsen dn (entydigt navn) som anker.

![ankre](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Følgende er en liste over LDAP-servere og anker bruges:

Directory | Anker attribut
--- | ---
Microsoft AD LDS og AD globalt katalog | objectGUID
389 directory-Server | DN
Apache Directory | DN
IBM Tivoli DS | DN
Isode Directory | DN
Novell/NetIQ eDirectory | GUID
Åbn DJ/DS | DN
Åbn LDAP | DN
Oracle ODSEE | DN
RadiantOne tjenesten | DN
Søn én Directory Server | DN

## <a name="other-notes"></a>Andre noter
Dette afsnit indeholder oplysninger om aspekter, der er specifikke for denne forbindelse eller andre årsager er vigtigt at kende.

### <a name="delta-import"></a>Deltaimport
Delta vandmærket i Åbn LDAP er UTC dato og klokkeslæt. Derfor skal være synkroniseret klokkeslæt mellem FIM synkronisering af tjenesten og åbne LDAP-SERVEREN. Hvis ikke, nogle poster i ændringslog delta kan udelades.

For Novell eDirectory er delta importen ikke finde en hvilken som helst objekt sletter. Derfor er det nødvendigt at køre en fuld import med jævne mellemrum for at finde alle slettede objekter.

Til mapper med en delta ændringslog, der er baseret på dato og klokkeslæt, anbefales det at køre en fuld import periodiske tiden. Denne proces kan synkronisere program til at finde og dissimilarities mellem LDAP-serveren, og hvad der aktuelt vises i den forbindelse plads.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

-   Du kan finde oplysninger om, hvordan du aktiverer logføring til at foretage fejlfinding af forbindelsen, [hvordan du kan aktivere ETW sporing for forbindelser](http://go.microsoft.com/fwlink/?LinkId=335731).
