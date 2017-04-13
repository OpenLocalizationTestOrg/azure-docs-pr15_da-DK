<properties
    pageTitle="Oprette en VM kører MySQL | Microsoft Azure"
    description="Oprette en Azure virtuelt, der kører Windows Server 2012 R2 og den MySQL-database ved hjælp af den klassiske implementeringsmodel."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Installere MySQL på en virtuel maskine, der er oprettet med den klassiske implementeringsmodel, der kører Windows Server 2012 R2

[MySQL](http://www.mysql.com) er en populære Åbn kilde, SQL-database. Dette selvstudium viser, hvordan du installere og køre community-versionen af MySQL 5.6.23 som en MySQL-Server på en virtuel maskine, der kører Windows Server 2012 R2. Finde en vejledning i installation af MySQL på Linux, refererer til: [hvordan du installerer MySQL på Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Oprette en virtuel maskine, der kører Windows Server 2012 R2

Hvis du ikke allerede har en VM, der kører Windows Server 2012 R2, kan du bruge dette [selvstudium](virtual-machines-windows-classic-tutorial.md) til at oprette den virtuelle maskine. 

## <a name="attach-a-data-disk"></a>Vedhæfte en datadisk

Når den virtuelle maskine er oprettet, kan du eventuelt vedhæfte en yderligere datadisk. Det anbefales til fremstilling arbejdsmængder og for at undgå at løbe tør for plads på OS-drev (C:), som indeholder operativsystemet.

Se, [hvordan du vedhæfter en datadisk til en Windows virtuel maskine](virtual-machines-windows-classic-attach-disk.md) , og følg instruktionerne for at vedhæfte en tom disk. Angiv indstillingen host cache til **ingen** eller **skrivebeskyttet tilstand**.

## <a name="log-on-to-the-virtual-machine"></a>Log på den virtuelle maskine

Derefter skal skal du [logge på den virtuelle maskine](virtual-machines-windows-classic-connect-logon.md) så du kan installere MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installere og køre MySQL Community Server på virtuelle maskine

Følg disse trin for at installere, konfigurere og køre Community-versionen af MySQL-Server:

> [AZURE.NOTE] Disse trin gælder for 5.6.23.0 Community version af MySQL og Windows Server 2012 R2. Din oplevelse kan være forskellige for forskellige versioner af MySQL eller Windows Server.

1.  Klik på **Internet Explorer** fra startskærmen, når du har forbindelse til den virtuelle maskine ved hjælp af Fjernskrivebord.
2.  Vælg knappen **værktøjer** i øverste højre hjørne (ikonet cogged hjulet), og klik derefter på **Internetindstillinger**. Klik på fanen **sikkerhed** , skal du klikke på ikonet **Pålidelige websteder** , og klik derefter på knappen **websteder** . Tilføje http://*. mysql.com til listen over pålidelige websteder. Klik på * *Luk**, og klik derefter på * *OK**.
3.  I adresselinjen i Internet Explorer, Skriv http://dev.mysql.com/downloads/mysql/.
4.  Brug MySQL-webstedet til at finde og hente den nyeste version af MySQL Installer til Windows. Når du vælger MySQL installationsprogrammet, kan du hente den version, der indeholder den komplette fil sæt (for eksempel mysql-installer-community-5.6.23.0.msi med en filstørrelse på 282.4 MB), og Gem installationsprogrammet.
5.  Når installationsprogrammet er hentet, skal du klikke på **Kør** for at starte installationen.
6.  Accepter licensaftalen, og klik på **Næste**på siden **Licensaftalen** .
7.  Klik på den ønskede konfiguration på siden **vælge en installationstype** , og klik derefter på **Næste**. Følgende trin forudsætter markeringen af installationstype **kun Server** .
8.  Klik på **Udfør**på siden **Installation** . Når installationen er fuldført, skal du klikke på **Næste**.
9.  Klik på **Næste**på siden **Konfiguration af produktet** .
10. Angiv dit ønskede type og forbindelse konfigurationsindstillinger, herunder TCP-porten, hvis det er nødvendigt, på siden **Type og netværk** . Vælg **Vis avancerede indstillinger**, og klik derefter på **Næste**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. Angiv en stærk MySQL rod adgangskode på siden **konti og roller** . Tilføj yderligere MySQL-brugerkonti, som det er nødvendigt, og klik derefter på **Næste**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. Angiv ændringer til standardindstillingerne for at køre den MySQL-Server som en Windows-tjeneste efter behov, og klik derefter på **Næste**på siden **Windows-tjenesten** .

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. Angive logføringsindstillinger ændringer efter behov, og klik derefter på **Næste**på siden **Avancerede indstillinger** .

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. Klik på **Udfør**på siden **Anvende serverkonfiguration** . Klik på **Udfør**, når trinnene til konfiguration er fuldført.
15. Klik på **Næste**på siden **Konfiguration af produktet** .
16. Klik på **Kopiér Log til Udklipsholder** på siden **Installationen er fuldført** , hvis du vil undersøge den senere, og klik derefter på **Udfør**.
17. Skriv **mysql**fra startskærmen, og klik derefter på **MySQL 5,6 kommandolinjen klient**.
18. Angiv den rod-adgangskode, du angav i trin 11, og du får præsenteres med en prompt, hvor du kan udføre kommandoer til at konfigurere MySQL. Du kan finde oplysninger om kommandoer og syntaks, [MySQL Reference brugervejledninger](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. Du kan også konfigurere server standard konfigurationsindstillinger, som den base og data mapper og drev, med poster i filen C:\Program Files (x86) \MySQL\MySQL Server 5.6\my-default.ini. Du kan finde flere oplysninger, se [5.1.2 Server Configuration standardindstillinger](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Konfigurere slutpunkter

Hvis du vil tjenesten MySQL Server skal være tilgængelig på klientcomputere MySQL på internettet, skal du konfigurere et slutpunkt for TCP-port, hvor tjenesten MySQL Server lytter og oprette en ekstra regel i Windows Firewall. Dette er TCP-port 3306, medmindre du har angivet en anden port på siden **Type og netværk** (trin 10 i den forrige procedure).


> [AZURE.NOTE] Du overveje nøje sikkerheden ved at gøre dette, da det vil gøre tjenesten MySQL Server tilgængelige på alle computere på internettet. Du kan definere sættet af kilde-IP-adresser, der har tilladelse til at bruge slutpunktet med en liste over ACL (Access Control). Se, [hvordan du angive slutpunkter til en virtuel maskine](virtual-machines-windows-classic-setup-endpoints.md)kan finde flere oplysninger.


Konfigurere et slutpunkt for tjenesten MySQL-Server:

1.  Klik på **virtuelle maskiner**i Azure klassisk-portalen, klik på navnet på din MySQL virtuelle maskine, og klik **slutpunkter**.
2.  I kommandolinjen skal du klikke på **Tilføj**.
3.  På siden **Tilføj et slutpunkt til en virtuel maskine** , skal du klikke på den højre pil.
4.  Hvis du bruger standard MySQL TCP port af 3306, klik på **MySQL** i **navn**, og klik derefter på markeringen.
5.  Hvis du bruger en anden TCP-port, Skriv et entydigt navn i **navn**. Vælg **TCP** i protokol, Skriv portnummeret i både **Port offentlige** og **Private Port**og derefter skal du markere afkrydsningsfeltet.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Tilføj en regel for Windows Firewall så MySQL trafik

Hvis du vil tilføje en Windows Firewall-regel, der tillader MySQL-trafik fra internettet, skal du køre følgende kommando fra en kommandoprompt til Windows PowerShell på MySQL virtuelle servercomputeren.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Teste din remote forbindelse


Hvis du vil teste din remote forbindelse til tjenesten MySQL-Server, der kører på en Azure virtuel maskine, skal du først fastlægge DNS-navnet, der svarer til den skybaseret tjeneste, der indeholder den virtuelle maskine kører MySQL-Server.

1.  Klik på **virtuelle maskiner**i Azure klassisk-portalen, klik på navnet på din MySQL server virtuelle maskine, og klik **Dashboard**.
2.  Bemærk værdien **DNS-navn** under afsnittet **Hurtig Glance** dashboard virtuelt. Her er et eksempel:

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  Fra en lokal computer, der kører MySQL eller MySQL-klienten, skal du køre følgende kommando for at logge på som bruger en MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    For eksempel bruge følgende kommando for MySQL bruger navnet dbadmin3 og testmysql.cloudapp.net DNS-navnet for den virtuelle maskine.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om at køre MySQL, skal du se [MySQL dokumentation](http://dev.mysql.com/doc/).
