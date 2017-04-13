<properties
   pageTitle="Fejlfinding i forbindelse med roller, der ikke starte | Microsoft Azure"
   description="Her er nogle almindelige årsager til, hvorfor en skybaseret tjeneste rolle kan ikke at starte. Løsninger på disse problemer er også angivet."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Fejlfinding i forbindelse med skybaseret tjeneste roller, der ikke startes

Her er nogle almindelige problemer og løsninger, der er relateret til Azure Cloud Services roller, der ikke startes.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Manglende DLL-filer eller afhængigheder

Svarer roller og roller, der skifte mellem **initialiserer**, **optaget**og **stoppe** stater kan skyldes manglende DLL-filer eller enheder.

Symptomer på mangler DLL-filer eller assemblies kan være:

- Din rolle forekomst skifte mellem **initialiserer**, **optaget**og **stoppe** tilstand.
- Din rolle forekomst er flyttet til **klar** , men hvis du navigerer til dit webprogram, vises siden ikke.

Der findes flere anbefalede metoder til at undersøge disse problemer.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticere manglende DLL-problemer i en web rolle

Når du navigerer til et websted, der er installeret på et websted rolle, og browseren viser en stil med følgende fejl, det kan betyde, at en DLL-fil mangler.

![Serverfejl i '/' Application.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticere problemer ved at deaktivere brugerdefinerede fejl

Mere komplet fejloplysninger kan ses ved at konfigurere web.config for rollen web til at angive brugerdefinerede fejl tilstand til slået fra og geninstallation tjenesten.

Have vist fejl for mere komplet uden brug af Fjernskrivebord:

1. Åbn løsningen i Microsoft Visual Studio.

2. I **Solution Explorer**, Find filen web.config og åbne den.

3. Find sektionen sektionsgruppen og tilføje følgende linje i filen web.config:

    ```xml
    <customErrors mode="Off" />
    ```

4. Gem filen.

5. Pakke og geninstaller tjenesten.

Når tjenesten er genaktiveres, får du vist en fejlmeddelelse med navnet på den manglende samling eller DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticere problemer ved at få vist fejlen over telefonen?

Du kan bruge Fjernskrivebord til at få adgang til rollen, og se mere komplet fejloplysninger fra en fjernplacering. Brug følgende trin til at få vist fejl ved hjælp af Fjernskrivebord:

1. Sørg for, at Azure SDK 1,3 eller nyere er installeret.

2. Under installationen af løsningen ved hjælp af Visual Studio, du Vælg at "Konfigurer forbindelser til Fjernskrivebord...". Du kan finde flere oplysninger om konfiguration af forbindelsen til Fjernskrivebord, [Ved hjælp af Remote Desktop med Azure roller](../vs-azure-tools-remote-desktop-roles.md).

3. I portalen Microsoft Azure klassisk, når forekomsten viser en status for **klar**, klik på en rolle forekomster.

4. Klik på ikonet **Opret forbindelse** i området **Fjernadgang** på båndet.

5. Log på den virtuelle maskine ved hjælp af de legitimationsoplysninger, der er angivet under konfigurationen af Fjernskrivebord.

6. Åbne et kommandovindue.

7. Skriv `IPconfig`.

8. Bemærk værdien IPv4-adresse.

9. Åbn Internet Explorer.

10. Skriv adressen og navnet på webprogrammet. For eksempel `http://<IPV4 Address>/default.aspx`.

Navigere til webstedet, returnerer mere detaljeret fejlmeddelelser nu:

* Serverfejl i '/' Application.

* Beskrivelse: Der opstod en ikke-afviklet undtagelse under udførelse af den aktuelle webanmodning. Gennemgå staksporingen kan finde flere oplysninger om fejlen, og hvor den stammer fra kode.

* Detaljer om undtagelse: System.IO.FIleNotFoundException: kan ikke indlæses fil eller enhed ' Microsoft.WindowsAzure.StorageClient, Version = 1.1.0.0, kultur = neutrale, PublicKeyToken = 31bf856ad364e35' eller én af dens afhængigheder. Systemet kan ikke finde den angivne fil.

Eksempel:

![Eksplicitte serverfejl i '/' Application](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticere problemer ved hjælp af Beregn emulatoren

Du kan bruge Microsoft Azure Beregn emulatoren til diagnosticering og fejlfinding af manglende afhængigheder og web.config fejl.

De bedste resultater på ved hjælp af denne metode til diagnosticering, skal du bruge en computer eller virtuel maskine, der har en ren installation af Windows. For at simulere bedst Azure-miljø, skal du bruge Windows Server 2008 R2 x64.

1. Installere den enkeltstående version af [Azure SDK](https://azure.microsoft.com/downloads/).

2. Opbygge skyen service projektet på maskinen udvikling.

3. I Windows Stifinder, gå til mappen bin\debug i skyen service projektet.

4. Kopier filen .csx mappe og .cscfg til den computer, du bruger til at foretage fejlfinding på problemer.

5. På den ren maskine, Åbn en Azure SDK kommandopromptvindue, og skriv `csrun.exe /devstore:start`.

6. Ved kommandoprompten skal du indtaste `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Når rollen starter, vil du se detaljerede fejloplysninger i Internet Explorer. Du kan også bruge standard Windows fejlfinding i forbindelse med værktøjer til at undersøge problemet nærmere.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticere problemer ved hjælp af IntelliTrace

For arbejder og web roller, der bruger .NET Framework 4, kan du bruge [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), som er tilgængelig i [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Følg disse trin for at installere tjenesten med IntelliTrace aktiveret:

1. Bekræft, at Azure SDK 1,3 eller nyere er installeret.

2. Installere løsningen ved hjælp af Visual Studio. Markér afkrydsningsfeltet **Aktivér IntelliTrace for .NET 4 roller** under installationen.

3. Når forekomsten starter, skal du åbne **Server Explorer**.

4. Udvid den **Azure\\Skytjenester** node og Find installationen.

5. Udvid installationen, indtil du ser en rolle forekomster. Højreklik på en af forekomsterne.

6. Vælg **Vis IntelliTrace logfiler**. **Oversigt over IntelliTrace** åbnes.

7. Find afsnittet undtagelser i oversigten. Hvis der er undtagelser, mærket sektionen **Undtagelsesdata**.

8. Udvid **Undtagelsesdata** og se efter **System.IO.FileNotFoundException** fejl som følger:

![Undtagelsesdata, manglende fil eller enhed](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adresse manglende DLL-filer og enheder

Du kan løse manglende DLL og samling fejl ved at følge disse trin:

1. Åbn løsningen i Visual Studio.

2. I **Solution Explorer**skal du åbne mappen **referencer** .

3. Klik på den samling, der er identificeret i fejl.

4. Find **lokal kopi egenskab** i ruden **Egenskaber** , og værdien til **Sand**.

5. Geninstaller skytjenesten.

Når du har bekræftet, at alle fejl er blevet rettet, kan du installere tjenesten uden at markere afkrydsningsfeltet **Aktivér IntelliTrace for .NET 4 roller** .

## <a name="next-steps"></a>Næste trin

Få vist flere [foretage fejlfinding af artikler](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) til skytjenester.

Hvis du vil lære at foretage fejlfinding af problemer med skyen tjenesten rolle ved hjælp af Azure PaaS computer diagnosticering data, skal du se [Kevin Williamson blogserier](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
