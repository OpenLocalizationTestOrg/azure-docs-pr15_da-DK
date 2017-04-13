
<properties 
    pageTitle="Indstillinger for overførsel af Azure RemoteApp | Microsoft Azure" 
    description="Få mere at vide om indstillinger for overførsel af Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Indstillinger for overførsel af Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Hvis du har holdt op med at bruge Azure RemoteApp på grund af [lukning af meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) , eller fordi du er færdig med din evaluering, du vil flytte fra Azure RemoteApp til en anden app-tjeneste. Der er to forskellige metoder for at overføre: en præsentation administrerede (ofte kaldet infrastruktur som en tjeneste [IaaS]) installation eller en fuldt administreret (kaldes ofte Platform som en tjeneste) og Software, som en tjeneste [PaaS/SaaS] med tilbud. 

Selvbetjening IaaS er en gør det selv installation, der administreres, drevet og ejes af dig direkte installeret på virtuelle maskiner (VM'er) eller fysisk systemer. I anden ende en fuldt administreret PaaS/SaaS tilbyder er mere som Azure RemoteApp - en partner giver et service lag oven på en remoting løsning, der håndterer drift og vedligeholdelse, mens du, som kunden, gøre nogle billede og app management.

Læs videre for at få flere oplysninger, herunder eksempler på de forskellige indstillinger for vært.    

## <a name="self-managed-iaas-solutions"></a>Selv administrerede (IaaS)-løsninger

### <a name="rds-on-iaas"></a>**RDS på IaaS** 
Du kan installere en oprindelig session-baserede Remote Desktop Services (i Windows Server)-installation ved hjælp af RemoteApp eller stationære computere lokalt eller i et værtsmiljø (like på Azure VM'er). RDS på IaaS installationer er mest velegnet til kunder, der allerede kender, og som har eksisterende teknisk ekspertise med RDS installationer. 

> [AZURE.NOTE]
> Du skal bruge Volume Licensing med Software Assurance (Systemadministratorens) for RDS client access licenser til at bruge denne indstilling for installation.

Implementere RDS på Azure FOS er nemmere end nogensinde før, når du bruger installation og rettelse skabeloner (Læs en [Oversigt over](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) og derefter [Gå få dem](https://aka.ms/rdautomation)). Du kan få de samme elastiske skalering funktioner med Azure klassisk installation model ressourcer (ikke Azure ressource Model ressourcer) i Azure RemoteApp ved hjælp af [automatisk skalering script](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), selvom der er flere tilpasninger og konfigurationer. Når du installerer RDS på Azure FOS, leveres support via [Azure understøtter](https://azure.microsoft.com/support/plans/), på samme supportmedarbejdere, der understøttes du med Azure RemoteApp. Du kan få omkostningsestimater afhængigt af den eksisterende brug ved at kontakte [Azure Support](https://azure.microsoft.com/support/plans/), eller du kan selv udføre beregninger ved hjælp af en udgivet snart omkostninger Lommeregner skal være.  Med N-serien FOS (i øjeblikket i private preview) du også føje vGPU - høre mere om at tilføje vGPU og om, hvordan at [organisere RDS forbedringer i Windows Server 2016](https://myignite.microsoft.com/videos/2794) i vores Ignite session.   

Vi har trinvise installationsvejledninger til [Windows Server 2012 R2](http://aka.ms/rdsonazure) og [Windows Server 2016](http://aka.ms/rdsonazure2016) for at hjælpe med din installation. Se [Fjernskrivebord blog](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) for at få de seneste nyheder.
 
### <a name="citrix-on-iaas"></a>**Citrix på IaaS** 
En oprindelig Citrix installation af session-baserede XenApp eller XenDesktop kan være installeret lokalt eller i et værtsmiljø (såsom på Azure VM'er). 

Se de trinvise installationsvejledning, [Citrix XA 7.6 på Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), kan finde flere oplysninger. Læs mere om [Citrix på Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), herunder en pris Lommeregner. Du kan også finde [Citrix kontakte](http://citrix.com/English/contact/index.asp) diskutere dine muligheder med.

## <a name="fully-managed-paassaas-offerings"></a>Fuldt administreret (PaaS/SaaS) tilbud

### <a name="citrix-cloud"></a>**Citrix skyen** 
[Citrix eksisterende skyen løsning](https://www.citrix.com/products/citrix-cloud/), identiske architecturally til Citrix XenApp Express. Citrix tilbyder [50% rabat reklame](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) for eksisterende Azure RemoteApp-kunder. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (i tech preview)**
[Tilmeld dig til deres tech preview](http://now.citrix.com/remoteapp), og se deres [Ignite session](https://myignite.microsoft.com/videos/2792) (fra og med minut 20:30). XenApp Express er architecturally identiske Citrix skyen, bortset fra den indeholder forenklet administration af Brugergrænsefladen og andre funktioner og egenskaber, der ligner Azure RemoteApp. 

Lær mere om [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Citrix Service Provider Program** 
Citrix Service Provider Program gør det nemt for tjenesteudbydere til at levere brugervenlighed virtuelle cloud computing til SMB'ere, tilbyder vedkommende de tjenester, de vil i en nemt, overkommelige model. Citrix tjenesteudbydere vokse deres forretninger Microsoft SPLA og udvide deres RDS platform investeringer med enhver enhed, hvor som helst adgang til, skal den længste programunderstøttelse, en omfattende oplevelse, sikkerhedsmæssige og øget skalerbarhed. Også Citrix tjenesteudbydere tiltrække flere abonnenter, øge kundetilfredshed og reducere deres funktionsdygtige omkostninger. [Få mere at vide](http://www.citrix.com/products/service-providers.html) , eller [finde en partner](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Microsoft hostet tjenesteudbyder** 
Hosting partnere tilbyder typisk en fuldt administreret hostet Windows-skrivebord og programtjenester, som kan omfatte administrere Azure ressourcer, operativsystemer, programmer og helpdesk ved hjælp af partner licenser aftaler med Microsoft og andre udbydere af software sammen med der en licensaftale for tjenesteudbyder tillade videresælger af abonnement Access licens (SAL). Følgende oplysninger indeholder oplysninger og kontaktoplysninger for nogle af de værter, specialize i hjælpe kunder med deres Azure RemoteApp migrering. Se [en liste over hostet tjenesteudbydere](http://aka.ms/rdsonazurecertified) , der har fuldført RDS på IaaS læ sti og vurdering.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) specialiseret i softwareudviklere overgang til skyen og ISV' vil optimere deres aktuelle skyen konfigurationer. ASPEX indeholder et bredt udvalg af administrerede services, devops og rådgivning.  

Primære placering: Antwerp, Belgien

Handlingen område: vestlige Europe

Partner status: [Silver](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Microsoft Cloud-udbyder: Ja

Tilbyder session-baserede RemoteApp og computeren løsninger: Ja, begge

Azure RemoteApp overførsel løsninger: Ja, [Få mere at vide](https://www.aspex.be/en/azure-remote-apps)

**Kontakt:**

- Telefon: +3232202198
- Mail:[info@aspex.be](mailto:info@aspex.be)
- Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (Platform navn: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) er en automatisering platform for IT-virksomheder at forenkle, optimere og skalere overførsel og levering af remote stationære computere, remote-programmer og infrastruktur i Microsoft Azure skyen. 

MyCloudIT platformen reducerer installation tid med 95%, Azure omkostninger ved 30%, og flytter deres klientens hele IT-infrastruktur i skyen i løbet af et par vigtige streger. Partnere kan nu administrere kunder fra én global dashboard, service slutbrugere over hele verden på som aldrig før og videreudvikle indtægter uden at tilføje ekstra spild eller omfattende Azure uddannelse.  

Primære placering: Dallas, TX, USA

Handlingen område: i hele verden

Partner status: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Microsoft Cloud-udbyder: Ja

Tilbyder session-baserede RemoteApp og computeren løsninger: Ja, begge

Azure RemoteApp overførsel løsninger: Ja, [Få mere at vide](https://mycloudit.com/remote-app-microsoft/)

**Kontakt:**
- Brian Garoutte, Vicedirektør for Business Development

   Telefon: 972-218-0741

   Mail:[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) specialiseret i give hostet computeren løsninger, fremvisning af hele skrivebordet og ISV-programmer oplevelser bygget på Microsoft-teknologi til en global klient base fra Azure og deres egne datacentre.

Primære placering: London, Storbritannien; Singapore; Far, TX

Handlingen område: i hele verden

Partner status: Gold

Microsoft Cloud-udbyder: Ja

Tilbyder session-baserede RemoteApp og computeren løsninger: Ja, begge

Azure RemoteApp overførsel løsninger: Ja, [Få mere at vide](http://www.acuutech.com/ara-migration/)

**Kontakt:**

- Storbritannien:

  5/6 York House, Langston vej

  Loughton, Essex IG10 3TQ
  
  Telefon: + 44 (0) 20 8502 2155
 
- Singapore:

  100 Cecil gade, #09-02 
  
  Globussen Singapore 069532
  
  Telefon: + 65 6709 4933
 
- Nordamerika: 

  3601 S. Sandman St.
  
  Pakke 200, far, TX 77098
  
  Telefon: +1 713 691 0800

## <a name="need-more-help"></a>Brug for mere hjælp?
Stadig har brug for at vælge eller har yderligere spørgsmål? Brug en af følgende metoder til at få hjælp. 

1.  Sende en e-mail til [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Kontakt [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Start ved at åbne en [Azure understøtter store og små bogstaver](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Ring til os. [Find et lokalnummer salg](https://azure.microsoft.com/overview/sales-number/).
