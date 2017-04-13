<properties
   pageTitle="Azure sø datalager sammenligning med Azure lagerplads Blob | Microsoft Azure"
   description="Azure datalager sø sammenligning med Azure lagerplads Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Sammenligning af Azure sø datalager og Azure Blob-lager

Tabellen i denne artikel opsummerer forskellene mellem Azure sø datalager og Azure Blob-lager langs nogle vigtige aspekter af stor databehandling. Azure Blob-lager er et generelt formål, SVG objekt store, der henvender sig til en bred vifte af lagerplads scenarier. Azure datalager sø er et hyper-skala lager, der er optimeret til stor data analytics arbejdsmængder.

|    | Azure sø datalager  | Azure Blob-lager  |
|----|-----------------------|--------------------|
| Formål  | Optimerede lagerplads på stor data analytics arbejdsmængder                                                                          | Generelle formål objekt lagre for en lang række lagerplads scenarier                                                                                |
| Use Cases                                   | Batch, interaktive, streaming analyser og machine learning data såsom logfiler, IoT data skal du klikke på streams, store datasæt | Alle typer tekst eller binære data, som programmet igen slutningen, sikkerhedskopidata, media storage til streaming og generelle formål data |
| Vigtige begreber                                | Sø datalager konto indeholder mapper, som også indeholder data, der gemmes som filer | Lagerplads konto har beholdere, som også har data i form af BLOB |
| Struktur | Hierarkisk filsystemet                                                                                                    | Objekt store med flad navneområdet  |
| API   | REST-API over HTTPS | REST-API over HTTP/HTTPS                                                                                                                            |
| Serversiden API                             | [WebHDFS-kompatible REST-API](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Azure Blob-lager REST-API](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Hadoop-fil systemets klient                   | Ja                                                                                                                         | Ja                                                                                                                                                 |
| Datahandlinger - godkendelse            | Baseret på [Azure Active Directory identiteter](../active-directory/active-directory-authentication-scenarios.md) | Baseret på delte hemmeligheder - [Konto hurtigtaster](../storage/storage-create-storage-account.md#manage-your-storage-account) og [Delte hurtigtaster signatur](../storage/storage-dotnet-shared-access-signature-part-1.md).                                                                       |
| Datahandlinger - godkendelsesprotokol     | OAuth 2.0. Opkald skal indeholde en gyldig JWT, (JSON Web Token) er udstedt af Azure Active Directory                     | Hash-baseret Message Authentication Code (HMAC). Opkald skal indeholde en Base64-kodet SHA 256 hash hen over en del af HTTP-anmodningen. |
| Datahandlinger - godkendelse               | POSIX adgangskontrollister (ACLs).  ACLs baseret på Azure Active Directory identiteter kan angives fil- og niveau. | Om konto niveau tilladelse – Brug [Konto Access-taster](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Til konto, beholderen eller godkendelse af blob - Brug [Delt signatur-adgangstaster](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Datahandlinger - overvågning                    | Tilgængelig. Se [her](data-lake-store-diagnostic-logs.md) for at få oplysninger.                                                                                                                   | Tilgængelige                                                                                                                                           |
| Resten kryptering data                     | Gennemsigtige, serversiden (kommer snart)<ul><li>Med service-administreret nøgler</li><li>Med kunde-administreret taster i Azure KeyVault</li></ul>| <ul><li>Gennemsigtige, serversiden</li> <ul><li>Med service-administreret nøgler</li><li>Med kunde-administreret taster i Azure KeyVault (kommer snart)</li></ul><li>Klientsiden kryptering</li></ul> |
| Administration af handlinger (fx konto oprette) | [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-what-is.md) (RBAC), som Azure for kontoadministration                                                                       | [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-what-is.md) (RBAC), som Azure for kontoadministration                                                                                               |
| Udvikler SDK'er                              | .NET, Java, Node.js                                                                                                         | .NET, Java, Python Node.js, C++, fonetisk                                                                                                              |
| Analytics arbejdsbelastningen ydeevne              | Optimeret ydeevne for parallel analytics arbejdsmængder. Høj overførselshastighed og IOP'ER.                                           | Ikke optimeret til analytics arbejdsmængder                                                                                                               |
| Begrænsninger for filstørrelse                                 | Ingen begrænsninger med hensyn til konto størrelser, filstørrelser eller antallet af filer                                                                   | Bestemte begrænsninger dokumenterede [her](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Geografisk redundans                              | Lokalt-overflødige (flere kopier af data i ét Azure område)                                                             | Lokalt overflødige (LRS), globalt overflødige (GRS), læseadgang globalt overflødige (RA-GRS). Se [her](../storage/storage-redundancy.md) for at få flere oplysninger |
| Tjenestetilstand                               | Public Preview                                                                                                              | Alment tilgængelig                                                                                                                                 |
| Internationale tilgængelighed  | Se [her](https://azure.microsoft.com/regions/#services)| Se [her](https://azure.microsoft.com/regions/#services) |
| Pris                                       |     Se [priser](https://azure.microsoft.com/pricing/details/data-lake-store/)| Se [priser](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Næste trin

- [Oversigt over Azure sø datalager](data-lake-store-overview.md)
- [Komme i gang med sø datalager](data-lake-store-get-started-portal.md)



