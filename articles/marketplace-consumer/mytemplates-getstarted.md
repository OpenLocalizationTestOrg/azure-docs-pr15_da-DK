<properties
   pageTitle="Introduktion til private skabeloner | Microsoft Azure"
   description="Tilføje, administrere og dele dine private skabeloner ved hjælp af portalen Azure, Azure CLI eller PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introduktion til private skabeloner på Azure-portalen

En skabelon til [Azure ressourcestyring](../resource-group-authoring-templates.md) er en deklarativ skabelon, der bruges til at definere din installation. Du kan definere ressourcerne, der skal installeres til en løsning, og Angiv parametre og variabler, der gør det muligt at inputværdier til forskellige miljøer. Skabelonen består af JSON og udtryk, som du kan bruge til at oprette værdier til din installation.

Du kan bruge den nye funktion i **skabeloner** i [Azure Portal](https://portal.azure.com) sammen med provideren **Microsoft.Gallery** ressource som en udvidelse af [Azure Marketplace](https://azure.microsoft.com/marketplace/) aktivere brugere til at oprette, administrere og installere privat skabeloner fra et personligt bibliotek.

Dette dokument fører dig gennem tilføjelse, administrere og dele en privat **skabelon** ved hjælp af portalen Azure.

## <a name="guidance"></a>Vejledning

Følgende forslag til at kunne udnytte **skabeloner** fuldt, når du arbejder med dine løsninger:

- En **skabelon** er en encapsulating ressource, der indeholder en ressourcestyring skabelon og ekstra metadata. Det fungerer på samme måde meget et element på markedet. Vigtige forskellen er, at det er et privat element i modsætning til de offentlige Marketplace elementer.
- Biblioteket **skabeloner** fungerer godt til brugere, der har brug for at tilpasse deres installationer.
- **Skabeloner** fungerer godt til brugere, der har brug for et enkelt lager i Azure.
- Start med en eksisterende ressourcestyring skabelon. Finde skabeloner i [github](https://github.com/Azure/azure-quickstart-templates) eller [eksportere skabelon](../resource-manager-export-template.md) fra en eksisterende ressourcegruppe.
- **Skabeloner** er knyttet til den bruger, der publicerer dem. Publisher-navn er synligt for alle, som har læseadgang til den.
- **Skabeloner** er ressourcestyring ressourcer og kan ikke omdøbes publiceret én gang.

## <a name="add-a-template-resource"></a>Føje en skabelonressource

Der er to måder at oprette en **skabelon** ressource i portalen Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Metode 1: Oprette en ny skabelonressource fra en igangværende ressourcegruppe

1. Gå til en eksisterende ressourcegruppe på Azure-portalen. Vælg **Eksporter skabelon** i **Indstillinger**.
2. Når skabelonen ressourceleder, der er eksporteret, kan du bruge knappen **Gem skabelon** til at gemme den til **skabeloner** -lager. Finde fuldført oplysninger om Eksportér skabelon [her](../resource-manager-export-template.md).
<br /><br />
![Ressource gruppen Eksportér](media/rg-export-portal1.PNG)  <br />

3. Vælg kommandoknappen **gemme skabelonen** .
<br /><br />

4. Angiv følgende oplysninger:

    - Navn – navnet på skabelonobjektet (Bemærk: Dette er navnet på en Azure ressourcestyring baseret. Alle naming begrænsninger og den kan ikke ændres, når oprettet).
    - Beskrivelse – hurtig oversigt over skabelonen.

    ![Gem skabelon](media/save-template-portal1.PNG)  <br />

5. Klik på **Gem**.

    > [AZURE.NOTE] Eksportér skabelon blade viser beskeder, når skabelonen eksporterede ressourceleder, der indeholder fejl, men du vil stadig kunne gemme denne ressourcestyring skabelon og bruge en skabelon. Sørg for, at du kontrollere og rette en hvilken som helst ressourcestyring skabelon problemer, før geninstallation skabelonen eksporterede ressourcestyring.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Metode 2: Tilføje en ny skabelonressource fra Gennemse

Du kan også tilføje en ny **skabelon** fra bunden ved hjælp af den + Tilføj kommandoknap i **Gennemse > skabeloner**. Du skal angive et navn, beskrivelse og skabelonen ressourcestyring JSON.

![Tilføje skabelon](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery er en lejer baseret Azure ressource udbyder. Skabelon ressourcen er knyttet til den bruger, der har oprettet den. Det er ikke er bundet til et bestemt abonnement. Et abonnement, skal være valgt kun, når du installerer en skabelon.

## <a name="view-template-resources"></a>Vis skabelonressourcer

Alle **skabeloner** , der er tilgængelige for dig kan ses på **Gennemse > skabeloner**. Dette omfatter **skabeloner** , du har oprettet samt dem, der er delt med dig med forskellige niveauer af tilladelser. Flere oplysninger i afsnittet [adgangskontrol](#access-control-for-a-tenant-resource-provider) nedenfor.

![Visningsskabelon](media/view-template-portal1.PNG)  <br />

Du kan få vist detaljerne for en **skabelon** ved at klikke på i et element på listen.

![Visningsskabelon](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Redigere en skabelonressource

Ved at højreklikke på elementet på listen gennemse eller ved at vælge knappen Rediger kommandoen, kan du starte Rediger strømmen efter en **skabelon** .

![Rediger skabelon](media/edit-template-portal1a.PNG)  <br />

Du kan redigere beskrivelsen eller Ressourcestyring skabelontekst. Du kan ikke redigere navnet, da det er en ressourcestyring ressourcenavn. Når du redigerer skabelonen ressourcestyring JSON vi vil validere for at sikre, at det er gyldig JSON. Vælg **OK** og derefter **gemme** gemme opdaterede skabelonen.

![Rediger skabelon](media/edit-template-portal2a.PNG)  <br />

Du får vist en meddelelse om bekræftelse, når **skabelonen** er gemt.

![Rediger skabelon](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Installere en skabelonressource

Du kan installere en **skabelon** , du har **læsetilladelser** til. Installation strømmen starter bladet til installation af standard Azure skabelon. Udfylde værdier for parametrene ressourcestyring skabelon til at fortsætte med installationen.

![Installere skabelon](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Dele en skabelonressource

En **skabelon** ressource kan deles med andre. Deling fungerer på samme måde [rolletildeling for en ressource på Azure](../active-directory/role-based-access-control-configure.md). Ejeren af **skabelonen** indeholder tilladelser til andre brugere, som kan interagere med en skabelonressource. Den person eller gruppe af personer, du deler **skabelon** med vil kunne se skabelonen ressourcestyring og dens egenskaber i galleriet.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Adgangskontrol for ressourcerne, Microsoft.Gallery

Rolle | Tilladelser
---|----
Ejer | Gør det muligt for fuld kontrol på den skabelonressource, herunder del
Reader | Tillader læse- og Execute(Deploy) på skabelon ressourcen
Bidragyder | Giver tilladelse til at Rediger og Slet på skabelon ressourcen. Brugeren kan ikke dele skabelonen med andre

Vælg **del** for Gennemse elementet, ved at højreklikke eller på bladet visning for et bestemt element. Dette åbner en del oplevelse.

![Del skabelon](media/share-template-portal1a.png)  <br />

 Nu kan du vælge en rolle og en bruger eller gruppe til at give adgang til en bestemt **skabelon**. Tilgængelige roller er ejer, læser og bidragyder. Flere oplysninger i afsnittet [adgangskontrol](#access-control-for-a-tenant-resource-provider) ovenfor.

![Del skabelon](media/share-template-portal2b.png)  <br />

![Del skabelon](media/share-template-portal3b.png)  <br />

Klik på **Vælg** og **Ok**. Du kan nu se de brugere eller grupper, du har føjet til ressourcen.

![Del skabelon](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] En skabelon kan kun blive delt med brugere og grupper i samme lejer Azure Active Directory. Hvis du vil dele en skabelon med en e-mailadresse, der ikke er i din lejer, sendes en invitation beder brugeren om at tilslutte sig lejer som gæst.

## <a name="next-steps"></a>Næste trin

- For at få mere for at vide om oprettelse af Ressourcestyring skabeloner, se [redigering skabeloner](../resource-group-authoring-templates.md)
- Du kan finde de funktioner, du kan bruge i en skabelon til Ressourcestyring, under [skabelon funktioner](../resource-group-template-functions.md)
- Vejledning i at designe dine skabeloner, se [bedste fremgangsmåder til design af Azure ressourcestyring skabeloner](../best-practices-resource-manager-design-templates.md)
