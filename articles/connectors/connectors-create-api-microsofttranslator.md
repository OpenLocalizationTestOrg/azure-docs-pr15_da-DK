<properties
    pageTitle="Føje Microsoft Translator i logik apps | Microsoft Azure"
    description="Oversigt over Microsoft Translator forbindelsen med REST-API parametre"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Introduktion til Microsoft Translator-forbindelse
Oprette forbindelse til Microsoft Translator oversætte tekst, skal du registrere et sprog, og meget mere. Med Microsoft Translator kan du: 

- Opbyg din virksomhed rutediagram, der er baseret på de data, du får fra Microsoft Translator. 
- Bruge handlinger til at oversætte tekst, registrerer et sprog, og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. Når der oprettes en ny fil i Dropbox, kan du oversætte tekst i et andet sprog ved hjælp af Microsoft Translator-filen.

For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger
Microsoft Translator omfatter følgende handlinger. Der findes ingen udløsere.

Udløsere | Handlinger
--- | ---
Ingen | <ul><li>Finde sprog</li><li>Tekst til tale</li><li>Oversætte tekst</li><li>Få sprog</li><li>Få tale sprog</li></ul>

Alle forbindelser understøtter data i JSON og XML-formater.


## <a name="create-a-connection-to-microsoft-translator"></a>Oprette en forbindelse til Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Swagger REST-API reference
Gælder for version: 1.0.

### <a name="detect-language"></a>Finde sprog    
Registrerer kildesprog af givet tekst.  
```GET: /Detect```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|forespørgsel|streng|Ja|forespørgsel|ingen |Tekst, hvis sprog vil blive identificeret|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="text-to-speech"></a>Tekst til tale    
Konverterer en given tekst til tale som en lydstreamet i bølge-format.  
```GET: /Speak```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|forespørgsel|streng|Ja|forespørgsel|ingen |Tekst til at konvertere|
|sprog|streng|Ja|forespørgsel|ingen |Sprogkode til at generere tale (eksempel: ' da-DK)|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="translate-text"></a>Oversætte tekst    
Oversætter tekst til et bestemt sprog ved hjælp af Microsoft Translator.  
```GET: /Translate```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|forespørgsel|streng|Ja|forespørgsel|ingen |Tekst til oversættelse|
|languageTo|streng|Ja|forespørgsel| ingen|Målsprog kode (eksempel: "fransk")|
|languageFrom|streng|Nej|forespørgsel|ingen |Kildesprog; Hvis du ikke er angivet, forsøger Microsoft Translator til automatisk registrering. (eksempel: da)|
|kategori|streng|Nej|forespørgsel|Generelt |Oversættelse kategori (standard: "Generelt")|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="get-languages"></a>Få sprog    
Henter alle sprog, der understøtter Microsoft Translator.  
```GET: /TranslatableLanguages```

Der er ingen parametre til dette opkald. 

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="get-speech-languages"></a>Få tale sprog    
Henter de sprog, der er tilgængelige for tale syntese.  
```GET: /SpeakLanguages``` 

Der er ingen parametre til dette opkald.

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|

## <a name="object-definitions"></a>Objektdefinitioner

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Sprog: sprog model for Microsoft Translator oversættes sprog

|Egenskabsnavn | Datatype | Påkrævet|
|---|---|---|
|Kode|streng|Nej|
|Navn|streng|Nej|


## <a name="next-steps"></a>Næste trin

[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

Gå tilbage til [API'er liste](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
