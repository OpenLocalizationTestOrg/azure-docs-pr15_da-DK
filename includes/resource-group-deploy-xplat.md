## <a name="how-to-deploy-with-azure-cli"></a>Hvordan du installerer med Azure CLI

1. Log på din Azure-konto.

        azure login

  Når du har indtastet dine legitimationsoplysninger, returnerer kommandoen resultatet af dit logon.

        ...
        info:    login command OK

2. Hvis du har flere abonnementer, skal du give det abonnement-id, du vil bruge til installation.

        azure account set <YourSubscriptionNameOrId>

3. Skift til Azure ressourcestyring modul

        azure config mode arm

   Du modtager en bekræftelse på den nye tilstand.

        info:     New mode is arm

4. Hvis du ikke har en eksisterende ressourcegruppe, kan du oprette en ny ressourcegruppe. Angiv navnet på den ressourcegruppe og placering, du skal bruge til din løsning.

        azure group create -n ExampleResourceGroup -l "West US"

   Der returneres en oversigt over den nye ressourcegruppe.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Køre følgende kommando for at oprette en ny installation til ressourcegruppen, og Angiv de nødvendige parametre. Parametrene, der indeholder et navn til din installation, navnet på din ressourcegruppe, sti eller URL-adressen til den skabelon, du har oprettet og andre parametre, der kræves for det pågældende scenarie.

   Du har følgende indstillinger til at give parameterværdier:

   - Bruge indbyggede parametre og en lokal skabelon.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Bruge indbyggede parametre og et link til en skabelon.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Bruge en parameterfil.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Når ressourcegruppen er blevet installeret, får du vist en oversigt over installationen.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. For at få oplysninger om din seneste installation.

         azure group log show -l ExampleResourceGroup

7. For at få detaljerede oplysninger om installation mislykkede forsøg.

         azure group log show -l -v ExampleResourceGroup
