<properties
   pageTitle="Sikre en tjeneste-strukturen klynge | Microsoft Azure"
   description="Beskriver sikkerhed scenarier for en tjeneste-strukturen klynge og de forskellige teknologier, der bruges til at implementere disse scenarier."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Tjenesten strukturen klynge sikkerhed scenarier

En tjeneste-strukturen klynge er en ressource, at du ejer. Klynger skal altid være fastgjort for at forhindre uautoriserede brugere i at oprette forbindelse til din klynge, især hvis den har fremstilling arbejdsbelastninger, som kører på den. Selvom det er muligt at oprette en usikker klynge, gøre så, så alle anonyme brugere til at oprette forbindelse til den, hvis den Fremviser management slutpunkter til det offentlige Internet. 

I denne artikel indeholder en oversigt over sikkerhed scenarier for klynger, der kører på Azure eller enkeltstående og de forskellige teknologier, der bruges til at implementere disse scenarier. Klynge sikkerhed scenarier er:

- Node-til-node sikkerhed
- Klient-til-node sikkerhed
- Rollebaseret adgangskontrol (RBAC)

## <a name="node-to-node-security"></a>Node-til-node sikkerhed
Sikrer kommunikationen mellem FOS eller maskiner i klyngen. Dette sikrer, at kun computere, der er autoriseret til at deltage i klyngen kan deltage i vært for programmer og tjenester i klyngen.

![Diagram over node-node-kommunikation][Node-to-Node]

Klynger, der kører på Azure eller enkeltstående klynger, der kører på Windows kan bruge enten [Certifikatsikkerhed](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-sikkerhedsgrupper](https://msdn.microsoft.com/library/ff649396.aspx) til Windows Server-computere.
### <a name="node-to-node-certificate-security"></a>Node-til-node Certifikatsikkerhed
Tjenesten strukturen bruger x.509-servercertifikater, som du angiver som en del af node af type konfigurationer, når du opretter en klynge. En hurtig oversigt over hvad certifikaterne er, og hvordan du kan få fat på eller oprette dem er angivet i slutningen af denne artikel.

Du har konfigureret certifikat sikkerheden under oprettelse af klynge enten via portalen Azure, Azure ressourcestyring skabeloner eller en enkeltstående JSON-skabelon. Du kan angive et primære certifikat og et valgfrit sekundær certifikat, der bruges til certifikat overgange. De primære og sekundære certifikater, du angiver skal være forskellig fra administrator klienten og skrivebeskyttet klientcertifikater, som du angiver for [klient-til-node sikkerhed](#client-to-node-security).

Azure at læser [konfigurere en klynge ved hjælp af en skabelon til Azure ressourcestyring](service-fabric-cluster-creation-via-arm.md) at lære, hvordan du kan konfigurere Certifikatsikkerhed i en klynge.

For enkeltstående læse Windows Server [Secure en enkeltstående klynge på Windows ved hjælp af x.509-certifikater](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Node-til-node windows-sikkerhed
For enkeltstående læse Windows Server [Secure en enkeltstående klynge på Windows ved hjælp af Windows-sikkerhed](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Klient-til-node sikkerhed
Godkender klienter og sikrer kommunikation mellem en klient og enkelte noder i klyngen. Denne type security godkender og sikrer klientkommunikation, hvilket sikrer, at kun godkendte brugere kan få adgang til klyngen og de programmer, der er installeret på klyngen. Klienter identificeres entydigt via deres Windows sikkerhedslegitimationsoplysninger eller sikkerhedslegitimationsoplysninger deres certifikat.

![Diagram over klient-til-node kommunikation][Client-to-Node]

Klynger, der kører på Azure eller enkeltstående klynger, der kører på Windows kan bruge enten [Certifikatsikkerhed](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-sikkerhed](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Klient-til-node Certifikatsikkerhed
 Klient-til-node Certifikatsikkerhed er konfigureret mens du opretter klyngen enten via portalen Azure, ressourcestyring skabeloner eller en enkeltstående JSON skabelon ved at angive en administrator klientcertifikat og/eller et bruger klientcertifikat.  Administrator-klienten og bruger klientcertifikater startposition skal være forskellig fra de primære og sekundære certifikater, du angiver for [Node-til-node sikkerhed](#node-to-node-security).

Klienter opretter forbindelse til den klynge ved hjælp af certifikatet, der administrator har fuld adgang til administrationsfunktioner.  Klienter opretter forbindelse til den klynge ved hjælp af klientcertifikat skrivebeskyttet bruger har kun læseadgang til administrationsfunktioner. Med andre ord bruges disse certifikater til den rolle grundtal adgangskontrol (RBAC) beskrives senere i denne artikel.

Azure at læser [konfigurere en klynge ved hjælp af en skabelon til Azure ressourcestyring](service-fabric-cluster-creation-via-arm.md) at lære, hvordan du kan konfigurere Certifikatsikkerhed i en klynge.

For enkeltstående læse Windows Server [Secure en enkeltstående klynge på Windows ved hjælp af x.509-certifikater](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Klient-til-node Azure Active Directory (AAD) sikkerhed på Azure
Klynger, der kører på Azure kan også sikker adgang til administration af slutpunkterne ved hjælp af Azure Active Directory (AAD). Se [konfigurere en klynge ved hjælp af en skabelon til Azure ressourcestyring](service-fabric-cluster-creation-via-arm.md) for oplysninger om hvordan du kan oprette de nødvendige AAD elementer, hvordan du kan udfylde dem under oprettelse af klynge og hvordan du opretter forbindelse til disse klynger bagefter.

## <a name="security-recommendations"></a>Anbefalinger til sikkerhed
Det anbefales, at du bruger AAD sikkerhed til at godkende klienter og certifikater til node-til-node sikkerhed til Azure klynger.

For enkeltstående Windows Server administrerede klynger det anbefales, at du bruger Windows-sikkerhed med gruppe konti (GMA), hvis du har Windows Server 2012 R2 og Active Directory. Ellers stadig bruge Windows-sikkerhed med Windows-konti.

## <a name="role-based-access-control-rbac"></a>Rollebaseret adgangskontrol (RBAC)
Adgangskontrol kan klyngeadministratoren for at begrænse adgangen til bestemte klyngehandlinger for forskellige grupper af brugere ved at gøre klyngen mere sikker. To forskellige access kontrolelementtyper understøttes til at oprette forbindelse til en klynge-klienter: administratorrolle og brugerrolle.

Administratorer har fuld adgang til funktioner til administration (herunder læse-og skriveadgang funktioner). Brugere, har som standard kun læseadgang til administrationsfunktioner (for eksempel forespørgsel funktioner), og muligheden for at løse programmer og tjenester.

Du angiver rollerne administrator- og -klienten på tidspunktet for oprettelse af klynge ved at indsende separate identiteter (certifikater, AAD osv.) for hver. Se [Rollebaseret adgangskontrol for tjenesten strukturen klienter](service-fabric-cluster-security-roles.md)kan finde flere oplysninger om indstillinger for adgangskontrol standard og hvordan du ændrer standardindstillingerne.


## <a name="x509-certificates-and-service-fabric"></a>X.509-certifikater og Service-strukturen
X.509-digitale certifikater bruges normalt til at godkende klienter og servere og til at kryptere og digitalt signere meddelelser. Gå til at [arbejde med certifikater](http://msdn.microsoft.com/library/ms731899.aspx)kan finde flere oplysninger om disse certifikater.

Nogle vigtige ting, du bør overveje:

- Certifikater, der bruges i klynger kører fremstilling arbejdsbelastninger skal være oprettet ved hjælp af en korrekt konfigureret Windows Server certifikat tjeneste eller hentet fra en godkendt [Certifikat nøglecenter (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
- Brug en midlertidig aldrig eller test certifikater i fremstilling, der er oprettet med funktioner som MakeCert.exe.
- Du kan bruge et selvsigneret certifikat, men du skal kun gøre det til test klynger og ikke i fremstilling.

### <a name="server-x509-certificates"></a>Server x.509-certifikater

Servercertifikater, der har den primære opgave af godkendelse af en server (node) til klienter eller godkendelse af en server (node) på en server (node). En af de indledende Kontroller, når en klient eller node godkender en node er at kontrollere værdien af det almindelige navn i feltet emne. Denne almindelige navn eller en af de certifikater emnenavne alternativ skal findes på listen over tilladte almindelige navne.

I følgende artikel beskriver, hvordan du oprette certifikater med alternativ emnenavne (SAN): [hvordan du tilføjer et alternativt emnenavn til et sikkert LDAP-certifikat](http://support.microsoft.com/kb/931351).

Feltet emne kan indeholde flere værdier, hver præfikset en initialisering til at angive, hvilken værdi. Mest almindeligt er initialiseringen "CN" til almindelige navn. for eksempel "CN = www.contoso.com". Du kan også til feltet emne skal være tom. Hvis feltet valgfrit Alternative emnenavn er udfyldt, skal den indeholde både det almindelige navn for certifikatet, og én post per alternative emnenavn. Disse er angivet som DNS-navn værdier.

Værdien i feltet formål for certifikatet, bør omfatte en passende værdi, som "Servergodkendelse" eller "klientgodkendelse".

### <a name="client-x509-certificates"></a>Klient x.509-certifikater

Klientcertifikater ikke typisk er udstedt af en tredjepartsudbyder af nøglecenter. I stedet indeholder det personlige lager for den aktuelle brugerplacering typisk klientcertifikater, der er placeret dér af et rodnøglecenter, med en formål "Klient godkendelse". Klienten kan bruge disse et certifikat, når fælles godkendelse er påkrævet.

>[AZURE.NOTE] Alle management handlinger på en tjeneste-strukturen klynge kræver servercertifikater. Klientcertifikater kan ikke bruges til at administrere.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Næste trin

I denne artikel indeholder grundlæggende oplysninger om klynge sikkerhed. Næste, [oprette en klynge i Azure ved hjælp af en ressourcestyring skabelon](service-fabric-cluster-creation-via-arm.md) eller via [Azure-portalen](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
