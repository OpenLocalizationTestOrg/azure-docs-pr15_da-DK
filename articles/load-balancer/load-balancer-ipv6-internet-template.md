<properties
    pageTitle="Installere en forbindelse til internettet Indlæs afstemmes-løsning med IPv6 ved hjælp af en skabelon | Microsoft Azure"
    description="Sådan installeres IPv6-understøttelse af Azure belastning og Indlæs afstemmes FOS."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6-azure justering af belastning dobbelt stak, offentlige IP-adresse, oprindelige ipv6, mobile, og iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Installere en forbindelse til internettet belastningsjustering løsning med IPv6 ved hjælp af en skabelon

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Azure CLI](./load-balancer-ipv6-internet-cli.md)
- [Skabelon](./load-balancer-ipv6-internet-template.md)

En Azure justering af belastning er belastningsjustering lag-4 (TCP, UDP). Justering af belastning giver høj tilgængelighed ved at distribuere indgående trafik mellem forekomster af sund tjenesten i skytjenester eller virtuelle maskiner i et Indlæs belastningsjusteringstjenesten sæt. Azure justering af belastning kan også give disse tjenester på flere porte, flere IP-adresser eller begge dele.

## <a name="example-deployment-scenario"></a>Eksempel på installation scenarie

I følgende diagram vises belastningsjustering løsning der installeres ved hjælp af skabelonen eksempel, der er beskrevet i denne artikel.

![Indlæse belastningsjusteringstjenesten scenarie](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

I dette scenarie skal du oprette følgende Azure ressourcer:

- et virtuelt netværksgrænsefladen til hver VM med både IPv4 og IPv6-adresser, der er tildelt
- en forbindelse til internettet justering af belastning med en IPv4 og IPv6 offentlige IP-adressen
- to indlæse justering af regler for at tilknytte de offentlige VIPs til private slutpunkterne
- en tilgængelighed angive, der indeholder to FOS
- to virtuelle maskiner (VM'er)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implementering af skabelonen ved hjælp af portalen Azure

I denne artikel henviser til en skabelon, der er blevet publiceret i skabelongalleriet [Azure Hurtig start](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Du kan hente skabelonen fra galleriet eller Start installationen i Azure direkte fra galleriet. I denne artikel antages det, du har hentet skabelonen til din lokale computer.

1. Åbn Azure portalen og logge på med en konto, der har tilladelse til at oprette FOS og netværk ressourcer inden for et Azure-abonnement. Også, medmindre du bruger eksisterende ressourcer, kontoen skal have tilladelse til at oprette en ressourcegruppe og en lagerplads-konto.

2. Klik på "+ Ny" fra menuen og derefter overskrive "skabelon" i søgefeltet. Vælg "Skabelon installation" i søgeresultaterne.

    ![kg-ipv6-portal-trin 2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. I den alt blade, klik på "Skabelon installation".

    ![kg-ipv6-portal-trin 3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klik på "Opret".

    ![kg-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klik på "Rediger skabelon". Slet det eksisterende indhold og kopiere/indsætte i hele indholdet af skabelonfilen (for at medtage start og Afslut {}) og derefter klikke på "Gem".

    > [AZURE.NOTE] Hvis du bruger Microsoft Internet Explorer, når du indsætter du får vist en dialogboks, der beder dig om at give adgang til Windows Udklipsholder. Klik på "Tillad adgang".

    ![kg-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klik på "Rediger parametre". Angive værdier for hvert vejledningen i afsnittet skabelon parametre i bladet parametre og derefter klikke på "Gem" for at lukke bladet parametre. Vælg dit abonnement, en eksisterende ressourcegruppe i bladet brugerdefineret installation, eller Opret en. Hvis du opretter en ressourcegruppe, skal du vælge en placering til ressourcegruppen. Derefter klikke på **juridiske vilkår**og derefter klikke på **Køb** for de juridiske vilkår. Azure begynder at implementere ressourcerne. Det kræver flere minutter at installere alle ressourcerne.

    ![kg-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Du kan finde flere oplysninger om disse parametre i afsnittet [Skabelonparametre og variabler](#template-parameters-and-variables) senere i denne artikel.

7. Klik på Gennemse for at få vist de ressourcer, der er oprettet af skabelonen, Rul ned på listen, indtil du se "Grupper", og klik derefter på den.

    ![kg-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klik på navnet på den ressourcegruppe, du angav i trin 6 bladet ressource grupper. Du kan se en liste over alle de ressourcer, der blev installeret. Hvis alle gik godt, skal der stå "Lykkedes" under "Sidste installation". Hvis ikke, kan du sikre dig, at den konto, du bruger har tilladelse til at oprette de nødvendige ressourcer.

    ![kg-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [AZURE.NOTE] Hvis du gennemser dine ressourcegrupper umiddelbart efter at udføre trin 6, vises "Sidste installation" status for "Installere", mens ressourcerne, der installeres.

9. Klik på "myIPv6PublicIP" på listen over ressourcer. Du kan se, at det har en IPv6-adresse under IP-adresse, og at navnet DNS er den værdi, du angav for parameteren dnsNameforIPv6LbIP i trin 6. Denne ressource er det offentlige IPv6-adresse og host navn, der er tilgængeligt til Internet-klienter.

    ![kg-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validere connectivity

Når skabelonen har installeret, kan du validere forbindelse ved at udføre følgende opgaver:

1. Log på portalen Azure og oprette forbindelse til hver af de VM'er, der er oprettet af skabelon installationen. Hvis du har installeret en Windows Server VM, der kører ipconfig/alt fra en kommandoprompt. Du kan se, at FOS har både IPv4 og IPv6-adresser. Hvis du har installeret Linux FOS, skal du konfigurere Linux OS for at hente dynamiske IPv6-adresser ved at følge vejledningen til din Linux-distribution.
2. Starte en forbindelse til den offentlige IPv6-adresse for justering af belastning fra en forbindelse til internettet IPv6-klient. For at bekræfte, at justering af belastning justering af mellem de to FOS, kan du installere en webserver som Microsoft Internet Information Services (IIS) på hver af FOS. Standard-webside på hver server kan indeholde teksten "Server0" eller "Server1" for at identificere den entydigt. Åbn en webbrowser på en forbindelse til internettet IPv6-klient derefter, og gå til hostname angivet for parameteren dnsNameforIPv6LbIP for justering af belastning for at bekræfte til slut IPv6-forbindelse til hver VM. Hvis du kun se websiden fra kun én server, skal du kan rydde browserens cache. Åbne flere private sessioner. Du bør se et svar fra hver server.
3. Starte en forbindelse til den offentlige IPv4-adresse for justering af belastning fra en forbindelse til internettet IPv4-klient. For at bekræfte, at justering af belastning er to FOS justering af belastning, kan du teste ved hjælp af IIS, som beskrevet i trin 2.
4. Aktivere en udgående forbindelse til en IPv6- eller IPv4-forbindelse til internettet enhed fra hver VM. I begge tilfælde er ses af destinationsenheden kilde-IP offentlige IPv4 eller IPv6-adressen på justering af belastning.

>[AZURE.NOTE]
ICMP for både IPv4 og IPv6 er blokeret i Azure netværket. Som et resultat mislykkedes ICMP-værktøjer, som ping altid. Bruge et TCP alternativ som TCPing eller cmdlet'en PowerShell Test-NetConnection til at teste forbindelsen. Bemærk, at de IP-adresser, der vises i diagrammet eksempler på værdier, som du kan se. Da disse IPv6-adresser er tildelt dynamisk, er de adresser, du modtager varierer og kan variere efter område. Det er også almindelige til den offentlige IPv6-adresse på justering af belastning til at starte med et andet præfiks end de private IPv6-adresser i back end-puljen.

## <a name="template-parameters-and-variables"></a>Skabelonparametre og variabler

En skabelon til Azure ressourcestyring indeholder flere variabler og parametre, du kan tilpasse til dine behov. Variabler bruges til faste værdier, som du ikke vil en bruger til at ændre. Parametre bruges for værdier, du vil en bruger til at levere ved implementering af skabelonen. Skabelonen eksempel er konfigureret til dette scenario, der er beskrevet i denne artikel. Du kan tilpasse til behovet i dit miljø.

Skabelonen eksempel anvendes i denne artikel indeholder følgende variabler og parametre:

| Parameter / variabel | Noter |
|-----------|-------|
| adminUsername | Angiv navnet på bruges til at logge på virtuelle maskiner med administratorkonto. |
| adminPassword | Angiv adgangskoden for kontoen Administrator, der bruges til at logge på virtuelle maskiner med. |
| dnsNameforIPv4LbIP | Angiv den DNS host-navn, du vil tildele som offentlige navnet på justering af belastning. Dette navn oversættes til justering af belastning offentlig IPv4-adresse. Navnet skal skrives med småt og tilpasse regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP | Angiv DNS-værtsnavn, du vil tildele som offentlige navnet på justering af belastning. Dette navn oversættes til justering af belastning offentlige IPv6-adresse. Navnet skal skrives med småt og tilpasse regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Det kan være det samme navn som IPv4-adressen. Når en klient sender en DNS-forespørgsel til dette navn Azure returnerer poster både A og AAAA når navnet er delt. |
| vmNamePrefix | Angive præfikset VM navn. Skabelonen føjer et tal (0, 1, osv.) til navnet, når der oprettes på FOS. |
| nicNamePrefix | Angive præfikset netværk interface navn. Skabelonen føjer et tal (0, 1, osv.) til navnet, når der oprettes netværksgrænseflader. |
| storageAccountName | Skriv navnet på en eksisterende lagerplads-konto, eller Angiv navnet på en ny skal have oprettet af skabelonen. |
| availabilitySetName | Angiv derefter navnet på tilgængeligheden angive skal bruges sammen med FOS |
| addressPrefix | Adressepræfikset bruges til at definere adresseområde på det virtuelle netværk |
| subnetName | Navnet på undernettet i oprettet for VNet |
| subnetPrefix | Adressepræfikset bruges til at definere adresseområde på undernettet |
| vnetName | Angiv et navn til den VNet, der anvendes af FOS. |
| ipv4PrivateIPAddressType | Metoden til fordeling, der bruges til privat IP-adresse (statisk eller dynamisk) |
| ipv6PrivateIPAddressType | Metoden til fordeling bruges til den private IP-adresse (dynamisk). IPv6 understøtter kun dynamisk fordeling. |
| numberOfInstances | Antallet af belastning afstemmes forekomster, der er installeret af skabelonen |
| ipv4PublicIPAddressName | Angiv DNS-navn, du vil bruge til at kommunikere med den offentlige IPv4-adresse på justering af belastning. |
| ipv4PublicIPAddressType | Metoden til fordeling bruges til den offentlige IP-adresse (statisk eller dynamisk) |
| Ipv6PublicIPAddressName | Angiv DNS-navn, du vil bruge til at kommunikere med den offentlige IPv6-adresse på justering af belastning. |
| ipv6PublicIPAddressType | Metoden til fordeling bruges til den offentlige IP-adresse (dynamisk). IPv6 understøtter kun dynamisk fordeling. |
| lbName | Angiv navnet på justering af belastning. Dette navn vises i portalen eller bruges til at referere til den med en CLI eller PowerShell-kommando. |

De resterende variabler i skabelonen indeholder afledte værdier, der er tildelt, når Azure opretter ressourcerne. Undlad at ændre variablerne.
