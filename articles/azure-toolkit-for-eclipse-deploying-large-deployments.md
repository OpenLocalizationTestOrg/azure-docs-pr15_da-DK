<properties
    pageTitle="Anvendelse af store installationer"
    description="Få mere at vide, hvordan du kan installere store installationer med Azure-værktøjskassen til Eklipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Anvendelse af store installationer #

Hvis din installation er for stor til at indgå i approot standardmappen, kan du bruge et lokalt lager ressource som rodmappen installation til din JDK og programserver.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Bruge et lokalt lager ressource som rodmappen installation for store installationer ##

1. Oprette en ny lokal lagerplads ressource. Navnet på ressourcen, betyder ikke noget. Lagerplads ressourcer er defineret på niveauet for rolle. Den hurtigste måde at få adgang til lokale lager konfigurationsdialogboksen, hvorfra du kan oprette en ny lokal lagerplads ressource er ved at benytte følgende fremgangsmåde: Højreklik på rollen i visningen **Projektstifinder** (udvide din Azure projektnode, hvis du ikke kan se rollen), klik på **Azure**, og klik derefter på **Lokale lager**. Klik på **Tilføj** for at oprette en ny lokal lagerplads ressource i dialogboksen **Lokale lager** .
1. Angiv den ønskede størrelse til mindst 2048 MB (noget mindre kan medføre den samme fil størrelse problemer som du kan støde på i approot).
1. Sørg for, at **rydde indholdet, når forekomsten af rolle er flyttet til papirkurven** er markeret; Dette vil hjælpe med at forhindre, at den installation Start logik støder på konflikter med eksisterende filer i ressourcen, når den rolle forekomst er flyttet til papirkurven.
1. Sørg for, at værdien **miljøvariablen lagring af ressourcens mappesti efter installation** er indstillet til strengen **DEPLOYROOT**. Din lokale lager ressource dialogboksen ser nogenlunde sådan følgende.
    ![][ic667943]

Hvis du bruger **DEPLOYROOT** som *navnet* på din lokale ressourcer, og du ikke ændrer genereres automatisk navnet på miljøvariablen (som indstilles til **DEPLOYROOT_PATH** i så fald), vil det også virker for dit program samt.

Yderligere oplysninger om oprettelse af en lokal lagerplads ressource kan findes på [lokale lager egenskaber][].

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Oprette et Hej verden program til Azure i Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Egenskaber for lokale lager]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
