<properties 
    pageTitle="Bruge SSH nøgler med Windows til Linux FOS | Microsoft Azure" 
    description="Lær at oprette og bruge SSH på en Windows-computer til at oprette forbindelse til en Linux virtuel maskine på Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Sådan Brug SSH taster med vinduer på Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

Når du opretter forbindelse til Linux virtuelle maskiner (VM'er) i Azure, skal du bruge [kryptering med offentlig nøgle](https://wikipedia.org/wiki/Public-key_cryptography) til en mere sikker måde til at logge på din Linux VM. Denne proces involverer en offentlige og private nøgler udveksling ved hjælp af kommandoen sikker shell (SSH) til at godkende dig selv i stedet for et brugernavn og din adgangskode. Adgangskoder er udsatte for skadelige angreb, især på internettet FOS som Web-servere. I denne artikel giver et overblik over SSH nøgler og hvordan du kan generere de korrekte nøgler på en Windows-computer.


## <a name="overview-of-ssh-and-keys"></a>Oversigt over SSH og nøgler

Du kan sikkert log på din Linux VM ved hjælp af offentlige og private nøgler:

- **Offentlig nøgle** er placeret på din Linux VM, eller en anden tjeneste, du vil bruge med offentlig nøgle-kryptering.
- **Privat nøgle** er, hvad du præsentere til din Linux VM når du logger på, for at bekræfte din identitet. Beskyt denne privat nøgle. Undlad at dele den.

Disse offentlige og private nøgler kan bruges på flere FOS og -tjenester. Du behøver ikke et par af nøgler for hver VM eller tjeneste, du vil have adgang til. Du kan finde en mere detaljeret overblik [kryptering med offentlig nøgle](https://wikipedia.org/wiki/Public-key_cryptography).

SSH er en krypteret forbindelse-protokollen, der giver mulighed for sikker logon over usikker forbindelser. Det er standard forbindelsesprotokol for Linux FOS Azure som vært. Selvom SSH selve giver en krypteret forbindelse, lader bruger adgangskoder med SSH forbindelser stadig VM udsatte til skadelige angreb eller gætte adgangskoder. Der er en mere sikker og Foretrukne, metode til at oprette forbindelse til en VM ved hjælp af SSH ved hjælp af disse offentlige og private nøgler, også kaldet SSH taster.

Hvis du ikke vil bruge SSH taster, kan du stadig logge på din Linux FOS ved hjælp af en adgangskode. Hvis din VM ikke er være tilgængelig på internettet, kan det være tilstrækkelige bruge adgangskoder. Men du stadig har brug at administrere dine adgangskoder for hver Linux VM og vedligeholde sund adgangskodepolitikker og fremgangsmåder, som minimum adgangskodelængde og opdaterer dem regelmæssigt. Brug af SSH taster reducerer kompleksiteten af administration af individuelle legitimationsoplysninger på tværs af flere FOS.


## <a name="windows-packages-and-ssh-clients"></a>Windows-pakker og SSH klienter

Du kan oprette forbindelse til og administrere Linux FOS i Azure ved hjælp af en **ssh** -klient. Windows-computere typisk er klienten ikke en **ssh** installeret. Almindelige Windows SSH klienter kan du installere er inkluderet i følgende:

- [Ciffer til Windows](https://git-for-windows.github.io/)
- [Trykfarver](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] Den seneste opdatering til Windows 10 Mærkedag omfatter Bash til Windows. Denne funktion giver dig mulighed at køre Windows-undersystem til Linux og access funktioner som en SSH-klient. Bash til Windows er stadig under udvikling, og der betragtes som en betaversion. Du kan finde flere oplysninger om Bash til Windows, kan du se [Bash på Ubuntu på Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>Hvilke vigtige filer skal du oprette?

Azure kræver mindst 2048-bit **ssh rsa** formatere offentlige og private nøgler. Hvis du administrerer Azure ressourcer ved hjælp af modellen Klassisk installation, skal du også oprette en PEM (`.pem` filer).

Her er installation scenarierne, og typerne filer, du bruger i hver:

1. **SSH rsa** -nøgler der kræves til en hvilken som helst ved hjælp af [Azure portal](https://portal.azure.com)installation og ressourcestyring installationer ved hjælp af [Azure CLI](../xplat-cli-install.md).
    - Disse taster er som regel alle de fleste personer har brug for.
2. `.pem`filen er påkrævet for at oprette FOS ved hjælp af [Klassisk portal](https://manage.windowsazure.com). Disse taster understøttes også i klassisk installationer, der bruger [Azure CLI](../xplat-cli-install.md).
    - Du skal kun oprette disse yderligere nøgler og certifikater, hvis du administrerer ressourcer, der er oprettet ved hjælp af implementeringsmodel klassisk.


## <a name="install-git-for-windows"></a>Installere ciffer til Windows

Den foregående sektion vises flere pakker, som indeholder den `openssl` værktøj til Windows. Dette værktøj er behov for at oprette offentlige og private nøgler. I følgende eksempler Detaljeformater hvordan du kan installere og bruge **Ciffer til Windows**, selvom du kan vælge den pakke, du foretrækker. **Ciffer til Windows** giver dig adgang til nogle ekstra open source-software ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) værktøjer og funktioner, der kan være nyttige, når du arbejder med Linux FOS.

1. Hent og Installer **Ciffer til Windows** fra følgende placering: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Acceptér standardindstillingerne under installationen, medmindre du specifikt skal ændre dem.

3. Køre **Ciffer fest** fra **menuen Start** > **ciffer** > **Ciffer fest**. Konsollen ligner i følgende eksempel:

    ![Ciffer for Windows Bash shell](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Oprette en privat nøgle

1. Brug af i vindue **Ciffer fest** `openssl.exe` til at oprette en privat nøgle. I følgende eksempel oprettes en nøgle med navnet `myPrivateKey` og certifikat med navnet `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    Output ligner i følgende eksempel:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Besvar instruktionerne til landenavn, placering, Organisationsnavn osv.

3. Din nye privat nøgle og certifikat oprettes i din aktuelle arbejdsmappe. For bedste fremgangsmåder for sikkerhed, skal du indstille tilladelser på din private nøgle, så kun du kan få adgang til det:

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Hvis du også har brug for til at administrere klassisk ressourcer, konvertere den `myCert.pem` til `myCert.cer` (DER-kodet X509 certifikat). Udføre dette valgfrit trin kun, hvis du har brug for til at administrere specifikt ældre klassisk ressourcer. 

    Konvertere det certifikat, ved hjælp af følgende kommando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Oprette en privat nøgle til trykfarver

Trykfarver er en fælles SSH klient til Windows. Du kan frit at bruge en hvilken som helst SSH-klienten, som du ønsker. Hvis du vil bruge trykfarver, skal du oprette en ekstra type nøgle – en trykfarver privat nøgle (PPK). Hvis du ikke vil bruge trykfarver, skal du springe dette afsnit.

I følgende eksempel oprettes denne yderligere privat nøgle specifikt for trykfarver bruge:

1. Brug **Ciffer fest** til at konvertere din private nøgle til en privat RSA-nøgle, der kan forstå PuTTYgen. I følgende eksempel oprettes en nøgle med navnet `myPrivateKey_rsa` fra den eksisterende nøgle med navnet `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    For bedste fremgangsmåder for sikkerhed, skal du indstille tilladelser på din private nøgle, så kun du kan få adgang til det:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Downloade og køre PuTTYgen fra følgende placering: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Klik på menuen: **fil** > **indlæse en privat nøgle**

4. Find din private nøgle (`myPrivateKey_rsa` i det forrige eksempel). Når du starter **Ciffer fest** standardmappen `C:\Users\%username%`. Ændre filfilter til at vise **alle filer (\*.\*)**:

    ![Indlæse eksisterende privat nøgle i PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Klik på **Åbn**. En meddelelse om angiver, tasten er blevet importeret:

    ![Importeret nøgle til PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Klik på **OK** for at lukke prompten.

7. Offentlig nøgle vises øverst i vinduet **PuTTYgen** . Du kopierer og indsætter denne offentlig nøgle til portalen Azure eller Azure ressourcestyring skabelon, når du opretter en Linux VM. Du kan også klikke på **Gem offentlig nøgle** for at gemme en kopi på din computer:

    ![Gemme trykfarver offentlig nøgle fil](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    I følgende eksempel viser, hvordan du vil kopiere og indsætte denne offentlig nøgle i portalen Azure, når du opretter en Linux VM. Offentlig nøgle er typisk derefter gemt i `~/.ssh/authorized_keys` på din nye VM.

    ![Brug offentlig nøgle, når du opretter en VM i portalen Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. Klik på tilbage i **PuTTYgen**, **Gem privat nøgle**:

    ![Gem trykfarver privat nøgle](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] En meddelelse spørger, hvis du vil fortsætte uden at angive en adgangskode til din nøgle. En adgangskode er som en adgangskode, der er knyttet til din private nøgle. Selvom en person skulle få din private nøgle, vil de stadig ikke kunne godkendes ved hjælp af lige tasten. De skal også adgangskoden. Uden en adgangskode, hvis nogen henter din private nøgle, kan de logge på en hvilken som helst VM eller tjeneste, der bruger tasten. Vi anbefaler, at du opretter en adgangskode. Hvis du glemmer adgangskoden, findes der ingen måde at gendanne den.

    Hvis du vil angive en adgangskode, skal du klikke på **Nej**, Angiv en adgangskode i hovedvinduet i PuTTYgen og klik derefter på **Gem privat nøgle** igen. Ellers skal du klikke på **Ja** for at fortsætte uden at give valgfrit adgangskoden.

8. Angiv et navn og placering for at gemme filen PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Brug trykfarver til SSH til en Linux maskine

Trykfarver er igen, en fælles SSH klient til Windows. Du kan frit at bruge en hvilken som helst SSH-klienten, som du ønsker. Følgende trin viser oplysninger om hvordan du bruger din private nøgle for at godkende med din Azure VM ved hjælp af SSH. Trinene er de samme i andre SSH vigtige klienter med hensyn til behøver at indlæse din private nøgle til at godkende SSH forbindelse.

1. Download og kør trykfarver fra følgende placering: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Udfyld værtsnavn eller IP-adressen på din VM fra Azure-portalen:

    ![Åbne ny trykfarver forbindelse](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Før du vælger **Åbn**, klik på **forbindelse** > **SSH** > **Auth** fanen. Gå til, og vælg din private nøgle:

    ![Vælg din trykfarver privat nøgle til godkendelse](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Klik på **Åbn** for at oprette forbindelse til din virtuelle maskine
 

## <a name="next-steps"></a>Næste trin
Du kan også oprette offentlige og private nøgler [ved hjælp af OS X og Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Du kan finde flere oplysninger om Bash til Windows og fordelene ved at have OSS værktøjer tilgængelige på din Windows-computer, kan du se [Bash på Ubuntu på Windows](https://msdn.microsoft.com/commandline/wsl/about).

Hvis du har problemer med at bruge SSH til at oprette forbindelse til din Linux FOS, se [fejlfinding i forbindelse med SSH forbindelser til en Azure Linux VM](virtual-machines-linux-troubleshoot-ssh-connection.md).