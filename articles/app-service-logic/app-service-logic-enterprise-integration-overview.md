<properties 
    pageTitle="Oversigt over virksomhedsintegration | Microsoft Azure App Service | Microsoft Azure" 
    description="Bruge funktionerne i virksomhedsintegration til at aktivere business-proces og integration scenarier ved hjælp af logik apps" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>Oversigt over Enterprise Integration Pack

## <a name="what-is-the-enterprise-integration-pack"></a>Hvad er Enterprise Integration Pack?
Enterprise Integration Pack er Microsofts skybaseret løsning til problemfrit aktivere business to business (B2B) kommunikation. Pakken bruger branche standard protokoller, herunder [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md)og [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) til at udveksle meddelelser mellem forretningsforbindelser. Meddelelser kan være fastgjort du kan også bruge både kryptering og digitale signaturer. 

Pakken giver organisationer, der bruger forskellige protokoller og formater for at udveksle meddelelser elektronisk ved at transformere de forskellige formater i et format, som begge organisationer systemer kan fortolke og handle på meddelelsen. 

Hvis du allerede kender, F.eks eller Microsoft Azure BizTalk-tjenester, finder du det let at bruge den virksomhedsintegration funktioner, fordi de fleste af begreberne, der ligner hinanden. En overordnet forskel er, virksomhedsintegration bruger integration konti til at forenkle opbevaring og håndtering af elementer, der bruges i B2B kommunikation. 

Enterprise Integration Pack er architecturally, baseret på **integration konti** , der gemmer alle elementer, der kan bruges til at designe, implementere og vedligeholde dine B2B apps. En integration konto er grundlæggende en skybaseret objektbeholder, hvor du gemmer elementer som ved hjælp af skemaer, partnere, certifikater, kort og aftaler. Disse elementer kan derefter bruges i logik apps til at oprette B2B arbejdsprocesser. Før du kan bruge elementer i en logik app, skal du sammenkæde kontoen integration med din logik app. Når du knytter dem, får din logik app adgang til kontoen integration elementer.  

## <a name="why-should-you-use-enterprise-integration"></a>Hvorfor skal du bruge virksomhedsintegration?
- Du kan få til at gemme alle elementer på ét sted, som er kontoen integration med virksomhedsintegration. 
- Du kan udnytte logik apps program og alle dens forbindelser til at oprette B2B arbejdsprocesser og integrere med 3 part SaaS programmer, lokale apps samt brugerdefinerede programmer
- Du kan også udnytte Azure funktioner

## <a name="how-to-get-started-with-enterprise-integration"></a>Hvordan du kan komme i gang med virksomhedsintegration?
Du kan oprette og administrere B2B apps ved hjælp af Enterprise Integration Pack via logik apps designer på **Azure-portalen**.  

Du kan også bruge [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "logik apps PowerShell emner") til at administrere dine logik apps. 

Her er en oversigt over de trin, du skal gå, før du kan oprette apps i portalen Azure: ![oversigt billede](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Hvad er nogle almindelige scenarier?

Virksomhedsintegration understøtter disse branchestandarder:   

- Eller-Brugergrupper - Electronic Data Interchange  
- EAI - programmet virksomhedsintegration  

## <a name="heres-what-you-need-to-get-started"></a>Her er, hvad du har brug at komme i gang
- Et Azure-abonnement med en integration-konto
- Visual Studio-2015 til at oprette kort og ved hjælp af skemaer
- [Microsoft Azure logik Apps virksomhedsintegration Tools til Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Prøv det
[Prøv det nu for](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) at installere en fuldt funktionsdygtige stikprøve AS2 send og modtag logik app, der bruger funktionerne B2B under logik Apps.

## <a name="learn-more-about"></a>Få mere at vide om:
- [Aftaler] (./app-service-logic-enterprise-integration-agreements.md "Få mere at vide om enterprise integration aftaler")
- [Business to Business (B2B) scenarier] (./app-service-logic-enterprise-integration-b2b.md "Lær at oprette logik apps med B2B funktioner")  
- [Certifikater] (./app-service-logic-enterprise-integration-certificates.md "Få mere at vide om enterprise integration certifikater")
- [Flad fil kodning/kodning] (./app-service-logic-enterprise-integration-flatfile.md "Lær at kode og afkode flad filindhold")  
- [Integration af konti] (./app-service-logic-enterprise-integration-accounts.md "Få mere at vide om integration af konti")
- [Kort] (./app-service-logic-enterprise-integration-maps.md "Få mere at vide om virksomhedsintegration kort")
- [Partnere] (./app-service-logic-enterprise-integration-partners.md "Få mere at vide om enterprise integration partnere")
- [Ved hjælp af skemaer] (./app-service-logic-enterprise-integration-schemas.md "Få mere at vide om enterprise integration skemaer")
- [XML-meddelelse validering] (./app-service-logic-enterprise-integration-xml.md "Lær at validere XML-meddelelser med logik apps")
- [XML-transformering] (./app-service-logic-enterprise-integration-transform.md "Få mere at vide om virksomhedsintegration kort")
- [Virksomhed-Integration forbindelser] (../connectors/apis-list.md "Få mere at vide om enterprise integration pack forbindelser")



