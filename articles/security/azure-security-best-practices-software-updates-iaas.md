<properties
   pageTitle="Bedste fremgangsmåder for softwareopdateringer på Microsoft Azure IaaS | Microsoft Azure"
   description="Artikel indeholder en samling af bedste fremgangsmåder til softwareopdateringer i et Microsoft Azure IaaS miljø.  Det er beregnet til IT-fagfolk og sikkerhed analytikere der håndtere ændre kontrolelementet, software opdatering og aktiv management dagligt, herunder dem, der er ansvarlig for organisationens sikkerhed og overholdelse anstrengelser."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Bedste fremgangsmåder til softwareopdateringer på Microsoft Azure IaaS

Før dykker i enhver type diskussion på bedste praksis for en Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) miljø, er det vigtigt at forstå, hvad scenarierne er, får du administrere softwareopdateringer og ansvarsområder. Diagrammet nedenfor kan hjælpe dig med at forstå disse grænser:

![Cloud-modeller og responsabilities](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

Kolonnen længst til venstre viser syv ansvarsområder (defineret i de følgende afsnit), bør overveje organisationer, som alle kan bidrage til sikkerhed og beskyttelse af personlige oplysninger i et miljø.
 
Data klassifikation og pålidelighed og klienten og slutpunkt beskyttelse er de opgaver, der er udelukkende i domænet for kunder, og fysiske, Host og netværk ansvarsområder er i domænet for skyen tjenesteudbydere PaaS og SaaS-modeller. 

De resterende ansvarsområder deles mellem kunder og tjenesteudbydere i skyen. Nogle ansvarsområder kræver CSP og kunde at administrere og administrere ansvar sammen, herunder overvågning af deres domæner. Overvej identitet og få adgang til administration, når du bruger Azure Active Directory Services, f.eks. konfiguration af tjenester som Multi-Factor authentication er op til kunden, men at sikre effektive funktionalitet er ansvarlig for Microsoft Azure.

> [AZURE.NOTE] Du kan finde flere oplysninger om delte ansvarsområder i skyen, læse [Delte ansvarsområder for Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Disse samme principper gælder i et hybridt scenarie, hvor din virksomhed bruger Azure IaaS FOS, kommunikerer med lokale ressourcer, som vist i diagrammet nedenfor.

![Typisk hybrid scenarie med Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Indledende vurdering

Selvom din virksomhed allerede bruger et update-system, og du allerede har software update politikker på plads, er det er vigtigt at ofte besøger forrige politik bedømmelser og opdaterer dem baseret på dine aktuelle krav. Det betyder, at du skal have kendskab til den aktuelle tilstand for ressourcerne i din virksomhed. Når denne tilstand, skal du kende:

-   De fysiske og virtuelle computere i virksomheden.

-   Operativsystemer og versioner, der kører på hver af disse fysiske og virtuelle computere.

-   Softwareopdateringer, der aktuelt er installeret på alle computere (service pack-versioner, softwareopdateringer og andre ændringer).

-   Funktionen udfører alle computere i virksomheden.

-   Programmer og programmer, der kører på alle computere.

-   Ejerskab og kontakte oplysninger for hver enkelt computer.

-   Aktiverne, der findes i dit miljø og deres relative værdi til at bestemme, hvilke områder skal mest opmærksomhed og beskyttelse.

-   Kendte sikkerhedsproblemer og processer har virksomheden i stedet for identificerende nye sikkerhedsproblemer eller ændringer i sikkerhedsniveauet.

-   Modforholdsregler, der er implementeret for at sikre dit miljø.

Bør du regelmæssigt opdatere oplysningerne, og det skal være tilgængelige for disse involveret i din software opdateringsprocessen administration.

## <a name="establish-a-baseline"></a>Oprette en oprindelig plan

En vigtig del af processen til administration af software update oprettelse af indledende standard installationer af versioner af operativsystemer, programmer og hardware til computere i virksomheden; Disse kaldes grundlinjer. En oprindelig plan er konfigurationen af et produkt eller den systemtype, der er oprettet på et bestemt sted i gang. Et program eller operativsystem oprindelig plan, for eksempel giver mulighed for at genopbygge en computer eller tjeneste til en bestemt tilstand.

Oprindelige planer giver grundlag for at finde og løse eventuelle problemer og forenkle software management opdateringsprocessen, både ved at reducere antallet af softwareopdateringer, skal du installere i virksomheden og ved at øge din mulighed for at kontrollere, at.

Når du har udført indledende audit af din virksomhed, skal du bruge de oplysninger, der er hentet fra revision til at definere en funktionsdygtige oprindelig plan for IT-komponenter i dit produktionsmiljø. Et antal grundlinjer kan være obligatorisk, afhængigt af de forskellige typer af hardware og software, der er implementeret i fremstilling.

For eksempel kræver nogle servere en softwareopdatering til at forhindre dem i hænger, når de skriver lukning, når du kører Windows Server 2012. En oprindelig plan for disse servere bør omfatte denne softwareopdatering.

I store organisationer er det ofte nyttigt at opdele computere i virksomheden i aktivkategorier og holde hver kategori på en standard oprindelig plan ved hjælp af de samme versioner af software og softwareopdateringer. Derefter kan du bruge disse aktivkategorier i prioritere en software update fordeling.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>Abonnere på de nødvendige software update meddelelse tjenester

Når du udfører en indledende kontrol af softwaren i brug i virksomheden, skal du finde den bedste metode til at modtage meddelelser om nye softwareopdateringer for hver softwareprodukt og version. Afhængigt af SOFTWAREPRODUKTET muligvis bedste underretningsmetode e-mail-meddelelser, websteder eller computer publikationer.

For eksempel Microsoft sikkerhed svar Center (MSRC) besvarer alle sikkerhedsrelaterede problemstillinger om Microsoft-produkter og giver tjenesten Microsoft sikkerhed fra, en gratis e-mail-meddelelse nyligt identificerede sikkerhedsrisici og softwareopdateringer, der er udgivet for at løse disse svagheder. Du kan abonnere på denne tjeneste på http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Overvejelser i forbindelse med software update

Når du udfører en indledende kontrol af softwaren i brug i virksomheden, skal du se kravene til at konfigurere din software update management system, som afhænger af det software update management system, du bruger. Læser [Planlægning efter softwareopdateringer i Konfigurationsstyring](https://technet.microsoft.com/library/gg712696)til WSUS læse [Bedste fremgangsmåder med Windows Server Update Services](https://technet.microsoft.com/library/Cc708536), System Center.

Der er dog nogle generelle overvejelser og bedste fremgangsmåder, du kan anvende uanset den løsning, du bruger som vist i afsnittene, der følger efter.

### <a name="setting-up-the-environment"></a>Konfigurere miljøet

Overvej følgende fremgangsmåder, når du planlægger at konfigurere softwaren opdatere management-miljø:

-   **Oprette fremstilling software opdatering af websteder, der er baseret på stabil kriterier**: Generelt, bruge stabil kriterier til at oprette samlinger for din software opdaterer lager og fordeling hjælper med at forenkle alle trin i processen til administration af software update. Kriteriet stabil kan medtage installerede klientversionen af operativsystem og service pack-niveau, system rollen eller target organisation.

-   **Oprette foreløbig fremstilling af websteder, der indeholder reference computere**: samlingen foreløbig fremstilling bør omfatte repræsentant konfigurationer af operativsystemversioner, linje med business software og andre programmer, der kører i din virksomhed.

Du skal også overveje, hvor software update server skal placeres, hvis det bliver i Azure IaaS infrastruktur i skyen, eller hvis det bliver i det lokale miljø. Dette er en vigtig beslutning, fordi du skal bruge til at evaluere mængden trafik mellem lokale ressourcer og Azure infrastruktur. Læs [forbinde et lokalt netværk til et Microsoft Azure virtuelle netværk](https://technet.microsoft.com/library/Dn786406.aspx) yderligere oplysninger om hvordan du knytter infrastrukturen i det lokale miljø til Azure.

Indstillingerne for design, der bestemmer, hvor serveren opdatering vil være placeret varierer også afhængigt af den aktuelle infrastruktur og software update systemet, som du aktuelt bruger. Læs [Installere Windows Server Update Services i din organisation](https://technet.microsoft.com/library/hh852340.aspx) til WSUS og til System Center Configuration Manager læse [planlægning for websteder og hierarkier i Konfigurationsstyring](https://technet.microsoft.com/library/Gg712681.aspx).

### <a name="backup"></a>Sikkerhedskopi

Regelmæssige sikkerhedskopier er vigtige ikke kun for software update management platformen selve men også til servere, der opdateres. Kræver, at organisationer, der har en [ændre proces til styring af](https://technet.microsoft.com/library/cc543216.aspx) sted IT for at justere årsager, hvorfor serveren skal opdateres, de anslåede nedetid og mulige virkning. For at sikre, at du har en rollback konfiguration på plads i tilfælde af en opdatering mislykkes, Sørg for at sikkerhedskopiere systemet regelmæssigt.

Nogle indstillinger for sikkerhedskopiering for Azure IaaS omfatter:

-   [Azure IaaS arbejdsbelastningen beskyttelse ved hjælp af Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Sikkerhedskopiere Azure virtuelle maskiner](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Overvågning

Du skal køre almindelige rapporter til at overvåge antallet af manglende eller installeret opdateringer eller opdateringer med ufuldstændig status for hver softwareopdatering, der er godkendt. På samme måde, rapporterer til softwareopdateringer, der ikke er endnu tilladelse kan lette nemmere installation beslutninger.

Du skal også overveje følgende opgaver:

-   Udføre en kontrol af gældende og installerede sikkerhedsopdateringer til alle computere i virksomheden.

-   Godkend og installere opdateringerne til de relevante computere.

-   Spore lagerbeholdningen, og Opdater installationsstatus og fremdrift for alle computerne i virksomheden.

Desuden til generelle overvejelser, der er beskrevet i denne artikel, du skal også overveje hvert produkt er bedste øve, for eksempel: Hvis du har en VM i Azure med SQL Server, skal du kontrollere, at du følger software opdateringer anbefaling for det pågældende produkt.

## <a name="next-steps"></a>Næste trin

Brug retningslinjer, der er beskrevet i denne artikel for at hjælpe dig med at afgøre de bedste indstillinger for softwareopdateringer til virtuelle maskiner med Azure IaaS. Der er mange ligheder mellem software update bedste fremgangsmåder i en traditionel datacenter kontra Azure IaaS, derfor det anbefales, at du evaluerer din aktuelle software update politikker for at medtage Azure FOS og medtage de relevante bedste fremgangsmåder fra denne artikel i din overordnede software opdateringsprocessen.
