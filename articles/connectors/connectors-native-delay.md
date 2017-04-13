<properties
    pageTitle="Føje en forsinkelse i logik apps | Microsoft Azure"
    description="Oversigt over de forsinkelse og forsinkelse-indtil handlinger, og hvordan du bruger dem med en Azure logik app."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Komme i gang med forsinkelse og forsinkelse-indtil handlinger

Ved hjælp af forsinkelsen og "forsinkelse-indtil" handlinger, du kan udføre arbejdsproces scenarier.

Det kan du f.eks.:

- Vente til en ugedag til at sende en statusopdatering via mail.
- Forsinke arbejdsprocessen, indtil en HTTP opkald har tid til at afsluttes, før genoptage og hentning af resultatet.

For at komme i gang ved hjælp af handlingen forsinkelse i en logik app skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Bruge forsinkelse handlinger

En handling er en handling, der udføres af arbejdsprocessen, der er defineret i en logik app. [Lær mere om handlinger](connectors-overview.md).

Her er et eksempel sekvens af, hvordan du bruger en forsinkelse trin i en logik app:

1. Når du har tilføjet en udløser, skal du klikke på **Nyt trin** for at tilføje en handling.
2. Søge efter **forsinkelse** at få vist de forsinkelse handlinger. I dette eksempel skal vælger vi **forsinkelse**.

    ![Forsinkelse handlinger](./media/connectors-native-delay/using-action-1.png)

3. Udføre handlingen egenskaber til at konfigurere forsinkelsen.

    ![Forsinkelse config](./media/connectors-native-delay/using-action-2.png)

4. Klik på **Gem** for at publicere og aktivere appen logik.


## <a name="action-details"></a>Handlingsdetaljer

Gentagelse udløser har følgende egenskaber, der kan konfigureres.

### <a name="delay-action"></a>Forsinkelse handling

Denne handling forsinkelser Kør til en bestemt periode.
A * betyder, at det er et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|Antal *|Tæl|Antallet af tidsenheder til forsinke|
|Enhed *|enhed|Tidsenheden: `Second`, `Minute`, `Hour`, eller`Day`|
<br>

### <a name="delay-until-action"></a>Forsinkelse-indtil handling

Denne handling forsinkelser Kør indtil en bestemt dato og klokkeslæt.
A * betyder, at det er et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|År *|tidsstempel|Året til udskyde til (GMT)|
|Måned *|tidsstempel|Måneden til udskyde til (GMT)|
|Dag *|tidsstempel|Den dag, du vil forsinke indtil (GMT)|
<br>


## <a name="next-steps"></a>Næste trin

Prøv nu den platform og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Du kan udforske andre tilgængelige forbindelserne i logik apps ved at kigge på vores [API'er liste](apis-list.md).
