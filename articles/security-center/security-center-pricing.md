<properties
   pageTitle="Sikkerhedscenter priser | Microsoft Azure"
   description="I denne artikel indeholder oplysninger om priser for Azure Sikkerhedscenter."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/12/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-pricing"></a>Azure Sikkerhedscenter priser

Azure Sikkerhedscenter hjælper dig med at forhindre, registrere og besvare trusler med øget indsigt i og kontrol over sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

## <a name="pricing-tiers"></a>Priser niveauer

Sikkerhedscenter fås i to niveauer:

- Det **gratis niveau** er automatisk aktiveret på alle Azure abonnementer. Det gratis niveau indeholder indsigt i tilstanden sikkerhed med Azure ressourcer, grundlæggende sikkerhedspolitik, anbefalinger til sikkerhed og integration med sikkerhedsprodukter og tjenester fra partnere.
- **Standard niveau** tilføjer avancerede truslen registrering af funktioner, herunder truslen intelligence, funktionsmæssige analyse, anomali registrering, sikkerhedshændelser og truslen vurderingsrapporter. En **90 dages gratis prøveversion** er tilgængelig for det Standard niveau.

Du kan finde flere oplysninger, se Sikkerhedscenter [priser side](https://azure.microsoft.com/pricing/details/security-center/).

> [AZURE.NOTE] Sikkerhedscenter bruger Azure-lager til at gemme sikkerhedsdata, der er dannet ud fra dine beskyttet noder. Omkostninger, der er knyttet til denne lagerplads medtages ikke i tjenesten pris og betaler separat til almindelige [Azure lagerplads satser](https://azure.microsoft.com/pricing/details/storage/blobs/). Lagerplads gebyrer gælder selv under prøveversionen.

## <a name="try-standard-free-for-90-days"></a>Prøv Standard gratis 90 dage

En 90 dages gratis prøveversion er tilgængelig for det Standard niveau. For at få den gratis prøveversion af Standard-niveauet skal du vælge feltet **politik** på bladet **Sikkerhedscenter** . Markere det abonnement, du vil opgradere til Standard. Vælg **priser niveau**på bladet **sikkerhedspolitik** . Vælg **Standard – gratis prøveversion**bladet **Vælg din priser niveau** .

![Gratis prøveversion][1]

I slutningen af 90 dage starter skal du vælge at fortsætte med at bruge tjenesten, vi automatisk betaler for at få hjælp.

## <a name="upgrade-to-standard"></a>Opgradere til Standard

Opgradere til det Standard niveau tilføje avancerede truslen registrering. For at få det Standard niveau skal du vælge feltet **politik** på bladet **Sikkerhedscenter** . Markere det abonnement, du vil opgradere til Standard. Vælg **priser niveau**på bladet **sikkerhedspolitik** . Vælg **Standard**bladet **Vælg din priser niveau** .

![Standard niveau][2]

## <a name="why-upgrade-to-standard"></a>Hvorfor opgradere til Standard?

Det Standard niveau i Sikkerhedscenter indeholder alle funktioner for gratis niveau samt avancerede truslen registrering. Avancerede truslen registrering hjælper med at identificere aktive trusler målretning af ressourcerne Azure og giver dig viden, der er behov for at svare hurtigt.

Sikkerhedscenter anvender Avanceret sikkerhed analytics, hvor langt overgår signatur-baserede fremgangsmåder. Gennembrud i big data og machine learning-teknologier opgraderede for at evaluere begivenheder på tværs af hele skyen strukturen – registrering af trusler, der ville være umuligt at identificere ved hjælp af manuel fremgangsmåder og forudsigelser udviklingen af angreb.

Sikkerhed analytics, der følger med det almindelige niveau er:

- **Truslen intelligence** - søger efter kendte dårlige aktører ved hjælp af globale truslen intelligence fra Microsoft-produkter og tjenester, Microsoft digitale forbrydelser enhed, Microsoft Security Response Center og eksterne kilder
- **Funktionsmæssige analyse** – gælder kendte mønstre for at finde skadelig opførsel
- **Anomali registrering** - bruger statistiske profiler til at opbygge en historiske oprindelig plan. Det giver besked på afvigelse fra eksisterende grundlinjer, der er i overensstemmelse med en mulig angreb

I nedenstående **sikkerhedsadvarsler** blade, har Sikkerhedscenter registreret en sikkerhed **hændelse**. En sikkerhed er en sammenlægning af alle beskeder for en ressource, som Juster med kill kæde mønstre. Valg af sikkerhed hændelsen vises flere oplysninger om hændelsen og viser de relaterede påmindelser. Hvis du vælger en besked indeholder flere oplysninger om den aktuelle forekomst.

![Sikkerhedshændelse][3]

**Netværkskommunikation** beskeden nedenfor indeholder oplysninger om påmindelsen. Oplysninger omfatter dens fulde beskrivelse, dens alvor, den aktuelle tilstand (som i dette tilfælde er lukket, hvilket betyder, at brugeren har udført handling til at afvise den), angrebet ressource og afhjælpning trin. Der er også en liste over links til Microsoft truslen Intelligence rapporter. Disse rapporter kan bruges til sikkerhed afhjælpning og defensive formål.

![Oplysninger om sikkerhed påmindelser][4]

## <a name="enable-data-collection"></a>Aktivere dataindsamling

Hvis du vil aktivere virtuelt funktionsmæssige analytics, skal være tændt dataindsamling. Du kan være nødvendigt at aktivere dataindsamling, når du først få adgang til Sikkerhedscenter, eller når du opretter en sikkerhedspolitik.

Hvis du vil validere, dataindsamling er aktiveret, skal du vælge feltet **politik** . Bladet **sikkerhedspolitik** åbner listen over dine Azure abonnementer. Vælg et abonnement. Hvis **dataindsamling** er slået fra, ændre den til til og gemme ændringen. Se [aktivere dataindsamling i Azure Sikkerhedscenter](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Næste trin

- I dette dokument, blev du introduceret til priser for Sikkerhedscenter. Yderligere prisoplysninger finde Sikkerhedscenter [priser side](https://azure.microsoft.com/pricing/details/security-center/).
- Hvis du vil vide mere om Security Center registrering af avancerede funktioner, skal du se [Azure Sikkerhedscenter registrering af funktioner](security-center-detection-capabilities.md).
- Hvis du har spørgsmål om brug af Sikkerhedscenter, skal du se [Azure Security Center ofte stillede spørgsmål](security-center-faq.md).
- Hvis du stadig har spørgsmål om brug af Sikkerhedscenter eller Azure, skal du besøge [Azure-fora](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png
