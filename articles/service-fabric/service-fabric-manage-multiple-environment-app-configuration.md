<properties
   pageTitle="Administrere flere miljøer i tjenesten strukturen | Microsoft Azure"
   description="Tjenesten strukturen programmer kan køres på klynger, der interval i størrelse fra én computer til tusindvis af computere. I nogle tilfælde vil du konfigurere dit program anderledes for disse forskellige miljøer. I denne artikel omhandler, hvordan du definerer andet program parametre per miljø."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Administrere programmets parametre for flere miljøer

Du kan oprette Azure Service strukturen klynger ved hjælp af et vilkårligt sted fra en til mange tusindvis af computere. Mens binære filer programmet på computeren kan køre uden ændringer på tværs af denne bred spektret af miljøer, vil du ofte konfigurere programmet forskelligt, afhængigt af antallet computere, du anvender for.

Overvej at som et enkelt eksempel `InstanceCount` til en uden status tjeneste. Når du kører programmer i Azure, vil du generelt indstiller denne parameter til særlige værdien "-1". Dette sikrer, at din tjeneste, der kører på hver node i klyngen. Denne konfiguration er dog ikke passer til en enkelt computer klynge, da du ikke har flere processer lytter på samme slutpunktet på en enkelt computer. I stedet skal du typisk indstiller `InstanceCount` til "1".

## <a name="specifying-environment-specific-parameters"></a>Angive miljø-specifikke parametre

Løsning på konfigurationsproblemet er et sæt parameteriseret standardtjenester og -parameter programfiler, der udfylder disse parameterværdier for et givet miljø. Standardtjenester og programmets parametre er konfigureret i de program, der service manifester. Skemadefinitionen for ServiceManifest.xml og ApplicationManifest.xml filerne er installeret med Service-strukturen SDK og værktøjer til *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Standardtjenester

Tjenesten strukturen programmer består af en samling af forekomster af tjenesten. Selvom det er muligt for dig at oprette et tomt program og derefter oprette alle forekomster af tjenesten dynamisk, har de fleste programmer et sæt af core services, der skal altid oprettes, når du er en forekomst af programmet. Dette kaldes "standard services". De er angivet i programmanifestet med pladsholdere til-miljø konfiguration inkluderet i kantede parenteser:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Hver af de navngivne parametre skal defineres i elementet parametre af programmanifestet:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

Egenskaben Standardværdi angiver værdien, der bruges i fravær af en parameter for mere specifikke for et givet miljø.

>[AZURE.NOTE] Ikke alle parametre for forekomst egner sig til konfiguration af-miljø. I det foregående eksempel defineres LowKey og HighKey værdierne for den tjeneste partitionsskema eksplicit efter alle forekomster af tjenesten da partition område er en funktion af domænet data, ikke miljø.


### <a name="per-environment-service-configuration-settings"></a>-Miljø service konfigurationsindstillinger

[Tjenesten strukturen programmet model](service-fabric-application-model.md) aktiverer tjenester medtage konfigurationspakker, der indeholder brugerdefinerede nøgle-værdi-par, der kan læses på kørselstidspunktet. Værdierne i disse indstillinger kan også skelnes ved miljø ved at angive en `ConfigOverride` i programmanifestet.

Antag, at du har følgende indstilling i filen Config\Settings.xml for den `Stateful1` service:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Hvis du vil tilsidesætte denne værdi for et bestemt program/miljø par, oprette en `ConfigOverride` når du importerer tjenestemanifestet i programmanifestet.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Denne parameter kan derefter konfigureres ved miljø, som vist ovenfor. Du kan gøre dette ved at erklære i afsnittet parametre i programmanifestet og angive miljø-specifikke værdier i parameter programfiler.

>[AZURE.NOTE] I forbindelse med tjenesten konfigurationsindstillinger, der er tre steder, hvor du kan angive værdien af en nøgle: konfiguration servicepakke, programmanifestet og parameter programfil. Tjenesten strukturen altid vælger fra parameter programfil først (Hvis angivet) og derefter programmanifestet, og til sidst konfigurationspakken.


### <a name="application-parameter-files"></a>Parameter programfiler

Tjenesten strukturen programmet projektet kan indeholde parameter i en eller flere programfiler. Hver af dem definerer de specifikke værdier for de parametre, der er defineret i programmanifestet:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Som standard indeholder et nyt program to parameter programfiler, med navnet Local.xml og Cloud.xml:

![Parameter programfiler i Solution Explorer][app-parameters-solution-explorer]

For at oprette en ny parameterfil skal blot kopiere og indsætte en eksisterende liste og give det et nyt navn.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificere miljø-specifikke parametre under installationen

Fra det øjeblik skal du vælge den relevante parameterfil til at anvende med dit program. Du kan gøre dette via dialogboksen Udgiv i Visual Studio eller via PowerShell.

### <a name="deploy-from-visual-studio"></a>Installere fra Visual Studio

Når du publicerer dit program i Visual Studio, kan du vælge fra listen over tilgængelige parameter filer.

![Vælg en parameterfil i dialogboksen Publicer][publishdialog]

### <a name="deploy-from-powershell"></a>Installere fra et PowerShell

Den `Deploy-FabricApplication.ps1` PowerShell-script, der er inkluderet i programmet projektskabelon accepterer en Publicer profil som en parameter, og PublishProfile indeholder en reference til programfilen parametre.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om nogle af de grundlæggende begreber, der er beskrevet i dette emne, få vist [Service-strukturen teknisk oversigt](service-fabric-technical-overview.md). Finde oplysninger om andre funktioner til app, der er tilgængelige i Visual Studio, [administrere programmerne Service-strukturen i Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
