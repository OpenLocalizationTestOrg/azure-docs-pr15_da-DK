<properties
    pageTitle="Hente Azure SDK til PHP"
    description="Lær at downloade og installere Azure SDK til PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Hente Azure SDK til PHP

## <a name="overview"></a>Oversigt

Azure SDK til PHP indeholder komponenter, der gør det muligt at udvikle, installere og administrere PHP programmer til Azure. Nærmere betegnet kan indeholder Azure SDK til PHP følgende:

* **Feltet PHP klient billedbiblioteker til Azure**. Disse klassebiblioteker har en grænseflade for at få adgang til Azure funktioner, som data management services og tjenester i skyen.  
* **Azure kommandolinjen til Mac, Linux, og Windows (Azure CLI)**. Dette er et sæt kommandoer til implementering og administration af Azure tjenester, som Azure websteder og virtuelle Azure-computere. Azure CLI arbejdet på en hvilken som helst platformen, herunder Mac, Linux og Windows.
* **Azure PowerShell (kun Windows)**. Dette er et sæt af PowerShell-cmdlet'er til implementering og administration af Azure tjenester, som Skytjenester og virtuelle computere.
* **Azure emulatorer (kun Windows)**. Beregn og lager emulatorer er lokale emulatorer af skytjenester og data management services, så du kan teste et program lokalt. Azure-emulatorer kører kun på Windows.

I nedenstående afsnit beskrives hvordan du kan hente og installere de komponenter, der er beskrevet ovenfor.

Vejledningen i dette emne forudsætter, at du har [PHP] [ install-php] installeret.

> [AZURE.NOTE] Du skal have PHP 5,5 eller nyere til at bruge PHP klientbiblioteker til Azure.

##<a name="php-client-libraries-for-azure"></a>PHP klient billedbiblioteker til Azure

PHP klientbiblioteker til Azure har en grænseflade for at få adgang til Azure funktioner, som data management services og tjenester fra operativsystemer i skyen. Disse biblioteker kan installeres via komponisten.

Finde oplysninger om hvordan du bruger PHP klientbiblioteker til Azure, se, [hvordan du bruger tjenesten Blob][blob-service], [hvordan du bruger tjenesten tabel] [ table-service] og [hvordan du bruger tjenesten kø][queue-service].

###<a name="install-via-composer"></a>Installere via komponist

1. [Installere ciffer][install-git].


    > [AZURE.NOTE] I Windows skal du også føje den eksekverbare ciffer til miljøvariablen PATH.

2. Oprette en fil med navnet **composer.json** i roden af projektet og tilføje følgende kode til den:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Hente ** [composer.phar] [ composer-phar] ** i dit projekt rod.

4. Åbn en kommandoprompt og udføre dette i dit projekt rod

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell og Azure emulatorer

Azure PowerShell er et sæt af PowerShell-cmdlet'er til implementering og administration af Azure Services (som Skytjenester og virtuelle computere). Azure-emulatorer er emulatorer af skytjenester og data management services, så du kan teste et program lokalt. Disse komponenter understøttes kun Windows.

Den anbefalede måde at installere Azure PowerShell og Azure emulatorer er at bruge [Microsoft Web Platform Installer][download-wpi]. Bemærk, at du kan også vælge at installere andre udviklingskomponenter, såsom PHP, SQL Server, Microsoft Drivers til SQL Server for PHP og WebMatrix.

Finde oplysninger om hvordan du bruger Azure PowerShell, se, [hvordan du bruger Azure PowerShell][powershell-tools].

##<a name="azure-cli"></a>Azure CLI

Azure CLI er en række kommandoer til implementering og administration af Azure tjenester, som Azure websteder og virtuelle Azure-computere. Oplysninger om installation af Azure CLI, kan du se [installere Azure CLI](xplat-cli-install.md).

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [PHP Developer Center](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
