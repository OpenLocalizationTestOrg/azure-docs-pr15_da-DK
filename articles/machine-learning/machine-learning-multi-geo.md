<properties
   pageTitle="Flere geografisk hjælp | Microsoft Azure"
   description="Lær at oprette et arbejdsområde og udgive en webtjeneste på et Azure område, der er forskellige fra de syd centrale USA (SCUS) Azure område."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Flere geografisk hjælp

I denne artikel beskrives, hvordan du kan oprette et arbejdsområde og udgive en webtjeneste i andre Azure områder.

## <a name="create-a-workspace"></a>Oprette et arbejdsområde

1. Log på portalen Azure klassisk.

2.  Klik på **+ Ny** > **DATATJENESTER** > **MACHINE LEARNING** > **Hurtig Opret**.  Vælg et andet område, som **Sydøstasien**under **placering** .
![Flere geografisk Hjælp billede 1][1]
3. Vælg arbejdsområdet, og klik derefter på **Log på ML Studio**.
![Flere geografisk Hjælp billede 2][2]

4. Du har nu et arbejdsområde i et andet område, som du kan bruge ligesom andre arbejdsområde. For at skifte mellem dine arbejdsområder, skal du se til øverste højre hjørne af skærmen. Klik på rullepilen, Vælg området, og vælg derefter arbejdsområdet. Alt er lokale for arbejdsområde område; for eksempel bliver alle dine webtjenester, der er oprettet ud fra et arbejdsområde i arbejdsområdet er placeret i samme område.
![Billede af Multi-geografisk Hjælp 3][3]

## <a name="open-an-experiment-from-gallery"></a>Åbne et forsøg fra galleriet

Hvis du åbner et forsøg fra galleriet, kan du også vælge det område, du vil kopiere forsøget til.

![Billede af Multi-geografisk Hjælp 4][4a]

## <a name="web-service-management"></a>Administration af Web-tjenester

Brug områdespecifik adresse for at administrere ved hjælp af programmering web-tjenester, f.eks til omskoling:
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Ting, du skal være opmærksom

1.  Du kan kun kopiere forsøg mellem arbejdsområder, der hører til samme region denne måde. Hvis du vil kopiere forsøg på tværs af arbejdsområder i forskellige områder, kan du bruge [PowerShell](http://aka.ms/amlps) cmdlet accepterer [*Kopi AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) for at udføre dette. En anden løsning er at publicere forsøget i galleriet i en tilstand, åbne den i arbejdsområdet fra andet området.
2.  Valgknappen område viser kun arbejdsområder for ét område ad gangen. I fremtiden, vil du kunne se en komplet liste over arbejdsområder, du har adgang til på tværs af alle områder på samme tid.  
3.  Et gratis arbejdsområde eller gæsteadgang (anonyme) arbejdsområde oprettes og vært syd centrale USA I fremtiden, vil du kunne oprette Free/gæsteadgang arbejdsområder i det område, som du vælger.  
4.  Webtjenester installeres fra et arbejdsområde i Sydøstasien kan også bruges i Sydøstasien. Fremover, du vil kunne har fleksibilitet for at oprette forsøg i ét område, og implementere genereres web service slutpunkter i forskellige områder.  

## <a name="more-information"></a>Få mere at vide

Stille et spørgsmål på [Azure Machine Learning-forummet](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
