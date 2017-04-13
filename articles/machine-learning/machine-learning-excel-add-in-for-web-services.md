<properties
    pageTitle="Excel-tilføjelsesprogrammet til webtjenester Machine Learning | Microsoft Azure"
    description="Sådan bruges Azure Machine Learning webtjenester direkte i Excel uden at skrive kode."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Excel-tilføjelsesprogrammet til Azure Machine Learning Web-tjenester

Excel gør det nemt at ringe til webtjenester direkte uden brug af til at skrive en kode.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Trin til at bruge en eksisterende webtjeneste i projektmappen

1. Åbn [Eksempel på Excel-fil](http://aka.ms/amlexcel-sample-2), som indeholder Excel-tilføjelsesprogrammet og data om passagerer på Titanic.
2. Vælg webtjenesten ved at klikke på den-"Titanic efterladt indikator for stigning (eksempel Excel-tilføjelsesprogram) [resultat]" i dette eksempel.

    ![Vælg webtjeneste][01]

3. Dette kommer du til sektionen **Predict** .  Denne projektmappe indeholder allerede eksempeldata, men til en tom projektmappe kan du markere en celle i Excel og klikke på **Brug eksempeldata**.
4. Markér dataene med sidehoveder, og klik på ikonet inputdataene område.  Kontrollér, at feltet "Mine data indeholder overskrifter" er markeret.
5. Under **Output**, Angiv antallet celle, hvor du vil output skal være, for eksempel "H1" her.
6. Klik på **forudsige**.

    ![Forudsige sektion][02]

## <a name="steps-to-add-a-new-web-service"></a>Trin til at tilføje en ny webtjeneste

Installere en webtjeneste, eller brug en eksisterende webtjeneste. Finde flere oplysninger om installation af en webtjeneste, [gennemgang trin 5: installere Azure Machine Learning webtjenesten](machine-learning-walkthrough-5-publish-web-service.md).

Få API-nøgle til din webtjeneste. Hvor du gør dette afhænger af, om du har udgivet en klassisk Machine Learning-webtjeneste af en ny computer Learning-webtjeneste.

**Bruge en klassisk webtjeneste** 

1. Klik på afsnittet **WEB SERVICES** i venstre rude i Machine Learning Studio, og vælg derefter webtjenesten.

    ![Studio Vælg en webtjeneste][04]

2. Kopiér API-nøgle til webtjenesten.

    ![Studio API-nøgle][05]

3. Klik på linket **Anmodning/svar** under fanen **DASHBOARD** for webtjenesten.
4. Se efter sektionen **Request URI** .  Kopier og Gem URL-adressen.

>[AZURE.NOTE] Det er nu muligt at logge på portalen [Azure Machine Learning Web Services](https://services.azureml.net) til at hente API-nøglen til en klassisk Machine Learning-webtjeneste.

**Bruge en ny webtjeneste**

1. Klik på **Web Services**i portalen [Azure Machine Learning Web Services](https://services.azureml.net) , og derefter vælge din webtjeneste. 
2. Klik på **forbruge**.
3. Se efter sektionen **grundlæggende forbrug info** . Kopier og Gem den **Primærnøgle** og **Anmodning om svar** URL-adressen.


## <a name="steps-to-add-a-new-web-service"></a>Trin til at tilføje en ny webtjeneste

1. Installere en webtjeneste, eller brug en eksisterende webtjeneste. Finde flere oplysninger om installation af en webtjeneste, [gennemgang trin 5: installere Azure Machine Learning webtjenesten](machine-learning-walkthrough-5-publish-web-service.md).
2. Klik på **forbruge**.
3. Se efter sektionen **grundlæggende forbrug info** . Kopier og Gem den **Primærnøgle** og **Anmodning om svar** URL-adressen.
2. I Excel skal du gå til afsnittet **Web Services** (Hvis du er i sektionen **Predict** , skal du klikke på pilen tilbage for at gå til listen over webtjenester).

    ![Gå til Web service markering][03]
    
3. Klik på **Tilføj webtjeneste**.
4. Indsæt URL-adressen i Excel-tilføjelsesprogram tekstfeltet med navnet **URL-adresse**.
5. Indsæt API/primær nøgle i tekstfeltet mærket **API-nøgle**.
6. Klik på **Tilføj**.

    ![URL-adresse og API nøgle til en klassisk webtjeneste.][06]

10. Hvis du vil bruge webtjenesten, skal du følge foregående anvisninger, "Trin til at bruge en eksisterende webtjeneste".

## <a name="sharing-your-workbook"></a>Dele din projektmappe

Hvis du gemmer din projektmappe, gemmes API/primær nøglen til webtjenester, du har tilføjet også. Det betyder, at du skal kun dele projektmappen med personer, du har tillid til.

Stille spørgsmål i afsnittet nedenfor kommentar eller på vores [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
