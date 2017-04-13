<properties
   pageTitle="Oprette en arbejds- eller skolekonto identitet i AAD | Microsoft Azure"
   description="Lær, hvordan du opretter en arbejds- eller skolekonto identitet i Azure Active Directory til brug med din Linux virtuelle computere."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Oprette en arbejde eller skole identitet i Azure Active Directory til brug med Linux FOS

Hvis du har oprettet en personlig Azure-konto eller har et personlig MSDN-abonnement og oprettet Azure-konto for at kunne udnytte MSDN Azure kredit – bruges en *Microsoft-konto* identitet til at oprette den. Mange fantastiske funktioner [ressource gruppeskabeloner](../azure-resource-manager/resource-group-overview.md) er et eksempel af Azure ---kræver en arbejds- eller skolekonto-konto (en identitet administreres af Azure Active Directory) til at arbejde. Du kan følge vejledningen nedenfor for at oprette en ny arbejds- eller skolekonto, fordi heldigvis en af de bedste ting ved din personlige Azure-konto er, at den er udstyret med et standard Azure Active Directory-domæne, du kan bruge til at oprette en ny arbejds- eller skolekonto, som du kan bruge med Azure funktioner, der kræver det.

Dog seneste ændringer gør det muligt at administrere dit abonnement med en hvilken som helst type Azure-konto ved hjælp af den `azure login` interaktive logon metode er beskrevet [her](../xplat-cli-connect.md). Du kan enten bruge denne metode, eller du kan følge instruktionerne, der følger. Du kan også [oprette en arbejde eller skole identitet i Azure Active Directory til brug med Windows FOS](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
