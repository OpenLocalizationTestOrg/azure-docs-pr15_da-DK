<properties
    pageTitle="Brug af flere input filer og komponentegenskaber med Premium Encoder | Microsoft Azure"
    description="Dette emne forklares, hvordan du bruger setRuntimeProperties til at bruge flere input filer og overføre brugerdefinerede data til Media Encoder Premium arbejdsproces medier processor."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Brug af flere input filer og komponentegenskaber med Premium Encoder

## <a name="overview"></a>Oversigt

Der findes scenarier, hvor du muligvis skal til at tilpasse komponentegenskaber, angive klip listen XML-indhold, eller sende flere input filer, når du sender en opgave med **Media Encoder Premium arbejdsproces** medier processor. Nogle eksempler er:

- Overlejring tekst på video og indstille tekstværdi (for eksempel dags dato) på kørselstidspunktet for hver input video.
- Tilpasning af klip listen XML (Hvis du vil angive en eller flere kildefiler, med eller uden justering af osv.).
- Overlejring et logo på video input, mens kodes videoen.

Hvis du vil lade **Media Encoder Premium arbejdsproces** ved, at du vil ændre nogle egenskaber i arbejdsprocessen, når du opretter opgaven eller sende flere input filer, du skal bruge en konfiguration streng indeholder, der **setRuntimeProperties** og/eller **transcodeSource**. Dette emnet forklares, hvordan du bruger dem.


## <a name="configuration-string-syntax"></a>Syntaksen for konfiguration streng

Konfiguration af strengen skal angives i kodning opgaven bruger et XML-dokument, der ser sådan ud:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Følgende er den C#-kode, der læser XML-konfigurationen fra en fil og sender den til opgaven i en sag:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Egenskaber for tilpasning  

### <a name="property-with-a-simple-value"></a>Egenskaben med en enkelt værdi
I nogle tilfælde er det nyttigt at tilpasse en komponent egenskab sammen med filen arbejdsproces, der skal udføres af Media Encoder Premium arbejdsproces.

Antag, at du udformer en arbejdsproces overlejrer teksten på dine videoer, og teksten (for eksempel dags dato) er meningen skal angives på kørselstidspunktet. Du kan gøre dette ved at sende teksten, der angives som den nye værdi for tekstegenskaben for komponenten overlejring fra kodning opgaven. Du kan bruge denne funktion til at ændre andre egenskaber for en komponent i arbejdsproces (som placering eller farven på overlejringen, bithastighed AVC encoder osv.).

**setRuntimeProperties** bruges til at tilsidesætte en egenskab i komponenterne i arbejdsprocessen.

Eksempel:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Egenskaben med en XML-værdi

Hvis du vil angive en egenskab, der forventer en XML-værdi, omfatter ved hjælp af `<![CDATA[ and ]]>`.

Eksempel:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Sørg for ikke at sætte en transport returnerede lige efter `<![CDATA[`.


### <a name="propertypath-value"></a>propertyPath værdi

I de tidligere eksempler på propertyPath blev "/ mediefiler Input/filnavn" eller "/ inactiveTimeout" eller "clipListXml".
Dette er generelt, navnet på komponenten, og klik derefter på navnet på egenskaben. Stien kan indeholde flere eller færre niveauer, vil "/ primarySourceFile" (fordi egenskaben er i roden af arbejdsprocessen) eller "/ Video behandling/grafik-overlejring/ugennemsigtig" (fordi overlejringen er i en gruppe).    

For at kontrollere sti og egenskab, skal du bruge handlingsknappen, der er umiddelbart ud for hver egenskab. Du kan klikke på denne handlingsknap og vælge **Rediger**. Her kan du se det faktiske navn for egenskaben, og umiddelbart oven over det, navneområdet.

![Handling/Edit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Egenskaben](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Flere input filer

De enkelte opgaver, der sendes til **Media Encoder Premium arbejdsproces** kræver to Aktiver:

- Den første opgave er en *Aktiv arbejdsproces* , der indeholder en arbejdsproces-fil. Du kan designe arbejdsproces filer ved hjælp af [Workflow Designer](media-services-workflow-designer.md).
- Den anden opgave er et *Medier aktiv* , der indeholder de media-filer, du vil kode.

Når du sender flere mediefiler til **Media Encoder Premium arbejdsproces** encoder, gælder følgende begrænsninger:

- Alle mediefilerne skal være i samme *Medier aktiv*. Brug af flere medieaktiver understøttes ikke.
- Du skal angive den primære fil i denne medier aktiv (ideelt set dette er den primære videofil, encoder er bedt om at behandle).
- Det er nødvendigt at overføre konfigurationsdata, der indeholder **setRuntimeProperties** og/eller **transcodeSource** elementet til processoren.
  - **setRuntimeProperties** bruges til at tilsidesætte egenskaben filename eller en anden egenskab i komponenterne i arbejdsprocessen.
  - **transcodeSource** bruges til at angive klip listen XML-indholdet.

Forbindelser i arbejdsprocessen:

 - Hvis du bruger en eller flere medier fil Input komponenter og planlægge for at bruge **setRuntimeProperties** til at angive navnet på den fil, derefter Tilslut ikke den primære fil komponent pinkode til dem. Sørg for, at der er ingen forbindelse mellem den primære filobjekt og medier fil Input(s).
 - Hvis du foretrækker at bruge XML-klip liste og én Media kildekomponent, kan derefter du forbinde begge sammen.

![Ingen forbindelse mellem primær kildefilen og medier fil Input](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Der er ingen forbindelse fra den primære fil til medier fil Input komponenter, hvis du bruger setRuntimeProperties til at angive egenskaben filename.*

![Forbindelsen fra Clip listen XML-skærmklip kildelisten](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Du kan oprette forbindelse klip listen XML til mediekilde og bruge transcodeSource.*


### <a name="clip-list-xml-customization"></a>Clipart-XML-listen tilpasning
Du kan angive klip listen XML i arbejdsprocessen på kørselstidspunktet ved hjælp af **transcodeSource** i strengen konfiguration XML. Dette kræver klip listen XML pinkoden for at være forbundet med komponenten mediekilde i arbejdsprocessen.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Hvis du vil angive /primarySourceFile for at bruge denne egenskab til at navngive outputfiler ved hjælp af 'Udtryk', derefter anbefaler vi der passerer klip listen XML som en egenskab *Når* egenskaben /primarySourceFile for at undgå at listen med klip tilsidesættes af indstillingen /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Med flere ramme nøjagtige justering af:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Eksempel

Overvej et eksempel, hvor du vil overlappende et logo på video input, mens kodes videoen. I dette eksempel input videoen hedder "MyInputVideo.mp4" og logoet har navnet "MyLogo.png". Du skal udføre følgende trin:

- Oprette en arbejdsproces aktiv med filen arbejdsproces (se eksemplet nedenfor).
- Oprette et medier aktiv, som indeholder to filer: MyInputVideo.mp4 som den primære fil og MyLogo.png.
- Sende en opgave til Media Encoder Premium arbejdsproces medier processor med de ovenstående input aktiver, og Angiv følgende konfiguration streng.

Konfiguration af:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


Navnet på videofilen der sendes til komponenten medier fil Input og egenskaben primarySourceFile i det foregående eksempel. Navnet på logofilen sendes til en anden medier fil Input, der er forbundet til komponenten grafisk overlejring.

>[AZURE.NOTE]Navnet på video fil sendes til egenskaben primarySourceFile. Årsagen til dette er at bruge denne egenskab i arbejdsprocessen opbygning af navnet på korrekte outputfilen ved hjælp af udtryk, f.eks.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Oprette en trinvis arbejdsprocesser, der ligger oven et logo oven på videoen     

Her er en vejledning til at oprette en arbejdsproces, der tager to filer som input: en video og et billede. Det vil markeringsoverlejringer billede oven på videoen.

Åbn **Workflow Designer** , og vælg **filen** > **Nyt arbejdsområde** > **Kode et grundrids**.

Den nye arbejdsproces viser tre elementer:

- Primære kildefil
- Clipart-listen XML
- Output fil/aktiv  

![Ny kodning arbejdsproces](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Ny kodning arbejdsproces*


For at acceptere input mediefilen, skal du starte med at tilføje en medier fil Input komponent. Søge efter den i feltet lager Søg for at tilføje en komponent til arbejdsprocessen, og træk det ønskede element til ruden Forespørgselsdesigner.

Dernæst skal tilføje videofilen skal bruges til at designe arbejdsprocessen. Klik på ruden baggrund i Workflow Designer for at gøre det, og se efter den primære kildefilen egenskab i egenskabsruden højre. Klik på mappeikonet, og vælg den relevante videofil.

![Primære kildefil](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primære kildefil*


Angiv derefter videofilen i komponenten medier fil Input.   

![Mediefiler inputkilde](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Mediefiler inputkilde*


Så snart dette er gjort, vil komponenten medier fil Input Undersøg filen og udfylde dens output ben afspejler den fil, der kontrolleres.

Næste trin er at tilføje et "Video Data Type Updater" for at angive den farve plads til Rec.709. Tilføje et "Video Format konverteringsprogram", der er angivet til Data Layout/layouttype = konfigurerbar plan. Video strømmen konverteres til et format, der kan udføres som en kilde til komponenten overlejring.

![video Data Type Updater og Formatér konverteringsprogram](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video Data Type Updater og Formatér konverteringsprogram*

![Layouttype = konfigurerbar plan](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Layouttype er konfigurerbar plan*

Derefter tilføje en Video overlappende komponent og oprette forbindelse (dekomprimerede) video pinkoden til (dekomprimerede) video pinkoden af medier fil input.

Tilføje en anden Media-fil-Input (for at indlæse logofilen), klik på denne komponent og Omdøb den til "Medier fil Input Logo", og vælg et billede (en .png-fil for eksempel) i filegenskaben. Oprette forbindelse dekomprimerede billede pinkoden til dekomprimerede billede pinkoden af masken.

![Overlappende komponent og billede kildefil](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Overlappende komponent og billede kildefil*


Hvis du vil ændre placeringen af logoet på videoen (for eksempel du muligvis vil placere det i 10 procent fra den øverste venstre hjørne af videoen), fjerne markeringen i afkrydsningsfeltet "Manuel Input". Du kan gøre dette, fordi du bruger en Media fil Input til at levere logofil til komponenten overlejring.

![Overlappende placering](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Overlappende placering*


Hvis du vil kode video strømmen til H.264, at føje AVC Video Encoder og AAC encoder komponenterne til Forespørgselsdesigner overfladen. Oprette forbindelse ben.
Konfigurere AAC encoder og vælge lyd Format konvertering/forudindstillet: 2.0 (L, R).

![Lyd- og kodere](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Lyd- og kodere*


Nu Føj **ISO Mpeg-4 mulitplekser** og **Fil Output** komponenter og forbinde benene som vist.

![MP4 mulitplekser og fil output](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 mulitplekser og fil output*


Du skal angive navnet på outputfilen. Klik på **Fil Output** -komponenten og redigere udtrykket for filen:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Output filnavn](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Output filnavn*

Du kan køre arbejdsprocessen lokalt for at kontrollere, at den kører korrekt.

Når den er færdig, kan du køre den i Azure Media Services.

Først skal forberede et aktiv i Azure Media Services med to filer i det: videofilen og logoet. Du kan gøre dette ved hjælp af .NET eller REST-API. Du kan også gøre dette ved hjælp af Azure portal eller [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Dette selvstudium viser, hvordan du administrere Aktiver med AMSE. Der er to måder at føje filer til et aktiv:

- Oprette en lokal mappe, kopiere de to filer i det, og træk og slip mappen til fanen **aktiv** .
- Upload videofilen som et aktiv, få vist oplysningerne om aktivet, gå til fanen filer, og Overfør en yderligere fil (logo).

>[AZURE.NOTE]Sørg for at angive en primær fil i aktiv (den primære videofil).

![Aktiver filer i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Aktiver filer i AMSE*


Vælg aktivet, og vælg til at kode med Premium Encoder. Overfør arbejdsprocessen og vælge den.

Klik på knappen for at overføre data til processoren, og Tilføj følgende XML-filen for at angive egenskaberne runtime:

![Premium Encoder i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium Encoder i AMSE*


Indsæt følgende XML-data. Du skal angive navnet på videofilen for både Media fil Input og primarySourceFile. Angiv navnet på navnet på filen med logoet for.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Hvis du bruger .NET SDK til at oprette og køre opgaven, skal denne XML-data overføres som strengen konfiguration.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Når jobbet er fuldført, vises filen MP4 i output aktivet masken!

![Overlappende på videoen](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Overlappende på videoen*


Du kan hente eksempelarbejdsprocessen fra [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Se også

- [Introduktion til Premium-kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Sådan bruges Premium kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Kodning efter behov indhold via Azure Media Services](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Media Encoder Premium arbejdsproces formater og codecs](media-services-premium-workflow-encoder-formats.md)

- [Arbejdsproces eksempelfiler](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Værktøjet til Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
