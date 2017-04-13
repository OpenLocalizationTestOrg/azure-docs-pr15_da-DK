<properties
    pageTitle="Skybaseret App registrering sikkerhed og beskyttelse af personlige oplysninger overvejelser i forbindelse med | Microsoft Azure"
    description="Dette emne beskrives de sikkerhed og beskyttelse af personlige oplysninger overvejelser, der er relateret til skyen App registrering."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Skyen App registrering sikkerhed og beskyttelse af personlige oplysninger overvejelser i forbindelse med

Microsoft er forpligtet til at beskytte dine personlige oplysninger og sikring af dine data, under fremvisning af software og -tjenester, der hjælper dig med at administrere sikkerheden for din organisation. <br>
Vi genkendes, når du overdrage dine data til andre, sikkerhed og rettighedsadministration kræver strenge sikkerhed tekniske investeringer og ekspertise til en sikkerhedskopi af den.
Microsoft overholder faste overholdelse af regler og retningslinjer for sikkerhed fra sikker software development livscyklus fremgangsmåder til operativsystem en tjeneste. <br>
Sikring af og beskyttelse af data er højeste prioritet hos Microsoft.

Dette emne forklares, hvordan data indsamles, behandles og sikret i Azure Active Directory skyen App registrering




##<a name="overview"></a>Oversigt

Skyen App registrering er en funktion i Azure AD og er hostet i Microsoft Azure. <br>
Skyen App registrering slutpunkt agent bruges til at indsamle programmet registrering data fra IT-administrerede computere. <br>
De indsamlede data sendes sikkert via en krypteret kanal til tjenesten Azure AD skyen App registrering. <br>
Skyen App registrering dataene til en organisation er derefter synligt i portalen Azure. <br>


<center>![Hvordan fungerer skyen App registrering](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


De følgende afsnit Følg strømmen af oplysninger og beskriver, hvordan det sikres, når den flyttes fra din organisation til tjenesten skyen App registrering og i sidste ende til portalen skyen App registrering.



## <a name="collecting-data-from-your-organization"></a>Indsamling af data fra din organisation

For at kunne bruge Azure Active Directorys skyen App registrering funktion til at få indsigt i de programmer, der bruges af medarbejderne i din organisation, skal du først installere Azure AD skyen App registrering slutpunkt agent til computere i din organisation.

Administratorer af Azure Active Directory-lejer (eller deres stedfortræder) kan hente installationspakken agent fra Azure-portalen. Agenten kan enten være manuelt installeret eller installeret på tværs af flere computere i organisationen bruger SCCM eller Gruppepolitik.

Se [Skyen App registrering Gruppepolitik installationsvejledning](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)have flere oplysninger om installationsindstillinger for.
<br>

### <a name="data-collected-by-the-agent"></a>Data, der indsamles af agenten

De oplysninger, der er beskrevet i nedenstående liste, der indsamles af agenten, når der oprettes en forbindelse til et webprogram. Oplysninger, der indsamles kun til de programmer, som administratoren har konfigureret til registrering. <br>
Du kan redigere listen over skyen apps, som agenten overvåger gennem bladet skyen App registrering i Microsoft [Azure-portalen](https://portal.azure.com/), under **Indstillinger for**->**Dataindsamling**->**liste over App samling**. Få mere at vide, skal du se [Komme i gang med skyen App registrering](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Du kan finde oplysninger kategori**: brugeroplysninger <br>
**Beskrivelse**: <br>
Windows-brugernavnet for den proces, der har oprettet en anmodning til destinationen webprogram (f.eks.: domæne\brugernavn) samt Windows sikkerhed id (SID) på brugeren.


**Du kan finde oplysninger kategori**: procesoplysninger <br>
**Beskrivelse**: <br>
Navnet på den proces, der har foretaget anmodningen til destinationen webprogram (f.eks.: "iexplore.exe")

**Du kan finde oplysninger kategori**: Machine oplysninger <br>
**Beskrivelse**: <br>
NetBIOS-navnet på den computer, hvor agenten er installeret.

**Du kan finde oplysninger kategori**: App trafikoplysninger <br>
**Beskrivelse**: <br>

De følgende forbindelsesoplysninger:

- Kilde (lokal computer) og destination IP-adresser og portnumre

- Den offentlige IP-adresse på den organisation, som din anmodning går ud.

- Tid for din anmodning

- Mængde trafik sendes og modtages

- IP-versionerne (4 eller 6)

- For TLS forbindelser kun: værtsnavn target fra filtypenavnet Server navn angivelse eller servercertifikatet.

HTTP følgende oplysninger:

- Metode (Hent, INDLÆG, osv.)

- Protocol (HTTP/1.1 osv.)

- Bruger agent streng

- Værtsnavn

- Target URI (bortset fra forespørgselsstreng)

- Oplysninger om indholdstype

- Referrer URL-adressen oplysninger (undtagen forespørgselsstreng)



> [AZURE.NOTE] Ovenstående HTTP oplysninger der indsamles for alle ikke-krypterede forbindelser.
Til TLS forbindelser hentes oplysningerne kun, når indstillingen 'Deep inspektion' er slået til i portalen. 'Til' er som standard.
Flere oplysninger, se nedenfor og [Komme i gang med skyen App registrering](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Ud over de data, agenten indsamler om aktiviteten på netværket, indsamler det også anonyme oplysninger om softwaren og hardwarekonfiguration, fejlrapporter samt oplysninger om, hvordan agenten bruges.

<br><br>
### <a name="how-the-agent-works"></a>Hvordan fungerer agenten

Agent-installation omfatter to komponenter:

- En bruger tilstand-komponent

- En kernen tilstand driverkomponent (Windows Filtering Platform driver)



Når agenten installeres første gemmer en machine-specifikke der er tillid til certifikatet på den computer, som derefter bruges til at oprette en sikker forbindelse med tjenesten skyen App registrering. <br>
Agenten henter med jævne mellemrum konfiguration af politik fra tjenesten skyen App registrering via denne sikker forbindelse. <br>
Politikken indeholder oplysninger om hvilke skyen programmer til at overvåge og om opdatering af automatisk skal aktiveres, blandt andet.

Som webtrafik sendes og modtages på maskinen fra Internet Explorer og Chrome, skyen App registrering agent analyserer trafikken og henter den relevante metadata (se afsnittet **Data indsamles af agenten** ovenfor). <br>
Hvert minut uploader agenten indsamlede metadata til tjenesten skyen App registrering over en krypteret kanal.

Komponenten driver opfanger krypteret trafik og indsætter selve i den krypterede stream. Flere oplysninger i afsnittet **Intercepting data fra krypterede forbindelser (Deep inspektion)** nedenfor.


### <a name="respecting-user-privacy"></a>Overholde lovgivningen om bruger beskyttelse af personlige oplysninger

Vores mål er at tilvejebringe administratorer værktøjerne til at angive balancen mellem detaljerede optik i programmet brugen og bruger beskyttelse af personlige oplysninger efter behov for organisationen. Hertil, vi giver dig følgende knapperne på indstillingssiden i portalen:

- **Dataindsamling**: Administratorer kan vælge at angive, hvilke programmer eller programkategorier, de vil hente registrering data.

- **Deep inspektion**: Administratorer kan vælge at angive, hvis agenten indsamler HTTP-trafik til SSL/TLS forbindelser (også kendt **' Deep inspektion '**). Mere om dette i næste afsnit.

- **Samtykke indstillinger**: Administratorer kan bruge portalen skyen App registrering kan vælge, om at informere brugere om Dataindsamling af agenten, og om du vil kræve, at bruger samtykke før agent startes indsamling af brugerdata.

Skyen App registrering slutpunkt agent indsamler kun de oplysninger, der er beskrevet i afsnittet **Data indsamles af agenten** ovenfor.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Opfange data fra krypterede forbindelser (Deep inspektion)
Som tidligere nævnt kan administratorer konfigurere agent for at overvåge data fra krypterede forbindelser ('deep inspektion'). TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) er en af de mest almindelige protokoller i brug på internettet i dag. Ved at kryptere kommunikation med TLS, kan en klient oprette et sikkert og privat kommunikationskanal med en webserver TLS beskytter væsentlige der passerer legitimationsoplysninger og forhindre videregivelse af følsomme oplysninger.

Mens den til slut secure krypteret kanal leveres af TLS aktiverer vigtige sikkerhed og beskyttelse af personlige skal misbrugt protokollen ofte til skadelig eller nefarious formål. Så meget så faktisk er pågældende TLS ofte kaldes "universal firewall til at springe over protokollen". I roden af problemet er, at de fleste firewalls kunne undersøge TLS kommunikation, fordi programlag dataene er krypteret med SSL. Viden om dette, udnytte hackere ofte TLS for at levere skadelig overførsler til en bruger, der er sikker på, at selv de mest intelligent programlag firewalls er helt blinde til TLS og skal blot videregive TLS kommunikation mellem hosts. Slutbrugere udnytte ofte TLS du kan tilsidesætte adgang til kontrolelementer for hos deres virksomhedens firewalls og proxyservere, bruge det til at oprette forbindelse til offentlige proxyer og for ikke-TLS tunnelføringsprotokoller via firewallen, der muligvis ellers blokeret af en politik.

Deep inspektion kan skyen App registrering agent skal fungere som et der er tillid til mand-i-feltet-midten. Når der foretages en klientanmodning at få adgang til en HTTPS beskyttet ressource, den første eller sidste ark Agent driver opfanger forbindelsen og opretter en ny forbindelse til destinationsserveren for at henter dens SSL-certifikat på vegne af klienten. Agenten bekræfter derefter, at være tillid til certifikatet (ved at kontrollere, at det ikke er tilbagekaldt og udføre andre certifikat kontrol), og hvis disse trin, den første eller sidste ark Agent derefter kopierer oplysningerne fra servercertifikatet og opretter et nyt servercertifikat – kaldet en aflytning certifikat – ved hjælp af disse oplysninger. Aflytning certifikatet er logget på løbende af slutpunkt agent med et certifikat, som er installeret i Windows der er tillid til certifikatet store. Denne selvsigneret certifikat er markeret som ikke kan eksporteres og er ACL skulle administratorer. Det er beregnet på at Efterlad aldrig den maskine, hvor det blev oprettet. Når den slutbrugerens klientprogrammet modtager aflytning certifikatet, den skal have tillid til det fordi den kan fuldført validering certifikatkæden helt til certifikatet. Denne proces er hovedsageligt gennemsigtig fra en slutbrugerens synspunkt med et par advarsler som beskrevet nedenfor.

Ved at aktivere deep inspektion skyen App registrering slutpunkt Agent dekryptere og undersøge TLS krypteret kommunikation tillade tjenesten at reducere støj og yde viden om brugen af krypterede skyen apps.

#### <a name="a-word-of-caution"></a>En lille advarsel
Før du slår deep inspektion, på det kraftigste, at du kommunikere dine hensigt til juridiske og HR afdelinger og hente deres accept. Undersøge slutbrugerens privat krypteret kommunikation kan være et følsomt emne indlysende årsager. Opret sikker på, at virksomhedens sikkerhed inden en fremstilling udrulning af deep undersøgelsen, og acceptabel brug politikker er blevet opdateret for at angive, at krypteret kontrolleres. Bruger meddelelser og momsfritagelse af websteder, der anses for følsomme (fx banker og medicinsk websteder) kan også være nødvendigt, hvis du konfigurerer skyen App registrering for at overvåge dem. Som nævnt ovenfor, kan administratorer kan bruge portalen skyen App registrering til at vælge, om at informere brugere om Dataindsamling af agenten, og om at kræve bruger samtykke, før agenten starter ved at indsamle brugerdata.

### <a name="known-issues-and-drawbacks"></a>Kendte problemer og ulemper
Der er nogle tilfælde, hvor TLS aflytning kan påvirke slutbrugerens oplevelse:

- Udvidet godkendelse (EV) certifikater gengive adresselinjen i webbrowseren grønne skal fungere som et synligt tegn på, at du besøger et sikkert websted. TLS inspektion kan ikke duplikere EV i det certifikat, der er problemer på klienten, så websteder, der bruger EV-certifikater fungerer normalt, men adresselinjen vises ikke grøn.  

- Offentlig nøgle Fastgør (også kendt som certifikat Fastgør) er designet til at beskytte brugere fra mand i midten angreb og uautoriserede nøglecentre. Når certifikatet for et websted, der er fastgjort ikke svarer til en af de kendte gode CA, afviser browseren forbindelse med en fejl. Da TLS aflytning er faktisk en mand-i-feltet-midten mislykkes disse forbindelser.

- Hvis brugerne klikker på låseikonet i browseren adresse liggende browser for at undersøge webstedets oplysninger, de se ikke en kæde, der ender med nøglecenter, der er brugt til at signere websted certifikatet, men i stedet for en certifikatkæde, der slutter med Windows tillid til lager.

Vi Hold styr på skytjenester og klientprogrammer kendte bruge udvidede validering eller offentlig nøgle Fastgør, og fortæl slutpunkt Agent for at undgå opfange påvirkede forbindelser for at reducere forekomster af disse problemer. Du modtager stadig rapporter over brug af disse skyen apps og mængden data, der overføres dog selv i disse tilfælde, men da de ikke er deep kontrolleres, ikke oplysninger om, hvordan apps er bruges bliver tilgængelig.


## <a name="sending-data-to-cloud-app-discovery"></a>Sende data til skyen App registrering

Når metadata er indsamlet af agenten, er den cachelagrede på computeren for op til et minut, eller indtil de cachelagrede data har en størrelse på 5MB. Det er derefter komprimeres og sendes via en sikker forbindelse til tjenesten skyen App registrering.

Hvis agenten er i stand til at kommunikere med tjenesten skyen App registrering for en eller anden grund, gemmes de indsamlede metadata i cachen til en lokal fil, der kan kun åbnes af privilegerede brugere på computeren (såsom administratorgruppen). <br>
Agenten forsøger automatisk at sende de cachelagrede metadata, indtil den er blevet leveret af tjenesten skyen App registrering.



## <a name="receiving-the-data-at-the-service-end"></a>Modtage data sidst service

Supportmedarbejdere godkende til skyen App registrering tjenesten ved hjælp af den maskine bestemte godkendelse af klientcertifikat der er nævnt ovenfor og videresender data over en krypteret kanal. <br>
Tjenesten skyen App registrering analytics pipeline behandler metadata for hver kunde separat ved logisk partitionering den gennem alle faser af analytics rørledning.
Analyserede metadataene drev de forskellige rapporter på portalen.

Ikke-behandlede metadataene og den analyserede metadata er gemt i op til 180 dage. Desuden kan kunder vælge at registrere analyserede metadataene i en Azure blob storage konto efter deres valg.
Dette er nyttigt til offline analyse af metadata samt længere opbevaring af dataene.

## <a name="accessing-the-data-using-the-azure-portal"></a>Adgang til data ved hjælp af portalen Azure

I et forsøg på at beskytte de metadata, der indsamles, som standard har kun globale administratorer lejeren adgang til funktionen skyen App registrering i portalen Azure. <br>
Administratorer kan dog vælge at stedfortræderadgang til andre brugere eller grupper.



> [AZURE.NOTE] Få mere at vide, skal du se [Komme i gang med skyen App registrering](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Alle brugere adgang til data i portalen, skal være underlagt en Azure AD Premium-licens.



##<a name="additional-resources"></a>Yderligere ressourcer


* [Hvordan kan jeg finde unsanctioned skyen apps, der bruges inden for min organisation](active-directory-cloudappdiscovery-whatis.md)
* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
