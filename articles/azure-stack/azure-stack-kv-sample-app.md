<properties
    pageTitle="Tillad program tilladelse til at revtrieve Azure stak nøgle samling hemmeligheder | Microsoft Azure"
    description="Brug en eksempel-app til at arbejde med Azure stak nøgle samling"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="run-the-sample-application-for-key-vault"></a>Køre eksempelprogrammet til tasten samling 

I denne vejledning, skal du bruge et eksempel på computeren til at hente hemmeligheder og adgangskoder fra nøgle samling.

## <a name="download-the-samples-and-prepare"></a>Hente eksemplerne og forberede

Hente Azure-tasten samling klient eksempler fra [Azure nøgle samling klient eksempelsiden](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Udtrække indholdet af .zip-filen til din lokale computer.

Læs filen **README.md** (dette er en tekstfil), og følg derefter vejledningen.

## <a name="run-sample-1--hellokeyvault"></a>Køre eksempel #1 – HelloKeyVault
HelloKeyVault er et console-program, der vejleder til de vigtigste scenarier, der understøttes af nøgle samling:

  1. Oprette/import en nøgle (HSM eller -software nøgle)
  2. Kryptere en hemmeligt ved hjælp af en indhold nøgle
  3. Ombryde tasten indhold ved hjælp af en nøgle samling nøgle
  4. Fjerne ombrydning af tasten indhold
  5. Dekryptere hemmeligheden
  6. Angive et hemmeligt

Console programmet skal køre uden ændringer, bortset fra at de relevante konfigurationsindstillinger i App.Config opdateres efter følgende trin:

1. Opdatere konfigurationsindstillinger app i HelloKeyVault\App.config med din samling URL-adresse, programmet primære ID og hemmeligt. Oplysninger, der kan eventuelt oprettes ved hjælp af **scripts\GetAppConfigSettings.ps1**.
2. Opdatere værdierne i de obligatoriske variabler i GetAppConfigSettings.ps1.
3. Start i Windows PowerShell-vinduet.
4. Kør scriptet GetAppConfigSettings.ps1 i PowerShell-vinduet.
5. Kopiere resultaterne af scriptet til HelloKeyVault\App.config filen.


## <a name="next-steps"></a>Næste trin

[Installere en VM med en nøgle samling adgangskode](azure-stack-kv-deploy-vm-with-secret.md)

[Installere en VM med en nøgle samling certifikat](azure-stack-kv-push-secret-into-vm.md)