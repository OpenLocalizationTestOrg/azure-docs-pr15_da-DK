## <a name="how-to-deploy-with-powershell"></a>Hvordan du installerer med PowerShell

1. Log på din Azure-konto.

          Add-AzureAccount

   Når du har indtastet dine legitimationsoplysninger, returnerer kommandoen oplysninger om din konto.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Hvis du har flere abonnementer, skal du give det abonnement-id, du vil bruge til installation. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Skifte til modulet Azure ressourcestyring.

          Switch-AzureMode AzureResourceManager

4. Hvis du ikke har en eksisterende ressourcegruppe, kan du oprette en ny ressourcegruppe. Angiv navnet på den ressourcegruppe og placering, du skal bruge til din løsning.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Der returneres en oversigt over den nye ressourcegruppe.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Kør kommandoen **Ny AzureResourceGroupDeployment** for at oprette en ny installation til ressourcegruppen, og Angiv de nødvendige parametre. Parametrene, der indeholder et navn til din installation, navnet på din ressourcegruppe, sti eller URL-adressen til den skabelon, du har oprettet og andre parametre, der kræves for det pågældende scenarie. 
   
   Du har følgende indstillinger til at give parameterværdier: 
   
   - Bruge indbyggede parametre.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Brug et parameter-objekt.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Brug af en parameterfil.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Når ressourcegruppen er blevet installeret, får du vist en oversigt over installationen.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. For at få oplysninger om installation mislykkede forsøg.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. For at få detaljerede oplysninger om installation mislykkede forsøg.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
