<properties
   pageTitle="Ønsket konfiguration af stat ressource Manager skabelon | Microsoft Azure"
   description="Ressourcestyring skabelon definition for ønsket konfiguration af tilstand i Azure med eksempler og fejlfinding"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS og beskedteksten tilstand konfiguration med Azure ressourcestyring skabeloner
I denne artikel beskrives skabelonen ressourcestyring til den [ønskede tilstand konfiguration lokalnummer handler](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Eksempel på skabelon til en Windows-VM

Følgende kodestykke går til afsnittet ressource i skabelonen.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Eksempel på skabelon til Windows VMSS

En VMSS node har en "" egenskabssektion med "VirtualMachineProfile", "extensionProfile"-attributten. DTK føjes under "udvidelser". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="detailed-settings-information"></a>Oplysninger om særlige indstillinger

I følgende skema er for indstillinger for del af filtypenavnet Azure DTK i en skabelon til Azure ressourcestyring.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Detaljer
| Egenskabsnavn | Type | Beskrivelse |
| --- | --- | --- |
| settings.wmfVersion | streng | Angiver versionen af Windows Management Framework, der skal være installeret på din VM. Angive denne egenskab til 'seneste' installationer den mest opdaterede version af WMF. Kun aktuelle mulige værdier for denne egenskab er **'4.0' og '5.0' ' 5.0PP' og 'seneste'**. Disse mulige værdier er underlagt opdateringer. Standardværdien er 'seneste'.|
| Settings.Configuration.URL | streng | Angiver URL-adressen fra, du vil overføre din DTK konfiguration zip-fil. Hvis på URL-adressen kræver et SAS token for adgang, skal du angive egenskaben protectedSettings.configurationUrlSasToken til værdien af din SAS token. Denne egenskab er påkrævet, hvis settings.configuration.script og/eller settings.configuration.function er defineret. |
| Settings.Configuration.script | streng | Angiver filnavnet på det script, der indeholder definitionen af DTK konfigurationen. Dette script skal være i rodmappen på den zip-fil, der er hentet fra den URL-adresse, der er angivet af egenskaben configuration.url. Denne egenskab er påkrævet, hvis settings.configuration.url og/eller settings.configuration.script er defineret. |
| Settings.Configuration.Function | streng | Angiver navnet på din DTK konfiguration. Konfigurationen med navnet skal være indeholdt i scriptet defineret af configuration.script. Denne egenskab er påkrævet, hvis settings.configuration.url og/eller settings.configuration.function er defineret. |
| settings.configurationArguments | Af websteder | Definerer de parametre, du vil gerne til at overføre din DTK konfiguration. Denne egenskab er ikke krypteret. |
| settings.configurationData.url | streng | Angiver den URL-adresse, du vil overføre din konfiguration-datafil (.pds1) skal bruges som input til din DTK konfiguration. Hvis på URL-adressen kræver et SAS token for adgang, skal du angive egenskaben protectedSettings.configurationDataUrlSasToken til værdien af din SAS token.|
| settings.privacy.dataEnabled | streng | Aktiverer eller deaktiverer telemetri af websteder. De eneste mulige værdier for denne egenskab er **'Aktiver', 'Deaktiver' ", eller $null**. At forlade denne egenskab, tom eller null gør det muligt for telemetri. Standardværdien er ". [Få mere at vide](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Af websteder | Definerer alternative placeringer, du vil hente WMF fra. [Få mere at vide](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Af websteder | Definerer de parametre, du vil gerne til at overføre din DTK konfiguration. Denne egenskab er krypteret. |
| protectedSettings.configurationUrlSasToken | streng | Angiver SAS tokenet for at få adgang til den URL-adresse, der er defineret af configuration.url. Denne egenskab er krypteret. |
| protectedSettings.configurationDataUrlSasToken | streng | Angiver SAS tokenet for at få adgang til den URL-adresse, der er defineret af configurationData.url. Denne egenskab er krypteret. |

## <a name="settings-vs-protectedsettings"></a>Indstillinger for kontra ProtectedSettings
Alle indstillinger er gemt i en fil med indstillinger tekst på VM.
Egenskaber under "indstillinger" er offentlige egenskaber, fordi de ikke er krypteret i tekstfilen indstillinger.
Egenskaber under 'protectedSettings' er krypteret med et certifikat og vises ikke i almindelig tekst i denne fil på VM.

Hvis konfigurationen skal legitimationsoplysninger, kan de medtages i protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Eksempel

I følgende eksempel henter i afsnittet "Introduktion" på [siden Oversigt over DTK lokalnummer Handler](virtual-machines-windows-extensions-dsc-overview.md).
I dette eksempel bruges ressourcestyring skabeloner i stedet for cmdlet'er til at udrulle filtypenavnet. Gemme "IisInstall.ps1" konfigurationen, placere det i en. ZIP-fil, og overfør filen i en handicapvenlige URL-adresse. I dette eksempel bruges Azure blob-lager, men det er muligt at hente. ZIP-filer fra en vilkårlig placering.

I skabelonen Azure ressourcestyring får følgende kode VM at hente den korrekte fil og køre den relevante PowerShell-funktion:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Opdatering fra den tidligere Format
Alle indstillinger i det forrige format (som indeholder de offentlige egenskaber ModulesUrl, ConfigurationFunction, SasToken eller egenskaber) automatisk tilpasse til det aktuelle format og køre samme måde som de gjorde tidligere.

I følgende skema er det forrige indstillingsskemaet så ud:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Her er, hvordan det tidligere format tilpasser sig efterhånden til det aktuelle format:

| Egenskabsnavn | Forrige skema tilsvarende |
| --- | --- |
| settings.wmfVersion | indstillinger for. WMFVersion |
| Settings.Configuration.URL | indstillinger for. ModulesUrl |
| Settings.Configuration.script | Første del af indstillinger. ConfigurationFunction (før '\\\\') |
| Settings.Configuration.Function | Anden del af indstillinger. ConfigurationFunction (når '\\\\') |
| settings.configurationArguments | indstillinger for. Egenskaber |
| settings.configurationData.url | protectedSettings.DataBlobUri (uden SAS token) |
| settings.privacy.dataEnabled | indstillinger for. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | indstillinger for. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | indstillinger for. SasToken |
| protectedSettings.configurationDataUrlSasToken | SAS token fra protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Fejlfinding - fejlkode 1100
Fejlkode 1100 angiver, at der er et problem med brugerinput til filtypenavnet DTK.
Teksten i disse fejl er variabel og kan ændres.
Her er nogle af de fejl, du kan støde på, og hvordan du kan løse dem.

### <a name="invalid-values"></a>Ugyldige værdier
"Privacy.dataCollection er '{0}'. Kun mulige værdier er ", 'Aktivér' og 'Deaktivere'" "WmfVersion er '{0}'. Kun mulige værdier er... og 'seneste' "

Problem: En medfølgende værdi er ikke tilladt.

Løsning: Ændr den ugyldige værdi til en gyldig værdi. Se tabellen under detaljer.

### <a name="invalid-url"></a>Ugyldig URL-adresse
"ConfigurationData.url er '{0}'. Dette er ikke en gyldig URL-adresse""DataBlobUri er '{0}'. Dette er ikke en gyldig URL-adresse""Configuration.url er '{0}'. Dette er ikke en gyldig URL-adresse"

Problem: A angivet URL-adressen ikke er gyldig.

Løsning: Kontrollér alle dine angivne URL-adresser. Kontrollér, at alle URL-adresser fortolkes som gyldige placeringer, filtypenavnet kan få adgang til på fjerncomputeren.

### <a name="invalid-configurationargument-type"></a>Ugyldige ConfigurationArgument Type
"Ugyldige configurationArguments Skriv {0}"

Problem: Egenskaben ConfigurationArguments ikke kan fortolkes som en Hashtable-objekt. 

Løsning: Kontrollér din ConfigurationArguments ejendom en hashtabel. Følg det format, der er angivet i det foregående eksempel. Hold øje med anførselstegn, kommaer og klammeparenteser.

### <a name="duplicate-configurationarguments"></a>Dublerede ConfigurationArguments
"Fundet dublerede argumenter '{0}' i både offentlige og beskyttet configurationArguments"

Problem: ConfigurationArguments i indstillinger for offentligt og ConfigurationArguments i indstillinger for beskyttet indeholder egenskaber med samme navn.

Løsning: Fjern en af de dublerede egenskaber.

### <a name="missing-properties"></a>Manglende egenskaber
"Configuration.function kræver, at configuration.url eller configuration.module er angivet"

"Configuration.url kræver pågældende configuration.script er angivet"

"Configuration.script kræver pågældende configuration.url er angivet"

"Configuration.url kræver pågældende configuration.function er angivet"

"ConfigurationUrlSasToken kræver pågældende configuration.url er angivet"

"ConfigurationDataUrlSasToken kræver pågældende configurationData.url er angivet"

Problem: En defineret egenskab skal have en anden egenskab, der mangler.

Løsninger: 
- Angiv egenskaben mangler.
- Fjern den egenskab, der skal egenskaben mangler.


## <a name="next-steps"></a>Næste trin
Få mere at vide om DTK og virtuelt skala angiver i [Ved hjælp af Virtual Machine skala angiver med filtypenavnet Azure DTK](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Finde flere oplysninger under [Administration af DTKS sikker legitimationsoplysninger](virtual-machines-windows-extensions-dsc-credentials.md). 

Se [Introduktion til Azure beskedteksten tilstand konfiguration lokalnummer handler](virtual-machines-windows-extensions-dsc-overview.md)kan finde flere oplysninger om Azure DTK lokalnummer handler. 

Du kan finde flere oplysninger om PowerShell DTK, [ved at besøge PowerShell dokumentation center](https://msdn.microsoft.com/powershell/dsc/overview). 
