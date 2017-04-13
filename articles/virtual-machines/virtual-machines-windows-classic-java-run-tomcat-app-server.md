<properties
    pageTitle="Tomcat på en virtuel maskine | Microsoft Azure"
    description="Dette selvstudium bruger ressourcer, der er oprettet med den klassiske implementeringsmodel og viser, hvordan du opretter en virtuel til Windows-maskine og Konfigurer den til at køre Apache Tomcat application server."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Sådan køres en Java application server på en virtuel maskine, der er oprettet med den klassiske implementeringsmodel

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Med Azure, kan du bruge en virtuel maskine til server-funktioner. En virtuel maskine, der kører på Azure kan konfigureres til at være vært for en Java application server, som Apache Tomcat som et eksempel. Når du har fuldført denne vejledning, har du en forståelse af, hvordan du opretter en virtuel maskine, der kører på Azure og Konfigurer den til at køre en Java application server.

Du kan få mere at vide:

* Sådan oprettes en virtuel maskine, der indeholder en Java Development Kit (JDK) allerede er installeret.
* Sådan fra en fjernplacering logge på din virtuelle maskine.
* Hvordan du installerer en Java application server på din virtuelle maskine.
* Sådan oprettes et slutpunkt for din virtuelle maskine.
* Sådan åbnes en port for din programserver i firewallen.

Med henblik på dette selvstudium installeres programserver Apache Tomcat på en virtuel maskine. Færdige installationen resulterer i en Tomcat installation f.eks.

![Virtuelt kører Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Du opretter en virtuel maskine

1. Log på [Azure klassisk portal](https://manage.windowsazure.com).
2. Klik på **Ny**, skal du klikke på **beregne**, skal du klikke på **virtuelt**og derefter klikke på **Fra galleriet**.
3. Vælg **JDK 7 Windows Server 2012**i dialogboksen **Vælg virtuelt billede** .
Bemærk, at **JDK 6 Windows Server 2012** er tilgængelig, hvis du har ældre programmer, der ikke er klar til at køre i JDK 7.
4. Klik på **Næste**.
5. I dialogboksen **virtuelt konfiguration** :
    1. Angiv et navn til den virtuelle maskine.
    2. Angiv størrelsen, der skal bruges til den virtuelle maskine.
    3. Angiv et navn til administratoren i feltet **Brugernavn** . Husk, at dette navn og den adgangskode, du vil angive næste, du vil bruge dem, når du fra en fjernplacering logger på den virtuelle maskine.
    4. Angive en adgangskode i feltet **Ny adgangskode** , og skriv det igen i feltet **Bekræft** . Dette er adgangskoden til administratorkontoen.
    5. Klik på **Næste**.
6. Næste **virtuelt konfiguration** i dialogboksen:
    1. Brug standard **Opret en ny tjeneste i skyen**for **skybaseret tjeneste**.
    2. Værdien for **skyen tjenesten DNS-navn** skal være entydige i hele cloudapp.net. Hvis det er nødvendigt, kan du ændre denne værdi, så Azure angiver det er entydige.
    2. Angiv et område, forbindelse gruppe eller virtuelt netværk. Angiv et område som **Vest USA**med henblik på dette selvstudium.
    2. Vælg **Brug en automatisk oprettede lagerplads konto**for **Lagerplads konto**.
    3. For at **Angive tilgængelighed**, skal du vælge **(ingen)**.
    4. Klik på **Næste**.
7. I endeligt dialogboksen **virtuelt konfiguration** :
    1. Acceptér standard slutpunkt poster.
    2. Klik på **udført**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Fra en fjernplacering logge på din virtuelle maskine

1. Log på [Azure klassisk portal](https://manage.windowsazure.com).
2. Klik på **virtuelle maskiner**.
3. Klik på navnet på den virtuelle maskine, du vil logge på.
4. Når den virtuelle maskine er startet, kan en pop op-menuen i bunden af siden forbindelser.
5. Klik på **Opret forbindelse**.
6. Besvare instruktionerne efter behov for at oprette forbindelse til den virtuelle maskine. Dette bør omfatte gemme eller åbne en RDP-fil, der indeholder forbindelsesoplysningerne. Det være nødvendigt at kopiere URL-adresse: port som den sidste del af den første linje i på RDP-filen og sætte dem ind i en eksterne logon-program.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Installere en Java application server på din virtuelle maskine

Du kan kopiere en Java application server til din virtuelle maskine, eller du kan installere en Java application server via et installationsprogram.

Med henblik på dette selvstudium installeres Tomcat.

1. Når du er logget på din virtuelle maskine, skal du åbne en browsersession til [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Dobbeltklik på linket for **32-bit/64-bit Windows Service Installer**. Ved hjælp af denne metode, installeres Tomcat som en Windows-tjeneste.
3. Når du bliver bedt om det, kan du vælge at køre installationsprogrammet.
4. Følg vejledningen for at installere Tomcat i installationsguiden **Apache Tomcat** . Med henblik på dette selvstudium acceptere standardindstillingerne er fint. Når du kommer til dialogboksen **Fuldfører Apache Tomcat konfigurationsguiden** , kan du eventuelt se **Køre Apache Tomcat** for at få Tomcat start nu. Klik på **Udfør** for at fuldføre konfigurationen af Tomcat.

## <a name="to-start-tomcat"></a>Starte Tomcat
Hvis du ikke har valgt at køre Tomcat i dialogboksen **fuldfører installationsguiden til Apache Tomcat** , kan du starte den ved at åbne en kommandoprompt på din virtuelle maskine og kører **redskaber starte Tomcat7**.

Du bør nu se Tomcat, der kører, hvis du kører den virtuelle maskine browser og åbne <http://localhost:8080>.

Hvis du vil se Tomcat kører fra eksterne computere, skal du oprette et slutpunkt og åbne en port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Oprette et slutpunkt for din virtuelle maskine
1. Log på [Azure klassisk portal](https://manage.windowsazure.com).
2. Klik på **virtuelle maskiner**.
3. Klik på navnet på den virtuelle maskine, der kører din Java application server.
4. Klik på **slutpunkterne**.
5. Klik på **Tilføj**.
6. I dialogboksen **Tilføj slutpunkt** skal sikre, at **Tilføj enkeltstående slutpunkt** er markeret, og klik derefter på **Næste**.
7. I dialogboksen **Ny slutpunkt oplysninger** :
    1. Angiv et navn til slutpunktet for eksempel, **HttpIn**.
    2. Angiv **TCP** protokollen.
    3. Angiv **80** for den offentlige port.
    4. Angiv **8080** for private port.
    5. Klik på **udført** for at lukke dialogboksen. Din slutpunkt oprettes nu.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Sådan åbnes en port i firewallen for din virtuelle maskine
1. Log på din virtuelle maskine.
2. Klik på **Start i Windows**.
3. Klik på **Kontrolpanel**.
4. Klik på **System og sikkerhed**, skal du klikke på **Windows Firewall**, og klik derefter på **Avancerede indstillinger**.
5. Klik på **Indgående regler**, og klik derefter på **Ny regel**.
 ![Ny indgående regel][NewIBRule]
6. Marker **Port** **Regeltype**, og klik derefter på **Næste**.
 ![Ny indgående regel port][NewRulePort]
7. Vælg **TCP**på skærmbilledet **protokol og porte** , angive **8080** som **bestemt lokale port**, og klik derefter på **Næste**.
 ![Ny indgående regel][NewRuleProtocol]
8. Vælg **Tillad forbindelsen**på skærmbilledet **handling** , og klik derefter på **Næste**.
 ![Ny indgående regelhandling][NewRuleAction]
9. Sikre, at **domæne**, **Private**og **offentlige** er markeret, og klik derefter på **Næste**på skærmbilledet **profil** .
 ![Ny indgående regel profil][NewRuleProfile]
10. Angiv et navn for reglen, som **HttpIn** (navnet på reglen ikke er påkrævet at svare til slutpunktsnavnet, men), og klik derefter på **Udfør**på skærmbilledet **Name** .  
 ![Ny indgående regelnavn][NewRuleName]

På dette tidspunkt webstedet Tomcat skal være synlige fra en ekstern browser ved hjælp af en URL-adresse i formularen * *http://*din\_DNS\_navn*. cloudapp.net**hvor ** *din\_DNS\_navn*** er DNS-navn, du angav, da du oprettede den virtuelle maskine.

## <a name="application-lifecycle-considerations"></a>Overvejelser i forbindelse med program livscyklus
* Du kan oprette din egen web application arkiv (KRIG) og tildele den til mappen **WebApp** . For eksempel oprette et grundlæggende Java Service side (JSP) dynamisk webprojekt og eksportere den som en KRIG-fil, kopiere KRIGEN til mappen Apache Tomcat **WebApp** på den virtuelle maskine, og derefter køre den i en browser.
* Når tjenesten Tomcat er installeret, som standard indstilles den til at starte manuelt. Du kan slå det til at starte automatisk ved hjælp af snap-in-programmet tjenester. Start snap-in-tjenester ved at klikke på **Start i Windows**, **Administration**og derefter **tjenester**. Dobbeltklik på tjenesten **Apache Tomcat** , og Indstil **Starttype** til **automatisk**.

    ![Angive en tjeneste til at starte automatisk][service_automatic_startup]

    Fordelen ved at have Tomcat start automatisk er, at den starter, hvis den virtuelle maskine genstartes (for eksempel efter softwareopdateringer, der kræver en genstart er installeret).

## <a name="next-steps"></a>Næste trin
Få mere at vide om andre tjenester (som Azure-lager, service bus og SQL-Database), kan du medtage med Java-programmer ved at få vist de oplysninger, der er tilgængelige [Java Developer Center](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
