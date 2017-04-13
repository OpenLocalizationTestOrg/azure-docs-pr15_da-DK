<properties 
    pageTitle="Sådan konfigureres en skybaseret tjeneste (portal) | Microsoft Azure" 
    description="Lær, hvordan du kan konfigurere skytjenester i Azure. Lær at opdatere skyen tjenestekonfiguration og konfigurere fjernadgang til rolle forekomster. Disse eksempler bruger Azure portalen." 
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

Du kan konfigurere de mest almindeligt anvendte indstillinger for en skybaseret tjeneste i portalen Azure. Eller hvis du ønsker at opdatere dine konfigurationsfiler direkte, kan du hente en tjeneste konfigurationsfil for at opdatere, og derefter overføre den opdaterede fil og opdatere skytjenesten med ændringerne i konfigurationen. Uanset hvilken mulighed, publiceres konfiguration opdateringerne på alle forekomster af rollen.

Du kan også administrere forekomster af din skyen service roller eller Fjernskrivebord i dem.

Azure kan kun sikre 99.95 procent tjenesten kører under konfiguration opdateringer, hvis du har mindst to rolle forekomster for hver rolle. Der gør det muligt for et virtuelt til at behandle klient-anmodninger, mens den anden opdateres. Du kan finde yderligere oplysninger finder [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ændre en skybaseret tjeneste

Når du har åbnet [Azure-portalen](https://portal.azure.com/), gå til din skybaseret tjeneste. Her kan du administrere mange aspekter af filen. 

![Indstillingssiden for](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**Listeindstillinger** eller **Indstillinger for alle** links åbnes af bladet **Indstillinger** , hvor du kan ændre **Egenskaber**, ændre **konfiguration**, administrere **certifikater**, konfiguration af **regler for påmindelser**og administrere de **brugere** , der har adgang til denne skybaseret tjeneste.

![Azure skyen service indstillinger blade](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
>Det operativsystem, der bruges til skytjenesten kan ikke ændres ved hjælp af **Azure-portalen**, du kan kun ændre denne indstilling via [Azure klassisk portal](http://manage.windowsazure.com/). Dette er detaljeret [her](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## <a name="monitoring"></a>Overvågning

Du kan føje påmindelser til din skybaseret tjeneste. Klik på **Indstillinger for** > **Besked regler** > **Tilføj påmindelse**. 

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Her kan du konfigurere en besked. Med **Mertic** rullelisten, kan du konfigurere en besked for følgende typer data.

- Læst fra disken
- Skrivning
- Netværk i
- Netværk ud
- CPU procentdel 

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurere overvågning fra et metriske felt

I stedet for ved hjælp af **Indstillinger** > **Regler for påmindelser**, kan du klikke på en af de metriske fliser i sektionen **overvågning** af bladet **skybaseret tjeneste** .

![Overvågning af tjenesten i skyen](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Her kan du tilpasse diagrammet bruges sammen med feltet, eller Tilføj en besked om.


## <a name="reboot-reimage-or-remote-desktop"></a>Genstart, reimage eller Fjernskrivebord

Du kan ikke konfigurere Fjernskrivebord ved hjælp af **Azure-portalen**på nuværende tidspunkt. Dog kan du konfigurere det via [Azure klassisk portal](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), eller via [Visual Studio](../vs-azure-tools-remote-desktop-roles.md). 

Først skal du klikke på forekomsten af skyen.

![Skyen forekomst af tjenesten](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Fra bladet, åbnes uou kan starte en forbindelse til Fjernskrivebord, fra en fjernplacering genstarte forekomsten eller fra en fjernplacering reimage (starter med et nyt billede) forekomsten.

![Skybaseret tjeneste forekomst knapper](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## <a name="reconfigure-your-cscfg"></a>Omkonfigurere din .cscfg

Du kan være nødvendigt at omkonfigurere du skybaseret tjeneste via filen [service config (cscfg)](cloud-services-model-and-package.md#cscfg) . Først skal du hente filen .cscfg, redigere, og Overfør det.

1. Klik på ikonet **Indstillinger** eller linket **alle indstillinger** for at åbne bladet **Indstillinger** .

    ![Indstillingssiden for](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. Klik på **konfiguration** element.

    ![Konfiguration af Blade](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. Klik på knappen **Hent** .

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. Når du opdaterer konfigurationsfil service, overføre, og Anvend konfiguration opdateringer:

    ![Overføre](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png) 
    
5. Vælg fil, .cscfg, og klik på **OK**.

            
## <a name="next-steps"></a>Næste trin

* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy-portal.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name-portal.md).
* [Administrer din skybaseret tjeneste](cloud-services-how-to-manage-portal.md).
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate-portal.md).