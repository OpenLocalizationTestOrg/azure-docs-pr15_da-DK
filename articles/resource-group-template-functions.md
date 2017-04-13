<properties
   pageTitle="Ressourcestyring skabelon funktioner | Microsoft Azure"
   description="I denne artikel beskrives funktionerne, der skal bruges i en Azure ressourcestyring skabelon til at hente værdier, arbejde med strenge og tal og hente installationsoplysninger."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-template-functions"></a>Azure ressourcestyring skabelon funktioner

Dette emne beskrives alle de funktioner, du kan bruge i en skabelon til Azure ressourcestyring.

Skabelon funktioner og deres parametre er case-insensitive. For eksempel løser ressourcestyring **variables('var1')** og **VARIABLES('VAR1')** som den samme. Når evalueres, medmindre funktionen ændrer udtrykkeligt store og små bogstaver (såsom toUpper eller toLower), funktionen bevarer kassen. Visse ressourcetyper muligvis tilfælde krav uanset hvordan funktioner evalueres.

## <a name="numeric-functions"></a>Numeriske funktioner

Ressourceleder, der indeholder følgende funktioner til at arbejde med heltal:

- [tilføje](#add)
- [copyIndex](#copyindex)
- [division](#div)
- [heltal](#int)
- [Rest](#mod)
- [mul](#mul)
- [Sub](#sub)


<a id="add" />
### <a name="add"></a>tilføje

**tilføje (operand1, operand2)**

Returnerer summen af de to angivne heltal.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| operand1                           |   Ja    | Første heltal, der skal tilføje.
| operand2                           |   Ja    | Anden heltal, der skal tilføje.

I følgende eksempel adderes to parametre.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />
### <a name="copyindex"></a>copyIndex

**copyIndex(offset)**

Returnerer det aktuelle indeks af en gentagelse løkke. 

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| forskydning                           |   Nej    | Mængden til at føje til gentagelse NUTIDSVÆRDI.

Denne funktion bruges altid med en **kopi** objekt. Se [oprette flere forekomster af ressourcer i Azure ressourcestyring](resource-group-create-multiple.md)for en fuldstændig beskrivelse af, hvordan du bruger **copyIndex**.

I følgende eksempel viser en kopi løkke og indeksværdien, der er inkluderet i navnet. 

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />
### <a name="div"></a>division

**div (operand1, operand2)**

Returnerer heltal divisionen med to medfølgende heltal.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| operand1                           |   Ja    | Heltal, der er inddelt.
| operand2                           |   Ja    | Heltal, der bruges til at dividere. Må ikke være 0.

I følgende eksempel dividerer en parameter ved en anden parameter.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />
### <a name="int"></a>heltal

**int(valueToConvert)**

Konverterer den angivne værdi til heltal.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   Ja    | Værdien, der skal konverteres til heltal. Hvilken type værdi kan kun være streng eller heltal.

I følgende eksempel konverterer leveret parameterværdi til heltal.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### <a name="mod"></a>Rest

**REST (operand1, operand2)**

Returnerer restværdien af heltal opdelingen ved hjælp af de to angivne heltal.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| operand1                           |   Ja    | Heltal, der er inddelt.
| operand2                           |   Ja    | Heltal, der bruges til at dividere, der skal være forskellige fra 0.

I følgende eksempel returnerer restværdien ved division af en parameter ved en anden parameter.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />
### <a name="mul"></a>mul

**mul (operand1, operand2)**

Returnerer multiplikationen med to medfølgende heltal.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| operand1                           |   Ja    | Første heltal til at multiplicere.
| operand2                           |   Ja    | Anden heltal til at multiplicere.

I følgende eksempel multiplicerer en parameter ved en anden parameter.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### <a name="sub"></a>Sub

**Sub (operand1, operand2)**

Returnerer subtraktion med to medfølgende heltal.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| operand1                           |   Ja    | Heltal, der trækkes fra.
| operand2                           |   Ja    | Heltal, der trækkes.

I følgende eksempel trækker en parameter fra en anden parameter.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## <a name="string-functions"></a>Strengfunktioner

Ressourceleder, der indeholder følgende funktioner til at arbejde med strenge:

- [Base64](#base64)
- [Sammenkæd](#concat)
- [længde](#lengthstring)
- [padLeft](#padleft)
- [Erstat](#replace)
- [Spring over](#skipstring)
- [opdele](#split)
- [streng](#string)
- [understreng](#substring)
- [tage](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [Trim](#trim)
- [uniqueString](#uniquestring)
- [URI](#uri)


<a id="base64" />
### <a name="base64"></a>Base64

**Base64 (inputString)**

Returnerer base64 repræsentationen af strengen input.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| inputString                        |   Ja    | Strengværdi til at returnere som en base64 repræsentation.

I følgende eksempel viser, hvordan du kan bruge funktionen base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### <a name="concat---string"></a>Sammenkæd - streng

**Sammenkæd (streng1, streng2, string3,...)**

Kombinerer flere strengværdier, og returnerer den sammenkædede streng. 

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| streng1                        |   Ja    | En strengværdi til at sammenkæde.
| Yderligere strenge             |   Nej     | Strengværdier til at sammenkæde.

Denne funktion kan tage et vilkårligt antal argumenter, og kan acceptere strenge eller matrixer for parametre. Et eksempel på sammenkæde matrixer, se [kontaktformular - matrix](#concatarray).

I følgende eksempel viser, hvordan til at kombinere flere strengværdier for at returnere en sammenkædede streng.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### <a name="length---string"></a>længde - streng

**length(String)**

Returnerer antallet af tegn i en streng.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| streng                        |   Ja    | Strengværdi skal bruges til at få antallet tegn.

Et eksempel på brug af længde med en matrix, se [længde - matrix](#length).

I følgende eksempel returneres antallet af tegn i en streng. 

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### <a name="padleft"></a>padLeft

**padLeft (valueToPad, totalLength, paddingCharacter)**

Returnerer en streng, højrejusteret ved at føje tegn til venstre til at kontakte den samlede angivne længde.
  
| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| valueToPad                         |   Ja    | Streng eller int til højrejusteres.
| totalLength                        |   Ja    | Det samlede antal tegn i den returnerede streng.
| paddingCharacter                   |   Nej     | Tegnet, der skal bruges til venstre margen indtil den samlede længde er nået. Standardværdien er et mellemrum.

I følgende eksempel viser, hvordan du tastatur leveret parameterværdi ved at tilføje den nultegn, indtil strengen når 10 tegn. Hvis den oprindelige værdi for parameteren er længere end 10 tegn, tilføjes ingen tegn.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### <a name="replace"></a>Erstat

**Erstat (originalString, oldCharacter, newCharacter)**

Returnerer en ny streng med alle forekomster af et tegn i den angivne streng, der erstattes af et andet tegn.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| originalString                     |   Ja    | Den streng, der indeholder alle forekomster af et tegn, der erstattes af et andet tegn.
| oldCharacter                       |   Ja    | Tegnet, der skal fjernes fra den oprindelige streng.
| newCharacter                       |   Ja    | Tegnet, der tilføjes i stedet for det fjernede tegn.

I følgende eksempel viser, hvordan du fjerner alle stiplet fra den streng, der bruger har udleveret.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### <a name="skip---string"></a>Spring over - streng
**Spring over (originalValue, numberToSkip)**

Returnerer en streng med alle tegn efter det angivne antal i strengen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| originalValue                      |   Ja    | String skal bruges til at springe over.
| numberToSkip                       |   Ja    | Antallet af tegn, der skal gå videre. Hvis denne værdi er 0 eller mindre, returneres alle tegn i strengen. Hvis det er større end længden af strengen, returneres en tom streng. 

Se et eksempel på brug af Spring med en matrix, [springe - matrix](#skip).

I følgende eksempel springer det angivne antal tegn i strengen.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />
### <a name="split"></a>opdele

**Opdel (inputString, delimiterString)**

**Opdel (inputString, delimiterArray)**

Returnerer en matrix af strenge, der indeholder understrenge input strengens, som er adskilt af de angivne afgrænsere.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| inputString                        |   Ja    | Strengen, der skal opdeles.
| afgrænser                          |   Ja    | Afgrænseren, der skal bruges, kan være en enkelt streng eller en matrix af strenge.

I følgende eksempel opdeles input strengen med et komma.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

Det næste eksempel opdeles input strengen med et komma eller semikolon.

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />
### <a name="string"></a>streng

**String(valueToConvert)**

Konverterer den angivne værdi til en streng.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   Ja    | Værdien, der skal konverteres til streng. En hvilken som helst type værdi kan konverteres, herunder objekter og matrixer.

I følgende eksempel konverterer leveret parameterværdierne til strenge.

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### <a name="substring"></a>understreng

**understreng (stringToParse, startIndex, længde)**

Returnerer en understreng, der starter med startposition, angivne tegn og indeholder det angivne antal tegn.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| stringToParse                     |   Ja    | Den oprindelige streng, hvor understrengen udtrækkes.
| startIndex                         | Nej      | Baseret på nul tegn startpositionen for understrengen.
| længde                             | Nej      | Antallet af tegn for understrengen.

I følgende eksempel henter de første tre tegn fra en parameter.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### <a name="take---string"></a>Tag - streng
**Tag (originalValue, numberToTake)**

Returnerer en streng med det angivne antal tegn fra starten af strengen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| originalValue                      |   Ja    | Den streng, der skal tage tegn fra.
| numberToTake                       |   Ja    | Antallet af tegn, der skal tage. Hvis denne værdi er 0 eller mindre, returneres en tom streng. Hvis det er større end længden af den angivne streng, returneres alle tegn i strengen.

Se et eksempel på brug af Tag med en matrix, [tage - matrix](#take).

I følgende eksempel vises det angivne antal tegn fra strengen.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />
### <a name="tolower"></a>toLower

**toLower(stringToChange)**

Konverterer den angivne streng til små bogstaver.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| stringToChange                     |   Ja    | Streng til at konvertere til små bogstaver.

I følgende eksempel konverterer leveret parameterværdi til små bogstaver.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### <a name="toupper"></a>toUpper

**toUpper(stringToChange)**

Konverterer den angivne streng til store bogstaver.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| stringToChange                     |   Ja    | Streng til at konvertere til store bogstaver.

I følgende eksempel konverterer leveret parameterværdi til store bogstaver.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### <a name="trim"></a>Trim

**Trim (stringToTrim)**

Fjerner alle foranstillede og efterstillede blanktegn fra den angivne streng.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| stringToTrim                       |   Ja    | Strengen trimme.

I følgende eksempel fjerner blanktegn fra leveret parameterværdi.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### <a name="uniquestring"></a>uniqueString

**uniqueString (baseString,...)**

Opretter en deterministisk hash streng, der er baseret på de værdier, der er angivet som parametre. 

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| baseString      |   Ja    | Den streng, der bruges i hash-funktionen til at oprette en entydig streng.
| Yderligere parametre efter behov    | Nej       | Du kan tilføje lige så mange strenge efter behov for at oprette den værdi, der angiver niveauet for at er entydige.

Denne funktion er nyttigt, når du har brug at oprette et entydigt navn for en ressource. Du kan angive parameterværdier, der begrænser omfanget af at er entydige for resultatet. Du kan angive, om navnet er entydige ned til abonnement, ressourcegruppe eller installation. 

Den returnerede værdi er ikke en tilfældig streng, men hellere resultatet af en. Den returnerede værdi er 13 tegn. Det er ikke globalt entydige. Du vil kombinere værdien med et præfiks fra din navngivningskonvention for at oprette et navn, der giver mening. I følgende eksempel viser formatet af den returnerede værdi. Den faktiske værdi varierer naturligvis efter de medfølgende parametre.

    tcvhiyu5h2o5o

I følgende eksempler viser, hvordan du bruger uniqueString til at oprette en entydig værdi for ofte anvendte niveauer.

Entydig fastsat til abonnement

    "[uniqueString(subscription().subscriptionId)]"

Entydig fastsat til ressourcegruppe

    "[uniqueString(resourceGroup().id)]"

Entydig fastsat til installation af en ressourcegruppe

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
Følgende eksempel viser, hvordan du opretter et entydigt navn til en lagerplads-konto, der er baseret på dine ressourcegruppe (i denne ressourcegruppe navnet ikke er entydige Hvis opbygget på samme måde).

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### <a name="uri"></a>URI

**URI (baseUri, relativeUri)**

Opretter en absolut URI ved at kombinere baseUri og relativeUri strengen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| baseUri                            |   Ja    | Den grundlæggende uri-streng.
| relativeUri                        |   Ja    | Relativ uri streng til at føje til den grundlæggende uri-streng.

Værdien for parameteren **baseUri** kan medtage en bestemt fil, men kun base stien bruges, når bygning af URI. For eksempel, der passerer **http://contoso.com/resources/azuredeploy.json** som baseUri parameter resultaterne i en grundlæggende URI af **http://contoso.com/resources/**.

I følgende eksempel vises, hvordan du kan oprette et link til en indlejret skabelon baseret på værdien af den overordnede skabelon.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## <a name="array-functions"></a>Matrixfunktioner

Ressourceleder, der indeholder flere funktioner til at arbejde med matrix værdier.

- [Sammenkæd](#concatarray)
- [længde](#length)
- [Spring over](#skip)
- [tage](#take)

En matrix med strengværdier afgrænset af en værdi, kan du se [opdele](#split).

<a id="concatarray" />
### <a name="concat---array"></a>Sammenkæd - matrix

**Sammenkæd (matrix1, matrix2; matrix3;...)**

Kombinerer flere matrixer og returnerer den sammenkædede matrix. 

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| matrix1                        |   Ja    | En matrix til at sammenkæde.
| flere matrixer             |   Nej     | Matrixer til at sammenkæde.

Denne funktion kan tage et vilkårligt antal argumenter, og kan acceptere strenge eller matrixer for parametre. Du kan finde et eksempel på sammenkæde strengværdier, [kontaktformular - streng](#concat).

I følgende eksempel viser, hvordan du kan kombinere to matrixer.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### <a name="length---array"></a>længde - matrix

**length(array)**

Returnerer antallet af elementer i en matrix.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| matrix                        |   Ja    | Matrixen, der skal bruges til at få antallet af elementer.

Du kan bruge denne funktion med en matrix til at angive antallet gentagelser, når du opretter ressourcer. I eksemplet nedenfor vil parameter **siteNames** refererer til en matrix med navne, der skal bruge til at oprette på websteder.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Du kan finde flere oplysninger om at bruge denne funktion med en matrix, kan du se [oprette flere forekomster af ressourcer i Azure ressourcestyring](resource-group-create-multiple.md). 

Du kan finde et eksempel på brug af længde med en strengværdi, [længde - streng](#lengthstring).

<a id="skip" />
### <a name="skip---array"></a>springe - matrix
**Spring over (originalValue, numberToSkip)**

Returnerer en matrix med alle elementer efter det angivne antal i matrixen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| originalValue                      |   Ja    | Matrixen, der skal bruges til at springe over.
| numberToSkip                       |   Ja    | Antallet af elementer for at springe over. Hvis denne værdi er 0 eller mindre, returneres alle elementer i matrixen. Hvis det er større end længden af matrixen, returneres en tom matrix. 

Et eksempel på brug af Spring med en streng, finde [springe - streng](#skipstring).

I følgende eksempel springer det angivne antal elementer i matrixen.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />
### <a name="take---array"></a>Her-matrix
**Tag (originalValue, numberToTake)**

Returnerer en matrix med det angivne antal elementer fra starten af matrixen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| originalValue                      |   Ja    | Matrix tage elementer fra.
| numberToTake                       |   Ja    | Antallet af elementer for at tage. Hvis denne værdi er 0 eller mindre, returneres en tom matrix. Hvis det er større end længden af den angivne matrix, returneres alle elementer i matrixen.

Et eksempel på brug af Tag med en streng, finde [tage - streng](#takestring).

I følgende eksempel vises det angivne antal elementer fra matrixen.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## <a name="deployment-value-functions"></a>Installation værdi funktioner

Ressourceleder, der indeholder følgende funktioner til at hente værdier fra sektioner på en skabelon og værdier, der er relateret til installationen:

- [Installation](#deployment)
- [parametre](#parameters)
- [variabler](#variables)

For at få værdier fra ressourcer, grupper eller abonnementer skal du se [ressource funktioner](#resource-functions).

<a id="deployment" />
### <a name="deployment"></a>Installation

**Deployment()**

Returnerer oplysninger om den aktuelle Installationshandling.

Denne funktion returnerer det objekt, der overføres under installationen. Egenskaberne i det returnerede objekt være forskellige afhængigt af om installationsobjektet der overføres som et link eller som et integrerede objekt. 

Når installationsobjektet er gået integrerede, f.eks, når du bruger parameteren **- TemplateFile** i Azure PowerShell til at pege på en lokal fil, har det returnerede objekt i følgende format:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Når overskredet objektet som et link, som når du bruger parameteren **- TemplateUri** til at pege på et eksternt objekt, der returneres objektet i følgende format. 

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

I følgende eksempel viser, hvordan du bruger deployment() til at oprette et link til en anden skabelon baseret på skabelonen overordnede URI.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### <a name="parameters"></a>parametre

**parametre (parameterName)**

Returnerer en parameterværdi. Den angivne parameternavn skal være defineret i afsnittet parametre i skabelonen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| parameterName                      |   Ja    | Navnet på parameteren til at returnere.

I følgende eksempel viser en forenklet anvendelse af funktionen parametre.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### <a name="variables"></a>variabler

**variabler (variableName)**

Returnerer værdien af variabel. Den angivne variabelnavn skal være defineret i afsnittet variabler i skabelonen.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| variabel navn                      |   Ja    | Navnet på variablen til at returnere.

I følgende eksempel bruges en variabel værdi.

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## <a name="resource-functions"></a>Ressource-funktioner

Ressourceleder, der indeholder følgende funktioner til at få ressourceværdier:

- [listKeys og listen {værdi}](#listkeys)
- [udbydere](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [abonnement](#subscription)

Værdier fra parametre, variabler eller den aktuelle installation, kan du se [installation værdi funktioner](#deployment-value-functions).

<a id="listkeys" />
<a id="list" />
### <a name="listkeys-and-listvalue"></a>listKeys og listen {værdi}

**listKeys (resourceName eller resourceIdentifier, apiVersion)**

**listen {værdi} (resourceName eller resourceIdentifier, apiVersion)**

Returnerer værdier for en ressourcetype, der understøtter handlingen liste. Brugen af de mest almindelige er **listKeys**. 
  
| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| resourceName eller resourceIdentifier |   Ja    | Entydigt id for ressourcen.
| apiVersion                         |   Ja    | API-version af ressource runtime tilstand.

Alle handlinger, der starter med **liste** kan være bruges en funktion i skabelonen. De tilgængelige handlinger omfatter ikke kun **listKeys**, men også handlinger som **liste**, **listAdminKeys**og **listStatus**. For at bestemme, hvilke ressourcetyper have en liste over handling skal du bruge følgende PowerShell-kommando.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

Eller du kan hente på listen med Azure CLI. I følgende eksempel henter alle handlinger for **apiapps**og bruger JSON utility [jq](http://stedolan.github.io/jq/download/) til at filtrere kun handlingerne liste.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"

ResourceId kan angives ved hjælp af [funktionen resourceId](#resourceid) eller ved hjælp af formatet **{providerNamespace} / {ressourcetypen} / {resourceName}**.

I følgende eksempel viser, hvordan til at returnere de primære og sekundære nøgler fra en lagerplads konto i sektionen output.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

Det returnerede objekt fra listKeys har følgende format:

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />
### <a name="providers"></a>udbydere

**udbydere (providerNamespace, [ressourcetypen])**

Returnerer oplysninger om en ressource udbyder og dens understøttede ressourcetyper. Hvis du ikke angiver en ressourcetype, returnerer funktionen alle understøttede datatyper for ressource-udbyder.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| providerNamespace                  |   Ja    | Namespace af provideren
| Ressourcetypen                       |   Nej     | Typen ressource i det angivne navneområde.

Hver understøttede type returneres i følgende format. Er ingen garanti for sortering af matrix.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

I følgende eksempel viser, hvordan du kan bruge funktionen udbyder:

    "outputs": {
        "exampleOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }

<a id="reference" />
### <a name="reference"></a>reference

**reference (resourceName eller resourceIdentifier, [apiVersion])**

Returnerer et objekt, der repræsenterer en anden ressource runtime tilstand.

| Parameter                          | Påkrævet | Beskrivelse
| :--------------------------------: | :------: | :----------
| resourceName eller resourceIdentifier |   Ja    | Navn eller entydigt id for en ressource.
| apiVersion                         |   Nej     | API-version af den angivne ressource. Medtage denne parameter, når ressourcen, der ikke er klargjort i samme skabelon.

Funktionen **reference** henter værdien fra en runtime-tilstand, og derfor bruges ikke i sektionen variabler. Det kan bruges i output sektion af en skabelon.

Ved hjælp af funktionen reference, erklære du implicit, en ressource afhænger af en anden ressource Hvis der refereres til ressourcen er klargjort i samme skabelon. Du behøver ikke at bruge egenskaben **dependsOn** . Funktionen evalueres ikke, indtil der refereres til ressourcen har fuldført installation.

I følgende eksempel refererer til en lagerplads-konto, der er implementeret i den samme skabelon.

    "outputs": {
        "NewStorage": {
            "value": "[reference(parameters('storageAccountName'))]",
            "type" : "object"
        }
    }

I følgende eksempel refererer til en lagerplads-konto, der ikke er implementeret i denne skabelon, men der findes i den samme ressourcegruppe som de ressourcer, der installeres.

    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }

Du kan hente en bestemt værdi fra den returnerede objekt, som blob slutpunktet URI, som vist i følgende eksempel.

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

I følgende eksempel refererer til en lagerplads konto i en anden ressourcegruppe.

    "outputs": {
        "BlobUri": {
            "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

Egenskaber på det objekt, der returneres fra funktionen **reference** varierer afhængigt af ressourcetype. Oprette en simpel skabelon, der returnerer objektet i sektionen **skriver** for at få vist de navne og værdier for en ressourcetype. Hvis du har en eksisterende ressource af den pågældende type, returnerer skabelonen lige objektet uden at installere nye ressourcer. Hvis du ikke har en eksisterende ressource af den pågældende type, installeres kun denne type skabelonen, og returnerer objektet. Tilføj derefter disse egenskaber til andre skabeloner, der skal hente værdierne dynamisk under installationen. 

<a id="resourcegroup" />
### <a name="resourcegroup"></a>resourceGroup

**resourceGroup()**

Returnerer et objekt, der repræsenterer den aktuelle ressourcegruppe. 

Det returnerede objekt er i følgende format:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

I følgende eksempel bruges ressource gruppe placering til at tildele en placering til et websted.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### <a name="resourceid"></a>resourceId

**resourceId ([subscriptionId], [resourceGroupName] ressourcetypen, resourceName1, [resourceName2] …)**

Returnerer det entydige id for en ressource. 
      
| Parameter         | Påkrævet | Beskrivelse
| :---------------: | :------: | :----------
| subscriptionId    |   Nej     | Standardværdien er det aktuelle abonnement. Angiv denne værdi, når du har brug at hente en ressource i et andet abonnement.
| resourceGroupName |   Nej     | Standardværdien er aktuelle ressourcegruppe. Angiv denne værdi, når du har brug at hente en ressource i en anden ressourcegruppe.
| Ressourcetypen      |   Ja    | Type ressource, herunder ressource udbyder navneområde.
| resourceName1     |   Ja    | Navnet på ressource.
| resourceName2     |   Nej     | Næste ressource navn segment Hvis ressource er indlejret.

Du kan bruge denne funktion, når ressourcenavnet er tvetydige eller ikke klargjort i den samme skabelon. Id returneres i følgende format:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

I følgende eksempel viser, hvordan til at hente ressource-id'erne for et websted og en database. Webstedet findes i en ressourcegruppe med navnet **myWebsitesGroup** og databasen findes i den aktuelle ressourcegruppe for denne skabelon.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
Ofte, skal du bruge denne funktion, når du bruger en lagerplads konto eller et virtuelt netværk i en alternativ ressourcegruppe. Lagerplads konto eller virtuelt netværk kan anvendes på tværs af flere ressourcegrupper. Derfor vil ikke du slette dem, når du sletter en enkelt ressourcegruppe. I følgende eksempel viser, hvordan en ressource fra en ekstern ressourcegruppe kan nemt bruges:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### <a name="subscription"></a>abonnement

**Subscription()**

Returnerer oplysninger om abonnementet i følgende format.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

I følgende eksempel viser funktionen abonnement kaldes i afsnittet output. 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## <a name="next-steps"></a>Næste trin
- Se en beskrivelse af sektionerne i en skabelon til Azure ressourcestyring [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)
- Hvis du vil flette flere skabeloner, skal du se [Brug af sammenkædede skabeloner med Azure ressourcestyring](resource-group-linked-templates.md)
- Til forbedrer et angivet antal gange når du opretter en type ressource, kan du se [oprette flere forekomster af ressourcer i Azure ressourcestyring.](resource-group-create-multiple.md)
- Hvis du vil se, hvordan du installerer den skabelon, du har oprettet, se [Implementer et program med Azure ressourcestyring skabelon](resource-group-template-deploy.md)

