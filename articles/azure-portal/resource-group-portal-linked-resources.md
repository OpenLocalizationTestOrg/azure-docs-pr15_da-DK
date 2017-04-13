<properties 
    pageTitle="Relaterede og sammenkædede ressourcer i galleriet felt" 
    description="Få mere at vide om relaterede og sammenkædede ressourcer, der vises i feltet galleri med portalen Azure preview." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Relaterede og sammenkædede ressourcer i galleriet felt

Galleriet felt gør det muligt at finde felter for en bestemt ressource, og træk dem til din aktuelle blade. Ved hjælp af galleriet felt, kan du oprette management visninger, der strækker sig over ressourcer. Relaterede ressourcer omfatter for en ressource som angivet, alle de ressourcer, der deler samme ressourcegruppe som ressourcen, og alle ressourcer, der linker til eller fra ressourcen.

## <a name="linked-resources-in-azure-resource-manager"></a>Sammenkædede ressourcer i Azure ressourcestyring.

Sammenkædning er en funktion af Azure ressourcestyring.  Det gør det muligt at definere relationer mellem ressourcer, selvom de ikke er placeret i samme ressourcegruppe. Sammenkædning har ingen indvirkning på kørselstidspunktet af dine ressourcer, uden at påvirke fakturering og uden at påvirke rollebaseret adgang.  Det er blot en funktion, du kan bruge til at repræsentere relationer, så funktioner såsom galleriet felt kan give en omfattende management oplevelse.  Dine værktøjer kan undersøge linkene ved hjælp af linkene API og angive brugerdefinerede relation management oplever samt. 

## <a name="how-do-i-link-my-resources"></a>Hvordan knytter jeg mine ressourcer?

Når du opretter ressourcer via portalen eller ved at anvende en skabelon via Azure PowerShell eller Azure CLI, oprettes automatisk links til nogle afhængige ressourcer. Du kan også automatisk sammenkæde ressourcer ved hjælp af [Sammenkædede ressourcer REST-API](https://msdn.microsoft.com/library/azure/mt238499.aspx) eller erklære relationer i skabelonen. Du kan finde en komplet diskussion for at arbejde med ressourcer, der er sammenkædet, [Linking ressourcer i Azure ressourcestyring](../resource-group-link-resources.md).

## <a name="next-steps"></a>Næste trin

- Hvis du har brug for en introduktion til at skrive Azure ressourcestyring skabeloner, kan du se [redigering skabeloner](../resource-group-authoring-templates.md).
- Se [oprette en kæde ressourcer i Azure ressourcestyring](../resource-group-link-resources.md)for at kaste dig ud i flere detaljer om at oprette links mellem ressourcer.
- Mere om at arbejde med ressourcegrupper via portalen preview se forstå [ved hjælp af portalen Azure Preview for at administrere dine Azure ressourcer](resource-group-portal.md).
