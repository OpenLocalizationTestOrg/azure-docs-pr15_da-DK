<properties
    pageTitle="Fejlfinding: Oprette og oprette forbindelse til et arbejdsområde til Machine Learning | Microsoft Azure"
    description="Løsninger på almindelige problemer i oprette og oprette forbindelse til et Azure Machine Learning arbejdsområde"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Fejlfinding i forbindelse med vejledning: oprette og oprette forbindelse til en Machine Learning arbejdsområde

Denne vejledning indeholder løsninger til nogle ofte stødt på udfordringer, når du indstiller Azure Machine Learning arbejdsområder.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Arbejdsområde ejer

Når du opretter et nyt arbejdsområde Machine Learning, det ID, du angiver i feltet ARBEJDSOMRÅDE ejer skal være en gyldig Microsoft-konto (tidligere Windows Live ID), for eksempel john-contoso@live.com eller john-contoso@hotmail.com. Det kan være en ikke-Microsoft-konto, som din virksomhedsmailkonto. Hvis du vil oprette en gratis Microsoft-konto, skal du gå til [www.live.com](http://www.live.com).

Bemærk, at den konto, du brugte til at logge på portalen Azure til at oprette arbejdsområdet automatisk ikke har tilladelse til at *åbne* dette arbejdsområde, medmindre du angiver kontoen som ejer. Hvis du vil åbne et arbejdsområde i Machine Learning Studio, skal du være logget på den Microsoft-Account, som blev defineret som ejeren af arbejdsområdet, eller du har brug at modtage en invitation fra ejeren til at deltage i arbejdsområdet. Fra portalen Azure klassisk kan du dog *administrere* arbejdsområdet, som omfatter muligheden for at ændre ejeren og konfigurerer adgang.

Se [administrere en Azure Machine Learning arbejdsområde]kan finde flere oplysninger om administration af et arbejdsområde.

[Administrere et Azure Machine Learning arbejdsområde]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Tilladte områder

Machine Learning findes i øjeblikket i et begrænset antal områder. Hvis dit abonnement ikke inkluderer en af disse områder, vises der muligvis en fejlmeddelelse om, "Du har ingen abonnementer i de tilladte områder."

For at anmode om, at et område føjes til dit abonnement, skal du markere **Kontakt Microsoft Support** fra portalen Azure klassisk, vælge **Fakturering** som problemtype og følg vejledningen for at sende din anmodning.

![Kontakt Microsoft support][screen1]

## <a name="storage-account"></a>Lagerplads konto

Tjenesten Machine Learning skal have en lagerplads konto til at gemme data. Du kan bruge en eksisterende lagerplads-konto, eller du kan oprette en ny konto med lagerplads, når du opretter nye Machine Learning arbejdsområdet (Hvis du har kvoter til at oprette en ny lagerplads konto).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Opret arbejdsområde][screen2]

Når det nye Machine Learning arbejdsområde er oprettet, kan du logge på Machine Learning Studio ved hjælp af den Microsoft-konto, du har angivet som ejer af arbejdsområdet. Hvis du støder på fejlmeddelelsen, "Arbejdsområdet blev ikke fundet" (svarende til følgende skærmbillede), skal du bruge følgende trin til at slette browsercookies din.

![Arbejdsområde, der blev ikke fundet][screen3]

**At slette browsercookies**

Hvis du bruger Internet Explorer, skal du klikke på knappen **værktøjer** i øverste højre hjørne, og vælg **Internetindstillinger**.  

![Internetindstillinger][screen4]

Under fanen **Generelt** skal du klikke på **Slet...**

![Under fanen Generelt][screen5]

Sørg for, at **Cookies og websted data** er markeret i dialogboksen **Slet browserdata** , og klik på **Slet**.

![Slette cookies][screen6]

Når cookies er blevet slettet, genstart browseren, og derefter gå til siden [Microsoft Azure Machine Learning](https://studio.azureml.net) . Når du bliver spurgt om brugernavn og adgangskode, Angiv den samme Microsoft-konto, du har angivet som ejer af arbejdsområdet.

Vores mål er at gøre den maskine Learning oplevelse som problemfri som muligt. Sende alle kommentarer og problemer på [Azure Machine Learning forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) til at hjælpe os med at betjene dig bedre.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
