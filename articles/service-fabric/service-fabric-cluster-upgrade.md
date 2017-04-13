<properties
   pageTitle="Opgradere en Azure Service strukturen klynge | Microsoft Azure"
   description="Opgradere Service-strukturen kode og/eller konfiguration, der kører en tjeneste-strukturen klynge, herunder indstille klynge opdateringstilstand, opgradering certifikater, tilføje programmet porte, at gøre OS rettelser, og så videre. Hvad kan du forvente, når opgraderinger udføres?"
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
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Opgradere en Azure Service strukturen klynge

> [AZURE.SELECTOR]
- [Azure klynge](service-fabric-cluster-upgrade.md)
- [Enkeltstående klynge](service-fabric-cluster-upgrade-windows-server.md)

Design af upgradability er til en hvilken som helst moderne system, for at nå langsigtede succes af dit produkt. En Azure Service strukturen klynge er en ressource, du ejer, men er delvist administreres af Microsoft. I denne artikel beskrives, hvad der administreres automatisk, og hvad du kan konfigurere dig selv.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Styre versionerne struktur, der kører på din klynge

Du kan angive din klynge for at modtage automatiske strukturen opgraderinger, når Microsoft udgiver en ny version eller vælge at vælge en understøttede strukturen version, du vil din klynge skal være på.

Det gør du ved at "upgradeMode" klyngekonfigurationen på portalen eller ved at benytte Ressourcestyring på tidspunktet for oprettelse eller nyere på en direkte klynge 

>[AZURE.NOTE] Sørg for at holde din klynge med en understøttede strukturen version altid. Som, og når vi oplyse version af en ny version af tjenesten strukturen, markeres den tidligere version til slutningen af support efter et minimum af 60 dage fra denne dato. i de nye versioner er offentliggjort [på service-strukturen teambloggen](https://blogs.msdn.microsoft.com/azureservicefabric/ ). Den nye version er tilgængelig til at vælge derefter. 

14 dage før udløbet af den udgave af din klynge kører, der hændelsen sundhed genereres, som indsætter din klynge i tilstanden en advarsel. Klyngen forbliver i tilstanden advarsel, før du opgraderer til en understøttet strukturen version.


### <a name="setting-the-upgrade-mode-via-portal"></a>Konfiguration af opgradering tilstand via portalen 

Du kan angive klyngen til automatisk eller manuel, når du opretter klyngen.

![Create_Manualmode][Create_Manualmode]

Du kan angive klyngen til automatisk eller manuelt, når jeg er på en direkte klynge ved hjælp af Administrer oplevelsen. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Opgradere til en ny version på en klynge, der er angivet til manuel tilstand via portalen.
 
For at opgradere til en ny version, skal du gøre blot vælge den version på rullelisten og gemme. Strukturen opgraderingen bliver Smidt automatisk. Klynge sundhed politikker (en kombination af node tilstand og tilstanden alle programmer, der kører i klyngen) er overholdt under opgraderingen.

Hvis klynge sundhed politikker ikke opfyldes, annulleres opgraderingen. Rul ned dette dokument for at få mere at vide om, hvordan du indstille disse brugerdefinerede sundhed politikker. 

Når du har løst de problemer, der udløste rollback, skal du starte opgraderingen igen ved at følge de samme trin som før.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Konfiguration af opgradering tilstand via en ressourcestyring skabelon 

Tilføje "upgradeMode" konfigurationen til Microsoft.ServiceFabric/clusters ressource definitionen og angive "clusterCodeVersion" til en af de understøttede struktur versioner som vist nedenfor, og derefter installere skabelonen. Gyldige værdier for "upgradeMode" er "Manuel" eller "Automatisk"
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Opgradere til en ny version på en klynge, der er angivet til manuel tilstand via en ressourcestyring skabelon.
 
Når klyngen er i manuel tilstand, for at opgradere til en ny version, ændre "clusterCodeVersion" til en understøttet version og installere den. Installation af skabelonen, starter opgraderingen strukturen bliver startet automatisk. Klynge sundhed politikker (en kombination af node tilstand og tilstanden alle programmer, der kører i klyngen) er overholdt under opgraderingen.

Hvis klynge sundhed politikker ikke opfyldes, annulleres opgraderingen. Rul ned dette dokument for at få mere at vide om, hvordan du indstille disse brugerdefinerede sundhed politikker. 

Når du har løst de problemer, der udløste rollback, skal du starte opgraderingen igen ved at følge de samme trin som før.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Få liste over alle tilgængelige version for alle miljøer til et bestemt abonnement

Køre følgende kommando, og du skal have en output nogenlunde sådan ud.

"supportExpiryUtc" fortæller din når en bestemt version udløber eller er udløbet. Den seneste udgave har ikke en gyldig dato – den har en værdi på "9999-12-31T23:59:59.9999999", hvilket betyder simpelthen, udløbsdatoen ikke er angivet endnu.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Strukturen opgradering funktionsmåde, når klynge opgradere tilstand er automatisk

Microsoft beskytter strukturen kode og konfiguration, der kører i en Azure klynge. Vi foretager automatiske overvåget opgraderinger til softwaren på efter behov. Disse opgraderinger kan være kode, konfiguration eller begge dele. Hvis du vil sikre dig, at dit program befinder sig ingen betydning eller minimale virkning på grund af disse opgraderinger, foretager vi opgraderinger i de følgende faser:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: En opgradering udføres ved hjælp af alle klynge sundhed politikker

I denne fase opgraderinger Fortsæt ét opgradering domæne ad gangen, og de programmer, der kørte i klyngen fortsætte med at køre uden en hvilken som helst nedetid. Klynge sundhed politikker (en kombination af node tilstand og tilstanden alle programmer, der kører i klyngen) er overholdt under opgraderingen.

Hvis klynge sundhed politikker ikke opfyldes, annulleres opgraderingen. Derefter sendes en mail til ejeren af abonnementet. E-mailen indeholder følgende oplysninger:

- Besked om, at vi nødt til at annullere en klynge opgradering.
- Foreslåede korrigerende handlinger eventuelt.
- Antallet af dage (n), indtil vi udføre fase 2.

Vi kan du prøve at udføre samme opgraderingen nogle gange i tilfælde af enhver opgraderinger mislykkedes infrastruktur årsager. Når du har de n dage fra datoen e-mailen blev sendt, vi gå videre til fase 2.

Hvis klynge sundhed politikker er opfyldt, er opgraderingen betragtes som vellykket og markeret som fuldført. Dette kan ske under den indledende opgradering eller nogen af opgradering genudsendelser i denne fase. Der er ingen e-mail-bekræftelse af en vellykket Kør. Dette er at undgå at sende du for mange mails – modtager en mail skal betragtes som en undtagelse til normal. Vi forventer, at de fleste af de klynge opgraderinger kan udføres uden at påvirke din programmet tilgængelighed.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: En opgradering udføres ved hjælp af kun standard sundhed politikker

Sundhed politikkerne i denne fase angives i en sådan måde, at antallet af programmer, der var sund i starten af opgraderingen forbliver uændret for varigheden af opgraderingsprocessen. Fase 2 opgraderinger Fortsæt ét opgradering domæne ad gangen som fase 1, og de programmer, der kørte i klyngen fortsætte med at køre uden en hvilken som helst nedetid. Klynge sundhed politikker (en kombination af node tilstand og tilstanden alle programmer, der kører i klyngen) er overholdt for varigheden af opgraderingen.

Hvis klynge sundhed politikker gældende ikke opfyldes, annulleres opgraderingen. Derefter sendes en mail til ejeren af abonnementet. E-mailen indeholder følgende oplysninger:

- Besked om, at vi nødt til at annullere en klynge opgradering.
- Foreslåede korrigerende handlinger eventuelt.
- Antallet af dage (n), indtil vi udføre fase 3.

Vi kan du prøve at udføre samme opgraderingen nogle gange i tilfælde af enhver opgraderinger mislykkedes infrastruktur årsager. En påmindelse via mail sendes et par dage før n dage, der er op. Når du har de n dage fra datoen e-mailen blev sendt, vi gå videre til fase 3. De e-mails, sender vi dig i fase 2 skal der tages alvorligt og korrigerende handlinger skal udføres.

Hvis klynge sundhed politikker er opfyldt, er opgraderingen betragtes som vellykket og markeret som fuldført. Dette kan ske under den indledende opgradering eller nogen af opgradering genudsendelser i denne fase. Der er ingen e-mail-bekræftelse af en vellykket Kør.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: En opgradering udføres ved hjælp af tilstanden sundhed politikker

Disse politikker for systemtilstand i denne fase henvender sig til fuldførelse af opgraderingen i stedet for tilstanden af programmerne. Meget få klynge opgraderinger ender i denne fase. Hvis din klynge får til denne fase, er der en god mulighed for, at dit program bliver beskadiget og/eller mister tilgængelighed.

Svarer til de andre to faser, fortsætte fase 3 opgraderinger ét opgradering domæne ad gangen.

Hvis klynge sundhed politikker ikke opfyldes, annulleres opgraderingen. Vi kan du prøve at udføre samme opgraderingen nogle gange i tilfælde af enhver opgraderinger mislykkedes infrastruktur årsager. Klyngen er herefter fastgjort, så det ikke længere vil modtage support og/eller opgraderinger.

En mail med disse oplysninger sendes til ejeren af abonnementet, sammen med de korrigerende handlinger. Vi forventer ikke en hvilken som helst klynger til ind i en tilstand, hvor fase 3 mislykkes.

Hvis klynge sundhed politikker er opfyldt, er opgraderingen betragtes som vellykket og markeret som fuldført. Dette kan ske under den indledende opgradering eller nogen af opgradering genudsendelser i denne fase. Der er ingen e-mail-bekræftelse af en vellykket Kør.

## <a name="cluster-configurations-that-you-control"></a>Klyngekonfigurationer, du styre

Ud over muligheden for at angive klyngen opgradere tilstand, her er de konfigurationer, du kan ændre på en direkte klynge.

### <a name="certificates"></a>Certifikater

Du kan tilføje nye eller slette certifikater til klynge og klienten via portalen nemt. Referere til [dette dokument for at få detaljerede anvisninger](service-fabric-cluster-security-update-certs-azure.md)

![Skærmbillede, der viser certifikat thumbprints i portalen Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Programmet porte

Du kan ændre programmet porte ved at ændre egenskaberne justering af belastning ressource, der er knyttet til nodetypen. Du kan bruge portalen, eller du kan bruge ressourcestyring PowerShell direkte.

Hvis du vil åbne en ny port på alle FOS i en node af type, skal du gøre følgende:

1. Tilføje en ny efterprøvning af af til den ønskede justering af belastning.

    Hvis du har installeret din klynge ved hjælp af portalen, Indlæs balancere navngives "Kg-navnet på den ressource gruppe-NodeTypename", én for hver nodetype. Da Indlæs belastningsjusteringstjenesten navnene er entydige kun inden for en ressourcegruppe, er det bedst, hvis du søger efter dem under en bestemt ressourcegruppe.

    ![Skærmbillede, der viser, at føje en efterprøvning af af til en justering af belastning på portalen.][AddingProbes]

2. Føje en ny regel til justering af belastning.

    Tilføje en ny regel til den samme justering af belastning ved hjælp af efterprøvning af af, du har oprettet i ovenstående trin.

    ![Tilføje en ny regel belastningsjustering på portalen.][AddingLBRules]


### <a name="placement-properties"></a>Egenskaber for placering

For hver af Nodetyperne, kan du tilføje brugerdefinerede placeringen egenskaber, du vil bruge i dine programmer. NodeType, en standardegenskab, som du kan bruge uden at tilføje den eksplicit.

>[AZURE.NOTE] Få mere at vide om brug af placeringen begrænsninger, egenskaber for knude og hvordan du definerer dem se afsnittet "Placering begrænsninger og egenskaber for knude" i tjenesten strukturen klynge ressourcestyring dokumentet på [Der beskriver din klynge](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Kapacitet målepunkter

For hver af Nodetyperne, kan du tilføje brugerdefinerede kapacitet mål, som du vil bruge i dine programmer til rapport belastning. Få mere at vide om brug af kapacitet målepunkter til rapport indlæse skal du henvise til tjenesten strukturen klynge ressourcestyring dokumenter på [Der beskriver din klynge](service-fabric-cluster-resource-manager-cluster-description.md) og [målepunkter og Indlæs](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Strukturen opgraderingsindstillingerne - tilstand politikker

Du kan angive brugerdefinerede sundhed politikker til strukturen opgradering. Hvis du har oprettet din klynge til automatisk strukturen opgraderinger, få disse politikker anvendt på den fase 1 af de automatiske struktur opgraderinger.
Hvis du har oprettet din klynge til manuel strukturen opgraderinger, anvendes disse politikker få hver gang du vælger en ny version, som udløser systemet til at starte opgraderingen strukturen i din klynge. Hvis du ikke tilsidesætte politikkerne, der skal bruges standardindstillingerne.

Du kan angive brugerdefinerede sundhed politikkerne eller gennemgå de aktuelle indstillinger under bladet "strukturen opgraderingen" ved at vælge dialogboksen Avancerede indstillinger for opgradering. Gennemse om, hvordan du følgende billede. 

![Administrere brugerdefinerede sundhed politikker][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Tilpasse strukturen indstillinger for din klynge

Se [strukturen klynge strukturen Tjenesteindstillinger](service-fabric-cluster-fabric-settings.md) på hvad, og hvordan du kan tilpasse dem.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>OS rettelser på VM'er, der udgør klyngen

Denne funktion er planlagt i fremtiden som en automatisk funktion. Men, du er ansvarlig for at programrettelse din FOS. Du skal gøre dette én VM ad gangen, så du ikke tager ned mere end én ad gangen.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>OS opgraderinger på VM'er, der udgør klyngen

Hvis du skal opgradere OS billedet på virtuelle maskiner af klyngen, skal du gøre det én VM ad gangen. Du er ansvarlig for denne opgradering – der findes i øjeblikket ingen automatisering af dette.

## <a name="next-steps"></a>Næste trin
- Lær at tilpasse nogle af de [strukturen klynge strukturen Tjenesteindstillinger](service-fabric-cluster-fabric-settings.md)
- Lær, hvordan du kan [tilpasse din klynge ind og ud](service-fabric-cluster-scale-up-down.md)
- Få mere at vide om [programmet opgraderinger](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG