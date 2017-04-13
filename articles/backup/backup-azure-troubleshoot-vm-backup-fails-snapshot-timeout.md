<properties
   pageTitle="Azure VM sikkerhedskopi mislykkes: kunne ikke kommunikere med VM agent for øjebliksbillede status - øjebliksbillede VM underordnede opgave fik timeout | Microsoft Azure"
   description="Symptomer årsager og løsninger til Azure VM sikkerhedskopiering fejl, der er relateret til kunne ikke kommunikere med VM agent for snapshot status. Øjebliksbillede VM underordnede opgave timeout for fejl"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Azure VM sikkerhedskopi mislykkes: kunne ikke kommunikere med VM agent for øjebliksbillede status - øjebliksbillede VM underordnede opgave fik timeout

## <a name="summary"></a>Oversigt

Når du registrere og planlægge et virtuelt (VM) til Azure sikkerhedskopiering, starter tjenesten Azure sikkerhedskopiering sikkerhedskopieringen på det planlagte tidspunkt ved at kommunikere med filtypenavnet sikkerhedskopiering i VM til at tage et øjebliksbillede af punkt-in-time. Der er betingelser, der kan forhindre snapshot der udløste, som fører til en sikkerhedskopi fejl. I denne artikel indeholder trin til fejlfinding for problemer i forbindelse med Azure VM sikkerhedskopiering fejl, der er relateret til snapshot timeoutfejl.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptom

Microsoft Azure Backup til infrastruktur som en tjeneste (IaaS) VM mislykkes, og returnerer følgende fejlmeddelelse i jobbet oplysninger om fejlen i [Azure-portalen](https://portal.azure.com/):

**Kunne ikke kommunikere med VM agent for øjebliksbillede status - timeout for øjebliksbillede VM underordnede opgave.**

## <a name="cause"></a>Årsag
Der er fire almindelige årsager til denne fejl:

- VM har ikke adgang til internettet.
- De Microsoft Azure VM agent er installeret i VM er forældet (for Linux FOS).
- Filtypenavnet sikkerhedskopiering kan ikke opdatere eller indlæse.
- Snapshots status kan ikke hentes, eller de kan ikke oprettes øjebliksbilleder.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Årsag 1: VM har ikke adgang til internettet
Per kravet installation VM har ingen forbindelse til internettet, eller har begrænsninger på plads, som forhindrer adgang til Azure infrastrukturen.

Filtypenavnet sikkerhedskopiering kræver forbindelse til de Azure offentlige IP-adresser til at fungere korrekt. Dette skyldes, at den sender kommandoer til Azure-lager ark (HTTP URL) til at administrere snapshot af VM. Hvis filtypenavnet ikke har adgang til det offentlige Internet, mislykkes sikkerhedskopien til sidst.

### <a name="solution"></a>Løsning
I dette scenarie skal du bruge en af følgende metoder til at løse problemet:

- Whitelist Azure datacenter IP-områder
- Oprette en sti til HTTP-trafik til mailflow

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Til whitelist på Azure områder datacenter IP-

1. Få vist [listen over Azure datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) for at være whitelisted.
2. Fjerne blokeringen af IP-adresser ved hjælp af ny NetRoute cmdlet. Kør denne cmdlet i Azure VM i en øgede PowerShell-vinduet (Kør som Administrator).
3. Tilføje regler til netværk sikkerhed gruppe (NSG), hvis du har en at give adgang til IP-adresser.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Oprette en sti for HTTP-trafik til mailflow

1. Hvis du har netværk begrænsninger på sted (for eksempel en NSG), skal du installere en HTTP-proxy-server for at omdirigere trafikken.
2. Hvis du har en netværk sikkerhedsgruppe (NSG), kan du tilføje regler for at give adgang til internettet fra HTTP-proxy.

Lær, hvordan du [konfigurerer en HTTP-proxy for VM sikkerhedskopier](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Årsag 2: Den Microsoft Azure VM agent er installeret i VM er forældet (for Linux FOS)

### <a name="solution"></a>Løsning
Mest agent-relaterede eller filtypenavn-relaterede fejl for Linux FOS skyldes problemer, der påvirker en gamle VM agent. Som en generel vejledning er de første trin til at foretage fejlfinding af problemet følgende:

1. [Installere den nyeste version Azure VM agent](https://github.com/Azure/WALinuxAgent).
2. Sørg for, at Azure agent der kører på VM. For at gøre dette skal du køre følgende kommando:```ps -e```

    Hvis denne proces ikke kører, kan du bruge følgende kommandoer til at genstarte den.

    For Ubuntu:```service walinuxagent start```

    For andre salgsdistributioner: "' service waagent start
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|
