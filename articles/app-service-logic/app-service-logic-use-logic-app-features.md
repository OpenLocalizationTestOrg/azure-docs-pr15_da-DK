<properties 
    pageTitle="Bruge logik App-funktioner | Microsoft Azure" 
    description="Lær at bruge de avancerede funktioner i logik apps." 
    authors="stepsic-microsoft-com" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="stepsic"/> 
    
# <a name="use-logic-apps-features"></a>Brug logik Apps-funktioner

I det [forrige emne](app-service-logic-create-a-logic-app.md), du har oprettet din første logik app. Nu vi viser dig, hvordan du opretter en mere komplet proces, ved hjælp af App Services logik Apps. I dette emne introduceres de følgende nye logik Apps begreber:

- Betinget logik, som udfører en handling, kun, når en bestemt betingelse opfyldes.
- Kodevisning for at redigere en eksisterende logik app.
- Indstillinger for at starte en arbejdsproces.

Før du har fuldført dette emne, skal du udføre trinnene i [oprette en ny logik app](app-service-logic-create-a-logic-app.md). Gå til din logik app [Azure-portalen], og klik på **udløsere og handlinger** i oversigt over redigere logik app definition.

## <a name="reference-material"></a>Referencemateriale

Følgende dokumenter kan være nyttige:

- [Administration og runtime REST API'er](https://msdn.microsoft.com/library/azure/mt643787.aspx) – herunder hvordan du kalde logik apps direkte
- [Language reference](https://msdn.microsoft.com/library/azure/mt643789.aspx) - en omfattende liste over alle understøttede funktioner/udtryk
- [Typer af udløser og en handling](https://msdn.microsoft.com/library/azure/mt643939.aspx) - de forskellige typer handlinger og de tage input
- [Oversigt over App Service](../app-service/app-service-value-prop-what-is.md) - beskrivelse af, hvilke komponenter for at vælge, hvornår du opbygge en løsning

## <a name="adding-conditional-logic"></a>Betinget logik for at tilføje

Selvom det oprindelige flow virker, er der nogle områder, der kan forbedres. 


### <a name="conditional"></a>Betinget
Denne logik app kan medføre du få en masse mails. Følgende trin føje logik til at sikre, at du kun modtager en mail, når tweet stammer fra en person med et bestemt antal tilhængere. 

1. Klik på plustegnet, og find handlingen, *Får brugeren* for Twitter.

2. Send i feltet **Tweeted ved** fra ved at få oplysninger om Twitter brugeren.

    ![Få bruger](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Klik på plustegnet igen, men denne gang vælge **Tilføj betingelse**

4. Klik på **...** under **Få bruger** til at finde feltet **tilhængere Tæl** i det første felt.

5. I rullemenuen Vælg **større end**

6. Skriv antallet tilhængere brugerne skal have i det andet felt.

    ![Betinget](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Til sidst skal feltet træk og slip e-mailen i feltet **Hvis Ja** . Det betyder, at du får kun mails, når tilhænger antallet er opfyldt.

## <a name="repeating-over-a-list-with-foreach"></a>Gentaget over en liste med forEach

ForEach løkke angiver en matrix for at gentage en handling. Hvis det ikke er en matrix mislykkes strømmen. Som et eksempel, hvis du har action1, som producerer en matrix med meddelelser, og du vil sende alle meddelelser, du kan medtage denne forEach-sætning i egenskaberne for din handling: forEach:"@action('action1').outputs.messages"
 

## <a name="using-the-code-view-to-edit-a-logic-app"></a>Brug af kodevisning til at redigere en logik App

Ud over designeren, kan du redigere den kode, der definerer en logik app således direkte. 

1. Klik på knappen **kodevisning** i kommandolinjen. 

    Dette åbner en fuld editor, der viser den definition, du lige har redigeret.

    ![Kodevisning](./media/app-service-logic-use-logic-app-features/codeview.png)

    Ved hjælp af tekstredigeringsprogrammet, kan du kopiere og indsætte en hvilken som helst antal handlinger i den samme logik app eller mellem logik apps. Du kan også nemt tilføje eller fjerne hele sektioner fra definitionen, og du kan også dele definitioner med andre.

2. Når du har foretaget dine ændringer i kodevisning, blot klikke på **Gem**. 

### <a name="parameters"></a>Parametre
Der er nogle funktioner i logik Apps, der kan kun bruges i visningen programkode. Et eksempel af disse er parametre. Parametre gør det nemt at genbruge værdier i hele din logik app. Hvis du har en e-mailadresse, du vil bruge i flere handlinger, skal du eksempelvis definerer det som en parameter.

Følgende opdaterer din eksisterende logik app for at bruge parametre til termen i forespørgslen.

1. I kodevisning, Find den `parameters : {}` objekt og indsætte følgende emne objektet:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
    
2. Rul ned til den `twitterconnector` handling, Find forespørgselsværdien, og Erstat den med `#@{parameters('topic')}`.
    Du kan også bruge funktionen **Kontakt** til at sammenføje strenge på to eller flere, for eksempel: `@concat('#',parameters('topic'))` er identisk med ovenstående. 
 
Parametre er en god metode til at udtrække værdier, som du sandsynligvis at ændre meget. De er især nyttig, når du har brug at tilsidesætte parametre i forskellige miljøer. Du kan finde flere oplysninger om hvordan du kan tilsidesætte parametre, der er baseret på miljø, vores [REST-API dokumentation](https://msdn.microsoft.com/library/mt643787.aspx).

Nu, når du klikker på **Gem**, hver time får du en ny tweets, der har mere end 5 retweets leveret til en mappe med navnet **tweets** i din Dropbox.

Hvis du vil vide mere om logik App definitioner, kan du se [redigere logik App definitioner](app-service-logic-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Starte en arbejdsproces for logik app
Der er flere forskellige muligheder for at starte arbejdsprocessen defineret i du logik app. En arbejdsproces kan altid startes efter behov i [Azure-portalen].

### <a name="recurrence-triggers"></a>Gentagelse udløsere
En gentagelse udløser kører med et interval, du angiver. Når udløseren har betinget logik, bestemmer udløseren eller ej arbejdsprocessen skal køre. En udløser angiver det skal køres ved at returnere en `200` statuskode. Når det ikke nødvendigt at køre, returnerer den en `202` statuskode.

### <a name="callback-using-rest-apis"></a>Tilbagekald ved hjælp af REST API'er
Tjenester kan ringe til et logik app slutpunkt for at starte en arbejdsproces. Du kan få flere oplysninger i [logik apps som kaldes slutpunkter](app-service-logic-connector-http.md) . Hvis du vil starte slags logik app efter behov, skal du klikke på knappen **Kør nu** på kommandolinjen. 

<!-- Shared links -->
[Azure-portalen]: https://portal.azure.com 