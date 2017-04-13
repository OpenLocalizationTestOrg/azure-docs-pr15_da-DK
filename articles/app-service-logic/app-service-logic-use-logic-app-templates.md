<properties
 pageTitle="Logik App-skabelonerne | Microsoft Azure"
 description="Lær, hvordan du bruger allerede er oprettet logik app-skabeloner til at hjælpe dig med at komme i gang"
 authors="kevinlam1"
 manager="dwrede"
 editor=""
 services="app-service\logic"
 documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="klam"/>

# <a name="logic-app-templates"></a>Logik App-skabeloner

## <a name="what-are-logic-app-templates"></a>Hvad er logik app-skabeloner

En skabelon til app logik er en færdigbyggede logik, hvor du kan bruge til hurtigt at komme i gang med at oprette din egen arbejdsproces. 

Disse skabeloner er en god måde at opdage forskellige mønstre, der kan konfigureres ved hjælp af logik apps. Du kan enten bruge disse skabeloner som-er eller tilpasse dem til det pågældende scenarie.

## <a name="overview-of-available-templates"></a>Oversigt over tilgængelige skabeloner

Der findes mange tilgængelige skabeloner i øjeblikket er udgivet i logik app platform. Nogle eksempel kategorier, samt typen forbindelser, der bruges i dem, vises nedenfor.

### <a name="enterprise-cloud-templates"></a>Virksomhedsskabeloner skyen
Skabeloner, der integreres Dynamics CRM, Salesforce, feltet, Azure Blob og andre forbindelser til din virksomheds skyen behov. Nogle eksempler på Hvad kan jeg gøre med disse skabeloner omfatter organisere dine kundeemner og sikkerhedskopiere dataene virksomhedens fil.

### <a name="enterprise-integration-pack-templates"></a>Integration pack virksomhedsskabeloner
Konfigurationer af VETER (validere udtrække transformere, forbedre, og distribuere) rørledninger, modtager en X12 eller-Brugergrupper dokument over AS2 samt at transformere det til XML, som X12 og AS2 meddelelse ekspedition.

### <a name="protocol-pattern-templates"></a>Protocol mønster skabeloner
Disse skabeloner består af logik apps, der indeholder protocol mønstre som anmodning om svar over HTTP samt integration på tværs af FTP- og SFTP. Brug disse som de findes, eller som udgangspunkt for at oprette mere komplekse protocol mønstre.  

### <a name="personal-productivity-templates"></a>Personlig produktivitet skabeloner
Mønstre for at forbedre personlig produktivitet indeholde skabeloner, angive daglige påmindelser, ændre vigtige arbejdselementer til opgavelister og automatisere længerevarende opgaver ned til godkendelsestrin for en enkelt bruger.

### <a name="consumer-cloud-templates"></a>Forbruger skyen skabeloner
Enkel skabeloner, der integreres med sociale medietjenester som Twitter, slæk og mail, i sidste ende kan styrke sociale medier marketinginitiativer. Dette omfatter også skabeloner som overskyet kopierer, som kan hjælpe dig med at øge produktiviteten ved at gemme tid brugt på traditionelt gentagne opgaver. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>Sådan oprettes en logik app ved hjælp af en skabelon 

For at komme i gang ved hjælp af en skabelon til app logik skal du gå til logik app designer. Hvis du angiver designer ved at åbne en eksisterende logik app, indlæser appen logik automatisk i visningen Forespørgselsdesigner. Men hvis du opretter en ny logik app, skal du se skærmbilledet nedenfor.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

Fra dette skærmbillede, kan du enten vælge at starte med en tom logik app eller en færdigbyggede skabelon. Hvis du vælger en af skabelonerne, får du med yderligere oplysninger. I dette eksempel skal bruge vi skabelonen *Når der oprettes en ny fil i Dropbox, kan du kopiere den til OneDrive* .  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

Hvis du vælger at bruge skabelonen, du blot vælge knappen *Brug denne skabelon* . Du bliver bedt om at logge på dine konti, der er baseret på hvilken forbindelser skabelonen anvender. Hvis du tidligere har oprettet en forbindelse med disse forbindelser, kan du også vælge Fortsæt som set nedenfor.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

Når du har oprettet forbindelse og derefter vælge *Fortsæt*, logik app'en åbner i visningen i Forespørgselsdesigner.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

I det foregående eksempel, som det er tilfældet med mange skabeloner kan nogle af felterne obligatoriske egenskaber udfyldes i forbindelser. Nogle kan stadig kræver en værdi, før du kan installere appen logik korrekt. Hvis du forsøger at installere uden at angive nogle af de manglende felter, får du besked med en fejlmeddelelse.

Hvis du vil vende tilbage til skabelon fremviseren, skal du vælge knappen *skabeloner* i den øverste navigationslinje. Ved at skifte tilbage til skabelon fremviseren, mister du alle ikke-gemte status. Før du skifter tilbage til skabelon viewer, ser du en advarsel om besked om dette.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>Sådan installeres et logik app, der er oprettet ud fra en skabelon

Når du har indlæst din skabelon og foretaget de ønskede ændringer, markere afkrydsningsfeltet Gem knappen i øverste venstre hjørne. Dette gemmer og publicerer din logik app.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

Hvis du vil have mere at vide på redigerer hvordan du kan tilføje flere trin i skabelon til en eksisterende logik app, eller Sørg Generelt kan få mere at vide i [oprette en logik app](app-service-logic-create-a-logic-app.md).