<properties
    pageTitle="Typer af risikohændelser, der er registreret af Azure Active Directory identitetsbeskyttelse | Microsoft Azure"
    description="Dette emne får du en detaljeret oversigt over de tilgængelige typer risikoen begivenheder i Azure Active Directory identitetsbeskyttelse"
    services="active-directory"
    keywords="beskyttelse af Azure active directory din identitet, skyen app registrering, administration af programmer, sikkerhed, risikoen, risikoen niveau, sikkerhedsrisiko, sikkerhedspolitik"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="markvi"/>

#<a name="types-of-risk-events-detected-by-azure-active-directory-identity-protection"></a>Typer af risikohændelser, der er registreret af Azure Active Directory identitetsbeskyttelse 

I Azure Active Directory identitetsbeskyttelse risikohændelser er hændelser, der:

- blev markeret som mistænkelig

- Angiv, at en identitet kan være blevet beskadiget. 

Dette emne giver dig et detaljeret overblik over de tilgængelige typer risikoen begivenheder.


## <a name="leaked-credentials"></a>Lækket legitimationsoplysninger

Lækket legitimationsoplysninger er fundet offentliggjort i webdelen mørke ved forskere i Microsoft sikkerhed. Disse legitimationsoplysninger findes normalt i almindelig tekst. De er markeret med Azure AD legitimationsoplysninger, og hvis der er en match, de rapporteres som "Leaked legitimationsoplysninger" i beskyttelse af din identitet.

Lækket legitimationsoplysninger risikohændelser er klassificeret som en "Høj" risikohændelse alvor, fordi de giver en klar angivelse, at brugernavn og adgangskode er tilgængelige for en hacker.

## <a name="impossible-travel-to-atypical-locations"></a>Umuligt rejse til atypiske placeringer

Denne risiko hændelsestype identificerer to logon stammer fra geografisk afstand placeringer, hvor mindst én af placeringerne måske også atypiske for brugeren, givet tidligere funktionsmåde. Desuden er tiden mellem de to logon mindre end den tid, det skulle være trådt brugeren rejser fra den første placering til den anden, der angiver, at en anden bruger bruger de samme legitimationsoplysninger. 

Denne computer learning algoritme, som ignorerer indlysende "*falsk positive*" bidrager til betingelsen umuligt rejse som VPN'er og placeringer, der jævnligt bruges af andre brugere i organisationen.  Systemet har en indledende learning periode for 14 dage, hvor det lærer en ny bruger-logon funktionsmåde.

Umuligt rejse er som regel en god indikator, som en hacker kunne korrekt logon. Falsk positive kan dog opstå, når en bruger er på farten, ved hjælp af en ny enhed eller bruger en VPN-forbindelse, der typisk ikke bruges af andre brugere i organisationen. En anden kilde for falsk-positive er programmer, der fejlagtigt overfører server IP'er som klient IP-adresser, som kan give udseendet af logon er hostet noter direkte fra det sted, hvor dette program er back end-datacenter (ofte er Microsoft datacentre, som kan give udseendet af logon tager placere fra Microsoft, der er ejet IP-adresser). Som et resultat af disse falsk-positive er risikoniveauet for begivenheden risikoen "**Medium**".

##<a name="sign-ins-from-infected-devices"></a>Logon fra inficerede enheder

Denne risiko hændelsestype identificerer logon fra inficeret med malware, enheder, der er blevet konstateret, at aktivt kommunikere med en bot server. Dette bestemmes ved at korrelere IP-adresserne på brugerens enhed mod IP-adresser, der er i kontakt med en bot server. 

Denne risikohændelse identificerer IP-adresser, ikke Brugerenheder. Hvis flere enheder er bag en enkelt IP-adresse, og kun nogle er styres af et bot netværk, logon fra andre enheder min udløser begivenheden unødvendigt, hvilket er årsagen til klassificering begivenheden risikoen som "**lavt**".  

Vi anbefaler, at du kontakte brugeren, og scanne alle brugerens enheder. Det er også muligt, at en brugers personlig enhed er inficeret, eller som tidligere nævnt, en anden bruger en inficeret enhed fra den samme IP-adresse som brugeren. Inficerede enheder er ofte inficeret med malware, der endnu ikke er blevet identificeret af antivirussoftware, og kan også angive som forkert bruger vaner, der kan være årsagen til kameraet, for at blive inficeret.

Du kan finde yderligere oplysninger om, hvordan du adresse malwareinfektioner [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## <a name="sign-ins-from-anonymous-ip-addresses"></a>Logon fra anonyme IP-adresser

Denne risiko hændelsestype identificerer brugere, der har logget på fra en IP-adresse, der er identificeret som en anonym proxy IP-adresse. Disse proxyer, der bruges af personer, der skal skjules deres enhed IP-adresse, og kan bruges til ondsindet angreb.

Vi anbefaler, at du straks kontakter brugeren for at bekræfte, hvis de blev brugt anonyme IP-adresser. Risikoniveauet for denne risiko hændelsestype er "**Medium**", fordi i sig selv en anonym IP ikke er en stærk angivelse af en konto skade.

## <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logon fra IP-adresser med mistænkelig aktivitet

Denne risiko hændelsestype identificerer IP-adresser, et stort antal mislykkede forsøg-kunne ses, på tværs af flere brugerkonti over en kort tidsperiode. Dette svarer til trafikmønstre af IP-adresser, der bruges af hackere og er en stærk indikator, at konti er enten allerede eller er ved at være kompromitteret. Dette er en maskine læ algoritme, ignorerer indlysende "*falsk positive*" som IP-adresser, der jævnligt bruges af andre brugere i organisationen.  Systemet har en indledende learning periode for 14 dage, hvor det lærer logon funktionsmåden for en ny bruger og lejer.

Vi anbefaler, at du kontakter brugeren for at bekræfte, hvis de har logget på fra en IP-adresse, der blev markeret som mistænkelig. Risikoniveauet for denne hændelsestype er "**Medium**", fordi flere enheder er muligvis bag den samme IP-adresse, mens kun nogle kan være ansvarlig for mistænkelige aktiviteten. 


## <a name="sign-in-from-unfamiliar-locations"></a>Log på fra fremmede placeringer

Denne risiko hændelsestype er en metode til beregning i realtid-logon, finder tidligere logon placeringer (IP-adresse, længde / breddegrader og ASN) til at bestemme nye / fremmede placeringer. Systemet indeholder oplysninger om tidligere placeringer, der bruges af en bruger og finder disse "fortrolig" placeringer. Risikoen udløses selv, når logonnavn foretages fra en placering, ikke der allerede findes på listen over velkendte placeringer. Systemet har en indledende learning periode af 14 dage, hvor det ikke markere alle nye placeringer som fremmede placeringer. Systemet ignorerer også logon fra velkendte enheder og placeringer, der geografisk er tæt på et sted. <br>
Fremmede placeringer kan give en stærk angivelse, som en hacker kan forsøger at bruge en stjålne identitet. Falsk positive kan opstå, når en bruger er ude at rejse, prøver en ny enhed eller bruger en ny VPN-forbindelse. Som et resultat af disse falsk positive er risikoniveauet for denne hændelsestype "**Medium**".


## <a name="azure-ad-anomalous-activity-reports"></a>Azure AD uoverensstemmende aktivitet rapporter

Nogle af disse risikohændelser er tilgængelige via Azure AD uoverensstemmende aktivitet rapporterne i portalen Azure. Tabellen nedenfor viser en liste over de forskellige typer af risikoen begivenhed og tilsvarende **Azure AD uoverensstemmende aktivitet** rapporten. Microsoft fortsætter med at investere i denne plads og planer om at løbende forbedre registrering nøjagtigheden af eksisterende risikoen begivenheder og tilføje nye risikoen begivenhed typer jævnligt. 



| Hændelsestype identitet beskyttelse risikoen | Tilsvarende Azure AD uoverensstemmende aktivitetsrapport |
| :-- | :-- |
| Lækket legitimationsoplysninger    | Brugere med lækket legitimationsoplysninger |
| Umuligt rejse til atypiske placeringer | Uregelmæssige logon aktivitet |
| Logon fra inficerede enheder    | Logon fra muligvis inficerede enheder |
| Logon fra anonyme IP-adresser  | Logon fra ukendte kilder |
| Logon fra IP-adresser med mistænkelig aktivitet | Logon fra IP-adresser med mistænkelig aktivitet |
| Fortegn i fra fremmede placeringer    | - |
| Låsning begivenheder    | - |

Følgende Azure AD uoverensstemmende aktivitet rapporter er ikke inkluderet som risikoen begivenheder i Azure AD identitet beskyttelse, og er derfor ikke tilgængelige til beskyttelse af din identitet. Disse rapporter er stadig tilgængelige i portalen Azure, men de kan være forældet på et tidspunkt i fremtiden som de er blive erstattet af risikoen begivenheder i beskyttelse af din identitet.

- Logon efter flere fejl
- Logon fra forskellige lande


## <a name="see-also"></a>Se også

- [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md)


