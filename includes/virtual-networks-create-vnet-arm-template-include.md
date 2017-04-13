## <a name="download-and-understand-the-arm-template"></a>Hente og forstå skabelonen ARM

Du kan hente den eksisterende ARM skabelon til oprettelse af en VNet og to undernet fra github, foretag eventuelle ændringer, kan du ønsker, og du kan genbruge den. Følg nedenstående trin for at gøre det.

1. Gå til [eksempelsiden skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Klik på **azuredeploy.json**, og klik derefter på **RAW**.
3. Gem filen til en en lokal mappe på din computer.
4. Hvis du er bekendt med ARM skabeloner, gå til trin 7.
5. Åbn den fil, du lige har gemt, og kig på indholdet under **parametre** i linje 5. ARM Skabelonparametre giver en pladsholder for værdier, der kan udfyldes under installationen.

    | Parameter | Beskrivelse |
    |---|---|
    | **placering** | Azure område, hvor VNet oprettes |
    | **vnetName** | Navn til den nye VNet |
    | **addressPrefix** | Adresseområde for VNet i CIDR format |
    | **subnet1Name** | Navnet på den første VNet |
    | **subnet1Prefix** | CIDR blokere for det første undernet |
    | **subnet2Name** | Navn til den anden VNet |
    | **subnet2Prefix** | CIDR blokere for det andet undernet |

    >[AZURE.IMPORTANT] ARM skabeloner vedligeholdes i github kan ændres med tiden. Kontrollér, at du markerer skabelonen inden du bruger den.
    
6. Kontrollere indhold under **ressourcer** , og Bemærk følgende:

    - **type**. Type ressource, der har oprettet af skabelonen. I dette tilfælde **Microsoft.Network/virtualNetworks**, som repræsenterer en VNet.
    - **navn**. Ressourcens navn. Bemærk brugen af **[parameters('vnetName')]**, hvilket betyder, at navnet bliver leveret som input af brugeren eller en parameterfil under installationen.
    - **Egenskaber**. Liste over egenskaber for ressourcen. Denne skabelon bruger egenskaberne adresse mellemrum og undernet under oprettelse af VNet.

7. Gå tilbage til [siden eksempel skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Klik på **azuredeploy paremeters.json**, og klik derefter på **RAW**.
9. Gem filen til en en lokal mappe på din computer.
10. Åbn den fil, du lige har gemt, og rediger værdierne for parametrene. Brug værdierne nedenfor til at installere den VNet, der er beskrevet i vores scenario.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Gem filen.
  