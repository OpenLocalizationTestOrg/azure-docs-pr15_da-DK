<properties 
    pageTitle="Katalogintegration mellem Azure Multi-Factor Authentication og Active Directory"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvordan du integrerer Azure Multi-Factor Authentication Server med Active Directory, så du kan synkronisere mapperne."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Katalogintegration mellem Azure MFA Server og Active Directory

Sektionen katalogintegration kan du konfigurere serveren for at integrere med Active Directory eller en anden LDAP-adresseliste.  Det kan du konfigurere attributter for at matche directory-skemaet og konfigurere automatiske synkronisering af brugere.

## <a name="settings"></a>Indstillinger
Azure Multi-Factor Authentication serveren er som standard konfigureret til at importere eller synkroniserer brugere fra Active Directory.  Fanen gør det muligt at tilsidesætte standardfunktionsmåden og bindes til en anden LDAP-mappe, en ADAM mappe eller bestemte Active Directory-domænecontrolleren.  Den indeholder også for brugen af LDAP-godkendelse til proxy LDAP eller LDAP Bind som RADIUS destination, før godkendelse for IIS-godkendelse eller primær godkendelse til User Portal.  Den følgende tabel beskrives de enkelte indstillinger.

![Indstillinger](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Funktion | Beskrivelse |
| ------- | ----------- |
| Bruge Active Directory | Vælg indstillingen Brug Active Directory til at bruge Active Directory til import og synkronisering.  Dette er standardindstillingen for. <br>Bemærk: Computeren skal være medlem af et domæne, og du skal være logget med en domænekonto til Active Directory-integration kan fungere korrekt. |
| Medtage der er tillid til domæner | Markere afkrydsningsfeltet Medtag domæner, der er tillid til at have agent forsøg på at oprette forbindelse til domæner, der er tillid til efter det aktuelle domæne, et andet domæne i området eller domæner involveret i en skov sikkerhed og rettighedsadministration.  Når du ikke importerer eller synkroniserer brugere fra en af de domæner, der er tillid til, kan du fjerne markeringen i afkrydsningsfeltet for at forbedre ydeevnen.  Standard er markeret. |
| Brug af bestemte LDAP-konfiguration | Vælg indstillingen Brug LDAP at bruge de LDAP-indstillinger, der er angivet til import og synkronisering. Bemærk: Når Brug LDAP er markeret, brugergrænsefladen ændres referencer fra Active Directory til LDAP. |
| Knappen Rediger | Knappen Rediger gør det muligt for de aktuelle LDAP-konfigurationsindstillinger, der har ændret. |
| Bruge attributten omfang forespørgsler | Angiver, om der skal bruges attributten omfang forespørgsler.  Attributten omfang forespørgsler Tillad effektiv directory søgninger kvalificere poster baseret på posterne i en anden post attribut.  Azure Multi-Factor Authentication serveren bruger attributten omfang forespørgsler til at forespørge effektivt de brugere, der er medlem af en sikkerhedsgruppe.   <br>Bemærk: Der er nogle tilfælde, hvor attributten omfang forespørgsler er understøttet, men ikke bør bruges.  Active Directory kan for eksempel har problemer med attributten omfang forespørgsler, når en sikkerhedsgruppe indeholder medlemmer fra mere end ét domæne.  I dette tilfælde skal afkrydsningsfeltet være markeret. |

I følgende tabel beskrives LDAP-konfigurationsindstillingerne.

| Funktion | Beskrivelse |
| ------- | ----------- |
| Server | Angiv værtsnavn eller IP-adressen på den server, der kører på LDAP-adresseliste.  En server til sikkerhedskopiering kan også angives adskilt med semikolon. <br>Bemærk: Når binde Type er SSL, et fuldt kvalificeret værtsnavn kræves Generelt. |
| Base DN | Angiv det entydige navn for objektet base directory, hvor alle directory forespørgsler starter.  For eksempel dc = abc, dc = com. |
| Binde type - forespørgsler | Vælg den relevante bind type til brug ved binding til at søge LDAP-adresseliste.  Det bruges til import, synkronisering og brugernavn opløsning. <br><br>  Anonym - en anonym binde vil blive udført.  Bind DN og binde adgangskode vil ikke blive brugt.  Dette virker kun, hvis LDAP-adresseliste kan anonyme indbinding og tilladelser kan forespørgsler over de relevante poster og attributter.  <br><br> Simpel - binde DN og binde adgangskode vil blive overført som almindelig tekst skal bindes til LDAP-adresseliste.  Dette bør kun bruges til testformål for at bekræfte, at der kan oprettes forbindelse til serveren og at kontoen bind har adgang til det relevante.  Det anbefales, at SSL skal bruges i stedet efter den relevante cert er blevet installeret.  <br><br> SSL - binde DN og binde adgangskode vil blive krypteret med SSL skal bindes til LDAP-adresseliste.  Dette kræver, at der installeres en cert lokalt, LDAP-adresseliste har tillid til.  <br><br> Windows - Bind brugernavn og din adgangskode binde bruges til at oprette sikker forbindelse til en Active Directory-domænecontrolleren eller ADAM mappe.  Hvis binde brugernavn er tomt, som er logget på brugerens konto skal bruges til binde. |
| Binde type - godkendelse | Vælg den relevante bind type til brug, når du udfører LDAP bind godkendelse.  Se bind Skriv beskrivelser under type af binding - forespørgsler.  For eksempel dette giver mulighed for anonyme binding skal bruges til forespørgsler, mens SSL bind bruges til at sikre LDAP bind godkendelse. |
| Bind DN eller binde brugernavn | Angiv det entydige navn med brugerposten for kontoen, der skal bruges ved binding til LDAP-adresseliste.<br><br>Det entydige navn bind bruges kun, når binde Type er enkel eller SSL.  <br><br>Angiv brugernavnet for Windows-kontoen, der skal bruges ved binding til LDAP-adresseliste, når binde Type er Windows.  Hvis tomt, som er logget på brugerens konto skal bruges til binde. |
| Binde adgangskode | Skriv adgangskoden binding for binde DN eller brugernavn, der anvendes til at binde til LDAP-adresseliste.  Hvis du vil konfigurere adgangskoden for tjenesten Multi-Factor Auth Server AdSync, skal være aktiveret synkronisering og tjenesten skal køre på den lokale computer.  Adgangskoden, gemmes i Windows gemt brugernavne og adgangskoder under kontoen Multi-Factor Auth Server AdSync tjenesten kører som.  Adgangskoden, også gemmes, under den konto, brugergrænsefladen Multi-Factor Auth Server kører som og under den konto, tjenesten Multi-Factor Auth Server kører som.  <br><br> Bemærk: Da adgangskoden er kun gemt i den lokale servers Windows gemt brugernavne og adgangskoder, dette trin skal udføres på hver Multi-Factor Auth Server, der skal have adgang til adgangskoden. |
| Størrelsesbegrænsning for forespørgsel | Angiv størrelsesgrænsen for det maksimale antal brugere, der returnerer en directory søgning.  Denne grænse skal svare til konfiguration på LDAP-adresseliste.  For store søgninger, hvor sideopdeling ikke understøttes, forsøger importere og synkronisering at hente brugere i batches.  Hvis størrelsesgrænsen angivet her er større end grænsen konfigureret på LDAP-adresseliste, kan være mistes nogle brugere. |
| Knappen testbillede til | Klik på knappen Test for at teste binding til LDAP-serveren.  <br><br> Bemærk: Indstillingen Brug LDAP behøver ikke at være markeret for at teste binding.  Dette giver mulighed for binding testes før LDAP-konfigurationen. |

## <a name="filters"></a>Filtre
Filtre kan du angive kriterier, der kvalificerer poster, når du udfører en directory søgning.  Du kan begrænse de objekter, du vil synkronisere ved at angive filteret.  

![Filtre](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication har følgende 3 indstillinger.

- **Objektbeholder filter** - angive filterkriterierne bruges til at kvalificere objektbeholder poster, når du udfører en directory søgning.  Til Active Directory og ADAM, (| () objectClass=organizationalUnit)(objectClass=container)) bruges normalt.  For andre LDAP-kataloger, der bør benyttes filterkriterier, berettiger hver type objektbeholder afhængigt af det aktuelle directory schema.  <br>Bemærk: Hvis tomt, ((objectClass=organizationalUnit)(objectClass=container)) bruges som standard.

- **Sikkerhed gruppefilter** - angive filterkriterierne bruges til at kvalificere sikkerhed gruppeposter, når du udfører en directory søgning.  Til Active Directory og ADAM, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) bruges normalt.  For andre LDAP-kataloger, der bør benyttes filterkriterier, berettiger hver type sikkerhed gruppeobjektet afhængigt af det aktuelle directory schema.  <br>Bemærk: Hvis tomt, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) bruges som standard.

- **Søgerod** - angive filterkriterierne bruges til at kvalificere brugerposter, når du udfører en directory søgning.  Til Active Directory og ADAM, (og (objectClass=user)(objectCategory=person)) bruges normalt.  For andre LDAP-kataloger, (Objektklassen = inetOrgPerson) eller noget lignende skal bruges afhængigt af det aktuelle directory schema. <br>Bemærk: Hvis tomt, (og (objectCategory=person)(objectClass=user)), der skal bruges som standard.

## <a name="attributes"></a>Attributter
Attributter kan tilpasses efter behov for en bestemt mappe.  Dette kan du tilføje brugerdefinerede attributter og finjustere synkroniseringen er kun de attributter, du har brug for.  Værdien for hvert attributfelt, skal være navnet på attributten, som defineret i skemaet directory.  Brug tabellen nedenfor for at få yderligere oplysninger.

![Attributter](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Funktion | Beskrivelse |
| ------- | ----------- |
| Entydigt id | Angiv attributnavn for attributten, der fungerer som entydigt id for objektbeholder, sikkerhedsgruppe og brugerposter.  I Active Directory er det som regel objectGUID.  I andre LDAP-installationer, kan det være entryUUID eller noget lignende.  Standard er objectGUID. |
| ... (Vælg attribut) knapper | Hvert attributfelt, har en knappen "... ud for det, der vises dialogboksen Vælg attributten tillade en attribut, der skal vælges fra en liste". <br><br>Vælg attributten dialogboks.<br><br>Bemærk: Egenskaber kan angives manuelt og er ikke nødvendigt at matche en attribut på attributlisten. |
| Entydigt id-type | Vælg typen af attributten entydigt id.  Attributten objectGUID er i Active Directory, af typen GUID.  I andre LDAP-installationer, kan det være af typen ASCII-bytematrix eller streng.  Standard er GUID. <br><br>Bemærk: Det er vigtigt at indstille denne korrekt, da der refereres til synkronisering elementer efter deres entydigt id og typen entydige id bruges til at finde direkte objektet i kataloget.  Værdien indstilles til streng, når mappen lagres faktisk værdien, som en bytematrix af ASCII-tegn, kan det blokere synkronisering fra fungerer korrekt. |
| Entydigt navn | Angiv attributnavn for attributten, der indeholder det entydige navn for hver post.  I Active Directory er det som regel distinguishedName.  I andre LDAP-installationer, kan det være entryDN eller noget lignende.  Standard er distinguishedName. <br><br>Bemærk: Hvis der ikke findes en attribut, der indeholder det entydige navn, adspath attributten kan bruges.  Den "LDAP: / /<server>/" del af stien bliver automatisk fjernet fra at forlade det entydige navn for objektet. |
| Navnet på objektbeholderen | Angiv attributnavn for den attribut, der indeholder navnet på en objektbeholder post.  Værdien af denne attribut vises i hierarkiet objektbeholder, når du importerer fra Active Directory og tilføje synkronisering elementer.  Standard er navn. <br><br>Bemærk: Hvis forskellige objektbeholdere bruger forskellige attributter for deres navne, flere objektbeholder navn attributter kan angives i adskilt af semikolon.  Første objektbeholder attributten name findes på en objektbeholder bruges til at få vist dets navn. |
| Sikkerhed gruppenavn | Angiv attributnavn for den attribut, der indeholder navnet på en sikkerhed gruppe post.  Værdien af denne attribut vises på listen sikkerhedsgruppe, når du importerer fra Active Directory og tilføje synkronisering elementer.  Standard er navn. |
| Brugere | Følgende attributter bruges til at søge efter, vise, importere og synkronisering af brugeroplysninger fra adresselisten. |
| Brugernavn | Angiv attributnavn for attributten, der indeholder feltet username i en brugerpost.  Værdien af denne attribut bruges som Multi-Factor Auth Server brugernavn.  En anden attribut kan angives som en sikkerhedskopi til først.  Den anden attribut anvendes kun, hvis den første attribut ikke indeholder en værdi for brugeren.  Standardværdierne er userPrincipalName og sAMAccountName. |
| Fornavn | Angiv attributnavn for attributten, der indeholder det første navn i en brugerpost.  Standard er givenName. |
| Efternavn | Angiv attributnavn for attributten, der indeholder det sidste navn i en brugerpost.  Standard er sn. |
| Mailadresse | Angiv attributnavn for attributten, der indeholder e-mail-adressen i en brugerpost.  E-mail-adresse bruges til at sende Velkommen og opdatere mails til brugeren.  Standard er mail. |
| Brugergruppe | Angiv attributnavn for attributten, der indeholder brugergruppen i en brugerpost.  Brugergruppe kan bruges til at filtrere brugere i agenten og rapporter i portalen Multi-Factor Auth Server Management. |
| Beskrivelse | Angiv attributnavn for den attribut, der indeholder en beskrivelse i en brugerpost.  Beskrivelse bruges kun til søgning.  Standard er beskrivelse. |
| VoIP-opkald sprog | Angiv attributnavn for attributten, der indeholder det korte navn på sproget, der skal bruges til taleopkald til brugeren. |
| SMS Tekstsprog | Angiv attributnavn for attributten, der indeholder det korte navn på sproget, der skal bruges til SMS SMS-beskeder til brugeren. |
| Telefon appsprog | Angiv attributnavn for attributten, der indeholder det korte navn på sproget, der skal bruges til phone app SMS-beskeder til brugeren. |
| ED token sprog | Angiv attributnavn for attributten, der indeholder det korte navn på sproget, der skal bruges til ed token tekstmeddelelser til brugeren. |
| Telefoner | Følgende attributter bruges til at importere eller synkronisere telefonnumre for brugerne.  Hvis et attributnavn ikke er angivet til telefontype, typen telefon er ikke tilgængelige hvornår import fra Active Directory eller føje synkronisering elementer. |
| Business | Angiv attributnavn for attributten, der indeholder arbejdstelefonnummer i en brugerpost.  Standard er telephoneNumber. |
| Startside | Angiv attributnavn for attributten, der indeholder telefon (privat) tallet i en brugerpost.  Standard er telefon (privat). |
| Personsøger | Angiv attributnavn for attributten, der indeholder personsøger tallet i en brugerpost.  Standard er personsøger. |
| Mobile | Angiv attributnavn for attributten, der indeholder mobiltelefonnummeret i en brugerpost.  Standard er mobile. |
| Faxforside | Angiv attributnavn for den attribut, der indeholder faxnummer i en brugerpost.  Standard er facsimileTelephoneNumber. |
| IP-telefon | Angiv attributnavn for attributten, der indeholder IP-telefonnummeret i en brugerpost.  Standard er ipPhone. |
| Brugerdefineret | Skriv attributnavnet på den attribut, der indeholder et brugerdefineret telefonnummer i |
|  | en brugerpost.  Standard er tom. |
| Filtypenavn | Angiv attributnavn for attributten, der indeholder lokalnummer i en brugerpost.  Værdien i feltet lokalnummer bruges som filtypenavnet til den primære telefonnummer kun.  Standard er tom. <br><br>Bemærk: Hvis attributten filtypenavn ikke er angivet, filtypenavne kan medtages som en del af attributten telefon.  Filtypenavnet skal indledes med et x, så det kan fortolkes.  For eksempel resulterer 555-123-4567 x890 i 555-123-4567 som telefonnummeret og 890 som filtypenavnet. |
| Gendanne standardindstillinger for en knap | Klik på knappen Gendan standardindstillinger for at returnere alle attributter tilbage til deres standardværdien.  Standardindstillingerne skal fungere korrekt med normal Active Directory eller ADAM skemaet. |

Hvis du vil redigere attributter, skal du blot klikke på redigeringsknappen under fanen attributter.  Dette vil få vist en windows, hvor du kan redigere attributterne.

![Redigere attributter](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## <a name="synchronization"></a>Synkronisering
Synkronisering hele tiden Azure Multi-Factor brugerdatabasen synkroniseret med brugere i Active Directory eller en anden Lightweight Directory Access Protocol (LDAP) Lightweight Directory Access Protocol-mappe.  Processen svarer til at importere brugere manuelt fra Active Directory, men med jævne mellemrum undersøger til Active Directory-brugeren og sikkerhed gruppen ændringer til at behandle.  Den indeholder også for at deaktivere eller fjerne brugere fjernet fra en objektbeholder eller sikkerhed gruppe og fjerne brugere har slettet fra Active Directory.

Tjenesten Multi-Factor Auth ADSync er en Windows-tjeneste, som udfører den periodiske afstemning af Active Directory.  Dette må ikke forveksles med Azure Active Directory-synkronisering eller Azure AD-forbindelse.  Multi-Factor Auth ADSync, er selvom bygget på en lignende kodebase specifikke for Azure Multi-Factor Authentication Server.  Det er installeret i tilstanden stoppet og er startet af tjenesten Multi-Factor Auth Server når er konfigureret til at køre.  Hvis du har en konfiguration med flere servere Multi-Factor Auth Server, kan Multi-Factor Auth ADSync kun køres på en enkelt server.

Tjenesten Multi-Factor Auth ADSync bruger filtypenavnet DirSync LDAP-serveren, der leveres af Microsoft til effektivt afstemning for ændringer.  Denne DirSync kontrolelement opkalds skal have "mappen få ændringer" højre og DS-gentagelse-Get-Changes udvidede styre adgangen til højre.  Som standard tildeles disse rettigheder til administratoren og LocalSystem brugerkonti på domænecontrollere.  Tjenesten Multi-Factor Auth AdSync er konfigureret til at køre som LocalSystem som standard.  Derfor er det nemmeste at køre tjenesten på et domænenavn fra domænecontrolleren.  Tjenesten kan køre som en konto med mindre tilladelser, hvis du konfigurerer den, så du altid udføre en komplet synkronisering.  Dette er mindre effektiv, men kræver mindre konto rettigheder.

Hvis konfigureret til at bruge LDAP- og LDAP-adresseliste understøtter kontrolelementet DirSync, derefter forespørges efter bruger og sikkerhed gruppen ændringer fungerer på samme måde som det er tilfældet med Active Directory.  Hvis LDAP-adresseliste ikke understøtter kontrolelementet DirSync, udføres en fuldstændig synkronisering i hver cyklus.

![Synkronisering](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Brug tabellen nedenfor til yderligere oplysninger om hver af de individuelle indstillinger under fanen synkronisering.

| Funktion | Beskrivelse |
| ------- | ----------- |
| Aktivere synkronisering med Active Directory | Hvis markeret, startes tjenesten Multi-Factor Auth Server med jævne mellemrum undersøges, Active Directory om ændringer. <br><br>Bemærk: mindst én synkronisering element skal tilføjes og en Synkroniser nu skal udføres før Multi-Factor Auth serveren tjeneste, kan starte behandling af ændringer. |
| Synkronisere alle | Angiv et tidsinterval, multi-Factor Auth Server-tjenesten skal vente mellem forespørge og behandling af ændringer. <br><br> Bemærk: Det interval, der er angivet er tid mellem begyndelsen af hver cyklus.  Hvis den tid på at behandle ændringer overstiger intervallet, afstemning tjenesten igen med det samme. |
| Fjerne brugere ikke længere i Active Directory | Hvis markeret, behandler tombstones-brugeren markerer Active Directory slettet Multi-Factor Auth Server-tjenesten og fjerne den pågældende bruger Multi-Factor Auth Server. |
| Altid foretage fuld synkronisering | Hvis markeret, Udfør tjenesten Multi-Factor Auth Server altid en fuldstændig synkronisering.  Når du ikke er markeret, udføre tjenesten Multi-Factor Auth Server en trinvis synkronisering af ved at forespørge kun brugere, der er blevet ændret.  Standard er markeret. <br><br> Bemærk: Når det er markeret, en trinvis synkronisering kan kun udføres, når mappen understøtter kontrolelementet DirSync og den konto, der bruges skal bindes til mappen, har de relevante tilladelser til at udføre DirSync trinvis forespørgsler.  Hvis kontoen har ikke de rette tilladelser eller flere domæner er involveret i synkroniseringen, udføre en komplet synkronisering anbefales. |
| Kræv administratorgodkendelse når mere end X-brugere vil blive deaktiveret eller fjernet | Synkronisering elementer kan være konfigureret til at deaktivere eller fjerne brugere, der er ikke længere er medlem af elementet objektbeholder eller sikkerhedsgruppe.  Som en beskytter kan administratorgodkendelse være nødvendigt når overstiger antallet af brugere at deaktivere eller fjerne en grænseværdi.  Hvis markeret, bliver godkendelse kræves til angivne grænseværdi.  Standard er 5 og området er 1 til 999. <br><br> Godkendelse foretages ved først at sende en e-mail-meddelelser til administratorer. E-mail-meddelelsen giver vejledning for at gennemse og godkendelse af deaktivering af og fjernelse af brugere.  Når brugergrænsefladen Multi-Factor Auth Server er startet, bliver du bedt om til godkendelse. |

Knappen **Synkroniser nu** kan du køre en fuldstændig synkronisering for de synkronisering elementer, der er angivet.  En komplet synkronisering er påkrævet, når synkronisering elementer er tilføjet, ændret, fjernes eller genbestilles.  Det er også kræves, før tjenesten Multi-Factor Auth AdSync vil være funktionsdygtige, da det angiver startpunktet hvorfra tjenesten bliver afstemning til trinvise ændringer.  Hvis der er foretaget ændringer til synkronisering af elementer og en fuldstændig synkronisering ikke er blevet udført, du vil blive bedt om at Synkroniser nu når du navigerer til en anden sektion, eller når du lukker brugergrænsefladen.

Knappen **Fjern** kan administratoren for at slette en eller flere synkronisering elementer fra listen over Multi-Factor Auth Server synkronisering elementer.

>[AZURE.WARNING]Når en synkronisering af post er blevet fjernet, kan den ikke gendannes. Du skal for igen at føje synkronisering af posten, hvis du har slettet den ved en fejl.

Synkronisering element eller synkronisering elementer er blevet fjernet fra Multi-Factor Auth Server.  Tjenesten Multi-Factor Auth Server behandler ikke længere synkronisering elementer.

Knapperne Flyt op og Flyt ned Tillad administratoren for at ændre rækkefølgen af synkronisering elementer.  Rækkefølgen er vigtigt, da den samme bruger kan være medlem af mere end ét element synkronisering (f.eks. en objektbeholder og en sikkerhedsgruppe).  De indstillinger, der anvendes til brugeren under synkronisering kommer fra det første synkronisering element på listen, som brugeren er tilknyttet.  Derfor synkronisering elementer, der skal lægges i prioritetsrækkefølge.

>[AZURE.TIP]En komplet synkronisering skal udføres, når du har fjernet synkronisering elementer.  En komplet synkronisering skal udføres efter rækkefølge synkronisering elementer.  Klik på knappen Synkroniser nu for at udføre en komplet synkronisering.

## <a name="multi-factor-auth-servers"></a>Multi-Factor Auth servere
Yderligere Multi-Factor Auth servere kan konfigureres til at fungere som en sikkerhedskopi RADIUS proxy, LDAP-proxy, eller for IIS-godkendelse. Konfigurationen af synkronisering skal deles af alle supportmedarbejdere. Kun én af disse supportmedarbejdere kan dog have Multi-Factor Auth Server service kører. Denne fane kan du vælge Multi-Factor Auth serveren, der skal være aktiveret for synkronisering.

![MFA-Factor-Auth servere](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)
