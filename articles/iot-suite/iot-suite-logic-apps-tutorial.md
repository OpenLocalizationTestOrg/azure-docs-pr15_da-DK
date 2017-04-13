<properties
  pageTitle="Azure IoT pakke og logik Apps | Microsoft Azure"
  description="Et selvstudium om, hvordan du logge på logik Apps til Azure IoT pakke for forretningsproces."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"/>
  
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Selvstudium: Oprette forbindelse logik App til din Azure IoT pakke Remote overvågning forudkonfigureret løsning

[Microsoft Azure IoT Suite] [ lnk-internetofthings] remote overvågning forudkonfigurerede løsning er en god måde at komme hurtigt i gang med en til slut funktionssæt, der er et eksempel på en IoT løsning. Dette selvstudium vejleder dig gennem hvordan du føjer logik App til din Microsoft Azure IoT pakke remote overvågning forudkonfigurerede løsning. Nedenstående trin viser, hvordan du kan udføre din IoT løsning yderligere ved at oprette forbindelse til en forretningsproces.

_Hvis du vil have en gennemgang på Sådan klargøres en ekstern måling forudkonfigureret løsning, se [Selvstudium: Introduktion til IoT forudkonfigureret løsningerne][lnk-getstarted]._

Inden du starter selvstudiet, skal du gøre følgende:

- Klargøre remote overvågning forudkonfigurerede løsningen i abonnementet Azure.

- Oprette en SendGrid konto, der gør det muligt at sende en mail, der udløser din forretningsproces. Du kan tilmelde dig en gratis prøveversion konto på [SendGrid](https://sendgrid.com/) ved at klikke på **Prøv gratis**. Når du har registreret for din gratis prøveversion konto, skal du oprette en [API-nøgle](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) i SendGrid, der giver tilladelse til at sende mail. Du skal bruge denne API-nøgle senere i selvstudiet.

Hvis du allerede har klargjort din remote overvågning forudkonfigureret løsning, gå til ressourcegruppen for den pågældende løsning på [Azure portal][lnk-azureportal]. Ressourcegruppen har samme navn som navnet på løsningen du vælger, når du har klargjort din remote overvågning løsning. I ressourcegruppen, kan du se alle de klargjorte Azure ressourcer til din løsning med undtagelse af Azure Active Directory-programmet, som du kan finde i portalen Azure klassisk. Følgende skærmbillede viser et eksempel **ressourcegruppe** blade til en ekstern overvågning forudkonfigurerede løsning:

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

For at starte skal du Konfigurer appen logik, der skal bruges sammen med den forudkonfigurerede løsning.

## <a name="set-up-the-logic-app"></a>Konfigurere Appen logik

1. Klik på __Tilføj__ øverst i din ressource gruppe blade i portalen Azure.

2. Søge efter __Logik App__, markere det, og klik derefter på **Opret**.

3. Udfyld __navn__ , og brug den samme **abonnement** og **ressourcegruppe** , du brugte, da du klargjort din remote overvågning løsning. Klik på __Opret__.

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. Når installationen er fuldført, kan du se logik App er angivet som en ressource i din ressourcegruppe.

5. Klik på den logik App til at gå til bladet logik App skal du vælge skabelonen **Tom logik App** til at åbne **Logik Apps Designer**.

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. Vælg __anmodning__. Denne handling angiver, at en indgående HTTP-anmodning med en bestemt JSON formateret data handlinger som en udløser.

7. Indsæt følgende i anmode om brødteksten JSON skemaet:

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    > [AZURE.NOTE] Du kan kopiere URL-adressen på HTTP-post, når du gemmer logik app, men først skal du føje en handling.

8. Klik på __+ nyt trin__ under din manuel udløser. Klik derefter på **Tilføj en handling**.

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. Søg efter **SendGrid - sende mail** , og klik på den.

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. Angiv et navn til forbindelsen, som **SendGridConnection**skal du angive den **SendGrid API-nøgle** , du har oprettet, når du konfigurerer kontoen SendGrid, og klik på **Opret**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. Tilføj mailadresser, at du ejer til både **fra** og **til** felter. Føje **eksterne overvågning besked [DeviceId]** til feltet **emne** . Tilføje i feltet **Mail brødtekst** **enhed [DeviceId] har rapporteret [measurementName] med værdi [measuredValue].** Du kan tilføje **[DeviceId]**, **[measurementName]**og **[measuredValue]** ved at klikke på i sektionen **du kan indsætte data fra de forrige trin** .

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. Klik på __Gem__ i den øverste menu.

13. Klik på den **anmode om** udløser og kopiere værdien __Http Post til denne URL-adresse__ . Denne URL-adresse skal du senere i dette selvstudium.

> [AZURE.NOTE] Logik Apps gør det muligt at køre [mange forskellige typer handling] [ lnk-logic-apps-actions] herunder handlinger i Office 365. 

## <a name="set-up-the-eventprocessor-web-job"></a>Konfigurere EventProcessor Web jobbet

I dette afsnit, skal tilslutte du din forudkonfigurerede løsning til Appen logik, du har oprettet. For at fuldføre denne opgave skal tilføje du URL-adressen for at udløse Appen logik til den handling, der udløses, når en enhed føler værdi er større end en grænseværdi.

1. Bruge kunden ciffer for at klone den seneste version af det [azure-iot-remote-overvågning github lager][lnk-rmgithub]. Eksempel:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. I Visual Studio, skal du åbne __RemoteMonitoring.sln__ fra den lokale kopi af lageret.

3. Åbn filen __ActionRepository.cs__ i den **infrastruktur\\lager** mappe.

4. Opdatere **actionIds** ordbogen med __Http Post til denne URL-adresse__ , du skrev ned fra din logik App således:

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. Gemme ændringerne i løsningen, og Afslut Visual Studio.

## <a name="deploy-from-the-command-line"></a>Installere fra kommandolinjen

I dette afsnit, skal installere du den opdaterede version af remote overvågning løsningen at erstatte den version, der er i øjeblikket kører i Azure.

1. Følge [Udviklingscenter konfigureres] [ lnk-devsetup] instruktioner til konfiguration af dit miljø til installation.

2.  Hvis du vil installere lokalt, skal du følge den [lokale installation] [ lnk-localdeploy] instruktioner.

3.  For at distribuere i skyen og opdatere din eksisterende cloud-installation, skal du følge den [skyen installation] [ lnk-clouddeploy] instruktioner. Brug navnet på din oprindelige installation som navnet på installation. For eksempel, hvis den oprindelige installation hedder **demologicapp**, bruge følgende kommando:

    ``
    build.cmd cloud release demologicapp
    ``
    
    Når build scriptet kører, skal du sørge for at bruge den samme Azure-konto, abonnement, region og Active Directory-forekomst, du brugte, da du klargjort løsningen.

## <a name="see-your-logic-app-in-action"></a>Se din logik App i praksis

Remote overvågning forudkonfigurerede løsningen har to regler, der er angivet som standard, når du klargør en løsning. Begge regler er på **SampleDevice001** enheden:

* Temperaturen > 38.00
* Luftfugtighed > 48.00

Temperaturen reglen udløser handlingen **Hæve Alarm** og luftfugtighed reglen udløser handlingen **SendMessage** . Hvis du brugte den samme URL-adresse for begge handlinger klassen **ActionRepository** , udløser din logik app for hver regel. Begge regler Brug SendGrid til at sende en mail til adressen **til** med oplysninger om beskeden.

> [AZURE.NOTE] Appen logik fortsætter med at udløse, hver gang i grænseværdi er opfyldt. For at undgå unødvendig mails, kan du enten deaktiverer reglerne i din løsning portal eller deaktivere logik App på [Azure portal][lnk-azureportal].

Ud over modtager mails, kan du også se når logik app'en kører i portalen:

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Næste trin

Nu hvor du har brugt en logik App til at oprette forbindelse forudkonfigurerede løsningen til en forretningsproces, kan du lære mere om indstillinger til tilpasning af de forudkonfigurerede løsninger:

- [Brug dynamiske telemetri med remote overvågning forudkonfigureret løsning][lnk-dynamic]
- [Enhed oplysninger metadata i remote overvågning forudkonfigureret løsning][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
