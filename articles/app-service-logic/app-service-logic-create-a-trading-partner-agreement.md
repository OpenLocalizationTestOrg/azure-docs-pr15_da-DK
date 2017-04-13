<properties 
   pageTitle="Oprette en handel partneraftale i Azure App Service | Microsoft Azure" 
   description="Oprette handel Partner aftaler" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Oprette en handel partneraftale   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Handel partnere er de enheder, der er involveret i B2B (Business to Business) kommunikation. Når to partnere etablere en relation, er dette kaldes for en *aftale*. Den aftale, der er defineret er baseret på kommunikation to partnere ønske at opnå og er protocol eller transport bestemte. De forskellige B2B protokoller og transporter, der understøttes af Azure App Service omfatter:

- AS2 (anvendelse sætning 2)
- EDIFACT (United Nations/elektroniske Data Interchange til Administration, handel og Transport (UN/EDIFACT))
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>BizTalk-API Apps, der understøtter B2B scenarier
De følgende API Apps aktivere disse funktioner ved hjælp af en omfattende og intuitiv oplevelse i portalen Azure:


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk handel Partner Administration (TPM)
- Oprettelse og administration af partnere, profiler og identiteter
- Lagring og administration af eller-Brugergrupper skemaer
- Lagring og administration af certifikater (bruges i AS2 protocol)
- Oprettelse og administration af AS2 aftaler
- Oprettelse og administration af EDIFACT aftaler (inkluderer samling i send side)
- Oprettelse og administration af X12 aftaler (inkluderer samling i send side)

![][1]


## <a name="as2-connector"></a>AS2 forbindelse
- Udfører AS2 aftaler, som defineret i den relaterede TPM API App-forekomst
- Overflader AS2 behandling/sporingsoplysningerne er opdateret til fejlfinding


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Udfører EDIFACT aftaler, som defineret i den relaterede TPM API App-forekomst
- Overflader EDIFACT behandling/sporingsoplysningerne er opdateret til fejlfinding
- Giver tilstand administration af batches (start og stop), som defineret i EDIFACT aftalerne i den relaterede TPM API App-forekomst


## <a name="biztalk-x12"></a>BizTalk X12
- Udfører X12 aftaler som defineret i den relaterede TPM API App-forekomst 
- Overflader X12 behandling/registrering oplysninger til fejlfinding
- Giver tilstand administration af batches (start og stop), som defineret i X12 aftalerne i den relaterede TPM API App-forekomst

Som tidligere nævnte AS2 X 12 og EDIFACT API Apps kræver en TPM-API-App til at fungere som forventet.


## <a name="getting-started"></a>Kom godt i gang
Sådan oprettes handel partner aftaler:

1. Oprette en forekomst af **BizTalk handel Partner administration** forbindelsen. Dette kræver en tom SQL-Database til funktionen. Før du starter, skal du sørge for at have en tom database tilgængelige og klar til brug.
2. Overføre ved hjælp af skemaer og certifikater, som kræves af aftalerne. Gøre dette ved at gennemse forekomsten TPM oprettet og væk i webdelen 'Skemaer' og/eller 'Certifikater'
3. Gå til den TPM-forekomst, der er oprettet og trinvist delen **partnere**
4. Oprette partnere efter behov. Også Rediger profil(er) efter behov, og Tilføj de nødvendige identiteter
5. Nu bruge delen **aftaler** til at oprette aftaler. Når du opretter en aftale, skal du vælge den protokol, der skal bruges. De resterende konfigurationsindstillinger er baseret på den protokol, du har valgt.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
