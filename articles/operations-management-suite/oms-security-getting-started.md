<properties
   pageTitle="Introduktion til handlinger Management pakke sikkerhed og Overvåg løsning | Microsoft Azure"
   description="Dette dokument hjælper dig med at komme i gang med handlinger Management pakke sikkerhed og Overvåg løsning funktioner til at overvåge dine hybrid skyen."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Introduktion til handlinger Management pakke sikkerhed og Overvåg løsning
Dette dokument hjælper dig med at komme hurtigt i gang med handlinger Management pakke (OMS) sikkerhed og Overvåg løsning-funktioner ved at lede dig gennem hver indstilling.

## <a name="what-is-oms"></a>Hvad er OMS?
Microsoft Operations Management pakke (OMS) er Microsofts skybaseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen. Du kan finde flere oplysninger om OMS du læse artiklen [Handlinger Management pakke](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>OMS sikkerhed og Overvåg dashboard

OMS sikkerhed og Overvåg løsningen indeholder et omfattende indblik i organisationens IT sikkerhed stilling med indbyggede søgeforespørgsler væsentlige problemer, der kræver din opmærksomhed. **Sikkerhed og Overvåg** dashboardet er startskærmen for alt i forbindelse med sikkerhed i OMS. Det giver overordnet indsigt i tilstanden sikkerhed på computerne. Den indeholder også muligheden for at få vist alle begivenheder fra de seneste 24 timer, 7 dage eller andre brugerdefinerede tidsramme. For at få adgang til **sikkerhed og Overvåg** dashboard ved at følge disse trin:

1. Klik på **Indstillinger for** feltet i venstre side i dashboardet **Microsoft handlinger Management Suite** primære.
2. Klik på **sikkerhed og Overvåg** indstilling under **løsninger** i bladet **Indstillinger** .
3. Dashboardet for **sikkerhed og Overvåg** vises:

    ![OMS sikkerhed og Overvåg dashboard](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Hvis du får adgang til dette dashboard for første gang, og du ikke har enheder overvåges af OMS, bliver billeder ikke udfyldes med data, der fås fra en agent. Når du installerer agenten, det kan tage lidt tid til at udfylde, derfor hvad du ser til at begynde muligvis mangler nogle data som de stadig overførsel til skyen.  I dette tilfælde er det normale at se nogle felter uden fysiske oplysninger. Læs [forbinde Windows-computere direkte til OMS](https://technet.microsoft.com/library/mt484108.aspx) kan finde flere oplysninger om, hvordan du installerer OMS agent i en Windows system og [oprette forbindelse Linux-computere at OMS](https://technet.microsoft.com/library/mt622052.aspx) kan finde flere oplysninger om, hvordan du udfører denne opgave i et Linux-system.

> [AZURE.NOTE] Agenten vil indsamle de oplysninger, der er baseret på de aktuelle hændelser, der er aktiveret, for eksempel computernavn, IP-adresse og bruger navn. Ingen dokument/filer, databasenavnet eller private data indsamles.   

Løsninger, der er en samling af logik, visualisering og data acquisition-regler, der adresserer vigtige kundeudfordringer. Sikkerhed og overvågningsloggen er en løsning, andre kan tilføjes separat. Læs artikel [Tilføj løsninger](https://technet.microsoft.com/library/mt674635.aspx) for at få flere oplysninger om hvordan du tilføjer en ny løsning.

Dashboardet OMS sikkerhed og overvågningsloggen er organiseret i fire overordnede kategorier:

- **Sikkerhed domæner**: i dette område, du vil ikke kunne yderligere udforske sikkerhedsposter over en periode, få adgang til malware vurdering, opdatere vurdering netværkssikkerhed identitets- og oplysninger, og computere med sikkerhed begivenheder og hurtigt få adgang til Azure Sikkerhedscenter dashboard.
- **Væsentlige problemer**: denne indstilling, kan du hurtigt kan identificere antallet af aktive problemer og alvorlighed af disse problemer.
- **Registreringer (Preview)**: gør det muligt at identificere mønstre, angreb ved at visualisere sikkerhedsadvarsler, som de foregå mod dine ressourcer.
- **Truslen Intelligence**: gør det muligt at identificere mønstre, angreb ved at visualisere det samlede antal servere med udgående skadelig IP-trafik, typen skadelig truslen og et kort, der viser, hvor disse IP'er kommer fra. 
- **Almindelige sikkerhed forespørgsler**: denne indstilling giver dig en liste over de mest almindelige sikkerhed forespørgsler, som du kan bruge til at overvåge dit miljø. Når du klikker på en af disse forespørgsler, åbnes bladet **søgning** med resultaterne for den pågældende forespørgsel.

> [AZURE.NOTE] flere oplysninger om hvordan OMS hele tiden dine data sikre, at læse hvordan OMS sikrer dine data.

## <a name="security-domains"></a>Sikkerhed domæner

Det er vigtigt at kunne få hurtig adgang til den aktuelle tilstand for dit miljø, når overvågning ressourcer. Men det er også vigtigt at kunne registrere tilbage hændelser, der fra fortiden, der kan medføre en bedre forståelse af hvad der sker i dit miljø på bestemt sted i gang. 

> [AZURE.NOTE] dataopbevaring er i overensstemmelse med OMS priser plan. Besøg [Microsoft handlinger Management Suite](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) priser siden kan finde flere oplysninger.

Hændelsen svar og forensics undersøgelse scenarier vil direkte drage fordel af de resultater, der er tilgængelige i feltet **Sikkerhedsposter over tid** .

![Sikkerhedsposter over tid](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Når du klikker på dette felt, bladet **søgning** åbnes og viser et forespørgselsresultat for **Security Events** (Type = SecurityEvents) med data, der er baseret på de seneste syv dage, som vist nedenfor:

![Sikkerhedsposter over tid](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

Søgeresultatet er opdelt i to ruder: venstre rude giver dig en opdeling af antallet af sikkerhedshændelser, der blev fundet de computere, hvor disse hændelser, der blev fundet, antallet af konti, der blev opdaget i disse computere og typerne aktiviteter. Højre rude giver dig de samlede resultater og en kronologisk visning af sikkerhedshændelser med computerens navn og begivenhed aktiviteter. Du kan også klikke på **Vis flere** for at få vist flere oplysninger om begivenheden, som begivenhed data, begivenhed-ID og begivenhed kilden.

> [AZURE.NOTE] Du kan finde flere oplysninger om OMS søgeforespørgsel Læs [OMS søge reference](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Antimalwareprogram vurdering

Denne indstilling kan du hurtigt identificere computere med utilstrækkelig beskyttelse og computere, som er skadet af en del af en malware. Malware vurdering status og fundet trusler på overvåget servere læses, og derefter data, der sendes til tjenesten OMS i skyen for behandling. Servere med fundet trusler og servere med utilstrækkelig beskyttelse vises i dashboardet malware vurdering, som er tilgængelige, når du klikker på i feltet **Antimalwareprogram vurdering** . 

![malware vurdering](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Når du klikker på den, åbnes bladet **Søg** ligesom enhver anden dynamisk felt tilgængeligt i OMS Dashboard, med resultatet af forespørgslen. Denne indstilling, hvis du klikker på indstillingen **Ikke rapportering** under **Beskyttelsesstatus**, får du det forespørgselsresultat, der viser denne enkelt post, der indeholder den computernavn og dens rang, som vist nedenfor:

![et søgeresultat](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *rang* er en klasse, hvorved til afspejle status for beskyttelsen (til, fra opdateret, osv) og trusler, der er fundet. Har, som et tal hjælper med at foretage sammenlægninger.

Hvis du klikker på navnet på computeren, får du den kronologiske visning af beskyttelsesstatus for denne computer. Dette er meget nyttigt for scenarier, som du har brug at forstå, hvis Antimalwareprogram når blev installeret og på et tidspunkt, den er blevet fjernet.   

### <a name="update-assessment"></a>Opdatere vurdering 

Denne indstilling kan du hurtigt at bestemme den overordnede visning potentielle problemer med sikkerhed, og om eller hvor vigtige disse opdateringer er i dit miljø. OMS sikkerhed og Overvåg løsning giver kun visualisering af disse opdateringer, de rigtige data, der kommer fra [System-løsninger, opdateringer](https://technet.microsoft.com/library/mt484096.aspx), som er et andet modul i OMS. Her er et eksempel på opdateringerne:

![systemopdateringer](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Du kan finde flere oplysninger om opdateringer løsning Læs [opdatere servere med løsningen systemopdateringer](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Identitets- og

Identitet skal være kontrolelement planet for virksomheden, beskytte din identitet skal være det højeste prioritet. Mens i tidligere var der grænser omkring organisationer, og disse grænser er en af de primære defensive grænser, i dag med flere data og flere apps flytte til skyen identitet, bliver nye kanten. 

> [AZURE.NOTE] data, der kun er baseret på Security Events login data (hændelse-ID 4624) i de kommende Office 365-logon og Azure AD-data også blive medtaget i øjeblikket.

Ved at overvåge dine identitet aktiviteter vil du kunne udføre proaktiv handlinger, før en hændelse finder sted eller aktiv handlinger til at holde et forsøg på angreb. Dashboardet for **identitets- og** giver dig et overblik over din identitet-tilstand, herunder antallet af mislykkede forsøg på at logge, brugerens konto, der blev brugt under disse forsøg, konti, der er låst ude, konti med ændret eller nulstille din adgangskode og aktuelt antallet af konti, der er logget på. 

Når du klikker på i feltet **identitets- og** får du vist dashboardet for følgende:

![identitets- og](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

De oplysninger, der er tilgængelige i dette dashboard kan med det samme kan hjælpe dig med at identificere en potentiel mistænkelig aktivitet. For eksempel, der er 338 forsøger at logge på som **Administrator** og 100% af disse forsøg mislykkedes. Det kan skyldes et råstyrke angreb mod denne konto. Hvis du klikker på denne konto, du kan få flere oplysninger, som kan hjælpe dig til at bestemme target ressourcen for denne potentielle angreb:

![søgeresultater](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

Detaljeret rapporten indeholder vigtige oplysninger om begivenheden, herunder: computeren, typen logon (i dette tilfælde netværkslogon), aktivitet (i dette tilfælde hændelse 4625) og en omfattende tidslinje af hvert forsøg. 

### <a name="computers"></a>Computere

Dette felt kan bruges til at få adgang til alle computere, som aktivt har sikkerhed begivenheder. Når du klikker i dette felt vises på listen over computere med sikkerhed begivenheder og antallet af hændelser på hver enkelt computer:

![Computere](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Du kan fortsætte med din undersøgelse ved at klikke på alle computere og gennemse de sikkerhedshændelser, der er markeret med flag.

### <a name="azure-security-center"></a>Azure Security Center

Dette felt er grundlæggende en genvej til at få adgang til Azure Sikkerhedscenter dashboard. Læs [Introduktion til Azure Sikkerhedscenter](../security-center/security-center-get-started.md) kan finde flere oplysninger om denne løsning.

## <a name="notable-issues"></a>Væsentlige problemer

Hovedformålet af denne gruppe af indstillinger er at tilvejebringe et hurtigt overblik over de problemer, du har i dit miljø, ved at kategorisere dem i kritisk, advarsel og information. Feltet aktive problem type, er det en visualisering af disse problemer, men det giver dig ikke mulighed for at undersøge flere detaljer om dem, at du skal bruge den nederste del af dette felt, der indeholder navnet på problemet (navn), hvor mange objekter havde dette sker (antal) og hvor vigtige det er (ALVORLIGHED).

![Væsentlige problemer](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Du kan se, at disse problemer er allerede er omfattet i forskellige områder af gruppen **Sikkerhed domæner** , som forstærker formålet med denne visning: visualisere de vigtigste problemer i dit miljø fra ét sted.

## <a name="detections-preview"></a>Registreringer (Preview)

Hovedformålet af denne indstilling er at give IT til hurtigt at identificere potentielle trusler til deres miljø via og alvorlighed af truslen.

![Truslen Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Denne indstilling kan også bruges under hændelsen svar undersøgelse til at udføre vurdering og få mere at vide om angrebet.

> [AZURE.NOTE] Se [Sådan udnytte Azure Sikkerhedscenter og Microsoft Operations Management pakke til en hændelse svar](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703), du kan finde flere oplysninger om, hvordan du bruger OMS til hændelse svar.

## <a name="threat-intelligence"></a>Truslen Intelligence

Den nye truslen intelligence sektion sikkerhed og Overvåg løsningens visualiseres de mulige angreb mønstre på flere måder: det samlede antal servere med udgående skadelig IP-trafik, typen skadelig truslen og et kort, der viser, hvor disse IP'er kommer fra. Du kan interagere med kortet og klikke på IP'er kan finde flere oplysninger.

Gul nåle på kortet angiver indgående trafik fra skadelig IP'er. Det er ikke usædvanlige til servere, der vises til internettet for at se indgående skadelig trafik, men vi anbefaler, at du gennemgå disse forsøg på at sikre, at ingen af dem fuldføres. Disse indikatorer er baseret på IIS-logfiler, WireData og logfører Windows Firewall.  

![Truslen Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Almindelige sikkerhed forespørgsler

Liste over almindelige sikkerhed forespørgsler tilgængelige kan være nyttige til du hurtigt få adgang til de oplysninger om ressourcen og tilpasse det baseret på dit miljø behov. Disse almindelige forespørgsler er:

- Alle sikkerhedsaktiviteter
- Sikkerhedsaktiviteter på computeren "computer01.contoso.com" (Erstat med navnet på din egen computer)
- Sikkerhedsaktiviteter på computeren "computer01.contoso.com" for konto "Administrator" (Erstat med din egen computer og konto navne)
- Logge på aktivitet efter Computer
- Konti, der afsluttes Microsoft Antimalwareprogram på enhver computer
- Computere, hvor Microsoft Antimalwareprogram processen blev afbrudt
- Computere, hvor "hash.exe" blev udført (Erstat med forskellige procesnavn)
- Alle procesnavne, der blev udført
- Logge på aktivitet efter firma
- Konti der fra en fjernplacering logget på computeren "computer01.contoso.com" (Erstat med navnet på din egen computer)

## <a name="see-also"></a>Se også

I dette dokument, blev du introduceret til OMS sikkerhed og Overvåg løsning. Hvis du vil vide mere om OMS sikkerhed skal du se følgende artikler:

- [Oversigt over operationer Management pakke (OMS)](operations-management-suite-overview.md)
- [Overvåge og reagere på sikkerhedsadvarsler i handlinger Management pakke sikkerhed og overvågningsloggen løsning](oms-security-responding-alerts.md)
- [Overvåge ressourcer i handlinger Management pakke sikkerhed og overvågningsloggen løsning](oms-security-monitoring-resources.md)
