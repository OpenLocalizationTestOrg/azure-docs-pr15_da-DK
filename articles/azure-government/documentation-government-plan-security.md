<properties
    pageTitle="Azure Government Services | Microsoft Azure"
    description="Giver og oversigt over de tilgængelige tjenester i Azure Government"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc" />


#  <a name="security"></a>Sikkerhed

##  <a name="principles-for-securing-customer-data-in-azure-government"></a>Principper for sikring af kundedata i Azure Government

Azure Government tilbyder en række funktioner og tjenester, du kan bruge til at opbygge skyen løsninger at opfylde dine behov omfattet/kontrolleres data. En kompatibel kundeløsning er ikke noget mere end en effektiv gennemførelse af out box Azure Government funktioner, forbundet til en vane at dækkende data sikkerhed.

Når du er vært for en løsning i Azure Government, håndterer Microsoft mange af disse krav på niveauet for skyen infrastruktur.

I følgende diagram vises Azure forsvarslinjer i dybde-modellen. Microsoft yder eksempelvis grundlæggende skyinfrastruktur DDOS, sammen med kunde-funktioner som sikkerhedsfunktioner for kundespecifikt program DDOS skal.

![alternativ tekst](./media/azure-government-Defenseindepth.png)

Denne side beskrives de fundamentale principper til sikring af dine tjenester og programmer, giver vejledning og bedste fremgangsmåder til, hvordan du anvende disse principper Det vil sige, hvordan kunder bør gøre smart brug af Azure Government til at opfylde forpligtelser og ansvarsområder, der kræves til en løsning, der håndterer ITAR oplysninger.

 Overordnet principper til sikring af kundedata er:

- Beskytte data ved hjælp af kryptering
- Administrere hemmeligheder
- Isolationsniveauet til at begrænse adgang til data

###  <a name="protecting-customer-data-using-encryption"></a>Beskyttelse af kundedata ved hjælp af kryptering

Mindske risikoen og møde lovmæssige forpligtelser kører bil stigende fokus og vigtigheden af kryptering af data. Bruge en effektiv kryptering implementering til at forbedre aktuelle netværk og programmet sikkerhedsforanstaltninger – og formindske den samlede risiko i dit miljø til skyen.

#### <a name="encryption-at-rest"></a>Kryptering på resten
Kryptering af data på resten gælder for beskyttelse af kunde indhold, der ligger i lagerplads på disken. Der er flere måder at dette sker:

#### <a name="storage-service-encryption"></a>Lagerplads Service kryptering

Azure-lager Service kryptering er aktiveret på konto lagerpladsen, hvilket resulterer i Bloker BLOB og siden BLOB som krypteres automatisk, når skrevet til Azure-lager. Når du læser data fra Azure-lager, dekrypteres af tjenesten lagerplads før den returneres. Brug dette til at sikre dine data uden at ændre eller tilføje kode i alle programmer.

#### <a name="client-side-encryption"></a>Klientsiden kryptering
Klientsiden kryptering er indbygget i Java og .NET lagerplads klientbiblioteker, som kan udnytte Azure-tasten samling API'er, hvilket gør det nemt at implementere. Brug Azure-tasten samling til at få adgang til hemmeligheder i Azure-tasten samling til bestemte personer ved hjælp af Azure Active Directory.

#### <a name="encryption-in-transit"></a>Kryptering undervejs

Den grundlæggende kryptering tilgængelig for forbindelse til Azure Government understøtter Transport niveau sikkerhed (TLS) 1.2-protokollen og x.509-certifikater. Forbundsrepublikken oplysninger behandling af FIPS (Standard) 140-2 niveau 1 cryptographic algoritmer bruges også til infrastruktur netværksforbindelser mellem Azure Government datacentre.  Windows Server 2012 R2 og Windows 8-plus FOS og Azure filshares kan bruge små og mellemstore virksomheder 3.0 til kryptering mellem VM og det pågældende filshare. Bruge klientsiden kryptering til at kryptere dataene, inden de overføres til lageret i en klientprogrammet, og du kan dekryptere dataene efter det overføres tør for lagerplads.

#### <a name="best-practices-for-encryption"></a>Bedste fremgangsmåder til kryptering

- IaaS FOS: Bruge Azure Disk kryptering. Slå lagerplads Service kryptering til at kryptere de Virtuelle filer, der bruges til at sikkerhedskopiere disketterne i Azure-lager, men dette krypterer kun nyligt skrevne data. Det betyder, at hvis du opretter en VM og derefter aktivere lagerplads Service kryptering på kontoen lagerplads, der indeholder filen Virtuelle, kun ændringerne, krypteres, ikke den oprindelige Virtuelle fil.
- Klientsiden kryptering: Dette er den mest sikre metode til at kryptere dine data, fordi den krypterer før overførsel og krypterer data på resten. Det kræver dog, at du føjer kode til programmerne ved hjælp af lagerplads, som du ikke måske gøre. I disse tilfælde kan du bruge HTTPs til dine data i overførsel og lagerplads Service kryptering til at kryptere dataene på resten. Klientsiden kryptering også flere belastning på klienten – du har konto til denne i dine planer, skalerbarhed, især hvis du kryptering og overførsel af en stor mængde data.

###  <a name="protecting-customer-data-by-managing-secrets"></a>Beskyttelse af kundedata ved at administrere hemmeligheder

Sikker key management er vigtigt for beskyttelse af data i skyen. Kunder skal bestræbe dig på at forenkle key management og stadig har kontrollen over nøgler, der bruges af skyen programmer og tjenester til at kryptere data.

#### <a name="best-practices-for-managing-secrets"></a>Bedste fremgangsmåder for administration af hemmeligheder

- Brug tasten samling af legitimationsoplysninger til at minimere risikoen for at få vist via faste konfigurationsfiler, scripts, eller i kildekode hemmeligheder. Azure-tasten samling krypterer nøgler (såsom kryptering taster til Azure Disk kryptering) og hemmeligheder (såsom adgangskoder), ved at gemme dem i FIPS 140-2 niveau 2 valideret hardware sikkerhed moduler (HSMs). Du kan importere eller oprette nøgler i disse HSMs for større sikkerhed.
- Programkode og skabeloner bør kun indeholde URI referencer til hemmeligheder (hvilket betyder, at de faktiske hemmeligheder ikke er i kode, konfiguration eller kildekode typer lagre). Dette forhindrer vigtige phishing-angreb på interne og eksterne repos som høst-BOT'er i GitHub.
- Anvende stærke RBAC objekter inden for nøgle samling. Hvis der er tillid til operatoren forlader virksomheden eller overførsler til en ny gruppe i organisationen, skal de forhindres i at få adgang til hemmeligheder.

Du kan finde flere oplysninger <a href="https://azure.microsoft.com/documentation/services/key-vault">Azure-tasten samling offentlige dokumentation.</a>

###  <a name="isolation-to-restrict-data-access"></a>Isolationsniveauet til at begrænse adgang til Data

Isolationsniveauet handler om brug af grænser, segmentering og beholdere til at begrænse adgang til data til kun godkendte brugere, tjenester og -programmer. Adskillelsen mellem lejere er for eksempel en væsentlige sikkerhed metode til multiprofiler skyen platforme som Microsoft Azure. Logiske isolationsniveauet hjælper med at forhindre, at én lejer forstyrrer datahandlinger af enhver anden lejer.

#### <a name="environment-isolation"></a>Miljø isolationsniveauet
Azure Government miljøet er en fysisk forekomst, der er adskilt fra resten af Microsofts netværk. Dette opnås gennem en række fysiske og logiske kontrolelementer, der omfatter følgende:

- Sikring af de fysiske grænser ved hjælp af Biometriske enheder og kameraer.
- Brug af bestemte legitimationsoplysninger og multifaktor-godkendelse af Microsoft medarbejdere, der kræver logiske adgang til produktionsmiljøet.
- Alle service infrastruktur til Azure Government er placeret i USA.

#### <a name="per-customer-isolation"></a>Hver kunde isolationsniveauet
Azure implementerer netværksadgangskontrol og opdeling gennem VLAN isolationsniveauet, ACLs, indlæse balancere og IP-filtre

Kunder kan yderligere isolere deres ressourcer på tværs af abonnementer, grupper, virtuelle netværk og undernet.

## <a name="screening"></a>Undersøgelsen

Senest er offentliggjort FedRAMP høj og afdeling af Forsvarslinjer (DoD) virkning niveau 4 godkendelse. Dette er opløftet værktøjslinjen sikkerhed og overholdelse på tværs af Azure Government-miljø.

Vi nu screening alle vores operatorer på nationale kontoret kontrollere med lovgivning og kredit (NACLC), som defineret i sektionen 5.6.2.2 af DoD Cloud Computing sikkerhed krav vejledning (SRG):

>[AZURE.NOTE] Mindste baggrund undersøgelsen kræves til CSP personale har adgang til niveau 4 og 5 oplysninger, der er baseret på en "ikke-kritisk-følsomme" (f.eks., Dod's ADP-2) er en nationale kontoret Kontakt lovvalg og kredit (NACLC) (for "ikke-kritisk-følsomme" entreprenører), eller en moderat risikoen baggrund undersøgelse (MBI) til en "mellem risiko" placering angive.

Den følgende tabel opsummerer vores aktuelle undersøgelsen til Azure Government operatorer:

Azure Gov screeninger og baggrund Kontroller | Beskrivelse|
---|---|
USA medborgerskab |Kontrol af US medborgerskab.
Microsoft cloud baggrund afkrydsningsfelt (hver to år)|CPR-nummer Søg, strafferetlige historik afkrydsningsfelt, Office af fremmed Aktiver Control liste (OFAC), liste over Bureau af branche og sikkerhed (BIS), Office for Forsvarslinjer handel kontrolelementer Debarred personer liste.
Nationale kontoret Kontakt lovvalg og kredit (NACLC) (hvert femte år) | Tilføjer fingeraftryk baggrund kontrol i forhold til FBI databaser. Gå til det<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/"> Websted i Office personale Management</a>kan finde flere oplysninger. | 
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS">Strafferetlige retfærdighed Information Services (CJIS)</a> | CJIS er en tilstand, lokale og FBI government screening hvilke processer fingeraftryk poster og valideret strafferetlige oversigter på funktionsdygtige personale, der kan angives adgang til kritiske strafferetlige retfærdighed oplysninger (CJI) data.  Hver enkelt tilstand betyder deres egne baggrund Kontroller og efterfølgende godkendelse af alle medarbejdere med potentielle adgang til CJI.|

Til Azure handlinger personale gælder følgende principper i access:

- Opgaver er klart definerede, med separat ansvarsområder for anmoder om, godkende og implementere ændringer.
- Access er via defineret grænseflader, der har bestemte funktioner.
- Access er just in time (JIT), og der gives kun på grundlag af per hændelse eller til en bestemt vedligeholdelse og altid i en begrænset varighed.
- Access er baseret på regler, med defineret roller, der tildeles kun de tilladelser, der kræves til fejlfinding.

Undersøgelsen standarder omfatter validering af US medborgerskab af alle Microsoft support og drift personale før har adgang til Azure Government hostet systemer. Supportpersonale, der har brug for til at overføre data Brug sikre funktionerne i Azure Government. Sikker dataoverførsel kræver en separat række legitimationsoplysninger til at få adgang. For eksempel for at få adgang til systemet metadata handlinger personalet anvende bestemte webbaseret interne administrationsværktøjer, skrivebeskyttet API'er og JIT udvidelse.

## <a name="next-steps"></a>Næste trin

Til supplerende oplysninger og opdateringer skal du abonnere på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
