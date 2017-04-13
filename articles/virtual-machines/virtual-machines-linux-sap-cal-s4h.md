<properties 
pageTitle="Implementering af S/4 HANA eller Båndbredden/4 HANA på en Azure VM | Microsoft Azure" 
description="Implementering af S/4 HANA eller Båndbredden/4 HANA på en Azure VM" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 


# <a name="deploying-s4-hana-or-bw4-hana-on-microsoft-azure"></a>Implementering af S/4 HANA eller Båndbredden/4 HANA på Microsoft Azure 

I denne artikel beskrives, hvordan du kan installere S/4 HANA på Microsoft Azure via SAP skyen maskinen bibliotek 3.0.
Skærmbillederne, der viser processen trin for trin. Installation af andre SAP HANA-baserede løsninger, såsom Båndbredden/4 HANA fungerer på samme måde fra et proces perspektiv. Én har lige til at vælge en anden løsning.

Skal starte med SAP skyen maskinen bibliotek (SAP CAL) gå [her](https://cal.sap.com/). Der findes en blog fra SAP om den nye [SAP skyen maskinen bibliotek 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


De følgende skærmbilleder Vis trinvise hvordan du kan installere S/4 HANA på Microsoft Azure. Processen fungerer på samme måde for andre løsninger likeBW/4 HANA.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

Det første billede viser alle SAP CAL HANA-baserede løsninger, der er tilgængelige på Microsoft Azure.
Exemplarily den "SAP S/4 HANA lokale udgave" (opløsning i bunden af skærmbilledet) blev valgt at gå gennem processen.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

En ny SAP CAL-konto har først skal oprettes. Der findes to valgmuligheder for Azure - standard Azure og Azure på det øvrige Kina, som er drevet af 21Vianet partner.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Én har derefter angive det Azure abonnement-ID, som kan findes på portalen Azure - også se yderligere ned Sådan får du den. Bagefter skal et Azure management-certifikat hentes.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

I den nye Azure finder portalen én elementet "Abonnementer" i venstre side. Klik på den for at få vist alle aktive abonnementer til din bruger.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Vælge en af abonnementerne, og derefter vælge "Management certifikater" forklarer, der er nye koncepter ved hjælp af "principper for tjenesten" til den nye Azure ressourcestyring model.
SAP CAL endnu ikke er tilpasset til denne nye model og stadig kræver "klassisk" modellen og tidligere Azure portalen for at arbejde med administration af certifikater.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Her kan se den tidligere Azure portal. Overførsel af et certifikat administration giver SAP CAL tilladelse til at oprette virtuelle maskiner med et kunde-abonnement. Fanen en kan finde det abonnement-ID, der skal angives i portalen SAP CAL under "ABONNEMENTER".

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Under fanen andet bliver det muligt at overføre det management-certifikat, der er hentet før fra SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

En lille dialogboks vises for at markere filen hentet certifikat.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Når certifikatet, der blev overført forbindelsen mellem SAP CAL og kunden Azure-abonnement kan testes i SAP CAl. En lille meddelelse skal pop op-der fortæller, at forbindelsen er gyldig.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Efter installation af en konto har til at vælge en løsning, der skal installeres og oprette en forekomst.
Med "grundlæggende" tilstand er det virkelig trivial. Angiv navnet på en forekomst, vælge et Azure område og definere master adgangskoden til løsningen.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Efter et stykke tid afhængigt af størrelsen og kompleksiteten af løsningen (et skøn er givet ved SAP CAL) vises den som "aktive" og er klar til brug. Det er meget simpel.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Se på nogle oplysninger om en løsningens kan se, hvilken FOS blev installeret. I dette tilfælde blev tre Azure FOS af forskellige størrelser og formål oprettet.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

På Azure-portalen, kan de virtuelle maskiner findes starter med det samme forekomstnavn, der er givet i SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Nu er det muligt at oprette forbindelse til løsning via knappen Opret forbindelse i SAP CAL-portalen. Dialogboksen lille indeholder et link til en brugervejledning, der beskriver alle standardlegitimationsoplysninger til at arbejde med løsningen.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

En anden mulighed er at logge på klienten Windows VM og starte f.eks den forudkonfigurerede SAP grafiske brugergrænseflade.







