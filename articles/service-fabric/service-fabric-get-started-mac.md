<properties
   pageTitle="Konfigurere din udviklingsmiljø på Mac OS X | Microsoft Azure"
   description="Installere runtime, SDK og værktøjer, og oprette en lokal udvikling klynge. Når installationen er fuldført, skal du være klar til at opbygge programmer på Mac OS X."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Konfigurere din udviklingsmiljø på Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [ALLE OSX](service-fabric-get-started-mac.md)

Du kan oprette Service-strukturen programmer til at køre på Linux klynger ved hjælp af Mac OS X. I denne artikel omhandler, hvordan du konfigurerer din Mac til udvikling.

## <a name="prerequisites"></a>Forudsætninger

Tjenesten strukturen kører ikke indbygget på OS X. Hvis du vil køre en lokal Service-strukturen klynge, giver vi en forudkonfigurerede Ubuntu virtuelt ved hjælp af Vagrant og VirtualBox. Før du går i gang, skal du:

- [Vagrant (v1.8.4 eller nyere)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Oprette lokale VM

Hvis du vil oprette den lokale VM, der indeholder en 5-node Service-strukturen klynge, skal du gøre følgende:

1. Klone Vagrantfile repo

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Gå til den lokale Klon af repo

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Valgfrit) Ændre standardindstillingerne for VM

    Som standard konfigureret den lokale VM på følgende måde:

    - 3 GB hukommelse allokeres
    - Privat host netværk konfigureret IP-192.168.50.50 aktivere passthrough trafik fra værten for Mac

    Du kan ændre en af disse indstillinger eller tilføje andre konfiguration til VM i Vagrantfile. Se [Vagrant dokumentation](http://www.vagrantup.com/docs) til den komplette liste over konfigurationsindstillinger.

4. Oprette VM

    ```bash
    vagrant up
    ```

    Dette trin henter forudkonfigurerede VM billedet, Start den lokalt og derefter konfigurere en lokal Service-strukturen klynge i den. Du kan forvente det vil tage et par minutter. Hvis installationen er fuldført, vises en meddelelse i outputtet, der angiver, at starter klyngen.

    ![Klynge installationsprogrammet starter efter VM klargøring][cluster-setup-script]

5. Test, klyngen er konfigureret korrekt ved at navigere til tjenesten strukturen Explorer på http://192.168.50.50:19080/Explorer (Hvis du informeret standard privat netværk IP-adresse).

    ![Tjenesten strukturen Explorer vises fra værten for Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Installere plug-in'en til tjenesten strukturen til Eklipse Neon (valgfrit)

Tjenesten strukturen indeholder et plug-in til den Eklipse Neon IDE, der kan forenkle processen med at opbygge og implementere Java tjenester.

1. Eklipse, Kontrollér, at du har Buildship version 1.0.17 eller nyere installeret. Du kan kontrollere versionerne af installerede komponenter ved at vælge **Hjælp > installationsoplysninger**. Du kan opdatere Buildship ved hjælp af vejledningen [her][buildship-update].

2. For at installere plug-in'en til tjenesten strukturen skal du vælge **Hjælp > installere ny Software...**

3. I tekstfeltet "Arbejde med" angive: http://dl.windowsazure.com/eclipse/servicefabric.

4. Klik på Tilføj.

    ![Eklipse Neon plug-in for Service-strukturen][sf-eclipse-plugin-install]

5. Vælg Service-strukturen plug-in'en, og klik på Næste.

6. Fortsæt gennem installationen, og accepter slutbrugerlicensaftalen.

## <a name="next-steps"></a>Næste trin

- [Oprette din første Service-strukturen program for Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Oprette en tjeneste-strukturen klynge i portalen Azure](service-fabric-cluster-creation-via-portal.md)
- [Oprette en tjeneste-strukturen klynge, ved hjælp af Ressourcestyring Azure](service-fabric-cluster-creation-via-arm.md)
- [Forstå Service-strukturen programmet datamodellen](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
