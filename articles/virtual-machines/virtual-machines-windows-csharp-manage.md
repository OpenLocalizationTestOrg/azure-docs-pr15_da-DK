<properties
    pageTitle="Administrere FOS ved hjælp af Azure ressourcestyring og C# | Microsoft Azure"
    description="Administrere virtuelle maskiner ved hjælp af Azure ressourcestyring og C#."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Administrere virtuelle Azure-computere ved hjælp af Azure ressourcestyring og C#  

Opgaverne i denne artikel viser dig, hvordan du administrerer virtuelle maskiner, som starte, stoppe og opdatering af. En virtuel maskine skal findes i en ressourcegruppe til at udføre opgaver i denne artikel.

For at fuldføre opgaverne i denne artikel, skal du:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Et godkendelse token](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>Oprette et Visual Studio-projekt og installere pakker

NuGet pakker er den nemmeste måde at installere de biblioteker, du har brug for at færdiggøre opgaverne i denne artikel. De biblioteker, du installerer til denne artikel er Azure Active Directory Authentication Library og biblioteket beregne ressource udbyder. Følg disse trin for at få vist bibliotekerne i Visual Studio:

1. Klik på **filer** > **nye** > **Project**.

2. I **skabeloner** > **Visual C#**, Vælg **Console program**, angive navn og placering for projektet, og klik derefter på **OK**.

3. Højreklik på projektnavnet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**.

4. Type *Active Directory* i søgefeltet, klik på **Installer** til Active Directory Authentication Library-pakken, og følg derefter vejledningen for at installere pakken.

5. Øverst på siden skal du vælge **Medtage foreløbig version**. Skriv *Microsoft.Azure.Management.Compute* i søgefeltet, klikke på **installation** for de biblioteker, beregne .NET, og følg derefter vejledningen for at installere pakken.

Nu er du klar til at begynde at bruge bibliotekerne til at administrere din virtuelle computere.

## <a name="set-up-the-project"></a>Konfigurere projektet

Nu, hvor programmet er oprettet, og bibliotekerne, der er installeret, kan du oprette et token ved hjælp af oplysningerne om programmet. Dette token bruges til at godkende anmodninger til Azure ressourcestyring.

1. Åbn filen Program.cs for det projekt, du har oprettet, og derefter tilføje dem ved hjælp af sætningerne til toppen af filen:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Tilføj variabler i metoden Main i klassen Program til at angive navnet på ressourcegruppen og navnet på den virtuelle maskine og dit abonnement-id:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";

    Du kan finde abonnement-id ved at køre Get-AzureRmSubscription.
    
3. For at få det token, der skal bruges til at oprette legitimationsoplysningerne, du Tilføj denne metode til klassen Program:

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
    
4. Oprette legitimationsoplysningerne, skal tilføje denne kode til metoden Main i Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Gem filen Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Få vist oplysninger om en virtuel maskine

1. Føje denne metode til klassen Program i det projekt, som du tidligere har oprettet:

        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
          
        }

2. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Gem filen Program.cs.

4. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

    Når du kører denne metode, skal du se noget i retning af dette eksempel:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Stoppe en virtuel maskine

Du kan stoppe en virtuel maskine på to måder. Du kan stoppe en virtuel maskine og bevare alle indstillingerne, men fortsætte med at betale for det, eller du kan stoppe en virtuel maskine og Dealloker den. Når en virtuel maskine er blevet deallokeret, er alle de ressourcer, der er knyttet til den også deallokeres og faktureringsadministrator ender for den.

1. Kommentarer til en hvilken som helst kode, du tidligere har føjet til metoden Main undtagen i koden for at få legitimationsoplysningerne.

2. Føje denne metode til klassen Program:

        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }

    Hvis du vil deallokere den virtuelle maskine, skal du ændre Sluk computeren opkald til denne kode:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Gem filen Program.cs.

5. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

    Du bør se status for stoppet virtuelt ændringen. Hvis du har kørt den metode, ikke er længere opkald Deallocate, status (deallokeres).

## <a name="start-a-virtual-machine"></a>Starte en virtuel maskine

1. Kommentarer til en hvilken som helst kode, du tidligere har føjet til metoden Main undtagen i koden for at få legitimationsoplysningerne.

2. Føje denne metode til klassen Program:

        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Gem filen Program.cs.

5. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

    Du bør se status for den virtuelle maskine ændre til kører.

## <a name="restart-a-running-virtual-machine"></a>Genstarte en igangværende virtuel maskine

1. Kommentarer til en hvilken som helst kode, du tidligere har føjet til metoden Main undtagen i koden for at få legitimationsoplysningerne.

2. Føje denne metode til klassen Program:

        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Gem filen Program.cs.

5. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

## <a name="resize-a-virtual-machine"></a>Ændre størrelsen på en virtuel maskine

I dette eksempel viser, hvordan du ændre størrelsen på en igangværende virtuel maskine.

1. Kommentarer til en hvilken som helst kode, du tidligere har føjet til metoden Main undtagen i koden for at få legitimationsoplysningerne.

2. Føje denne metode til klassen Program:

        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Gem filen Program.cs.

5. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

    Du bør se størrelsen på Standard_A1 virtuelt ændringen.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Føje disken data til en virtuel maskine

I dette eksempel viser, hvordan du føjer en datadisk til en igangværende virtuel maskine.

1. Kommentarer til en hvilken som helst kode, du tidligere har føjet til metoden Main undtagen i koden for at få legitimationsoplysningerne.

2. Føje denne metode til klassen Program:

        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Gem filen Program.cs.

5. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

## <a name="delete-a-virtual-machine"></a>Slette en virtuel maskine

1. Kommentarer til en hvilken som helst kode, du tidligere har føjet til metoden Main undtagen i koden for at få legitimationsoplysningerne.

2. Føje denne metode til klassen Program:

        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Gem filen Program.cs.

5. Klik på **Start** i Visual Studio, og derefter logge på Azure AD ved hjælp af det samme brugernavn og adgangskode, du bruger til dit abonnement.

## <a name="next-steps"></a>Næste trin

Hvis der var problemer med en installation, kan du se [fejlfinding ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)
