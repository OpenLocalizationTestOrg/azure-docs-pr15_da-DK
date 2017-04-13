<properties
    pageTitle="Azure AD-forbindelse synkronisering: funktioner Reference | Microsoft Azure"
    description="Reference deklarativ klargøring udtryk i Azure AD-forbindelse synkronisering."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD-forbindelse synkronisering: funktioner Reference

I Azure AD-forbindelse bruges funktionerne til at ændre en værdi for attributten under synkronisering.  
Syntaksen for funktionerne udtrykkes i følgende format:  
`<output type> FunctionName(<input type> <position name>, ..)`

Hvis en funktion er overbelastet og accepterer flere eksempler, vises alle gyldige syntaks.  
Funktionerne er kraftigt skrevet, og de bekræfter, at typen overføres i matches typen dokumenterede.  
Hvis typen ikke stemmer overens, er der opstod en fejl.

Typerne udtrykkes med følgende syntaks:

- **placering** – binært tal
- **boolesk** – boolesk
- **dt** – UTC dato/klokkeslæt
- **Optæl** – optælling af kendte konstanter
- **EKSP** -udtryk, der forventes skal evalueres til en boolesk værdi
- **mvbin** – Multi-Valued binære
- **mvstr** – Multi-Valued streng
- **mvref** – Multi-Valued Reference
- **num** – numeriske
- **ref** -Reference
- **str** – streng
- **Varians** – en variant (næsten) enhver anden type
- **Annuller** – returnerer ikke en værdi

Funktionerne med typer **mvbin**, **mvstr**og **mvref** kan kun arbejde med flere værdier attributter. Funktioner med **placering**, **str**og **ref** arbejde på både enkeltværdi- og med flere valgmuligheder attributter.

## <a name="functions-reference"></a>Funktioner Reference

Liste over funktioner | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Konvertering** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[' Cref '](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Dato / klokkeslæt** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [Formaterdatoogklokkeslæt](#formatdatetime) | [Nu](#now)
[NumFromDate](#numfromdate) |  
**Directory** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Evaluering** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matematisk** |  
[Bitog](#bitand) | [Biteller](#bitor) | [RandomNum](#randomnum)
**Med flere værdier** |  
[Indeholder](#contains) | [Tæl](#count) | [Element](#item) | [ItemOrNull](#itemornull)
[Deltage i](#join) | [RemoveDuplicates](#removeduplicates) | [Opdelt](#split) |
**Programflow** |  
[Fejl](#error) | [IIF](#iif)  | [Skift](#switch)
**Tekst** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[Venstre](#left) | [Længde](#len) | [LTrim](#ltrim) | [Funktionen midt](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Erstat](#replace)
[ReplaceChars](#replacechars) | [Højre](#right) | [RTrim](#rtrim) | [Trim](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>Bitog

**Beskrivelse:**  
Funktionen Bitog angiver angivne bit på en værdi.

**Syntaks:**  
`num BitAnd(num value1, num value2)`

- Værdi1; værdi2: numeriske værdier, der skal være featurekeys sammen

**Bemærkninger:**  
Denne funktion konverterer begge parametre til binær repræsentation og angiver en smule til:

- 0 - Hvis ét eller begge af de tilsvarende bit i *masken* og *flag* er 0
- 1 – Hvis begge de tilsvarende bit er 1.

Det vil sige, returneres 0 i alle tilfælde undtagen, når de tilsvarende bit i begge parametre er 1.

**Eksempel:**  
`BitAnd(&HF, &HF7)`  
Returnerer 7, fordi heksadecimalt "F" og "F7" evalueres til denne værdi.

----------
### <a name="bitor"></a>Biteller

**Beskrivelse:**  
Funktionen Biteller angiver angivne bit på en værdi.

**Syntaks:**  
`num BitOr(num value1, num value2)`

- Værdi1; værdi2: numeriske værdier, der skal være or sammen

**Bemærkninger:**  
Denne funktion konverterer begge parametre til binær repræsentation og angiver en smule til 1, hvis ét eller begge af de tilsvarende bit i masken og flag er mellem 1 og 0, hvis begge af de tilsvarende bit er 0. Det vil sige, returneres 1 i alle tilfælde undtagen hvor de tilsvarende bit i begge parametre er 0.

----------
### <a name="cbool"></a>CBool

**Beskrivelse:**  
Funktionen CBool returnerer en boolesk værdi baseret på udtrykket evalueres

**Syntaks:**  
`bool CBool(exp Expression)`

**Bemærkninger:**  
Hvis udtrykket evalueres til en værdi, og derefter CBool returnerer SAND, ellers returneres falsk.

**Eksempel:**  
`CBool([attrib1] = [attrib2])`  

Returnerer SAND, hvis begge attributter have den samme værdi.

----------
### <a name="cdate"></a>CDate

**Beskrivelse:**  
Funktionen CDate returnerer en UTC DateTime fra en streng. Dato/klokkeslæt er ikke en oprindelig attributten type, der er synkroniseret, men der bruges af nogle funktioner.

**Syntaks:**  
`dt CDate(str value)`

- Value: En streng med en dato, klokkeslæt og eventuelt tidszone

**Bemærkninger:**  
Den returnerede streng er altid i UTC.

**Eksempel:**  
`CDate([employeeStartTime])`  
Returnerer en DateTime baseret på medarbejderens starttidspunktet

`CDate("2013-01-10 4:00 PM -8")`  
Returnerer en dato/klokkeslæt, der repræsenterer "2013-01-11 12:00 AM"

----------
### <a name="cguid"></a>CGuid

**Beskrivelse:**  
Funktionen CGuid konverterer Strengrepræsentationen af en GUID til dens binær repræsentation.

**Syntaks:**  
`bin CGuid(str GUID)`

- En streng, der er formateret med dette mønster: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx eller {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Indeholder

**Beskrivelse:**  
Funktionen indeholder ikke finder en streng i en opslagskolonne med flere valgmuligheder attribut

**Syntaks:**  
`num Contains (mvstring attribute, str search)`-store og små bogstaver  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-store og små bogstaver

- attribut: attributten med flere valgmuligheder til at søge.
- Søg: streng til at finde i attributten.
- Casetype: CaseInsensitive eller CaseSensitive.

Returnerer indeks i attributten med flere værdier, hvor strengen, der blev fundet. Hvis strengen ikke findes, returneres 0.

**Bemærkninger:**  
For flere værdier strengattributter finder søgningen understrenge i værdierne.  
Til referenceattributter gennemsøgt strengen skal svare nøjagtigt til værdien for at betragtes som et match.

**Eksempel:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Hvis proxyAddresses-attributten har en primær mailadresse (angivet med store bogstaver "SMTP:"), returneres attributten proxyAddress, ellers returnerer en fejl.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Beskrivelse:**  
Funktionen ConvertFromBase64 konverterer den angivne base64-kodet værdi til en almindelig streng.

**Syntaks:**  
`str ConvertFromBase64(str source)`-antages det Unicode til kodning  
`str ConvertFromBase64(str source, enum Encoding)`

- kilde: Base64-kodet streng  
- Kodning: Unicode, ASCII, UTF8

**Eksempel**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Begge eksempler returnerer "*Hej verden!*"

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Beskrivelse:**  
Funktionen ConvertFromUTF8Hex konverterer den angivne UTF8 Hex kodet værdi til en streng.

**Syntaks:**  
`str ConvertFromUTF8Hex(str source)`

- kilde: UTF8 2 byte kodet sting

**Bemærkninger:**  
Forskellen mellem denne funktion og ConvertFromBase64([],UTF8), der er fuldt for attributten DN resultatet.  
Dette format bruges af Azure Active Directory som DN.

**Eksempel:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Returnerer "*Hej verden!*"

----------
### <a name="converttobase64"></a>ConvertToBase64

**Beskrivelse:**  
Funktionen ConvertToBase64 konverterer en streng til en Unicode-base64 streng.  
Konverterer værdien af en matrix af heltal til dens tilsvarende strengrepræsentation, der er kodet med base 64 cifre.

**Syntaks:**  
`str ConvertToBase64(str source)`

**Eksempel:**  
`ConvertToBase64("Hello world!")`  
Returnerer "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Beskrivelse:**  
Funktionen ConvertToUTF8Hex konverterer en streng til en UTF8 Hex kodet værdi.

**Syntaks:**  
`str ConvertToUTF8Hex(str source)`

**Bemærkninger:**  
Outputformat med denne funktion bruges af Azure Active Directory som DN attribut format.

**Eksempel:**  
`ConvertToUTF8Hex("Hello world!")`  
Returnerer 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Tæl

**Beskrivelse:**  
Funktionen TÆL returnerer antallet af elementer i en opslagskolonne med flere valgmuligheder attribut

**Syntaks:**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Beskrivelse:**  
Funktionen CNum tager en streng, og returnerer en numerisk datatype.

**Syntaks:**  
`num CNum(str value)`

----------
### <a name="cref"></a>' Cref '

**Beskrivelse:**  
Konverterer en streng til attributten reference

**Syntaks:**  
`ref CRef(str value)`

**Eksempel:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**Beskrivelse:**  
Funktionen CStr konverterer til datatypen streng.

**Syntaks:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- værdi: kan være en numerisk værdi, referenceattribut eller boolesk.

**Eksempel:**  
`CStr([dn])`  
Kan returnere "cn = Joe, dc = contoso, dc = com"

----------
### <a name="dateadd"></a>DateAdd

**Beskrivelse:**  
Returnerer en dato, der indeholder en dato, som er blevet føjet et angivet tidsinterval.

**Syntaks:**  
`dt DateAdd(str interval, num value, dt date)`

- interval: strengudtryk, der er tidsinterval, du vil tilføje. Strengen skal have en af følgende værdier:
 - åååå år
 - q kvartal
 - m måned
 - y dag i året
 - d dag
 - w ugedag
 - ww uge
 - h time
 - n minut
 - s anden
- værdi: antallet enheder, du vil tilføje. Det kan være positivt (for at få datoer i fremtiden) eller negativt (for at få datoer i fortiden).
- dato: DateTime, der repræsenterer den dato, hvor intervallet, der er tilføjet.

**Eksempel:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Lægger 3 måneder, og returnerer en dato/klokkeslæt, der repræsenterer "2001-04-01".

----------
### <a name="datefromnum"></a>DateFromNum

**Beskrivelse:**  
Funktionen DateFromNum konverterer en værdi i ADS dato formatere til typen DateTime.

**Syntaks:**  
`dt DateFromNum(num value)`

**Eksempel:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Returnerer en dato/klokkeslæt, der repræsenterer 2012-01-01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Beskrivelse:**  
Funktionen DNComponent returnerer værdien af en bestemt DN-komponent, der går fra venstre.

**Syntaks:**  
`str DNComponent(ref dn, num ComponentNumber)`

- DN: attributten reference til at fortolke
- ComponentNumber: Komponenten i DN til at returnere

**Eksempel:**  
`DNComponent([dn],1)`  
Hvis dn er "cn = Joe, ou =...," returneres Joe

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Beskrivelse:**  
Funktionen DNComponentRev returnerer værdien af en bestemt DN komponent gå fra højre (slutningen).

**Syntaks:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- DN: attributten reference til at fortolke
- ComponentNumber - komponenten i DN til at returnere
- Indstillinger for: DC – ignorere alle komponenter med "dc ="

**Eksempel:**  
Hvis dn er "cn = Joe, ou = Atlanta, ou = GA, ou = DK, dc = contoso, dc = com" derefter  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Begge returnerer os.

----------
### <a name="error"></a>Fejl

**Beskrivelse:**  
Fejlfunktionen, der bruges til at returnere en brugerdefineret fejl.

**Syntaks:**  
`void Error(str ErrorMessage)`

**Eksempel:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Hvis attributten kontonavn ikke er til stede, Udløs en fejl på objektet.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Beskrivelse:**  
Funktionen EscapeDNComponent tager en komponent i en DN og handling lukker den, så det kan være repræsenteret i LDAP.

**Syntaks:**  
`str EscapeDNComponent(str value)`

**Eksempel:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Sikrer, at objektet, der kan oprettes i en LDAP-adresseliste, selvom attributten vist navn indeholder tegn, der skal være escape-tegn i LDAP.

----------
### <a name="formatdatetime"></a>Formaterdatoogklokkeslæt

**Beskrivelse:**  
Funktionen Formaterdatoogklokkeslæt bruges til at formatere en dato/klokkeslæt til en streng med et bestemt format

**Syntaks:**  
`str FormatDateTime(dt value, str format)`

- værdi: en værdi i DateTime-format
- format: en streng, der repræsenterer det format for at konvertere til.

**Bemærkninger:**  
De mulige værdier for formatet, der kan findes her: [Brugerdefineret dato/klokkeslæt-formater (funktionen Format)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Eksempel:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resulterer i "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kan medføre "20140905081453.0Z"

----------
### <a name="guid"></a>GUID

**Beskrivelse:**  
Funktionen GUID genererer et nyt tilfældigt GUID

**Syntaks:**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**Beskrivelse:**  
Funktionen IIF returnerer én af en række mulige værdier baseret på en bestemt betingelse.

**Syntaks:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- betingelse: en værdi eller et udtryk, der kan evalueres til SAND eller FALSK.
- VærdiHvisSand: Hvis betingelsen evalueres til sand, den returnerede værdi.
- VærdiHvisFalsk: Hvis betingelsen evalueres til falsk, den returnerede værdi.

**Eksempel:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Hvis brugeren er en intern, returnerer alias for en bruger med "t-" som føjet til begyndelsen af det, ellers returnerer brugerens alias som den er.

----------
### <a name="instr"></a>InStr

**Beskrivelse:**  
Funktionen InStr finder den første forekomst af en understreng i en streng

**Syntaks:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- strengkontrol: streng, der skal søges
- strengmatch: streng, der skal findes
- Start: startposition for at finde understrengen
- sammenligne: vbTextCompare eller vbBinaryCompare

**Bemærkninger:**  
Returnerer den placering, hvor understrengen blev fundet eller 0 Hvis blev ikke fundet.

**Eksempel:**  
`InStr("The quick brown fox","quick")`  
Evalues til 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Evalueres til 7

----------
### <a name="instrrev"></a>InStrRev

**Beskrivelse:**  
Funktionen InStrRev finder den sidste forekomst af en understreng i en streng

**Syntaks:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- strengkontrol: streng, der skal søges
- strengmatch: streng, der skal findes
- Start: startposition for at finde understrengen
- sammenligne: vbTextCompare eller vbBinaryCompare

**Bemærkninger:**  
Returnerer den placering, hvor understrengen blev fundet eller 0 Hvis blev ikke fundet.

**Eksempel:**  
`InStrRev("abbcdbbbef","bb")`  
Returnerer 7

----------
### <a name="isbitset"></a>IsBitSet

**Beskrivelse:**  
Funktionen IsBitSet test, hvis et bit er angivet eller ej

**Syntaks:**  
`bool IsBitSet(num value, num flag)`

- værdi: en numerisk værdi, der er evaluated.flag: en numerisk værdi, der indeholder bit skal evalueres

**Eksempel:**  
`IsBitSet(&HF,4)`  
Returnerer SAND, fordi bit "4" er angivet i den hexadecimale værdi "F"

----------
### <a name="isdate"></a>IsDate

**Beskrivelse:**  
Hvis udtrykket kan være evalueres som en dato/klokkeslæt, og derefter funktionen IsDate evalueres til sand.

**Syntaks:**  
`bool IsDate(var Expression)`

**Bemærkninger:**  
Bruges til at bestemme, hvis CDate() kan være vellykket.

----------
### <a name="isempty"></a>IsEmpty

**Beskrivelse:**  
Hvis attributten findes i CS eller MV men evalueres til en tom streng, og derefter funktionen IsEmpty evalueres til sand.

**Syntaks:**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Beskrivelse:**  
Hvis strengen kan konverteres til en GUID, derefter funktionen IsGuid evalueres til sand.

**Syntaks:**  
`bool IsGuid(str GUID)`

**Bemærkninger:**  
En GUID er defineret som en streng, følge en af disse mønstre: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx eller {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Bruges til at bestemme, hvis CGuid() kan være vellykket.

**Eksempel:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Hvis StrAttribute har et GUID-format, skal returnere en binær repræsentation, ellers returnerer en null-værdi.

----------
### <a name="isnull"></a>IsNull

**Beskrivelse:**  
Hvis udtrykket evalueres til Null, derefter returneres funktionen IsNull sand.

**Syntaks:**  
`bool IsNull(var Expression)`

**Bemærkninger:**  
For en attribut angives en null-værdi ved fravær af attributten.

**Eksempel:**  
`IsNull([displayName])`  
Returnerer SAND, hvis attributten ikke findes i CS eller MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Beskrivelse:**  
Hvis udtrykket er nul eller en tom streng, derefter returneres funktionen IsNullOrEmpty sand.

**Syntaks:**  
`bool IsNullOrEmpty(var Expression)`

**Bemærkninger:**  
For en attribut, vil dette evalueres til True, hvis attributten er fraværende eller er til stede, men er en tom streng.  
Den inverse fordelingsfunktion for denne funktion hedder IsPresent.

**Eksempel:**  
`IsNullOrEmpty([displayName])`  
Returnerer SAND, hvis attributten findes ikke eller er en tom streng i CS eller MV.

----------
### <a name="isnumeric"></a>IsNumeric

**Beskrivelse:**  
Funktionen IsNumeric returnerer en boolesk værdi, der angiver, om et udtryk kan evalueres som et tal.

**Syntaks:**  
`bool IsNumeric(var Expression)`

**Bemærkninger:**  
Bruges til at bestemme, hvis CNum() kan være vellykket til at fortolke udtrykket.

----------
### <a name="isstring"></a>IsString

**Beskrivelse:**  
Hvis udtrykket kan evalueres til en strengtypen, evaluerer funktionen IsString til sand.

**Syntaks:**  
`bool IsString(var expression)`

**Bemærkninger:**  
Bruges til at bestemme, hvis CStr() kan være vellykket til at fortolke udtrykket.

----------
### <a name="ispresent"></a>IsPresent

**Beskrivelse:**  
Hvis udtrykket evalueres til en streng, der ikke er Null og ikke er tomt, derefter returneres funktionen IsPresent sand.

**Syntaks:**  
`bool IsPresent(var expression)`

**Bemærkninger:**  
Den inverse fordelingsfunktion for denne funktion hedder IsNullOrEmpty.

**Eksempel:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Element

**Beskrivelse:**  
Funktionen element returnerer et element fra en opslagskolonne med flere valgmuligheder streng/attribut.

**Syntaks:**  
`var Item(mvstr attribute, num index)`

- attribut: attribut med flere værdier
- indeks: indeks til et element i strengen med flere værdier.

**Bemærkninger:**  
Funktionen element er nyttig sammen med funktionen indeholder, da denne funktion returnerer indekset til et element i attributten med flere værdier.

Viser en fejl, hvis indekset er overskredet.

**Eksempel:**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Returnerer den primære mailadresse.

----------
### <a name="itemornull"></a>ItemOrNull

**Beskrivelse:**  
Funktionen ItemOrNull returnerer et element fra en opslagskolonne med flere valgmuligheder streng/attribut.

**Syntaks:**  
`var ItemOrNull(mvstr attribute, num index)`

- attribut: attribut med flere værdier
- indeks: indeks til et element i strengen med flere værdier.

**Bemærkninger:**  
Funktionen ItemOrNull er nyttig sammen med funktionen indeholder, da denne funktion returnerer indekset til et element i attributten med flere værdier.

Hvis indekset er overskredet, returnerer en Null-værdi.

----------
### <a name="join"></a>Deltage i

**Beskrivelse:**  
Funktionen Join tager en opslagskolonne med flere valgmuligheder streng og returnerer en enkeltværdi-streng med angivne separator, der er indsat mellem hvert element.

**Syntaks:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- attribut: med flere valgmuligheder attribut, der indeholder strenge, der skal sammenkædes.
- afgrænser: en streng, der bruges til at adskille understrengene i den returnerede streng. Hvis den udelades, mellemrumstegnet ("") bruges. Hvis afgrænser er en streng af længden nul ("") eller intet, alle elementer på listen er sammenføjet uden afgrænsere.

**Bemærkninger**  
Der er fungerer ensartet mellem de deltage i og Delte funktioner. Funktionen Join tager en matrix af strenge og sammenkæder dem med en afgrænser streng til at returnere en enkelt streng. Funktionen Opdel tager en streng og adskiller den på afgrænseren, til at returnere en matrix af strenge. Vigtige forskel er dog joinforbindelse kan sammenføje strenge med en hvilken som helst afgrænser streng, opdelt kan kun adskille strenge ved hjælp af en enkelt tegn afgrænser.

**Eksempel:**  
`Join([proxyAddresses],",")`  
Kan returnere:"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>LCase

**Beskrivelse:**  
Funktionen LCase konverterer alle tegn i en tekststreng til små bogstaver.

**Syntaks:**  
`str LCase(str value)`

**Eksempel:**  
`LCase("TeSt")`  
Returnerer "test".

----------
### <a name="left"></a>Venstre

**Beskrivelse:**  
Funktionen venstre returnerer et angivet antal tegn fra venstre for en streng.

**Syntaks:**  
`str Left(str string, num NumChars)`

- streng: strengen skal returnere tegn fra
- NumChars: et nummer, der identificerer antallet tegn til at returnere fra starten (venstre) af strengen

**Bemærkninger:**  
En streng, der indeholder de første numChars tegn i streng:

- Hvis numChars = 0, returnerer tom streng.
- Hvis numChars < 0, returnerer en streng.
- Hvis strengen er null-værdi, returnere tom streng.

Hvis streng indeholder færre tegn end tal angivet i numChars, returneres en streng, der er identisk med streng (som er, der indeholder alle tegn i parameter 1).

**Eksempel:**  
`Left("John Doe", 3)`  
Returnerer "Joh".

----------
### <a name="len"></a>Længde

**Beskrivelse:**  
Funktionen længde returnerer antallet af tegn i en streng.

**Syntaks:**  
`num Len(str value)`

**Eksempel:**  
`Len("John Doe")`  
Returnerer 8

----------
### <a name="ltrim"></a>LTrim

**Beskrivelse:**  
Funktionen LTrim fjerner foranstillede mellemrum fra en streng.

**Syntaks:**  
`str LTrim(str value)`

**Eksempel:**  
`LTrim(" Test ")`  
Returnerer "Test"

----------
### <a name="mid"></a>Funktionen midt

**Beskrivelse:**  
Funktionen MIDT returnerer et angivet antal tegn fra en bestemt placering i en streng.

**Syntaks:**  
`str Mid(str string, num start, num NumChars)`

- streng: strengen skal returnere tegn fra
- Start: et nummer, der identificerer det første placere i streng til at returnere tegn fra
- NumChars: et nummer, der identificerer antallet tegn til at returnere fra positionen i streng

**Bemærkninger:**  
Returnere numChars tegn begyndende fra placering start i streng.  
En streng, der indeholder numChars tegn fra placering start i streng:

- Hvis numChars = 0, returnerer tom streng.
- Hvis numChars < 0, returnerer en streng.
- Hvis start > længden af strengen, returneres input streng.
- Hvis start < = 0, returnerer en streng.
- Hvis strengen er null-værdi, returnere tom streng.

Hvis der ikke er numChar tegn returneres resterende i streng fra start til placering, så mange tegn som muligt.

**Eksempel:**  
`Mid("John Doe", 3, 5)`  
Returnerer "hn gør".

`Mid("John Doe", 6, 999)`  
Returnerer "Jensen"

----------
### <a name="now"></a>Nu

**Beskrivelse:**  
Funktionen nu returnerer en dato/klokkeslæt, der angiver den aktuelle dato og klokkeslæt, afhængigt af computerens systemdato og klokkeslæt.

**Syntaks:**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Beskrivelse:**  
Funktionen NumFromDate returnerer en dato i ADS datoformat.

**Syntaks:**  
`num NumFromDate(dt value)`

**Eksempel:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Returnerer 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Beskrivelse:**  
De PadLeft funktionen venstre-led en streng til en bestemt længde ved hjælp af et medfølgende indre margen tegn.

**Syntaks:**  
`str PadLeft(str string, num length, str padCharacter)`

- streng: strengen skal tastatur.
- længde: et heltal, der repræsenterer den ønskede længde af streng.
- padCharacter: en streng bestående af et enkelt tegn skal bruges som tegnet tastatur

**Bemærkninger:**

- Hvis strengens længde er mindre end længden, tilføjet padCharacter flere gange i starten (venstre) af strengen, indtil den har en længde lig med længde.
- PadCharacter kan være et mellemrumstegn, men det være ikke en null-værdi.
- Hvis strengens længde er lig med eller større end længden, returneres strengen uændret.
- Hvis du har en længde større end eller lig med længden streng, returneres en streng, der er identisk med streng.
- Hvis strengens længde er mindre end længden, er en ny streng af den ønskede længde returneres med streng foret med en padCharacter.
- Hvis strengen er null-værdi, returnerer funktionen en tom streng.

**Eksempel:**  
`PadLeft("User", 10, "0")`  
Returnerer "000000User".

----------
### <a name="padright"></a>PadRight

**Beskrivelse:**  
De PadRight funktionen højre-led en streng til en bestemt længde ved hjælp af et medfølgende indre margen tegn.

**Syntaks:**  
`str PadRight(str string, num length, str padCharacter)`

- streng: strengen skal tastatur.
- længde: et heltal, der repræsenterer den ønskede længde af streng.
- padCharacter: en streng bestående af et enkelt tegn skal bruges som tegnet tastatur

**Bemærkninger:**

- Hvis strengens længde er mindre end længden, derefter føjes padCharacter flere gange til slutningen (højre) af strengen indtil den har en længde, der er lig med længde.
- padCharacter kan være et mellemrumstegn, men det være ikke en null-værdi.
- Hvis strengens længde er lig med eller større end længden, returneres strengen uændret.
- Hvis du har en længde større end eller lig med længden streng, returneres en streng, der er identisk med streng.
- Hvis strengens længde er mindre end længden, er en ny streng af den ønskede længde returneres med streng foret med en padCharacter.
- Hvis strengen er null-værdi, returnerer funktionen en tom streng.

**Eksempel:**  
`PadRight("User", 10, "0")`  
Returnerer "User000000".

----------
### <a name="pcase"></a>PCase

**Beskrivelse:**  
Funktionen PCase konverterer det første tegn i hver mellemrumssepareret ord i en tekststreng til store bogstaver, og alle andre tegn, der er konverteret til små bogstaver.

**Syntaks:**  
`String PCase(string)`

**Bemærkninger:**

- Denne funktion giver ikke i øjeblikket stort hus for at konvertere et ord, der er helt store bogstaver, som en forkortelse.

**Eksempel:**  
`PCase("TEsT")`  
Returnerer "Test".

`PCase(LCase("TEST"))`  
Returnerer "Test"

----------
### <a name="randomnum"></a>RandomNum

**Beskrivelse:**  
Funktionen RandomNum returnerer et tilfældigt tal mellem et angivet tidsinterval.

**Syntaks:**  
`num RandomNum(num start, num end)`

- Start: et nummer, der identificerer den nedre grænse for værdien tilfældigt til at generere
- Afslut: et nummer, der identificerer den øvre grænse for værdien tilfældigt til at generere

**Eksempel:**  
`Random(100,999)`  
Kan returnere 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Beskrivelse:**  
Funktionen RemoveDuplicates tager en opslagskolonne med flere valgmuligheder streng, og Sørg for, at hver enkelt værdi er entydige.

**Syntaks:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Eksempel:**  
`RemoveDuplicates([proxyAddresses])`  
Returnerer en sanitized proxyAddress-attribut, hvor alle de dublerede værdier er blevet fjernet.

----------
### <a name="replace"></a>Erstat

**Beskrivelse:**  
Funktionen Erstat erstatter alle forekomster af en streng til en anden streng.

**Syntaks:**  
`str Replace(str string, str OldValue, str NewValue)`

- streng: en streng til at erstatte værdier i.
- Tidligere værdi: Strengen til at søge efter og erstatte.
- NewValue: Streng til at erstatte.

**Bemærkninger:**  
Funktionen genkender følgende speciel monikere:

- \n – ny linje
- \r – vognretur
- \t – fanen

**Eksempel:**  
`Replace([address],"\r\n",", ")`  
Erstatter CRLF med et komma og et mellemrum og kan føre til "Én Microsoft måde, Redmond, WA, USA"

----------
### <a name="replacechars"></a>ReplaceChars

**Beskrivelse:**  
Funktionen ReplaceChars erstatter alle forekomster af tegn, der findes i ReplacePattern strengen.

**Syntaks:**  
`str ReplaceChars(str string, str ReplacePattern)`

- streng: en streng, der skal erstatte tegn i.
- ReplacePattern: en streng, der indeholder en ordbog med tegn, der skal erstatte.

Formatet er {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} hvor kilde er tegnet til at finde og målrette strengen skal Erstat med.

**Bemærkninger:**

- Funktionen tager hver forekomst af definerede kilder og erstatter dem med mål.
- Kilden være ét (unicode) tegn.
- Kilden kan ikke være tom eller mere end ét tegn (fejl ved fortolkning af).
- Destinationen kan indeholde flere tegn, for eksempel ö:oe, β:ss.
- Destinationen kan være tom, der angiver, at tegnet, der skal fjernes.
- Kilden er store og små bogstaver og skal være en nøjagtig match.
- (Komma) og: (kolon) er reserverede tegn og kan ikke erstattes med denne funktion.
- Mellemrum og andre hvide tegn i strengen ReplacePattern ignoreres.

**Eksempel:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Returnerer Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Returnerer "ONeil", enkelt aksemærker er defineret for at blive fjernet.

----------
### <a name="right"></a>Højre

**Beskrivelse:**  
Funktionen højre returnerer et angivet antal tegn fra højre (end) i en streng.

**Syntaks:**  
`str Right(str string, num NumChars)`

- streng: strengen skal returnere tegn fra
- NumChars: et nummer, der identificerer antallet tegn til at returnere fra slutningen (højre) af strengen

**Bemærkninger:**  
NumChars tegn returneres fra den sidste position af streng.

En streng, der indeholder de sidste numChars tegn i streng:

- Hvis numChars = 0, returnerer tom streng.
- Hvis numChars < 0, returnerer en streng.
- Hvis strengen er null-værdi, returnere tom streng.

Hvis streng indeholder færre tegn end tal angivet i NumChars, returneres en streng, der er identisk med streng.

**Eksempel:**  
`Right("John Doe", 3)`  
Returnerer "Jensen".

----------
### <a name="rtrim"></a>RTrim

**Beskrivelse:**  
Funktionen RTrim fjerner efterstillede mellemrum fra en streng.

**Syntaks:**  
`str RTrim(str value)`

**Eksempel:**  
`RTrim(" Test ")`  
Returnerer "Test".

----------
### <a name="split"></a>Opdelt

**Beskrivelse:**  
Funktionen Opdel tager en streng, der er adskilt med en afgrænser og gør det en opslagskolonne med flere valgmuligheder streng.

**Syntaks:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- værdi: strengen med en afgrænsningstegn til at adskille.
- afgrænser: enkelttegn som skal bruges som afgrænseren.
- grænse: maksimale antal værdier, der kan returnere.

**Eksempel:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Returnerer en streng, der er flere værdier med 2 elementer nyttige for attributten proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Beskrivelse:**  
Funktionen StringFromGuid tager en binær GUID og konverterer det til en streng

**Syntaks:**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Beskrivelse:**  
Funktionen StringFromSid konverterer en bytematrix, der indeholder et sikkerheds-id til en streng.

**Syntaks:**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Skift

**Beskrivelse:**  
Funktionen Switch bruges til at returnere en enkelt værdi, der er baseret på betingelser, der er evalueret.

**Syntaks:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- udtryk: Variantudtryk, du ønsker at evaluere.
- værdi: værdi, der skal returneres, hvis det tilsvarende udtryk er sand.

**Bemærkninger:**  
Skift argument funktionslisten består af par af udtryk og værdier. Udtrykkene evalueres fra venstre mod højre, og den værdi, der er knyttet til det første udtryk, der evalueres til sand, returneres. Hvis delene, ikke er korrekt parvis, opstår der en kørselsfejl.

Eksempelvis hvis udtryk1 er sand, returnerer Skift værdi1. Hvis udtr-1 er falsk, men udtr-2 er sand, returnerer Skift værdien 2 osv.

Skift returnerer en ingen varer Hvis:

- Ingen af udtrykkene er sand.
- Det første sande udtryk har en tilsvarende værdi, der er Null.

Skift evaluerer alle udtryk, selvom den returnerer kun én af dem. Derfor skal du holde øje med uønskede side effekter. Eksempelvis hvis evalueringen af et udtryk, der resulterer i en division med nul fejl, opstår en fejl.

Værdien kan også være fejlfunktionen, hvilket returnerer en brugerdefineret streng.

**Eksempel:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Returnerer det sprog, talte i nogle større byer, ellers returnerer en fejl.

----------
### <a name="trim"></a>Trim

**Beskrivelse:**  
Funktionen Trim fjerner foranstillede og efterstillede mellemrum fra en streng.

**Syntaks:**  
`str Trim(str value)`  

**Eksempel:**  
`Trim(" Test ")`  
Returnerer "Test".

`Trim([proxyAddresses])`  
Fjerner foranstillede og efterstillede mellemrum for hver værdi i attributten proxyAddress.

----------
### <a name="ucase"></a>UCase

**Beskrivelse:**  
Funktionen UCase konverterer alle tegn i en tekststreng til store bogstaver.

**Syntaks:**  
`str UCase(str string)`

**Eksempel:**  
`UCase("TeSt")`  
Returnerer "TEST".

----------
### <a name="word"></a>Word

**Beskrivelse:**  
Word-funktionen returnerer et ord, der er indeholdt i en tekststreng baseret på parametre, der beskriver afgrænserne skal bruges, og word-nummeret til at returnere.

**Syntaks:**  
`str Word(str string, num WordNumber, str delimiters)`

- streng: strengen til at returnere et ord fra.
- WordNumber: et nummer, der identificerer, word hvis nummer skal returnere.
- afgrænsere: en streng, der repræsenterer den delimiter(s), der skal bruges til at identificere ord

**Bemærkninger:**  
De enkelte strenge af tegn i streng, der er adskilt af et tegn i afgrænsere er identificeret som ord:

- Hvis tal < 1, returnerer tom streng.
- Hvis streng er null, returneres tom streng.

Hvis strengen indeholder mindre end tal ord eller streng indeholder ikke en hvilken som helst ord, der er identificeret med afgrænsere, returneres en tom streng.

**Eksempel:**  
`Word("The quick brown fox",3," ")`  
Returnerer "brun"

`Word("This,string!has&many separators",3,",!&#")`  
Returnerer "indeholder"

## <a name="additional-resources"></a>Yderligere ressourcer

* [Forstå deklarativ klargøring udtryk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure Active Directory forbinde-synkronisering: Tilpasse indstillinger for synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
