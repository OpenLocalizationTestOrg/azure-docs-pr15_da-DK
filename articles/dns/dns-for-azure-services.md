<properties
  pageTitle="Ved hjælp af Azure DNS med andre Azure tjenester | Microsoft Azure"
  description="Forstå, hvordan du kan bruge Azure DNS til at løse navn til andre Azure tjenester"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>Ved hjælp af Azure DNS med andre Azure tjenester

Azure DNS er en hostet DNS-administration og navn opløsning tjeneste. Dette kan du oprette offentlige DNS-navne for de andre programmer og tjenester, du har installeret i Azure. Oprette et navn til en Azure service i dit eget domæne er så enkelt som at tilføje en post af typen korrekt til din tjeneste.

* Dynamisk tildelte IP-adresser, skal du oprette en DNS CNAME-post, der er tilknyttet DNS-navn, Azure oprettes til din tjeneste. DNS-standarder forhindre dig i at bruge en CNAME-post til dennes zone nedre.
* Til statisk tildelte IP-adresser, kan du oprette en DNS A-post ved hjælp af en hvilken som helst navn, herunder en _nøgen_ domænenavn i zone af toppen.

Den følgende tabel beskrives de understøttede posttyper, der kan bruges til forskellige Azure tjenester. Som du kan se fra denne tabel, understøtter Azure DNS kun DNS-poster for ressourcer med internetadgang netværk. Azure DNS kan ikke bruges til navneoversættelse af interne, private adresser.

| Azure Service | Netværksgrænsefladen | Beskrivelse |
|---------------|-------------------|-------------|
| Application Gateway | Front end offentlige IP-adresse | Du kan oprette en DNS-A eller CNAME-post. |
| Justering af belastning | Front end offentlige IP-adresse | Du kan oprette en DNS-A eller CNAME-post. Justering af belastning kan have en IPv6 offentlige IP-adresse, der er tildelt dynamisk. Derfor skal du oprette en CNAME-post til en IPv6-adresse. |
| Trafik Manager | Offentligt navn | Du kan kun oprette en CNAME, der er tilknyttet det trafficmanager.net navn, der er tildelt til din profil trafik Manager. Se Få mere at vide, [hvordan trafik Manager fungerer](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Skybaseret tjeneste | Offentlige IP-adresse | Statisk tildelte IP-adresser, kan du oprette en DNS-A-post. Dynamisk tildelte IP-adresser, skal du oprette en CNAME-post, der knytter _cloudapp.net_ til navnet på. Denne regel gælder for VM'er, der er oprettet i portalen klassisk, fordi de er implementeret som en skybaseret tjeneste. Se [konfigurere et brugerdefineret domænenavn i Cloud Services](../cloud-services/cloud-services-custom-domain-name-portal.md)kan finde flere oplysninger. |
| App Service | Ekstern IP-adresse | Du kan oprette en DNS-A-post til eksterne IP-adresser. Ellers skal du oprette en CNAME-post, der knytter azurewebsites.net til navnet på. Du kan finde flere oplysninger, se [knytte et brugerdefineret domænenavn til en Azure-app](../app-service-web/web-sites-custom-domain-name.md) |
| Ressourcestyring FOS | Offentlige IP-adresse | Ressourcestyring FOS kan have offentlige IP-adresser. En VM med en offentlige IP-adresse er måske også bag en justering af belastning. Du kan oprette en DNS-A eller CNAME-post til den offentlige adresse. Denne brugerdefineret navn kan bruges til at tilsidesætte VIP på justering af belastning. |
| Klassisk FOS | Offentlige IP-adresse | Klassisk FOS oprettet ved hjælp af PowerShell eller CLI kan konfigureres med en dynamisk eller statisk (reserveret) virtuel adresse. Du kan oprette en DNS CNAME eller en post, henholdsvis. |
