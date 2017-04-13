<properties
    pageTitle="Fejlfinding i forbindelse med Retraining til en webtjeneste, Azure Machine Learning klassisk | Microsoft Azure"
    description="Finde og rette almindelige problemer med encounted, når du omskoling modellen til en webtjeneste til Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Fejlfinding i forbindelse med omskoling af en Azure Machine Learning klassisk-webtjeneste

## <a name="retraining-overview"></a>Omskoling oversigt

Det er en statisk model, når du installerer en skønnet forsøg som en vurdering webtjeneste. Som nye data bliver tilgængelig, eller når destination for API har deres egne data, skal være retrained modellen. 

En komplet gennemgang af retraining processen med en klassisk webtjeneste, kan du se [Henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>Omskoling proces

Når du har brug at omskole webtjenesten, skal du tilføje nogle flere stykker:

* En webtjeneste, der installeres fra kursus forsøget. Forsøget skal have et **Web Service Output** modul, der er knyttet til output fra modulet **Tog Model** .  

    ![Vedhæft web service output til tog modellen.][image1]

* Et nyt slutpunkt, der er føjet til din vurdering webtjeneste.  Du kan tilføje slutpunktet automatisk med eksempelkoden, der refereres til i henblik på omstilling Machine Learning modellerne fra et program emne eller via portalen Azure klassisk.

Du kan derefter bruge eksempel C#-koden fra den kursus webtjeneste API Hjælp-siden for at omskole model. Når du har evalueret resultaterne, og du er tilfreds med dem, kan du opdatere erfaren modellen vundne webtjeneste ved hjælp af det nye ark, du har tilføjet.

Med alle elementerne på plads er de overordnede trin, du skal bruge for at omskole modellen på følgende måde:

1.  Ringe til webtjenesten kursus: opkaldet er til den batchen udførelse af Service (BES), ikke på Anmod om svar Service (Ressourceposter). Du kan bruge eksempel C#-koden på siden API hjælp til at foretage opkaldet. 
2.  Finde værdierne for den *BaseLocation*, *RelativeLocation*og *SasBlobToken*: disse værdier returneres i output fra dit opkald til webtjenesten kursus. 
      ![Viser output fra eksemplet retraining og BaseLocation, RelativeLocation og SasBlobToken værdier.][image6]
3.  Opdatere tilføjede slutpunktet fra vurdering webtjenesten med den nye erfarne model: ved hjælp af den eksempelkode, der er angivet i henblik på omstilling Machine Learning modellerne fra et program, opdatere det nye slutpunkt, du har føjet til vurdering modellen med den nyligt erfaren model fra webtjeneste kursus.

## <a name="common-obstacles"></a>Almindelige hindringer

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Kontrollér, om du har den korrekte programrettelse URL-adresse

PROGRAMRETTELSE URL-adressen du bruger skal være knyttet til den nye vurdering slutpunkt, du har føjet til vurdering webtjenesten. Der er flere måder at få den programrettelse URL-adresse:

**Indstilling 1: programmeringsmæssigt**

Sådan får du den korrekte programrettelse URL-adresse:

1.  Køre [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) eksempelkode.
2.  Finde værdien *HelpLocation* fra outputtet af AddEndpoint, og Kopiér URL-adressen.

    ![HelpLocation i outputtet i eksemplet addEndpoint.][image2]

3.  Indsæt URL-adressen i en browser for at gå til en side, der indeholder links til hjælp til webtjenesten.
4.  Klik på linket **Opdater ressource** for at åbne siden programrettelse Hjælp.

**Mulighed 2: Bruge Azure klassisk portal**

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com).
2.  Åbn fanen Machine Learning. 
     ![Maskine læner op ad fanen.][image4]
3.  Klik på navnet på din arbejdsområde, derefter **Webtjenester**.
4.  Klik på den vurdering webtjeneste, du arbejder med. (Hvis du ikke ændre standardnavnet for webtjenesten, det afsluttes i [vundne EKSP.].)
5.  Klik på **Tilføj slutpunkt**.
6.  Når slutpunktet er tilføjet, skal du klikke på navnet på. Klik derefter på **Ressource til opdatering af** for at åbne Hjælpsiden rettelse.

>[AZURE.NOTE] Hvis du har tilføjet slutpunktet til webtjenesten kursus i stedet for skønnet webtjenesten, får du vist følgende fejlmeddelelse når du klikker på linket **Opdater ressource** : Vi beklager, men denne funktion er ikke understøttet eller tilgængelig i denne kontekst. Denne webtjeneste ikke har nogen kan opdateres ressourcer. Vi beklager gener for brugerne og arbejder på forbedring af arbejdsprocessen.

![Nyt slutpunkt dashboard.][image3]

Siden programrettelse Hjælp indeholder programrettelse URL-adressen skal du bruge og giver eksempelkode, du kan bruge til at ringe til den.

![Programrettelse URL-adressen.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Kontrollér, at du opdaterer det korrekte resultat slutpunkt

* Ikke programrettelse webtjenesten kursus: Handlingen programrettelse skal udføres på vurdering webtjenesten.
* Ikke programrettelse standardslutpunktet på webtjeneste: Handlingen programrettelse skal udføres på ny vurdering Web tjenesteslutpunkt, du har tilføjet.

Du kan kontrollere hvilke webtjeneste slutpunktet er som at gå til Azure klassisk portalen. 

>[AZURE.NOTE] Sørg for, at du tilføjer slutpunktet til skønnet webtjenesten, ikke kursus webtjeneste. Hvis du har korrekt installeret både et kursus og en skønnet webtjeneste, skal du se to separate webtjenester vises. Skønnet webtjenesten skal slutte med "[skønnet EKSP.]".

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com).
2.  Åbn fanen Machine Learning. 
     ![Machine learning arbejdsområde brugergrænseflade.][image4]
3.  Vælg dit arbejdsområde.
4.  Klik på **Web-tjenester**.
5.  Vælg din skønnet webtjeneste.
6.  Kontrollere, at din nye slutpunkt blev føjet til webtjenesten.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Markér det arbejdsområde, som din webtjeneste er i at sikre, at det er i det rigtige område

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com).
2.  Vælg Machine Learning i menuen.
      ![Machine learning område brugergrænseflade.][image4]
3.  Kontroller placeringen af arbejdsområdet.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png