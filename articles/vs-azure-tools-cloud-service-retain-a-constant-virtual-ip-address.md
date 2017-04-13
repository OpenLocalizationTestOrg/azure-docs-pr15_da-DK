<properties
   pageTitle="Hvordan til at bevare en konstant virtuelle IP-adresse til en skybaseret tjeneste | Microsoft Azure"
   description="Lær at sikre, at den virtuelle IP-adresse (VIP) på din Azure skybaseret tjeneste ikke ændre."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Hvordan til at bevare en konstant virtuelle IP-adresse til en skybaseret tjeneste

Når du opdaterer en skybaseret tjeneste, der er placeret i Azure, skal du sikre dig, at den virtuelle IP-adresse (VIP) til tjenesten ikke ændre. Mange management-domænetjenester Brug Domain Name System (DNS) til registrering af domænenavne. DNS fungerer kun, hvis VIP forbliver uændret. Du kan bruge **Guiden Udgivelse** i Azure værktøjer til at sikre, at ikke ændre VIP af skybaseret tjeneste, når du opdaterer den. Se [konfigurere et brugerdefineret domænenavn til en Azure skybaseret tjeneste](./cloud-services/cloud-services-custom-domain-name.md), kan finde flere oplysninger om hvordan du bruger DNS domæneadministration til skytjenester.

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicere en skybaseret tjeneste uden at ændre dets VIP

VIP af en skybaseret tjeneste er allokeret, når du først installere det på Azure i et bestemt miljø, som produktionsmiljøet. VIP ændrer ikke, medmindre du sletter installationen eksplicit eller den slettes implicit af opdateringsprocessen installation. Hvis du vil bevare VIP, skal du ikke slette din installation, og du skal også sørge for, at Visual Studio ikke slette din installation automatisk. Du kan styre funktionsmåden ved at angive indstillinger for indholdsinstallation i **Guiden Udgivelse**, som understøtter flere installationsindstillinger. Du kan angive et nyt installation eller en installation i opdatering, som kan være stigende eller samtidig, og begge typer opdatering installationer beholde VIP. Du kan finde definitioner af disse forskellige typer installation, [Publicere Azure-guiden](vs-azure-tools-publish-azure-application-wizard.md).  Desuden kan du kontrollere, om den tidligere installation af en skybaseret tjeneste slettes, hvis der opstår fejl. VIP blive uventet ændret, hvis du ikke angiver indstillingen korrekt.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Opdatere en skybaseret tjeneste uden at ændre dets VIP

1. Når du installerer skybaseret tjeneste mindst én gang, Åbn genvejsmenuen for noden for projektet Azure, og vælg derefter **Publicer**. Guiden **Publicere Azure program** vises.

1. Vælg det, som du vil installere på listen over abonnementer, og vælg derefter knappen **Næste** . Siden **Indstillinger** i guiden vises.

1. Kontroller, at navnet på skytjenesten, du anvender, **miljø**, **Opbygge konfiguration**og **Tjenestekonfiguration** er alle korrekte under fanen **Almindelige indstillinger** .

1. Kontrollér, lagerplads kontoen og etiketten installation er korrekt, afkrydsningsfeltet **Slet installation ved fejl** ikke er markeret, og, at afkrydsningsfeltet **installation** Opdater er markeret på fanen **Avancerede indstillinger** . Ved at markere afkrydsningsfeltet **installation** opdatering, sikre du, at installationen ikke blive slettet og din VIP ikke tabt, når du publicere programmet igen. Ved at fjerne den **slette installationen mislykkedes afkrydsningsfeltet**, du sikre dig, at din VIP ikke gå tabt, hvis der opstår fejl under installation.

1. Vælg linket **Indstillinger** ud for feltet **Installation opdatere** yderligere specificere, hvordan du vil rollerne, der skal opdateres, og vælg derefter den trinvise opdateringer eller samtidig opdateringsindstilling i dialogboksen **Installation opdatere** indstillinger for. Hvis du vælger trinvis opdatering, hver forekomst opdateres efter hinanden, så programmet altid er tilgængelig. Hvis du vælger samtidig opdatering, opdateres alle forekomster på samme tid. Opdatering af samtidig er hurtigere, men din tjeneste muligvis ikke være tilgængelige under opdateringsprocessen.

1. Når du er tilfreds med indstillingerne, skal du vælge knappen **Næste** .

1. Bekræfte dine indstillinger på siden **Oversigt** i guiden, og vælg derefter knappen **Publicer** .

  >[AZURE.WARNING] Hvis installationen mislykkes, skal du løse hvorfor det mislykkedes og geninstaller straks, hvis du vil undgå at forlade din skybaseret tjeneste i en beskadiget tilstand.

## <a name="next-steps"></a>Næste trin

Se [publicere Azure-guiden](vs-azure-tools-publish-azure-application-wizard.md)for at vide om publicering Azure fra Visual Studio.
