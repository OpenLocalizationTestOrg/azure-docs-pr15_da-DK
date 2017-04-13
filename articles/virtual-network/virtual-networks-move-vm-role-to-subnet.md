<properties 
   pageTitle="Sådan flyttes en forekomst af VM eller rolle til et andet undernet"
   description="Lær, hvordan du flytter FOS og rolle forekomster til et andet undernet"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Sådan flyttes en forekomst af VM eller rolle til et andet undernet

Du kan bruge PowerShell til at flytte dine FOS fra ét undernet til en anden i det samme virtuelle netværk (VNet). Rolle forekomster kan flyttes ved at redigere CSCFG i stedet for at bruge PowerShell.

>[AZURE.NOTE] I denne artikel indeholder oplysninger, der er i forhold til kun Azure klassisk installationer.

Hvorfor flytte FOS til en anden undernet? Overførsel af undernet er nyttig, når det ældre undernet er for lille og kan ikke udvides på grund af eksisterende kører FOS i pågældende undernet. Det er tilfældet, kan du oprette et nyt, større undernet og overføre FOS til det nye undernet, og når overførslen er fuldført, kan du slette det gamle tomme undernet.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Sådan flytter du en VM til en anden undernet

Hvis du vil flytte en VM, skal du køre sæt AzureSubnet PowerShell-cmdlet, i eksemplet nedenfor som en skabelon. I eksemplet nedenfor vi flytte TestVM fra dens Præsenter undernet til undernet 2. Sørg for at redigere eksempel afspejler dit miljø. Bemærk, at når du kører Cmdletten opdatering AzureVM som en del af en procedure, det genstarter din VM som en del af opdateringsprocessen.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Hvis du har angivet en statiske interne private IP-Adresser til din VM, får du rydde indstillingen, før du kan flytte VM til en ny undernet. I dette tilfælde skal du bruge følgende:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Flytte en forekomst af rolle til en anden undernet

Hvis du vil flytte en forekomst af rolle, skal du redigere filen CSCFG. I eksemplet nedenfor, vi flyttes "Role0" i virtuelt netværk *VNETName* fra dens Præsenter undernet til *undernet 2*. Fordi den rolle forekomst blev allerede installeret, skal du blot ændre navnet på undernet = undernet 2. Sørg for at redigere eksempel afspejler dit miljø.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
