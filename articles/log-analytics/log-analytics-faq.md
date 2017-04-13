<properties
    pageTitle="Log Analytics ofte stillede spørgsmål om | Microsoft Azure"
    description="Svar på ofte stillede spørgsmål om tjenesten Log Analytics."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="log-analytics-faq"></a>Log Analytics ofte stillede spørgsmål

I denne Microsoft FAQ er en liste over ofte stillede spørgsmål om Log Analytics i Microsoft Operations Management pakke (OMS). Hvis du har yderligere spørgsmål om Log Analytics, skal du gå til det [diskussionsforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) og stil dit spørgsmål. En person fra vores community hjælper dig med at få dine svar. Hvis et spørgsmål er oftest stillede, kan vi tilføje den til denne artikel, så du kan finde hurtigt og nemt.

## <a name="general"></a>Generelt

**Spørgsmål: hvilke Kontroller udføres af AD og SQL vurdering løsninger?**

A. Følgende forespørgsel vises en beskrivelse af alle de Kontroller, der aktuelt udføres:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Resultaterne kan derefter eksporteres til Excel til yderligere gennemsyn.

* *SP: Hvorfor ser noget andet end *OMS* i SCOM Administration? **

SV: afhængigt af, hvilke opdatering opsummering af SCOM du befinder dig i, kan du se en node til *System Center Advisor*, *Funktionsdygtige indsigt*eller *Log analyser*.

Opdater tekststreng til *OMS* er inkluderet i en management pack, som skal importeres manuelt. Følg vejledningen på seneste SCOM Update Rollup KB-artiklen, og Opdater konsollen OMS for at få vist de seneste opdateringer i noden *OMS* .

* *SP: er der en *lokalt* version af OMS? **

SV: Nej. Log Analytics behandler og gemmer meget store datamængder. Som en skybaseret tjeneste kan Log Analytics skalere op Hvis det er nødvendigt, og undgå nogen indflydelse på ydeevnen i dit miljø.

Også blive en skybaseret tjeneste betyder, at du ikke behøver at holde Log Analytics-infrastruktur og kører og kan modtage hyppige funktionsopdateringer og løsninger.

## <a name="configuration"></a>Konfiguration
**Spørgsmål: kan jeg ændre navnet på objektbeholderen tabel/blob bruges til at læse fra Azure diagnosticering (WAD)?**  

A.  Nej, det er ikke muligt i øjeblikket, men er planlagt til en senere version.

**Spørgsmål: Hvad IP-adresser gøre OMS services brug? Hvordan sikrer jeg, at min firewall kun tillader trafik til OMS-tjenester?**  

A. Tjenesten Log Analytics er bygget oven på Azure og slutpunkterne modtage IP-adresser, der findes i den [Microsoft Azure Datacenter IP-intervaller](http://www.microsoft.com/download/details.aspx?id=41653).

Når tjenesten installationer er foretaget, ændre OMS tjenesterne faktisk IP-adresser. På DNS-navne for at tillade via din firewall er beskrevet på [Konfigurer proxy og firewall-indstillinger i Log Analytics](log-analytics-proxy-firewall.md).

**Spørgsmål: jeg bruge ExpressRoute til at oprette forbindelse til Azure. Anvender min Log Analytics-trafik ExpressRoute forbindelsen?**  

A. De forskellige typer ExpressRoute trafik er beskrevet i [ExpressRoute dokumentation](./expressroute/expressroute-faqs.md#supported-services).

Trafik til Log Analytics bruger det offentlige peering ExpressRoute kredsløb.

**Spørgsmål: er der en enkel og nem måde at flytte et eksisterende Log Analytics-arbejdsområde til et andet Log Analytics arbejdsområde/Azure-abonnement?**  Vi har flere kundens OMS arbejdsområder, som vi test og trialing i vores Azure-abonnement, og de flyttes til fremstilling, så vi vil gerne flytte dem til deres egen Azure/OMS abonnement.  

A. Den `Move-AzureRmResource` cmdlet, kan du flytte et Log Analytics-arbejdsområde, samt en konto med Automation fra ét Azure abonnement til en anden. Du kan finde yderligere oplysninger finder [Flyt AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Denne ændring kan også foretages i portalen Azure.

Du kan ikke flytte data fra én Log Analytics arbejdsområde til en anden, eller du kan ændre det område, Log Analytics-data er gemt i.

**Sp: Hvordan kan jeg føje OMS til SCOM?**

SV: opdatere til den nyeste opdateringspakke og importere management packs gør det muligt at oprette forbindelse SCOM til Log Analytics.

Bemærk, at SCOM forbindelsen til Log Analytics er kun tilgængelig for SCOM 2012 SP1 eller nyere.

**Sp: Hvordan kan jeg bekræfte, at en agent er kan kommunikere med Log Analytics?**

SV: for at sikre, at agenten kan kommunikere med OMS, gå til: I Kontrolpanel, sikkerhed og indstillinger for **Microsoft overvågning Agent**.

Se efter et grønt flueben under fanen **Azure Log Analytics (OMS)** . Et ikon for grønt flueben bekræfter, agenten er i stand til at kommunikere med OMS-tjenesten.

Ikon med en gul advarsel betyder, at agenten har problemer med kommunikation med OMS. En almindelig årsag er tjenesten Microsoft overvågning Agent er blevet standset og skal genstartes.

**Sp: Hvordan kan jeg standse en agent i at kommunikere med Log Analytics?**

SV: I SCOM, fjerne computeren fra listen OMS administrerede. Dette stopper al kommunikation via SCOM for denne agent. Til supportmedarbejdere tilsluttet OMS direkte, kan du forhindre dem i at kommunikere med OMS gennem: Kontrolpanel, sikkerhed og indstillinger for **Microsoft overvågning Agent**.
Fjern alle arbejdsområder, der er angivet under **Azure Log Analytics (OMS)**.

## <a name="agent-data"></a>Agent for data

**Spørgsmål: hvor meget data kan jeg sende gennem agenten til Log Analytics? Er der en maksimale mængde data hver kunde?**  
A. Den gratis plan angiver en daglig knop 500 MB i arbejdsområdet. Standard og premium planer har ingen grænse på mængden data, der overføres. Som en skybaseret tjeneste, Log Analytics i OMS udviklet til at automatisk skala op til håndtaget lydstyrken kommer fra en kunde – også selvom det er TB om dagen.

Log Analytics-agent er udviklet til at sikre, at det har en lille miljøet og indeholder nogle grundlæggende datakomprimering. En af vores kunder faktisk skrevet en blog på følgende test de udføres mod vores agent, og hvordan imponerede det var. Data lydstyrken varierer afhængigt af de løsninger, der gør det muligt for dine kunder. Du kan finde detaljerede oplysninger om data lydstyrken og se den fordeling af løsning under **brugen** af flisen OMS oversigt på siden.

Du kan få en [kunde blog](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) om lav miljøet af OMS agent kan finde flere oplysninger.

**Spørgsmål: hvor meget netværksbåndbredde bruges af Microsoft Management Agent (MMA), når du sender data til Log Analytics?**

A. Båndbredden er en funktion på mængden data, der sendes. Data komprimeres, som den er sendt via netværket.

**Spørgsmål: hvor meget data sendes per agent?**

A. Dette afhænger:

- de løsninger, du har aktiveret
- antallet af logfiler og tællere i ydeevne indsamles
- mængden data i logfiler

Det gratis priser niveau er en god måde at indbyggede flere servere og måler typisk data lydstyrken. Overordnede vises brugen på siden **Brug** af.
For computere, der er i stand til at køre WireData agent, kan du se, hvor meget data bliver sendt ved hjælp af følgende forespørgsel:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Næste trin

- [Komme i gang med Log analyser](log-analytics-get-started.md) for at få mere at vide mere om Log analyser og få op at køre i minutter.
