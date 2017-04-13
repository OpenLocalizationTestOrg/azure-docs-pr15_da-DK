<properties
    pageTitle="Ofte stillede spørgsmål om Windows FOS | Microsoft Azure"
    description="Indeholder svar på nogle af de almindelige spørgsmål om Windows virtuelle maskiner, der er oprettet med ressourcestyring modellen."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Ofte stillede spørgsmål om Windows virtuelle maskiner 


I denne artikel omhandler nogle almindelige spørgsmål om Windows virtuelle maskiner, der er oprettet i Azure ved hjælp af Ressourcestyring implementeringsmodel. Linux-version af dette emne, skal du se [ofte stillede spørgsmål om Linux virtuelle maskiner](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Hvad kan jeg køre på en Azure VM?

Alle abonnenter kan køre serversoftware på en Azure virtuelt. Finde oplysninger om supportpolitikken til kørsel af Microsoft-serversoftware i Azure, i [Microsoft-serversoftware understøtter for virtuelle Azure-computere](https://support.microsoft.com/kb/2721672)

Visse versioner af Windows 7 og Windows 8.1 er tilgængelige for MSDN Azure benefit abonnenter og abonnenter på MSDN-Udviklingscenter og teste Pay-As-You-Go, for udvikling og test opgaver. Du kan finde oplysninger, herunder instruktioner og begrænsninger, [Windows-klient billeder til MSDN-abonnenter](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hvor meget lagerplads kan jeg bruge sammen med en virtuel maskine?

Hver datadisk kan være op til 1 TB. Antallet af datadisce, du kan bruge, afhænger af størrelsen på den virtuelle maskine. Yderligere oplysninger finder du [størrelser til virtuelle computere](virtual-machines-windows-sizes.md).

En Azure lagerplads konto giver lagring til operativsystem disken og en hvilken som helst datadisce. Hver disk er en .vhd-fil, der er gemt som en side blob. Se [Lagerplads priser detaljer](https://azure.microsoft.com/pricing/details/storage/)for priser detaljer.


## <a name="how-can-i-access-my-virtual-machine"></a>Hvordan kan jeg få adgang til min virtuelt?

Oprette en ekstern forbindelse ved hjælp af Remote Desktop Connection (RDP) til en Windows-VM. Finde en vejledning, se, [hvordan du opretter forbindelse og logge på en Azure virtuelt, der kører Windows](virtual-machines-windows-connect-logon.md). Op til to samtidige forbindelser understøttes, medmindre serveren er konfigureret som en Remote Desktop Services sessionsværten.  


Hvis du har problemer med Fjernskrivebord, se [fejlfinding af Fjernskrivebord forbindelser til en Windows-baseret Azure virtuel maskine](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Hvis du kender til Hyper-V, kan du søger efter et værktøj, der svarer til VMConnect. Azure tilbyder ikke et lignende værktøj, fordi console adgang til en virtuel maskine ikke understøttes.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan jeg bruge den midlertidige disk (D: drev som standard) til at gemme data?

Brug ikke midlertidige disken til at gemme data. Det er kun midlertidige lagerplads, så du risikerer at miste data, der ikke kan gendannes. Tab af data kan opstå, når den virtuelle maskine flyttes til en anden vært. Ændre størrelsen på en virtuel maskine, er opdaterer værten eller en hardwarefejl på værten nogle af grundene til en virtuel maskine kan flytte.

Hvis du har et program, der skal bruge bogstavet D:, kan du tildele drevbogstaver, så den midlertidige disk bruger et andet tal end D:. Flere oplysninger under [ændre bogstavet for Windows midlertidige disken](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hvordan kan jeg ændre bogstavet for den midlertidige disk?

Du kan ændre bogstavet ved at flytte siden filen og gentildele drevbogstaver, men du har brug at sikre, at du udføre trinnene i en bestemt rækkefølge. Flere oplysninger under [ændre bogstavet for Windows midlertidige disken](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan jeg tilføje en eksisterende VM til en tilgængelighed indstilling?

Nej. Hvis du vil din VM skal være en del af en tilgængelighed er defineret, skal du oprette VM inden for sæt. Der i øjeblikket ikke er en måde at tilføje en VM til en tilgængelig Angiv, når den er blevet oprettet.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan jeg overføre en virtuel maskine til Azure?

Ja. Flere oplysninger under [overføre en Windows VM billede til Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>Kan jeg tilpasse OS disken?

Ja. Finde en vejledning, se, [hvordan du udvide OS drevet af en virtuel maskine i en Azure ressourcegruppe](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jeg kopiere eller klone en eksisterende Azure VM?

Ja. Finde en vejledning, se, [hvordan du oprette en kopi af en Windows virtuel maskine i implementeringsmodel ressourcestyring](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Hvorfor får jeg ikke vist Canada Central- og Canada Øst områder via Azure ressourcestyring?

De to nye områder af Canada Central- og Canada Øst registreres ikke automatisk til oprettelse af virtuelt for eksisterende Azure abonnementer. Denne registrering sker automatisk, når en virtuel maskine installeres via Azure-portalen til en hvilken som helst anden region, ved hjælp af Azure ressourcestyring. Når en virtuel maskine er distribueret til Azure område, skal de nye områder være tilgængelige for efterfølgende virtuelle computere.

## <a name="does-azure-support-linux-vms"></a>Understøtter Azure Linux FOS?

Ja. Se [oprette en Linux VM på Azure ved hjælp af portalen](virtual-machines-linux-quick-create-portal.md)for hurtigt at oprette en Linux VM til at teste.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jeg tilføje en NIC til min VM, når den er blevet oprettet?

Nej. Tilføje en NIC kan kun udføres på klokkeslættet for oprettelsen af.

## <a name="are-there-any-computer-name-requirements"></a>Er der en hvilken som helst computer navn?

Ja. Navnet på den computer kan maksimalt være på 15 tegn. Du kan få flere oplysninger om navngivning af dine ressourcer i [infrastruktur naming retningslinjer](virtual-machines-windows-infrastructure-naming-guidelines.md) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Hvad er kravene brugernavn, når du opretter en VM?

Brugernavne kan være maksimalt 20 tegn og kan ikke slutter med et punktum ("."). 

De følgende brugernavne er ikke tilladt:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> administrator </td><td style="text-align:center"> bruger </td><td style="text-align:center"> bruger 1</td>
    </tr>
    <tr>
        <td style="text-align:center">Test </td><td style="text-align:center"> User2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> User3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> en</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">sikkerhedskopi </td><td style="text-align:center"> Console </td><td style="text-align:center"> David </td><td style="text-align:center"> Gæst</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> ejer </td><td style="text-align:center"> rod </td><td style="text-align:center"> Server</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> for</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> User4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Hvad er kravene til adgangskoden, når du opretter en VM?

Adgangskoder skal være 8 123 tegn og opfylder 3 af 4 kompleksitet følgende krav:

- Har lavere tegn
- Har øverste tegn
- Har et ciffer
- Har et specialtegn (Regex overens med [\W_])

De følgende adgangskoder er ikke tilladt:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$ word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Adgangskode!</td><td style="text-align:center">Adgangskode1</td><td style="text-align:center">Password22</td><td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
