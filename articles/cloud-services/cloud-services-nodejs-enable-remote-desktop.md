<properties 
    pageTitle="Aktivere via Fjernskrivebord skytjenester (Node.js)" 
    description="Lær at aktivere remote desktop adgang til de virtuelle maskiner, der er vært for Azure Node.js programmet." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>Aktiverer Fjernskrivebord i Azure

Remote Desktop gør det muligt at få adgang til skrivebordet på en rolle forekomst, der kører i Azure. Du kan bruge en forbindelse til Fjernskrivebord til at konfigurere den virtuelle maskine eller foretage fejlfinding af problemer med programmet.

> [AZURE.NOTE] I denne artikel gælder for Node.js programmer, der er hostet som en Azure skybaseret tjeneste.


## <a name="prerequisites"></a>Forudsætninger

- Installere og konfigurere [Azure Powershell](../powershell-install-configure.md).
- Installere en app til en Node.js til en Azure skybaseret tjeneste. Yderligere oplysninger finder du se [opbygge og anvende et Node.js program til en Azure skybaseret tjeneste](cloud-services-nodejs-develop-deploy-app.md).


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Trin 1: Bruge Azure PowerShell til at konfigurere tjenesten for Fjernskrivebord adgang

Hvis du vil bruge Fjernskrivebord, skal du opdatere definitionen Azure tjenesten og konfiguration med et brugernavn, din adgangskode og certifikat. 

Du kan udføre følgende trin fra en computer, der indeholder kildefilerne for din app.

1. Kør **Windows PowerShell** som Administrator. (Fra **Menuen Start** eller **Startskærmbilledet**, søge efter **Windows PowerShell**.)

2.  Gå til den mappe, hvor tjenestedefinition (.csdef) og service konfiguration (.cscfg) filer.

3. Angiv følgende PowerShell-cmdlet:

        Enable-AzureServiceProjectRemoteDesktop

4. Når du bliver spurgt, angive et brugernavn og adgangskode.

    ![Aktivér azureserviceprojectremotedesktop][enable-rdp]

3.  Angiv følgende PowerShell-cmdlet for at publicere ændringerne:

        Publish-AzureServiceProject

    ![publicere azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Trin 2: Oprette forbindelse til den rolle forekomst

Når du publicerer definitionen af update-tjenesten, kan du oprette forbindelse til den rolle forekomst.

1.  Vælg **Cloud Services** i [Azure klassisk portal], og vælg derefter din tjeneste.

    ![Azure klassisk portal][cloud-services]

2.  Klik på **forekomster**, og klik derefter på **fremstilling** eller **midlertidige** for at få vist forekomster til din tjeneste. Vælg en forekomst, og klik derefter på **Opret forbindelse** nederst på siden.

    ![Siden forekomster][3]

2.  Når du klikker på **Opret forbindelse**, webbrowseren beder dig om at gemme en RDP-fil. Åbne denne fil. (For eksempel, hvis du bruger Internet Explorer, skal du klikke på **Åbn**.)

    ![prompt om at åbne eller gemme filen RDP][4]

3.  Når filen åbnes, vises følgende sikkerhed prompt:

    ![Meddelelse om sikkerhed i Windows][5]

4.  Klik på **Opret forbindelse**, og der vises en meddelelse i sikkerhed for at angive legitimationsoplysninger for at få adgang til forekomsten. Skriv den adgangskode, du oprettede i [trin 1] [trin 1: konfigurere tjenesten for Fjernskrivebord adgang ved hjælp af PowerShell Azure], og klik derefter på **OK**.

    ![meddelelse om brugernavn og adgangskode][6]

Når forbindelsen er oprettet, vises Fjernskrivebord skrivebordet på forekomsten i Azure. 

![Fjernskrivebord-session][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Trin 3: Konfigurere tjenesten til at deaktivere Fjernskrivebord adgang 

Når du ikke længere kræver forbindelser til Fjernskrivebord til de rolle forekomster i skyen, kan du deaktivere adgang til Fjernskrivebord ved hjælp af [Azure PowerShell].

1.  Angiv følgende PowerShell-cmdlet:

        Disable-AzureServiceProjectRemoteDesktop

2.  Angiv følgende PowerShell-cmdlet for at publicere ændringerne:

        Publish-AzureServiceProject

## <a name="additional-resources"></a>Yderligere ressourcer

- [Adgang fra en fjernplacering til rolle forekomster i Azure] 
- [Ved hjælp af Fjernskrivebord med Azure roller]
- [Node.js Developer Center](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure klassisk portal]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Adgang fra en fjernplacering til rolle forekomster i Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Ved hjælp af Fjernskrivebord med Azure roller]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 