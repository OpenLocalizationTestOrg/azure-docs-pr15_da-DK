## <a name="obtain-an-azure-resource-manager-token"></a>Få et Azure Resource Manager token

Azure Active Directory skal godkende alle de opgaver, du udfører på ressourcer ved hjælp af Azure Resource Manager. I eksemplet her bruger autentifikation af adgangskode finder andre muligheder i [anmodninger om godkendelse af Azure Resource Manager][lnk-authenticate-arm].

1. Føj følgende kode til **Main** -metode i Program.cs til at hente et token fra Azure AD ved hjælp af program-id og adgangskode.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Opret et **ResourceManagementClient** -objekt, der bruger tokenet ved at føje følgende kode til slutningen af den **vigtigste** metode:

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Oprette eller hente en reference til den ressourcegruppe, du bruger:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx