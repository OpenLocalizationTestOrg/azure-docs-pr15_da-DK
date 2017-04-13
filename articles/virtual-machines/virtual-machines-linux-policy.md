<properties
    pageTitle="Anvende politikker til Azure ressourcestyring virtuelle maskiner | Microsoft Azure"
    description="Hvordan du anvender en politik til en Azure ressourcestyring Linux Virtual Machine"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Anvende politikker til Ressourcestyring virtuelle Azure-computere

Ved hjælp af politikker, kan en organisation gennemtvinge forskellige konventioner og regler i hele virksomheden. Aktivering af de ønskede funktionalitet kan hjælpe dig med at reducere risikoen under bidrager til en vellykket organisationen. I denne artikel vil vi beskriver, hvordan du kan bruge Azure ressourcestyring politikker til at definere den ønskede funktionsmåde for din organisation virtuelle computere.

Dispositionen for at se trinnene til at udføre dette er som nedenfor

1. Azure ressourcestyring politik 101
2. Definere en politik for din virtuelle maskine
3. Oprette politikken
4. Anvende politikken

## <a name="azure-resource-manager-policy-101"></a>Azure ressourcestyring politik 101

For at komme i gang med Azure ressourcestyring politikker, anbefaler vi læse følgende artikel og derefter fortsætte med trin i denne artikel. Følgende artikel beskrives de grundlæggende definition og strukturen af en politik, hvordan politikker få evalueres og giver forskellige eksempler på Politikdefinitioner.

* [Brug politik til at administrere ressourcer og kontrollere adgangen](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definere en politik for din virtuelle maskine

En af de almindelige scenarier, hvor en virksomhed kan være til kun at tillade brugerne til at oprette virtuelle maskiner fra bestemte operativsystemer, der er testet så den er kompatibel med et LOB-program. Ved hjælp af en politik til Azure ressourcestyring kan denne opgave udføres i et par trin. Vi vil tillade kun Ubuntu 14.04.2-LTS virtuelle maskiner skal have oprettet i dette eksempel politik. Politikdefinitionen ligner under

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

Politikken ovenfor kan nemt ændres til et scenarie, hvor du muligvis vil tillade en hvilken som helst Ubuntu LTS billede der skal bruges til en Virtual Machine-installation med den under Skift

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>Virtuelt egenskabsfelter

Tabellen nedenfor beskrives de virtuelt egenskaber, der kan bruges som felter i politikdefinitionen for din. Flere oplysninger om felter til politik, artiklen nedenfor:

* [Felter og kilder](../resource-manager-policy.md#fields-and-sources)


| Feltnavn     | Beskrivelse                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Angiver udgiveren af billedet               |
| imageOffer     | Angiver tilbuddet til det valgte billede publisher |
| imageSku       | Angiver SKU til den valgte tilbud             |
| imageVersion   | Angiver versionen af billedet til valgt SKU     |

## <a name="create-the-policy"></a>Oprette politikken

En politik kan nemt oprettes ved hjælp af REST-API direkte eller PowerShell-cmdlet'er. Oprette politikken artiklen nedenfor:

* [Oprette en politik](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Anvende politikken

Når du har oprettet politikken skal du anvende den på et defineret område. Omfanget kan være et abonnement, ressourcegruppe eller endda ressourcen. Anvende politikken artiklen nedenfor:

* [Oprette en politik](../resource-manager-policy.md#applying-a-policy)
