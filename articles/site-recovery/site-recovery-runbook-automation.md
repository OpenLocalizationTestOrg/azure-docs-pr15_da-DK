<properties
   pageTitle="Føje Azure automatisering runbooks til gendannelse planer | Microsoft Azure"
   description="I denne artikel beskrives, hvordan Azure gendannelse af websteder nu gør det muligt at udvide gendannelse plan ved hjælp af Azure automatisering til at udføre kompleks opgaver under genoprettelsen til Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Føje Azure automatiske runbooks til gendannelse planer


I dette selvstudium beskrives, hvordan Azure gendannelse af websteder integreres med Azure automatiske til at levere udvidelsesmuligheder til gendannelse planer. Gendannelse planer kan dirigerer gendannelse af din virtuelle maskiner, der er beskyttet via Azure gendannelse af websteder til både gentagelse sekundær skyen og gentagelse til Azure scenarier. De også hjælpe med at foretage den gendannelse **ensartede og nøjagtige** **gentaget**og **automatiseret**. Hvis du mislykkes over din virtuelle maskiner til Azure, integration med Automation Azure udvider gendannelse planer og giver dig mulighed for at udføre runbooks, således at effektiv automatiseringsopgaver.

Hvis du ikke har hørt om Azure automatisering endnu, Tilmeld dig [her](https://azure.microsoft.com/services/automation/) og downloade eksempelscripts deres [her](https://azure.microsoft.com/documentation/scripts/). Du kan finde flere oplysninger om [Gendannelse af Azure websteder](https://azure.microsoft.com/services/site-recovery/) og Sådan dirigerer gendannelse til Azure ved hjælp af gendannelse planer [her](https://azure.microsoft.com/blog/?p=166264).

I dette selvstudium vil vi se på hvordan du kan integrere Azure automatisering runbooks i gendannelse planer. Vi vil automatisere enkle opgaver, der tidligere påkrævet manuel handling og se, hvordan du konverterer en flere trin genoprettelse til et enkelt klik gendannelse handling. Vi ser også på, hvordan du kan foretage fejlfinding af et simpelt script, hvis det går galt.

## <a name="customize-the-recovery-plan"></a>Tilpasse gendannelse planen

1. Lad os starte med at operning bladet ressource af gendannelse planen. Du kan se gendannelse plan indeholder to virtuelle maskiner, der er føjet til den for gendannelse. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Klik på knappen Tilpas til at begynde at tilføje en runbook. Dette åbner gendannelse planen tilpasse blade.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Højreklik på gruppen start 1, og vælg 'Tilføj indlæg handlingen' skal tilføjes.

4. Vælg for at vælge et script i den nye blade.

5. Navngiv scriptet 'Hej verden'.

6. Vælg navnet på en konto med Automation. Dette er den automatiske Azure-konto. Bemærk, at denne konto kan være i en hvilken som helst Azure Geografi, men skal være i samme abonnement som samling af legitimationsoplysninger gendannelse af websteder.

7. Vælg en runbook kontoen automatisering. Dette er det script, der kører under udførelse af gendannelse planen efter at genoprettelsen første gruppe.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Vælg OK for at gemme scriptet. Dette føjer scriptet til gruppen indlæg handling i gruppen 1: Start.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Vigtige punkter af at tilføje et script

1. Du kan højreklikke på scriptet og vælge at 'slette trin' eller 'Opdater script'.

2. Et script kan køre på Azure under failover fra det lokale til Azure, og den kan køre på Azure som primære side script før lukning, under failback fra Azure til en lokal installation.

3. Når der kører et script, der det indsætter en gendannelse plan kontekst.

Nedenfor vises et eksempel på udseendet af variablen kontekst.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Tabellen nedenfor indeholder navn og beskrivelse til hver variabel i konteksten.

**Variabelnavn** | **Beskrivelse**
---|---
RecoveryPlanName | Navnet på plan køres. Hjælper med at handle ud fra navn ved hjælp af det samme script
FailoverType | Angiver, om sekundære test, planlagt eller uventet.
FailoverDirection | Angiv, om gendannelse er primær eller sekundær
Gruppe-id | Identificere gruppe tal i den gendannelse plan, når planen kører
VmMap | Matrix af alle virtuelle maskiner i gruppen
VMMap nøgle | Entydig nøgle (GUID) for hver VM. Det er den samme som den virtuelle maskine VMM ID, hvis det er relevant.
RoleName | Navnet på den Azure VM, gendannes
CloudServiceName | Azure skybaseret tjeneste navn som den virtuelle maskine er oprettet under.
CloudServiceName (i ressourcestyring implementeringsmodel) | Azure ressourcegruppe navn som den virtuelle maskine er oprettet under.


## <a name="using-complex-variables-per-recovery-plan"></a>Brug af komplekse variabler per gendannelse plan

Nogle gange, kræver et runbook mere end bare RecoveryPlanContext. Der er ingen førsteklasses metode til at overføre en parameter til en runbook. Men hvis du vil bruge det samme script via flere gendannelse planer kan du bruge variablen gendannelse planlægge kontekst 'RecoveryPlanName' og bruge den under forsøg metode til at bruge en Azure automatiske komplekst variabel i en runbook. I eksemplet nedenfor viser, hvordan du kan oprette tre forskellige komplekst variabel aktiver og bruge dem i runbook baseret på navnet på den gendannelse plan.

Overvej, du vil bruge 3 yderligere parametre i en runbook. Lad os kode dem i en formular JSON {"Var1": "testautomation", "Var2": "Ikke-planlagt", "Var3": "PrimaryToSecondary"}

Brug [å komplekst variabel](../automation/automation-variables.md#variable-types Complex variable) til at oprette et nyt automatisering aktiv.
Navngive variablen som <RecoveryPlanName>- parametre.
Du kan bruge referencen her til at oprette en [kompleks variabel](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Navngive variablen som for forskellige gendannelse planer

1. recoveryPlanName1 >-Parametre
2. recoveryPlanName2 >-Parametre
3. recoveryPlanName3 >-Parametre

Nu, i scriptet referere til parametre som

1. Få RP navnet fra $rpname = $Recoveryplancontext variabel
2. Få aktiv tilhørende $paramValue = "$($rpname)-parametre"
3. Brug dette som en kompleks variabel for gendannelse plan ved at ringe til Get-AzureAutomationVariable [-AutomationAccountName] <String> -navngive $paramValue.

Oprette en Azure automatiske komplekst variabel, som kaldes SharepointApp-parametre for at få komplekst variabel/parameteren for SharepointApp gendannelse plan, som et eksempel.

Bruge det i den gendannelse plan ved at udtrække variablen fra aktivet ved hjælp af sætningen Get-AzureAutomationVariable [-AutomationAccountName] <String> [-navn] $paramValue. [Henvise til dette kan finde flere oplysninger](https://msdn.microsoft.com/library/dn913772.aspx)

Denne måde det samme script kan bruges til forskellige gendannelse plan ved at gemme plan bestemt komplekst variabel i aktiverne.

## <a name="sample-scripts"></a>Eksempel på scripts

Et lager af scripts, som du kan importere direkte til din konto med automation, skal du se [Kristian Nese OMS lager til scripts](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

Scriptet her er en Azure ressourcestyring skabelon, der implementerer alt det under scripts

* NSG

NSG runbook tildeles offentlige IP-adresser til hver VM i gendannelse planlægge og vedhæfte deres virtuelle netværkskort til en netværk sikkerhedsgruppe, som tillader standard kommunikation

* PublicIP

Den offentlige IP-runbook tildele offentlige IP-adresser til hver VM i gendannelse planlægge. Adgang til de computere og programmer, afhænger af firewall-indstillinger i hvert gæst


* CustomScript

CustomScript runbook tildeles offentlige IP-adresser til hver VM i gendannelse planlægge og installere filtypenavnet brugerdefineret script, som vil trække scriptet du refererer til under installation af skabelonen

* NSGwithCustomScript

NSGwithCustomScript runbook tildeler offentlige IP-adresser til hver VM inden for den gendannelse planlægge, installere en brugerdefineret script ved hjælp af lokalnummer og forbinde de virtuelle netværkskort til en NSG tillade standard indgående og udgående kommunikation til fjernadgang

## <a name="additional-resources"></a>Yderligere ressourcer

[Azure Automation Service køre som konto](../automation/automation-sec-configure-azure-runas-account.md)

[Azure automatisering oversigt] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure automatisering oversigt")

[Eksempel på Azure automatisering-Scripts] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Eksempel på Azure automatisering-Scripts")
