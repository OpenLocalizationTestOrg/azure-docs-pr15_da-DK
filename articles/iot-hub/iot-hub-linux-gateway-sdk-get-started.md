<properties
    pageTitle="Komme i gang med IoT Hub Gateway SDK | Microsoft Azure"
    description="Denne gennemgang Azure IoT Gateway SDK bruger Linux til at illustrere vigtige begreber, skal du vide, når du bruger Azure IoT Gateway SDK."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>Azure IoT Gateway SDK (beta) - Introduktion til brug af Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Sådan oprettes stikprøvernes

Før du går i gang, skal du [konfigurere din udviklingsmiljø] [ lnk-setupdevbox] til at arbejde med SDK på Linux.

1. Åbn en shell.
2. Gå til rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager.
3. Kør scriptet **tools/build.sh** . Dette script bruger værktøjet **cmake** til at oprette en mappe med navnet **opbygge** i rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager og generere en makefile. Scriptet derefter opbygger løsningen og kører testene.

> [AZURE.NOTE]  Hver gang du kører scriptet **build.sh** , slettes, og derefter genopretter mappen **opbygge** i rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager.

## <a name="how-to-run-the-sample"></a>Sådan køres stikprøvernes

1. Scriptet **build.sh** genererer outputtet i mappen **opbygge** i din lokale kopi af **azure-iot-gateway-sdk** -lager. Dette omfatter de to moduler, der bruges i dette eksempel.

    Scriptet build placerer **liblogger_hl.so** i den **moduler-build/logføring/** mappe og **libhello_world_hl.so** i den **build/moduler/hello_world/** mappe. Brug disse URL-adresser for værdien **modul sti** , som vist i indstillingsfil JSON nedenfor.

2. Filen **hello_world_lin.json** i mappen **eksempler/hello_world/src** er et eksempel JSON-indstillingsfil til Linux, som du kan bruge til at køre eksemplet. Eksempel JSON indstillinger vist nedenfor antages det, du vil køre eksemplet i roden af din lokale kopi af **azure-iot-gateway-sdk** -lager.

3. Indstil værdien **filnavn** til navnet og stien til filen, der indeholder dataene i logfilen i sektionen **argumenter** for modulet **logger_hl** .

    Dette er et eksempel på en fil med JSON indstillinger for Linux, der skriver til **log.txt** til den mappe, hvor du kører stikprøvernes.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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

3. I din shell, skal du gå til **azure-iot-gateway-sdk** mappe.
4. Kør følgende kommando:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
