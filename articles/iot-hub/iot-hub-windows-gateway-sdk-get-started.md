<properties
    pageTitle="Komme i gang med IoT Hub Gateway SDK | Microsoft Azure"
    description="Azure IoT Gateway SDK gennemgang ved hjælp af Windows til at illustrere vigtige begreber, skal du vide, når du bruger Azure IoT Gateway SDK."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>Azure IoT Gateway SDK (beta) - Introduktion til brug af Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Sådan oprettes stikprøvernes

Før du går i gang, skal du [konfigurere din udviklingsmiljø] [ lnk-setupdevbox] til at arbejde med SDK i Windows.

1. Åbn en kommandoprompt **udvikler kommandoprompten for VS2015** .
2. Gå til rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager.
3. Køre den **værktøjer\\build.cmd** script. Dette script opretter en fil til løsning af Visual Studio, opbygger løsningen og kører testene. Du kan finde Visual Studio-løsning i mappen **opbygge** i din lokale kopi af **azure-iot-gateway-sdk** -lager.

## <a name="how-to-run-the-sample"></a>Sådan køres stikprøvernes

1. Scriptet **build.cmd** opretter en mappe med navnet **opbygge** i din lokale kopi af lageret. Denne mappe indeholder de to moduler, der bruges i dette eksempel.

    Scriptet build placerer **logger_hl.dll** i den **opbygge\\moduler\\logføring\\fejlfinding** mappe og **hello_world_hl.dll** i den **opbygge\\moduler\\hello_world\\fejlfinding** mappe. Brug disse URL-adresser for værdien **modul sti** , som vist i indstillingsfil JSON nedenfor.

2. Filen **hello_world_win.json** i den **eksempler\\hello_world\\src** mappen er et eksempel JSON-indstillingsfil til Windows, du kan bruge til at køre eksemplet. Eksempel JSON indstillinger vist nedenfor antages det, at du klonet IoT Gateway SDK lager i roden af din **C:** -drevet. Hvis du har hentet den til en anden placering, du vil justere **modul sti** værdierne i den **eksempler\\hello_world\\src\\hello_world_win.json** fil i overensstemmelse hermed.

3. Indstil værdien **filnavn** til navnet og stien til filen, der indeholder dataene i logfilen i sektionen **argumenter** for modulet **logger_hl** .

    Dette er et eksempel på en JSON indstillingsfil til Windows, der skriver til filen **log.txt** i roden af din **C:** -drevet.

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. Gå til rodmappen i din lokale kopi af **azure-iot-gateway-sdk** lager ved en kommandoprompt.
4. Kør følgende kommando:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md