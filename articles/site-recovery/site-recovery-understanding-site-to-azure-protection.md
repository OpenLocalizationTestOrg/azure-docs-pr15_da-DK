<properties
    pageTitle="Hyper-V replikering med Azure gendannelse af websteder | Microsoft Azure"
    description="Brug denne artikel til at forstå de tekniske begreber, hvordan du kan installere, konfigurere og administrere Azure gendannelse af websteder."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Hyper-V replikering med Azure gendannelse af websteder

I denne artikel beskrives de tekniske begreber, der kan hjælpe dig med at konfigurere og administrere et Hyper-V-websted eller et System Center Virtual Machine Manager (VMM)-websted til Azure beskyttelse ved hjælp af Azure gendannelse af websteder korrekt.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Konfiguration af kilde-miljø til replikering mellem lokale og Azure

Som en del af konfigurationen af nedbrud mellem lokale og Azure, skal du sørge for at hente og installere Azure websted gendannelse udbyder på VMM-serveren. Installer Azure betroet Services bruger på hver Hyper-V-vært.

![VMM websted installation af replikering mellem lokale og Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Konfiguration af kilde-miljø i en Hyper-V administrerede infrastruktur minder om konfiguration af kilde-miljø i en VMM administrerede infrastruktur. Den eneste forskel er, den udbyder og agent er installeret på værten Hyper-V sig selv. Udbyderen, der er installeret på serveren VMM i VMM-miljøet, og agenten er installeret på Hyper-V hosts (i tilfælde af replikering til Azure).

## <a name="workflows"></a>Arbejdsprocesser

### <a name="enable-protection"></a>Aktivér beskyttelse
Når du beskytter en virtuel maskine fra Azure-portalen eller lokalt, starter et gendannelse af websteder job med navnet **Aktiver beskyttelse** . Du kan overvåge den under fanen **job** .

!["Aktiver beskyttelse" tingene på listen over job](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

**Aktivér beskyttelse** jobbet kontrollerer, om forudsætningerne før aktivering af [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metoden. Denne metode opretter Azure-replikering ved hjælp af input, der er konfigureret under beskyttelse.

**Aktivér beskyttelse** jobbet starter den første replikering fra det lokale ved aktivering af [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metoden. Denne metode sender den virtuelle maskine virtuelle disk til Azure.

![Oplysninger om jobbet "Aktivér beskyttelse"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Færdiggøre beskyttelse under den virtuelle maskine
Et [Hyper-V VM snapshot](https://technet.microsoft.com/library/dd560637.aspx) hentes, når første replikering udløses. Virtuelle harddiske er behandlede én ad gangen, indtil alle diske er overført til Azure. Det tager normalt et stykke tid at afslutte, baseret på diskstørrelse og båndbredden. Se, [hvordan du administrere lokalt til Azure beskyttelse netværk båndbredden](https://support.microsoft.com/kb/3056159)for at optimere din brug af netværket.

Når den første replikering er fuldført, konfigurerer **Færdiggør beskyttelse under den virtuelle maskine** jobbet indstillingerne for netværk og efter gentagelse. Mens første replikering er i gang:

- Alle ændringerne er diskene registreres. 
- Ekstra plads er brugt til snapshot og Hyper-V replika Log (HRL) filer.

Afsluttet første replikering slettes Hyper-V VM snapshot. Denne sletning resulterer i fletning af dataændringer efter første replikering til den overordnede disk.

!["Færdiggøre beskyttelse under den virtuelle maskine" tingene på listen over job](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Delta gentagelse
Hyper-V replika gentagelse sporingen, som er en del af Hyper-V replika gentagelse program, spor ændres til en virtuel harddisk som Hyper-V replika logfiler (*.hrl). HRL filer er i samme mappe som de tilknyttede diske.

Hver disk, der er konfigureret til replikering har en tilknyttet HRL-fil. Denne log sendes til kundens konto for lagerplads, når første replikering er fuldført. Når en logfil er undervejs til Azure, registreres ændringerne i primært i en anden logfilen i samme mappe.

Under første replikering eller delta gentagelse, kan du overvåge VM gentagelse tilstand i visningen VM som nævnt i [overvåge gentagelse tilstand for virtuel maskine](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Ny synkronisering
En virtuel maskine er markeret på ny synkronisering, når både delta gentagelse mislykkes og fulde første replikering er dyrt med hensyn til netværksbåndbredde eller et klokkeslæt. Når HRL filstørrelse bunker op til 50% af den samlede diskstørrelse, markeres den virtuelle maskine for eksempel på ny synkronisering. Ny synkronisering minimeres mængden data, der sendes via netværket ved computing kontrolsummer kilde- og destinationswebsteder virtuelt diskene og sende kun forskellen.

Når ny synkronisering er fuldført, genoptage normal delta gentagelse. Du kan genoptage ny synkronisering, hvis der opstår en netværk afbrydelse eller en anden afbrydelse.

Automatisk planlagt ny synkronisering er som standard konfigureret til at ske uden for arbejdstimer. Hvis den virtuelle maskine skal være synkroniseret manuelt, skal du markere den virtuelle maskine portalen og klikke på **Synkroniser**.

![Ny manuel synkronisering](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Ny synkronisering bruger en fast Bloker segmenteret algoritme, hvor kilde- og destinationswebsteder filer er opdelt i fast stykker. Kontrolsummer for hver del genereres og derefter sammenlignes for at afgøre, som forhindrer fra kilden skal anvendes til destinationen.

### <a name="retry-logic"></a>Retry-logik
Der er indbygget forsøg for gentagelse fejl. Denne logik kan klassificeres i to kategorier:

| Kategori                  | Scenarier                                    |
|---------------------------|----------------------------------------------|
| Uoprettelig fejl     | Ingen nye forsøg forsøges. Virtuelt gentagelse status er **kritisk**, og administratoren brugerinput er påkrævet. Som eksempler kan nævnes: <ul><li>Brudt Virtuelle kæde</li><li>Ugyldig tilstand for virtuelt replika</li><li>Netværk godkendelsesfejl</li><li>Godkendelse fejl</li><li>Virtuel maskine, der ikke findes, hvis det er en enkeltstående Hyper-V server</li></ul>|
| Genoprettelig fejl         | Nye forsøg opstå hver gentagelse interval, ved hjælp af en eksponentiel back-fra, der øger intervallet for gentagelse fra starten af det første forsøg (1, 2, 4, 8, 10 minutter). Hvis fejlen opstår igen, skal du prøve hver 30 minutter. Som eksempler kan nævnes: <ul><li>Netværksfejl</li><li>Lav diskplads.</li><li>Manglende hukommelse</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Hyper-V virtuelt beskyttelse og gendannelse livscyklus

![Hyper-V virtuelt beskyttelse og gendannelse livscyklus](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Andre referencer

- [Overvåge og fejlfinding i forbindelse med beskyttelse for VMware, VMM, Hyper-V og fysiske websteder](./site-recovery-monitoring-and-troubleshooting.md)
- [At kontakte for Microsoft Support](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Almindelige fejl i Azure gendannelse af websteder og deres løsninger](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
