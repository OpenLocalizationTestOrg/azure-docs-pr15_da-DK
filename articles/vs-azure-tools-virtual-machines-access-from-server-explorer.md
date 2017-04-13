<properties
   pageTitle="Få adgang til Azure virtuelle maskiner fra Server Explorer | Microsoft Azure"
   description="Få en oversigt over, hvordan du se oprette og administrere Azure virtuelle maskiner (VM'er) i Server Explorer i Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Få adgang til Azure virtuelle maskiner fra Server Explorer

Ved hjælp af Server Explorer i Visual Studio, kan du få vist oplysninger om din virtuelle maskiner, der er hostet af Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Få adgang til virtuelle maskiner i Server Explorer

Hvis du har virtuelle maskiner, der er hostet af Azure, kan du få adgang til dem i Server Explorer. Du skal først logge på abonnementet Azure til at få vist dine mobile tjenester. Åbne genvejsmenuen for noden Azure i Server Explorer for at logge på, og vælg **Opret forbindelse til Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>At få oplysninger om din virtuelle maskiner

1. Vælg en virtuel maskine i Server Explorer, og vælg derefter på F4 for at få vist vinduet dens egenskaber.

    Tabellen nedenfor viser, hvilke egenskaber er tilgængelige, men de er alle skrivebeskyttet. For at ændre dem, skal du bruge [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Egenskaben|Beskrivelse|
  	|---|---|
  	|DNS-navn|URL-adresse med URL-adressen for den virtuelle maskine.|
  	|Miljø|Værdien af denne egenskab er altid fremstilling for en virtuel maskine.|
  	|Navn|Navnet på den virtuelle maskine.|
  	|Størrelse|Størrelsen af den virtuelle maskine, som afspejler mængden hukommelse og diskplads plads, som er tilgængelig. Du kan finde flere oplysninger, se sådan: konfigurere virtuelt størrelser.|
  	|Status|Værdier omfatter Start, introduktion, stoppe, stoppet og hentning af Status. Hvis hentning af Status vises, er den aktuelle status ukendt. Værdier for denne egenskab er forskellige fra de værdier, der bruges på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|Abonnement-ID til kontoen Azure. Du kan vise disse oplysninger på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885) ved at få vist egenskaberne for et abonnement.|

1. Vælg en slutpunkt node, og derefter få vist vinduet **Egenskaber** .

1. I følgende tabel beskrives de tilgængelige egenskaber for slutpunkter, men de er skrivebeskyttet. Tilføje eller redigere slutpunkterne for en virtuel maskine, skal du bruge [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|Egenskaben|Beskrivelse|
  	|---|---|
  	|Navn|Et id for slutpunktet.|
  	|Privat Port|Port til netværksadgang interne i dit program.|
  	|Protokol|Den protokol, der bruger Transportlaget for dette slutpunkt, enten TCP eller UDP.|
  	|Offentlige Port|Den port, der bruges til offentlig adgang til dit program.|

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om at bruge Azure roller i Visual Studio, skal du se [Ved hjælp af Remote Desktop med Azure roller](vs-azure-tools-remote-desktop-roles.md).
