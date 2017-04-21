Du kan oprette Azure lagerplads køer ved hjælp af Visual Studio **Server Explorer**.

![Server Explorer BLOB][Image1]

1. På menuen **Vis** skal du vælge **Server Explorer**.
2. Udvid noden **Azure** for dit abonnement i Server Explorer, udvid noden **lager** og noden for kontoen lagerplads du har angivet i tjenesten Azure-lager, der er forbundet.
3. Vælg noden **køer** , og vælg **Opret kø** i kontekstmenuen.
4. Angiv et navn til objektbeholderen, og vælg **OK**.   

Ny objektbeholder er privat, og skal du angive produktnøglen lagerplads adgang for at hente BLOB fra denne beholder som standard. Hvis du vil gøre filerne i beholderen offentligt, skal du vælge objektbeholderen i **Server Explorer** og trykke på `F4` at få vist vinduet **Egenskaber** . Angiv den **offentlige læseadgang** til **Blob**. Alle på internettet kan se BLOB i en offentlig beholder, men du kan ændre eller slette dem kun, hvis du har den relevante hurtigtast.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png