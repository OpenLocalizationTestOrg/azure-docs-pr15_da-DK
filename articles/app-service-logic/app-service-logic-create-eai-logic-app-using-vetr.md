<properties
   pageTitle="Oprette EAI logik App ved hjælp af VETR i logik apps i Azure App Service | Microsoft Azure"
   description="Validere, kode og transformere funktioner i BizTalk XML services"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Oprette EAI logik App ved hjælp af VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

De fleste Enterprise Application Integration (EAI) scenarier formidle data mellem en kilde og en destination. Disse scenarier har ofte en fælles gruppe krav:

- Sørg for, at data fra forskellige systemer er formateret korrekt.
- Udføre "opslagsværdi" af indgående data til at tage beslutninger.
- Konvertere data fra ét format til en anden. For eksempel konvertere data fra et CRM-system dataformat til en ERP-system dataformat.
- Distribuere data til de ønskede program eller system.

I denne artikel beskrives et almindelige integration mønster: "envejs meddelelse mægling" eller VETR (Valider, bliv bedre, transformation, distribuere). VETR mønster mediates data mellem en kildeobjekt og en destination for enhed. Kilde- og er som regel datakilder.

Overvej at et websted, der accepterer ordrer. Brugere skriver indlæg ordrer til systemet med HTTP. I baggrunden systemet validerer indgående data korrekt, skal normalisere den og fortsætter i en Service Bus kø til videre behandling. Systemet tager ordrer fra køen, du forventer at modtage den i et bestemt format. Til Slut strømmen er således:

**HTTP** → **validere** → **transformere** → **Service Bus**

![Grundlæggende VETR Flow][1]

Følgende BizTalk-API Apps hjælpe med at opbygge dette mønster:

* **Http-udløser** - kilde, der skal udløse meddelelse begivenhed
* **Valider** - valideret korrekte indgående data
* **Transformere** - transformationer data fra indgående format til at formatere kræves af efterfølgende system
* **Tjenesten Bus Connector** - Destination for enhed hvor data er sendt


## <a name="constructing-the-basic-vetr-pattern"></a>Bygning af det grundlæggende VETR mønster
### <a name="the-basics"></a>De grundlæggende funktioner

I portalen Azure, Vælg **+ Ny**skal du vælge **Web + Mobile**og derefter vælge **Logik App**. Vælg et navn, placering, abonnement, ressourcegruppe og placering, der fungerer. Ressourcegrupper fungere som beholdere for dine apps alle ressourcerne for din app gå til den samme ressourcegruppe.

Næste, Lad os tilføje udløsere og handlinger.


## <a name="add-http-trigger"></a>Tilføje HTTP udløser
1. Vælg **Opret fra bunden**i **Logik App-skabeloner**.
1. Vælg **HTTP lytteren** fra galleriet for at oprette en ny lytter. Kald den **HTTP1**.
2. Angiv den **Send svar automatisk?** angive til falsk. Konfigurere handlingen udløser ved at _HTTP metode_ til at _INDLÆG_ og indstille den _Relative URL-adresse_ til _/OneWayPipeline_:  
    ![HTTP-udløser][2]
3. Vælg den grønne markering til at fuldføre udløseren.

## <a name="add-validate-action"></a>Tilføje validere handling

Nu, Lad os angive handlinger til at køre, når udløseren udløses – det vil sige, når der modtages et opkald på http-slutpunktet.

1. Tilføje **BizTalk XML-kontrollere** fra galleriet, og kald den _(Validate1)_ til at oprette en forekomst.
2. Konfigurere et XSD-skema for at validere indgående XML-meddelelser. Vælg handlingen, _Valider_ , og vælg _triggers('httplistener').outputs. Indhold_ som værdi for parameteren _inputXml_ .

Handlingen Valider er nu den første handling efter HTTP lytteren: 

![XML-BizTalk kontrollere][3]

På samme måde, kan vi tilføje resten af handlingerne. 

## <a name="add-transform-action"></a>Tilføje transformation handling
Lad os konfigurere transformationer, så normalisere indgående data.

1. Tilføje **BizTalk transformere Service** fra galleriet.
2. Vælg handlingen **transformere** for at konfigurere en transformering for at transformere indgående XML-meddelelser, som den handling, der skal udføres, når denne API kaldes. Vælg ```triggers(‘httplistener’).outputs.Content``` som værdien for _inputXml_. *Tilknytning* er en valgfri parameter, da de indgående data sammenlignes med alle konfigurerede transformeringer, og kun dem, der svarer til skemaet anvendes.
3. Endelig kører transformeringen kun, hvis Valider lykkes. Vælg tandhjulsikonet øverst til højre for at konfigurere denne betingelse, og vælg _Tilføj en betingelse opfyldes_. Angiv betingelsen til ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![BizTalk transformationer][4]


## <a name="add-service-bus-connector"></a>Tilføje busforbindelse
Næste, Lad os tilføje destinationen – en tjeneste Bus kø – til at skrive data til.

1. Tilføj en **Tjeneste busforbindelse** fra galleriet. Angiv **navnet** til _Servicebus1_, angive **Forbindelsesstreng** til forbindelsesstrengen til din tjeneste bus forekomst, angive **Objektnavn** til _kø_og springe **abonnementets navn**.
2. Vælg handlingen, **Send meddelelse** , og angiv feltet **indhold** til handlingen til _actions('transformservice').outputs. OutputXml_.
3. Angive feltet **Indholdstype** til *programmet/xml*:  

![Service Bus][5]


## <a name="send-http-response"></a>Sende HTTP-svar
Når pipeline behandling er færdig, sende tilbage et HTTP-svar til både fuldførte og mislykkede med følgende trin:

1. Tilføj en **HTTP lytteren** fra galleriet, og vælg handlingen, **Sende HTTP-svar** .
2. Angiv **svar-ID** til at sende *meddelelsen*.
2. Angive **Svar indhold** til *Pipeline behandling fuldført*.
3. **Svar statuskode** til *200* til at angive HTTP 200 OK.
4. Vælg rullemenuen i øverste højre hjørne, og vælg **Tilføj en betingelse opfyldes**.  Angiv betingelsen til følgende udtryk:  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Gentag disse trin for at sende et HTTP-svar ved fejl samt. Ændre **betingelse** til følgende udtryk:  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Vælg **OK** og derefter **oprette**.



## <a name="completion"></a>Fuldførelse
Hver gang nogen sender en meddelelse til http-slutpunkt, udløser appen og udfører de handlinger, du lige har oprettet. Til at administrere sådanne logik-apps du opretter, Vælg **Gennemse** i portalen Azure, og vælg **Logik Apps**. Vælg din app til at se flere oplysninger.

Nogle nyttige emner:

[Administrere og overvåge dine API Apps og forbindelser](app-service-logic-monitor-your-connectors.md)  <br/>
[Overvåge dine logik Apps](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG
