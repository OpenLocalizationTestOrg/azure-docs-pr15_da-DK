<properties 
    pageTitle="Lær at oprette en AS2 aftale til Enterprise-Integration Pack" 
    description="Lær at oprette en AS2 aftale til Enterprise-Integration Pack | Microsoft Azure App Service" 
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
    ms.date="06/29/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-as2"></a>Virksomhedsintegration med AS2

## <a name="create-an-as2-agreement"></a>Oprette en AS2 aftale
For at bruge funktionerne enterprise i logik apps, skal du først oprette aftaler. 

### <a name="heres-what-you-need-before-you-get-started"></a>Her er, hvad du skal før du går i gang
- En [integration konto](./app-service-logic-enterprise-integration-accounts.md) , der er defineret i abonnementet Azure  
- Mindst to [partnere](./app-service-logic-enterprise-integration-partners.md) allerede er defineret i kontoen integration  

>[AZURE.NOTE]Når du opretter en aftale, skal indholdet i filen aftale til aftaletypen.    


Når du har [oprettet en integration konto](./app-service-logic-enterprise-integration-accounts.md) og [føjet partnere](./app-service-logic-enterprise-integration-partners.md), kan du oprette en aftale ved at følge disse trin:  

### <a name="from-the-azure-portal-home-page"></a>Fra Azure portalwebstedets startside

Når du logger på [Azure portal](http://portal.azure.com "Azure-portalen"):  
1. Vælg **Find** i menuen til venstre.  

>[AZURE.TIP]Hvis du ikke kan se linket **Gennemse** , skal du muligvis først udvide menuen. Gør dette ved at vælge linket **Vis menu** , der er placeret på øverst til venstre i menuen skjult.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Skriv *integration* i feltet filter Søg og vælg derefter **Integration konti** på listen over resultater.       
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Vælg kontoen integration, hvor du vil oprette aftalen i bladet **Integration konti** , der åbnes. Hvis du ikke kan se integration konti lister, [opretter en første](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4.  Vælg feltet **aftaler** . Hvis du ikke kan se feltet aftaler, tilføje den første.   
![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
5. Vælg knappen **Tilføj** i bladet aftaler, der åbnes.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Angiv et **navn** til aftalen, og vælg **Host Partner**, **Host identitet**, **Gæst Partner**, **Gæst identitet**, i bladet aftaler, der åbnes.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

Her er nogle ting, du kan være nyttigt, når du konfigurerer indstillingerne for din accept af: 
  
|Egenskaben|Beskrivelse|
|----|----|
|Host Partner|En aftale skal både en host og Gæst partner. Host partner repræsenterer den organisation, som konfigurerer aftalen.|
|Host identitet|Et id for host partner. |
|Gæst Partner|En aftale skal både en host og Gæst partner. Gæst partner repræsenterer den organisation, der handler med host partner.|
|Gæst identitet|Et id for gæst partner.|
|Indstillinger for Modtag|Disse egenskaber gælder for alle meddelelser, der modtages af en aftale|
|Sende indstillinger|Disse egenskaber gælder for alle meddelelser, som sendes af en aftale|  
Lad os fortsætte:  
7. Vælg **Modtager indstillinger** til at konfigurere, hvordan meddelelser, der er modtaget via denne aftale er skal håndteres.  
 
 - Du kan eventuelt tilsidesætte egenskaberne i den indgående meddelelse. Marker afkrydsningsfeltet **tilsidesætte meddelelsesegenskaber** for at gøre dette.
  - Marker afkrydsningsfeltet **meddelelse skal signeres** , hvis du vil have til at kræve alle indgående meddelelser skal underskrives. Hvis du vælger denne indstilling, skal du også at markere det **certifikat** , der skal bruges til at validere signatur i meddelelser.
  - Du kan eventuelt kræve meddelelser skal krypteres samt. Marker afkrydsningsfeltet **meddelelse skal krypteres** for at gøre dette. Du skal derefter Vælg det **certifikat** , der skal bruges til at afkode indgående meddelelser.
  - Du kan også kræve meddelelser der skal komprimeres. For at gøre dette skal du markere afkrydsningsfeltet **meddelelse skal komprimeres** .  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

Se tabellen nedenfor, hvis du vil have mere at vide om, hvad Modtag aktiver.  

|Egenskaben|Beskrivelse|
|----|----|
|Tilsidesætte meddelelsesegenskaber|Vælg denne indstilling for at angive, at egenskaber i modtagne meddelelser kan tilsidesættes |
|Meddelelsen skal signeres|Aktivere denne indstilling for at kræve meddelelser skal signeres digitalt|
|Meddelelsen skal krypteres|Aktivér denne indstilling for at kræve meddelelser skal krypteres. Ikke-krypterede meddelelser vil blive afvist.|
|Meddelelsen skal komprimeres|Aktivér denne indstilling for at kræve meddelelser der skal komprimeres. Ikke-komprimerede meddelelser vil blive afvist.|
|MDN tekst|Dette er et standard MDN skal sendes til meddelelsens afsender|
|Sende MDN|Aktivér denne indstilling for at tillade MDNs skal sendes.|
|Send signerede MDN|Aktivér denne indstilling for at kræve MDNs skal underskrives.|
|Mikrofon algoritme||
|Sende asynkron MDN|Aktivér denne indstilling for at kræve meddelelser sendes asynkront.|
|URL-ADRESSE|Dette er den URL-adresse, som sendes meddelelser.|
Nu kan du fortsætte:  
8. Vælg **Send indstillinger** til at konfigurere, hvordan meddelelser, der sendes via denne aftale er skal håndteres.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

Se tabellen nedenfor, hvis du vil have mere at vide om, hvilke send aktiver.  

|Egenskaben|Beskrivelse|
|----|----|
|Aktivere meddelelse signering|Markér dette afkrydsningsfelt for at aktivere alle meddelelser, der sendes fra aftalen skal underskrives.|
|Mikrofon algoritme|Vælg algoritmen til brug i meddelelsen logge|
|Certifikat|Vælg certifikatet, der skal bruges i meddelelsen logge|
|Aktivere meddelelseskryptering|Markér dette afkrydsningsfelt for at kryptere alle de meddelelser, der er sendt fra denne aftale.|
|Krypteringsalgoritme|Vælg krypteringsalgoritmen til brug i meddelelseskryptering|
|Foldes ud HTTP sidehoveder|Markér dette afkrydsningsfelt for at folde sig ud overskriften HTTP-indholdstype til en enkelt linje.|
|Anmode om MDN|Aktivér dette afkrydsningsfelt for at anmode om en MDN for alle de meddelelser, der er sendt fra denne aftale|
|Anmode om signeret MDN|Aktivér til at anmode om, at alle MDNs sendes til denne aftale er signeret|
|Anmode om asynkron MDN|Giver mulighed for at anmode om asynkron MDN skal sendes til denne aftale|
|URL-ADRESSE|URL-adressen, MDNs sendes|
|Aktivere NRR|Markér dette afkrydsningsfelt for at aktivere ikke-afvisning af kvittering|
Vi er næsten færdig!  
9. Vælg feltet **aftaler** på bladet Integration konto, og du får vist nyligt tilføjede aftalen angivet.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

