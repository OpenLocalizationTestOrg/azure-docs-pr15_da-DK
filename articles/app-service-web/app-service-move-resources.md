<properties
    pageTitle="Flytte Web App-ressourcer til en anden ressourcegruppe"
    description="I denne artikel beskrives de scenarier, hvor du kan flytte Web Apps og App-tjenester fra én ressourcegruppe til en anden."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Understøttede Flyt konfigurationer

Du kan flytte Azure online ressourcer ved hjælp af [ARM flytte ressourcer Api](../resource-group-move-resources.md).

Azure Online understøtter i øjeblikket Flyt følgende scenarier:

* Flytte hele indholdet af en ressourcegruppe (online, app tjenesteplanerne og certifikater) til en anden ressourcegruppe 
    * Bemærk: Ressourcegruppen destination kan ikke indeholder nogen Microsoft.Web ressourcer i dette scenarie
* Flytte individuelle Onlines til en anden ressourcegruppe, mens du stadig vært dem i deres aktuelle app-serviceaftale (app-serviceaftale forbliver i den gamle ressourcegruppe)
