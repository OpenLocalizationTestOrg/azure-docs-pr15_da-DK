<properties
   pageTitle="Overvåge og reagere på sikkerhedsadvarsler i handlinger Management pakke sikkerhed og Overvåg løsning | Microsoft Azure"
   description="Dette dokument hjælper dig med at bruge truslen intelligence indstilling findes i OMS sikkerhed og overvågning til at overvåge og reagere på beskeder om sikkerhed."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Overvåge og reagere på sikkerhedsadvarsler i handlinger Management pakke sikkerhed og Overvåg løsning

Dette dokument hjælper dig med at bruge truslen intelligence indstilling findes i OMS sikkerhed og overvågning til at overvåge og reagere på beskeder om sikkerhed.

## <a name="what-is-oms"></a>Hvad er OMS?

Microsoft Operations Management pakke (OMS) er Microsofts skyen baseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen. Du kan finde flere oplysninger om OMS du læse artiklen [Handlinger Management-pakken](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Truslen intelligence

I et virksomhedsmiljø, hvor brugerne har bred adgang til netværket og bruge flere forskellige enheder til at oprette forbindelse til virksomhedens data, er det vigtigt, at du kan overvåge aktivt dine ressourcer og hurtigt at besvare sikkerhedshændelser. Dette er særlig vigtigt fra sikkerhed livscyklus perspektiv, fordi nogle cybersecurity trusler ikke kan optage giver besked eller mistænkelige aktiviteter, der kan blive identificeret ved traditionelle tekniske sikkerhedskontrol. 

Ved hjælp af indstillingen **Truslen Intelligence** tilgængelig i OMS sikkerhed og overvågning, kan IT-administratorer identificere trusler mod miljøet, for eksempel, identificere, om en bestemt computer er en del af en [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Computere kan være knuderne i et botnet, når hackere ulovligt går ind installere malware, der forbinder hemmeligt denne computer til kommandoen eller kontrolelement. Det kan også identificere potentielle trusler kommer fra udførelse kommunikationskanaler, som [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Bruge data, der kommer fra flere kilder i Microsoft for at oprette denne truslen intelligence, OMS sikkerhed og overvågning. OMS sikkerhed og overvågningsloggen kan udnytte disse data for at identificere potentielle trusler mod dit miljø.

Ruden truslen Intelligence består af tre overordnede indstillinger:
- Servere med udgående skadelig trafik
- Fundet trusler typer
- Truslen intelligence kort

> [AZURE.NOTE] Læs [Introduktion til handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-getting-started.md)for en oversigt over alle disse indstillinger.

### <a name="responding-to-security-alerts"></a>Besvare sikkerhedsadvarsler

Et af trinnene i en proces i [sikkerhed hændelsen svar](https://technet.microsoft.com/library/cc512623.aspx) er at identificere alvorlighed af sammensat systemer. I denne fase skal du udføre følgende opgaver:

- Vælge typen af angrebet
- Bestem punktet, angreb nulpunkt
- Find ud af formålet med angrebet. Var angreb dirigeres til at hente specifikke oplysninger i din organisation, eller det vilkårlige?
- Identificere de systemer, der er blevet kompromitteret
- Identificere de filer, der er åbnet og bestemme følsomheden for disse filer

Du kan udnytte **Truslen Intelligence** -oplysninger i OMS sikkerhed og Overvåg løsning med hjælp til disse opgaver. Følg nedenstående trin for at få adgang til denne **Truslen Intelligence** indstillinger:

1. Klik på **sikkerhed og Overvåg** felt i **Microsoft Operations Management Suite** primære dashboard.

    ![Sikkerhed og overvågning](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. I dashboardet til **sikkerhed og overvågning** , kan du kun se indstillingerne **Truslen Intelligence** i højre side, som vist nedenfor:

    ![Truslen intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Disse tre felter giver dig et overblik over de aktuelle trusler. I feltet **Server til udgående trafik skadelig** , du vil kunne identificere, om der er en hvilken som helst computer, som du overvåger (i eller uden for dit netværk), der bruges til at sende skadelig trafik til internettet. 

Feltet **fundne truslen typer** viser en oversigt over de trusler, der er aktuelle "i vilde", hvis du klikker på dette felt kan du se flere oplysninger om disse trusler som vist nedenfor:

![Fundet truslen typer](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Du kan udtrække kan finde flere oplysninger om hver truslen ved at klikke på den. Eksemplet herunder viser flere oplysninger om Botnet:

![flere oplysninger om en truslen](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Som beskrevet i begyndelsen af dette afsnit, kan disse oplysninger være meget nyttigt under en sag hændelsen svar. Det kan også være vigtige løbet sikkerhedsværktøj undersøgelsen, hvor du vil finde kilden til angrebet, hvilket system er blevet afsløret og tidslinjen. I denne rapport, du kan nemt identificere nogle vigtige oplysninger om angreb, f.eks.: kilden til angrebet, den lokale IP-adresse, der er blevet afsløret og den aktuelle sessionstilstanden for forbindelsen. 

**Truslen intelligence kort** hjælper dig med at identificere de aktuelle placeringer hele verden, der har skadelig trafik. Der er orange (indgående) og rød (udgående) pile i denne tilknytning, der identificerer Trafikretning, hvis du klikker på en af disse pile, vises den type af truslen og Trafikretning som vist nedenfor:

![truslen intel-kort](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] Du kan se en demonstration om, hvordan du bruge denne egenskab under en hændelse svar processen ved at se præsentationen [Mitigate datacenter trusler med Automatiseret undersøgelse ved hjælp af handlinger Management Suite](https://myignite.microsoft.com/videos/5000) leveret på Microsoft Ignite.

## <a name="see-also"></a>Se også

I dette dokument, du har lært hvordan du bruger indstillingen **Truslen Intelligence** i OMS sikkerhed og Overvåg løsning for at besvare sikkerhedsadvarsler. Hvis du vil vide mere om OMS sikkerhed skal du se følgende artikler:

- [Oversigt over operationer Management pakke (OMS)](operations-management-suite-overview.md)
- [Introduktion til handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-getting-started.md)
- [Overvåge ressourcer i handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-monitoring-resources.md)
