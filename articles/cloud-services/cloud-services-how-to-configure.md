<properties 
    pageTitle="Sådan konfigureres en skybaseret tjeneste (klassisk portal) | Microsoft Azure" 
    description="Lær, hvordan du kan konfigurere skytjenester i Azure. Lær at opdatere skyen tjenestekonfiguration og konfigurere fjernadgang til rolle forekomster." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-configure-cloud-services"></a>Sådan konfigureres Cloud Services

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-how-to-configure-portal.md)
- [Azure klassisk portal](cloud-services-how-to-configure.md)

Du kan konfigurere de mest almindeligt anvendte indstillinger for en skybaseret tjeneste i portalen Azure klassisk. Eller hvis du ønsker at opdatere dine konfigurationsfiler direkte, kan du hente en tjeneste konfigurationsfil for at opdatere, og derefter overføre den opdaterede fil og opdatere skytjenesten med ændringerne i konfigurationen. Uanset hvilken mulighed, publiceres konfiguration opdateringerne på alle forekomster af rollen.

Azure klassisk portalen kan du også aktivere [Remote Desktop Connection til en rolle i Azure Cloud Services](cloud-services-role-enable-remote-desktop.md)

Azure kan kun sikre 99.95 procent tjenesten kører under konfiguration opdateringerne Hvis du har mindst to rolle forekomster for hver rolle. Der gør det muligt for et virtuelt til at behandle klient-anmodninger, mens den anden opdateres. Du kan finde yderligere oplysninger finder [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ændre en skybaseret tjeneste

1. [Azure klassisk portal](http://manage.windowsazure.com/), skal du klikke på **Cloud Services**, klik på navnet på skytjenesten og derefter klikke på **Konfigurer**.

    ![Konfigurationssiden](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Du kan konfigurere overvågning på siden **Konfigurer** , Opdater Rolleindstillinger, og vælg gæst operativsystemet og familie efter rolle forekomster. 

2. Angiv niveauet for overvågning til detaljeret eller minimale i **overvågning**, og Konfigurer diagnosticering forbindelse strengene, der kræves til detaljeret overvågning.

3. Tjenesten roller (grupperet efter rolle), kan du opdatere følgende indstillinger:
    
    >**Indstillinger**  
    >Redigere værdierne for diverse konfigurationsindstillinger, der er angivet i *ConfigurationSettings* elementerne i service-konfigurationsfil (.cscfg).
    >
    >**Certifikater**  
    >Ændre det certifikat miniature, der bruges i SSL-kryptering til en rolle. Hvis du vil ændre et certifikat, skal du først overføre det nye certifikat (på siden **certifikaterne** ). Derefter opdatere miniature i certifikatstrengen i rolleindstillingerne for.

4. I **operativsystem**, kan du ændre operativsystem familie eller version til rolle forekomster eller vælge **automatisk** at aktivere automatiske opdateringer af den aktuelle version af operativsystemet. Indstillingerne for operativsystem, som gælder for web roller og arbejder roller, men påvirker ikke virtuelle computere.

    Den seneste version af operativsystemet er installeret på alle forekomster af rolle under installationen, og operativsystemerne opdateres automatisk som standard. 
    
    Hvis du skal bruge til din skytjeneste til at køre på en anden operativsystemversion på grund af kompatibilitetskrav i din kode, kan du vælge en af operativsystemer og version. Når du vælger en bestemt operativsystemversion, automatisk operativsystemopdateringer til skytjenesten afbrydes midlertidigt. Du skal sikre operativsystemerne modtage opdateringer.
    
    Hvis du har rettet alle kompatibilitetsproblemer, som dine apps har med den seneste version af operativsystemet, kan du aktivere automatiske opdateringer til operativsystemer ved at angive operativsystemversionen til **automatisk**. 
    
    ![Indstillinger for Operativsystem](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Klik på **Gem**for at gemme dine konfigurationsindstillinger, og distribuere dem til de rolle forekomster. (Klik på **Slet** for at annullere ændringerne). **Gem** og **Slet** føjes til kommandolinjen, når du ændrer en indstilling.

## <a name="update-a-cloud-service-configuration-file"></a>Opdatere en skybaseret tjeneste konfigurationsfil

1. Hente en skybaseret tjeneste konfigurationsfil (.cscfg) med den aktuelle konfiguration. Klik på **Hent**på siden **Konfigurer** for skytjenesten. Derefter skal du klikke på **Gem**, eller klik på **Gem som** for at gemme filen.

2. Når du opdaterer konfigurationsfil service, overføre, og Anvend konfiguration opdateringer:

    1. Klik på **Send**på siden **Konfigurer** .
    
        ![Overføre konfiguration](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. I **konfigurationsfil**skal du klikke på **Gennemse** for at markere filen opdaterede .cscfg.
    
    3. Hvis din skybaseret tjeneste indeholder de roller, har kun én forekomst, Markér afkrydsningsfeltet **Anvend konfiguration, selvom en eller flere roller indeholder en enkelt forekomst** for at aktivere konfiguration opdateringer til roller for at fortsætte.
    
        Medmindre du definerer mindst to forekomster af hver rolle, Azure kan ikke garantere mindst 99.95 procent tilgængeligheden af skybaseret tjeneste under konfiguration af tjenesteopdateringerne. Du kan finde yderligere oplysninger finder [Niveau serviceaftaler](https://azure.microsoft.com/support/legal/sla/).
    
    4. Klik på **OK** (markering). 


## <a name="next-steps"></a>Næste trin

* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name.md).
* [Administrer din skybaseret tjeneste](cloud-services-how-to-manage.md).
* [Aktivere Remote Desktop Connection til en rolle i Azure Cloud Services](cloud-services-role-enable-remote-desktop.md)
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate.md).
