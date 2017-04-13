## <a name="azure-storage-linked-service"></a>Azure-lager sammenkædet Service

**Azure-lager sammenkædet tjeneste** , kan du sammenkæde et firma med Azure lagerplads med en Azure data factory ved hjælp af **kontonøgle**. Dette giver data factory global adgang til Azure-lager. Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Azure-lager, der er sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| :-------- | :----------- | :-------- |
| type | Typeegenskaben skal være angivet til: **AzureStorage** | Ja |
| connectionString | Angive oplysninger, der er behov for at oprette forbindelse til Azure lager for egenskaben connectionString. | Ja |

Se følgende artikel trin til at få vist eller kopiere tasten konto til en Azure-lager: [visning, kopiere, og Regenerer lagerplads hurtigtaster](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Eksempel:**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Azure-lager Sas sammenkædet Service  
En delt adgang signatur (SAS) giver delegeret adgang til ressourcer i kontoen lagerplads. Det betyder, at du kan give en klient begrænsede tilladelser til objekter på din lagerplads konto i en angivet periode tid og med et bestemt sæt tilladelser, uden at dele din konto adgangstaster. Sikkerhedstilknytningerne er en URI, der omfatter i dets forespørgselsparametre alle de nødvendige oplysninger til godkendt adgang til en ressource, lagerplads. For at få adgang til lagerplads ressourcer med Sikkerhedstilknytningerne skal klienten kun sende i Sikkerhedstilknytningerne til den relevante parametre eller metode. Finde detaljerede oplysninger om SAS [delt Access signaturer: om SAS datamodellen](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
Azure-lager SAS sammenkædet tjenesten kan du sammenkæde et firma med Azure lagerplads med en Azure data factory ved hjælp af en delt Access signatur (SAS). Dette giver data factory begrænsede/klokkeslæt-bundne adgang til alle/specifikke ressourcer (blob/objektbeholder) i lagerplads. Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Azure lagerplads SAS sammenkædet tjeneste. 

| Egenskaben | Beskrivelse | Påkrævet |
| :-------- | :----------- | :-------- |
| type | Typeegenskaben skal være angivet til: **AzureStorageSas**  | Ja |
| sasUri | Angiv delt Access signatur URI til Azure-lager ressourcer som blob, objektbeholder eller tabel. Se noterne nedenfor for at få oplysninger. | Ja | 


**Eksempel:**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Når du opretter en **SAS URI**, at du overveje følgende:  

- Azure Data Factory understøtter kun **Service SAS**, ikke konto SAS. Du kan finde oplysninger om disse to typer i [Typer af delt Access signaturer](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) .
- Relevante læse-og skriveadgang **tilladelser** skal angives på objekter, der er baseret på hvordan tjenesten sammenkædede (Læs, Skriv, læse-og skriveadgang) der skal bruges i dine data factory.
- **Udløbstid** skal være angivet korrekt. Sørg for, at adgangen til Azure-lager objekter ikke udløber i den aktive periode af rørledninger.
- URI skal oprettes højre objektbeholder/blob eller tabelniveau baseret på brug. En SAS Uri til en Azure blob gør det muligt for tjenesten Data Factory til at få adgang til den pågældende blob. En SAS Uri til en Azure blob-objektbeholder kan tjenesten Data Factory til at navigere gennem BLOB i objektbeholderen. Hvis du vil give adgang til mere/færre objekter senere eller opdatere SAS URI skal du huske at opdatere tjenesten sammenkædet med den nye URI.   
