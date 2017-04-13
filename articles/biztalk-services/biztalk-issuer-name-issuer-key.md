<properties 
    pageTitle="Navn på udsteder og udsteder nøgle i BizTalk Services | Microsoft Azure" 
    description="Lær at hente udstedernavn og udsteder nøgle til Service Bus eller Access ACS (Control) i BizTalk-tjenester. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk-tjenester: Udstedernavn og udsteder nøgle

Azure BizTalk Services bruger navnet på tjenesten Bus udsteder og udsteder nøgle, og den Access udsteder Kontrolelementnavn og udsteder nøgle. Mere specifikt:

Opgave | Hvilke udstedernavn og udsteder nøgle
--- | ---
Implementering af dit program fra Visual Studio | Få adgang til udsteder Kontrolelementnavn og udsteder nøgle
Konfiguration af portalen Azure BizTalk-tjenester | Få adgang til udsteder Kontrolelementnavn og udsteder nøgle
Oprette LOB relæer med BizTalk-Adapter tjenester i Visual Studio | Tjenesten Bus udstedernavn og udsteder nøgle

Dette emne viser trin for at hente den udstedernavn og udsteder nøgle. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Få adgang til udsteder Kontrolelementnavn og udsteder nøgle
Access udsteder Kontrolelementnavn og udsteder nøgle anvendes af følgende:

- Dit Azure BizTalk Service-program, der er oprettet i Visual Studio: for at installere korrekt BizTalk Service programmet i Visual Studio til Azure, skal du angive Access udsteder Kontrolelementnavn og udsteder nøgle. 
- Azure BizTalk Services portalen: Når du opretter en BizTalk Service og Åbn BizTalk Services-portalen, dit Access udsteder Kontrolelementnavn og udsteder nøgle registreres automatisk for din-installationer med de samme adgangskontrol værdier.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Kopiere og indsætte Access udsteder Kontrolelementnavn og udsteder nøgle

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk Services**i den venstre navigationsrude.
3. Vælg din BizTalk-tjeneste. 
4. Vælg **Forbindelsesoplysningerne** på proceslinjen. Access kontrolelement Namespace, standard udsteder (udstedernavn), og som standard nøgle (udsteder nøgle) vises og kan kopieres og indsættes.  

Opsummering:  
Navn på udsteder = standard udsteder  
Udsteder nøgle = standard-tasten


Du kan også vælge **Åbn ACS Management Portal** for at hente adgangskontrol værdier:

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk Services**i den venstre navigationsrude.
3. Vælg din BizTalk-tjeneste.
4. Vælg knappen forbindelsesoplysningerne, og vælg **Åbn ACS Management Portal**.
5. Vælg **Service identiteter**på portalen under **Tjenesteindstillinger**. Dette viser din tjenesteidentitet, som er dit Access udsteder Kontrolelementnavn værdi. Vælg din tjenesteidentitet hyperlink for at se den adgangskode, som er dit udsteder nøgleværdi. Deres værdier kan kopieres.<br/><br/>
I **Tjenesteidentiteter**se eksempelvis "ejer". "Ejer" er din Access Kontrolelementnavn udsteder. Når du klikker på linket "ejer", vises feltet **adgangskode**. Når du klikker på linket "Adgangskode", kan du se værdien. Denne adgangskodeværdi er Access kontrolelement udsteder nøglen.  

Opsummering:  
Navn på udsteder = Service identitet navn  
Udsteder nøgle = adgangskodeværdi

Du kan også vælge **Active Directory** til at hente adgangskontrol værdier i den venstre navigationsrude. 

> [AZURE.IMPORTANT]Når en Access kontrolelement Namespace oprettes oprettes ved hjælp af **Active Directory**, en tjenesteidentitet **ikke** automatisk. Når du klargør et BizTalk Service, en Access-kontrolelement Namespace Service identitet med navnet "ejer" (udsteder navn), adgangskode (udsteder nøgle), og symmetriske nøgle oprettes automatisk.<br /> 
[Sådan: Brug ACS Management-tjenesten til at konfigurere tjenesteidentiteter](http://go.microsoft.com/fwlink/p/?LinkID=303942) indeholder flere oplysninger om Access Control Service identiteter.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Tjenesten Bus udstedernavn og udsteder nøgle
Tjenesten Bus udstedernavn og udsteder nøgle bruges af BizTalk-Adapter tjenester. I projektet BizTalk-tjenester i Visual Studio, kan du bruge BizTalk-Adapter Services til at oprette forbindelse til et lokalt Line of Business (LOB) system. Hvis du vil oprette forbindelse, du opretter den LOB Relay og angive LOB system detaljerne. Når du gør dette, skal angive du også navn på tjenesten Bus udsteder og udsteder nøgle.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Til at hente Service Bus udstedernavn og udsteder nøgle

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **Service Bus**i den venstre navigationsrude.
3. Vælg din navneområde. Vælg **Forbindelsesoplysningerne**i proceslinjen. Dette viser **Standard udsteder** (udstedernavn) og **Standard nøgle** (udsteder nøgle). Deres værdier kan kopieres.  

Opsummering:  
Navn på udsteder = standard udsteder  
Udsteder nøgle = standard-tasten

## <a name="next"></a>Næste
Flere emner i Azure BizTalk Services:

-  [Installation af Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Selvstudier: Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Se også
-  [Sådan: Brug ACS Management Service til at konfigurere tjenesteidentiteter](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk-tjenester: Udvikler, Basic, Standard og Premium udgaver diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk-tjenester: Ved hjælp af Azure klassisk portal klargøring](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk-tjenester: Klargøring Status diagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk-tjenester: Dashboard, overvåge og skala faner](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk-tjenester: Sikkerhedskopiering og gendannelse](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk-tjenester: (throttling)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
