<properties 
    pageTitle="Hvordan du installerer Apache Qpid Proton-C på en Linux VM | Microsoft Azure"
    description="Sådan oprettes en CentOS Linux VM, ved hjælp af virtuelle Azure-computere og hvordan du kan oprette og installere Apache Qpid Proton-C-bibliotek."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Installere Apache Qpid Proton-C på en Azure Linux VM

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Dette afsnit beskrives, hvordan du opretter en CentOS Linux VM, ved hjælp af virtuelle Azure-computere og hvordan du kan hente, oprette og installere biblioteket Apache Qpid Proton-C sammen med Python og PHP sprog bindingerne. Når du har udført disse trin, vil du kunne køre Python og PHP eksemplerne inkluderet i denne vejledning.

Det første trin udføres ved hjælp af [Azure klassisk portal][]. Følgende skærmbillede viser, hvordan en CentOS VM med navnet "Søren-centos" oprettes:

![Proton på en Azure Linux VM][0]

Efter klargøring viser portalen følgende:

![Proton på en Azure Linux VM][1]

For at logge på computeren, skal du vide SSH slutpunkt-port. Du kan få denne værdi fra [Azure klassisk portal][] ved at vælge den netop oprettede VM og klikke på fanen **slutpunkter** . Følgende skærmbillede viser, at den offentlige SSH port til denne computer er 57146.

![Proton på en Azure Linux VM][2]

Du kan nu oprette forbindelse til computeren med SSH. I dette eksempel bruges værktøjet trykfarver som i følgende skærmbillede:

![Proton på en Azure Linux VM][3]

I dette eksempel bruges til Python og PHP apps fra Proton klientbiblioteker på Apache. Disse biblioteker kan downloades fra [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Filen med vigtige oplysninger i pakken fordeling beskriver trinnene til at installere afhængighederne og opbygge Proton. Her er en oversigt over trinnene:

1.  Rediger filen yum config (/ etc/yum.conf), og fjerne udelukkelse for opdateringer til kernen overskrifter (\# udelade = kernen\*). Det er nødvendigt at installere gcc compileren.

2.  Installere de påkrævede pakker:

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Hent biblioteket Proton:

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Udtrække Proton kode fra fordeling arkiv:

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Oprette og installere den kode, der er at benytte følgende fremgangsmåde, tages fra filen med vigtige oplysninger:

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Når du har udført disse trin, er Proton installeret på computeren og klar til brug.

## <a name="next-steps"></a>Næste trin

Klar til at få mere at vide? Besøg følgende link:

- [Tjenesten Bus AMQP oversigt][]

[Tjenesten Bus AMQP oversigt]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure klassisk portal]: http://manage.windowsazure.com


