<properties
    pageTitle="Ofte stillede spørgsmål om Linux FOS | Microsoft Azure"
    description="Indeholder svar på nogle af de almindelige spørgsmål om Linux virtuelle maskiner, der er oprettet med ressourcestyring modellen."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Ofte stillede spørgsmål om Linux virtuelle maskiner

I denne artikel omhandler nogle almindelige spørgsmål om Linux virtuelle maskiner, der er oprettet i Azure ved hjælp af Ressourcestyring implementeringsmodel. For Windows-versionen af dette emne, se [ofte stillede spørgsmål om Windows virtuelle maskiner](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Hvad kan jeg køre på en Azure VM?

Alle abonnenter kan køre serversoftware på en Azure virtuelt. Du kan finde flere oplysninger, se [Linux på Azure-Endorsed Salgsdistributioner](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hvor meget lagerplads kan jeg bruge sammen med en virtuel maskine?

Hver datadisk kan være op til 1 TB. Antallet af datadisce, du kan bruge, afhænger af størrelsen på den virtuelle maskine. Yderligere oplysninger finder du [størrelser til virtuelle computere](virtual-machines-linux-sizes.md).

En Azure lagerplads konto giver lagring til operativsystem disken og en hvilken som helst datadisce. Hver disk er en .vhd-fil, der er gemt som en side blob. Se [Lagerplads priser detaljer](https://azure.microsoft.com/pricing/details/storage/)for priser detaljer.


## <a name="how-can-i-access-my-virtual-machine"></a>Hvordan kan jeg få adgang til min virtuelt?

Oprette en ekstern forbindelse til at logge på den virtuelle maskine, ved hjælp af Secure Shell (SSH). Se vejledningen i at oprette forbindelse [fra Windows](virtual-machines-linux-ssh-from-windows.md) eller [Linux- og Mac](virtual-machines-linux-mac-create-ssh-keys.md). SSH kan som standard maksimalt 10 samtidige forbindelser. Du kan øge dette tal ved at redigere konfigurationsfilen.


Hvis du har problemer, se [fejlfinding i forbindelse med Secure Shell (SSH) forbindelser](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan jeg bruge den midlertidige disk (/ Udviklingscenter/sdb1) til at gemme data?

Brug ikke midlertidige disken (/ Udviklingscenter/sdb1) til at gemme data. Det er kun der til midlertidig lagring. Du risikoen for at miste data, der ikke kan gendannes.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jeg kopiere eller klone en eksisterende Azure VM?

Ja. Finde en vejledning, se, [hvordan du oprette en kopi af en Linux virtuel maskine i implementeringsmodel ressourcestyring](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Hvorfor får jeg ikke vist Canada Central- og Canada Øst områder via Azure ressourcestyring?

De to nye områder af Canada Central- og Canada Øst registreres ikke automatisk til oprettelse af virtuelt for eksisterende Azure abonnementer. Denne registrering sker automatisk, når en virtuel maskine installeres via Azure-portalen til en hvilken som helst anden region, ved hjælp af Azure ressourcestyring. Når en virtuel maskine er distribueret til Azure område, skal de nye områder være tilgængelige for efterfølgende virtuelle computere.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jeg tilføje en NIC til min VM, når den er blevet oprettet?

Nej. Tilføje en NIC kan kun udføres på klokkeslættet for oprettelsen af.


## <a name="are-there-any-computer-name-requirements"></a>Er der en hvilken som helst computer navn?

Ja. Navnet på den computer, kan være op til 64 tegn. Du kan få flere oplysninger om navngivning af dine ressourcer i [infrastruktur naming retningslinjer](virtual-machines-linux-infrastructure-naming-guidelines.md) .


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Hvad er kravene brugernavn, når du opretter en VM?

Brugernavne skal være 1-64 tegn.

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

Adgangskoder skal være 6-72 tegn og opfylder 3 af 4 kompleksitet følgende krav:

- Har nederste tegn
- Har øverste tegn
- Har et ciffer
- Har et specialtegn (Regex overens med [\W_])

De følgende adgangskoder er ikke tilladt:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$ word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Adgangskode!</td>
        <td style="text-align:center">Adgangskode1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
