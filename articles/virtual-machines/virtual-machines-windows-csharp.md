<properties
    pageTitle="Installere Azure ressourcer ved hjælp af C# | Microsoft Azure"
    description="Lær at bruge C# og Azure ressourcestyring til at oprette Microsoft Azure ressourcer."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>Installere Azure ressourcer ved hjælp af C# 

I denne artikel beskrives, hvordan du opretter Azure ressourcer ved hjælp af C#.

Du skal først at sikre, at du er færdig med disse opgaver:

- Installer [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Kontrollere installation af [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Få et [godkendelse token](../resource-group-authenticate-service-principal.md)

Det tager om 30 minutter for at udføre disse trin.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Trin 1: Oprette et Visual Studio-projekt og installere bibliotekerne

NuGet pakker er den nemmeste måde at installere de biblioteker, du vil afslutte dette selvstudium. For at få de biblioteker, du skal bruge i Visual Studio skal du gøre følgende:

1. Klik på **filer** > **nye** > **Project**.

2. I **skabeloner** > **Visual C#**, Vælg **Console program**, angive navn og placering for projektet, og klik derefter på **OK**.

3. Højreklik på projektnavnet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**.

4. Type *Active Directory* i søgefeltet, klik på **Installer** til Active Directory Authentication Library-pakken, og følg derefter vejledningen for at installere pakken.

5. Øverst på siden skal du vælge **Medtage foreløbig version**. Skriv *Microsoft.Azure.Management.Compute* i søgefeltet, klikke på **installation** for de biblioteker, beregne .NET, og følg derefter vejledningen for at installere pakken.

6. Type *Microsoft.Azure.Management.Network* i søgefeltet, klikke på **installation** for de biblioteker, .NET netværk, og følg derefter vejledningen for at installere pakken.

7. Type *Microsoft.Azure.Management.Storage* i søgefeltet, klik på **Installer** for lagerplads .NET biblioteker, og følg derefter vejledningen for at installere pakken.

8. Skriv *Microsoft.Azure.Management.ResourceManager* i søgefeltet skal du klikke på **Installer** for ressource Management biblioteker.

Nu er du klar til at begynde at bruge bibliotekerne til at oprette dit program.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Trin 2: Oprette de legitimationsoplysninger, der bruges til at godkende anmodninger

Nu kan du formatere den, du tidligere har oprettet til legitimationsoplysninger, der bruges til at godkende anmodninger til Azure ressourcestyring programoplysninger.

1. Åbn filen Program.cs for det projekt, du har oprettet, og derefter tilføje dem ved hjælp af sætningerne til toppen af filen:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Oprette tokenet, der skal bruges, skal tilføje denne metode til klassen Program:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    Erstatte {klient-id} med id for Azure Active Directory program, {klient-hemmeligt} hurtigtast AD programmet og {lejer-id} med lejer id for dit abonnement. Du kan finde lejer-id'et ved at køre Get-AzureRmSubscription. Du kan finde hurtigtast ved hjælp af portalen Azure.

3. Hvis du vil ringe den metode, du tidligere har oprettet, du Føj denne kode til metoden Main i filen Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Gem filen Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Trin 3: Registrere udbyderne ressource og oprettes ressourcerne

### <a name="register-the-providers-and-create-a-resource-group"></a>Registrere udbyderne og oprette en ressourcegruppe

Alle de ressourcer, der skal indgå i en ressourcegruppe. Før du kan tilføje ressourcer til en gruppe, skal være registreret dit abonnement med udbyderne ressource.

1. Tilføj variabler i metoden Main i klassen Program til at angive de navne, du vil bruge for ressourcerne:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Erstat alle de variable værdier med navne og id, du vil bruge. Du kan finde abonnement-id ved at køre Get-AzureRmSubscription.

2. Oprette ressourcegruppen og registrere udbydere, skal du tilføje denne metode til klassen Program:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Hvis du vil ringe den metode, du tidligere har oprettet, du Føj denne kode til metoden Main:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

En [lagerplads konto](../storage/storage-create-storage-account.md) skal bruges til at gemme den virtuelle harddisk-fil, der er oprettet for den virtuelle maskine.

1. Oprette kontoen lagerplads, skal tilføje denne metode til klassen Program:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. For at ringe den metode, du tidligere har oprettet, kan du føje denne kode til metoden Main i klassen Program:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Oprette den offentlige IP-adresse

En offentlig IP-adresse er nødvendig for at kommunikere med den virtuelle maskine.

1. Hvis du vil oprette den offentlige IP-adresse på den virtuelle maskine, du Føj denne metode til klassen Program:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. For at ringe den metode, du tidligere har oprettet, kan du føje denne kode til metoden Main i klassen Program:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Oprette det virtuelle netværk

En virtuel maskine, der er oprettet med implementeringsmodel ressourceleder, der skal være i et virtuelt netværk.

1. Oprette et undernet og et virtuelt netværk, skal tilføje denne metode til klassen Program:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. For at ringe den metode, du tidligere har oprettet, kan du føje denne kode til metoden Main i klassen Program:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>Oprette netværksgrænsefladen

En virtuel maskine skal have en netværksgrænseflade til at kommunikere på det virtuelle netværk.

1. Hvis du vil oprette en netværksgrænseflade, du Føj denne metode til klassen Program:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. For at ringe den metode, du tidligere har oprettet, kan du føje denne kode til metoden Main i klassen Program:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>Oprette et sæt tilgængelighed

Tilgængelighed sæt gør det nemmere for dig at administrere vedligeholdelse af de virtuelle maskiner, der bruges af programmet.

1. Oprette sættet tilgængelighed, tilføje denne metode til klassen Program:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. For at ringe den metode, du tidligere har oprettet, kan du føje denne kode til metoden Main i klassen Program:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Oprette en virtuel maskine

Nu hvor du har oprettet alle de ressourcer, der er understøttende, kan du oprette en virtuel maskine.

1. Hvis du vil oprette den virtuelle maskine, du Føj denne metode til klassen Program:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] Dette selvstudium opretter en virtuel maskine, der kører en version af Windows Server-operativsystemet. Hvis du vil vide mere om at vælge andre billeder, skal du se [Naviger og vælge Azure virtuelt billeder med Windows PowerShell og Azure CLI](virtual-machines-linux-cli-ps-findimage.md).

2. Hvis du vil ringe den metode, du tidligere har oprettet, du Føj denne kode til metoden Main:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>Trin 4: Slet ressourcerne

Fordi du betaler for ressourcer, der bruges i Azure, er det altid er en god ide at slette ressourcer, som ikke længere skal bruges. Hvis du vil slette de virtuelle maskiner og alle de supplerende ressourcer, du skal gøre blot slette ressourcegruppen.

1.  Hvis du vil slette ressourcegruppen, skal du føje denne metode til klassen Program:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Hvis du vil ringe den metode, du tidligere har oprettet, du Føj denne kode til metoden Main:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>Trin 5: Køre programmet console

1. Hvis du vil køre programmet console, klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

2. Tryk på **Enter** efter hver statuskode returneres til at oprette hver ressource. Når den virtuelle maskine er oprettet, skal du gøre det næste trin før du trykker på Enter for at slette alle ressourcerne.

    Det skal tage om fem minutter for denne console program tilladelse til at køre helt fra start til slut. Før du trykker på Enter for at begynde at slette ressourcer, kan du tage et par minutter til at kontrollere oprettelse af ressourcer på portalen Azure, før du slette dem.

3. For at se status for ressourcerne, der skal du gå til overvågningslogge i portalen Azure:

    ![Gennemse overvågningslogge Azure-portalen](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>Næste trin

- Udnyt ved hjælp af en skabelon til at oprette en virtuel maskine ved hjælp af oplysningerne på [Implementer en Azure virtuelt med C# og en ressourcestyring skabelon](virtual-machines-windows-csharp-template.md).
- Få mere at vide, hvordan du administrerer den virtuelle maskine, du har oprettet ved at gennemgå [Administrer virtuelle maskiner ved hjælp af Azure ressourcestyring og PowerShell](virtual-machines-windows-csharp-manage.md).
