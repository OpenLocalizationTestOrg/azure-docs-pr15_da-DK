### <a name="prerequisites"></a>Forudsætninger
- En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)
- En [Azure Blob-lager-konto](../articles/storage/storage-create-storage-account.md) , herunder kontonavn lager og access-nøglen. Disse oplysninger vises i egenskaberne for kontoen lagerplads på portalen Azure. Læs mere om [Azure-lager](../articles/storage/storage-introduction.md).

Før du bruger kontoen Azure Blob-lager i en logik app, oprette forbindelse til kontoen Azure Blob-lager. Du kan gøre dette nemt i din logik app på Azure-portalen.  

Oprette forbindelse til kontoen Azure Blob-lager at benytte følgende fremgangsmåde:  

1. Oprette en logik app. Tilføj en udløser i designvisningen logik Apps, og Tilføj derefter en handling. Vælg **Vis Microsoft administrerede API'er** i på rullelisten, og angiv derefter "blob" i søgefeltet. Vælg en af handlingerne:  

    ![Azure Blob-lager forbindelse oprettelse af trin](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. Hvis du ikke tidligere har oprettet alle forbindelser til Azure-lager, bliver du bedt om forbindelsesoplysningerne:   

    ![Azure Blob-lager forbindelse oprettelse af trin](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. Angiv kontooplysninger lagerplads. Egenskaber med en stjerne er påkrævet.

    | Egenskaben | Detaljer |
|---|---|
| Forbindelsesnavn * | Angiv et navn til din forbindelse. |
| Kontonavn Azure lager * | Angiv firmanavn lagerplads. Kontonavn lager vises i egenskaberne for lagerplads på portalen Azure. |
| Azure-lager konto hurtigtast * | Angive kontonøgle lagerplads. Hurtigtasterne vises i egenskaberne for lagerplads på portalen Azure. |

    Disse legitimationsoplysninger anvendes til at tillade, at din logik app til at oprette forbindelse, og få adgang til dine data. 

4. Vælg **Opret**.

5. Bemærk, at forbindelsen er oprettet. Fortsæt nu med andre trinnene i din app, logik: 

    ![Azure Blob-lager forbindelse oprettelse af trin](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  
