<properties
   pageTitle="Konfigurere en Docker vært med VirtualBox | Microsoft Azure"
   description="En trinvis vejledning til at konfigurere en forekomst af standard Docker ved hjælp af Docker maskine og VirtualBox"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Konfigurere en Docker vært med VirtualBox

## <a name="overview"></a>Oversigt
I denne artikel fører dig gennem konfigurationen af en forekomst af standard Docker ved hjælp af Docker maskine og VirtualBox. Hvis du bruger [Docker til Windows beta](http://beta.docker.com/), er denne konfiguration er ikke nødvendigt.

## <a name="prerequisites"></a>Forudsætninger
Følgende værktøjer skal være installeret.

- [Docker værktøjskasse](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Konfiguration af Docker klienten med Windows PowerShell

For at konfigurere en Docker klient skal du blot Åbn Windows PowerShell, og udføre følgende trin:

1. Oprette en standard docker host forekomst.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Kontrollere standardforekomsten er konfigureret og kører. (Du skal se en forekomst med navnet 'Standard' kører.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![docker maskine ls output][0]
 
1. Angive standard som den aktuelle vært, og Konfigurer din shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Få vist de aktive Docker beholdere. På listen skal være tom.

    ```PowerShell
    docker ps
    ```

    ![docker ps output][1]
 
> [AZURE.NOTE]Hver gang du genstarter computeren udvikling, du skal genstarte din lokale docker vært.
> For at gøre dette, udstede følgende kommando fra en kommandoprompt: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
