<properties 
    pageTitle="Avanceret kodning med Media Encoder Standard" 
    description="Dette emne viser, hvordan du udfører avanceret kodning ved at tilpasse Media Encoder Standard opgave forudindstillinger. Emnet viser, hvordan du bruger Media Services .NET SDK til at oprette en kodning opgave og job. Det også viser, hvordan du angiver brugerdefinerede forudindstillinger til kodning jobbet." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>Avanceret kodning med Media Encoder Standard

##<a name="overview"></a>Oversigt

Dette emne viser, hvordan du udfører avanceret kodning opgaver med Media Encoder Standard. Emnet viser, [hvordan du bruger .NET for at oprette en opgave i en kodning og et job, som udfører denne opgave](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). Også se, hvordan du kan angive brugerdefinerede forudindstillinger kodning opgaven. Se [dette dokument](https://msdn.microsoft.com/library/mt269962.aspx)for beskrivelse af elementer, der bruges af forudindstillingerne. 

De brugerdefinerede forudindstillinger, der udfører følgende kodning opgaver vises:

- [Generere miniaturer](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Trimme en video (skærmklip)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Oprette en overlejring](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Indsætte en uovervåget lydspor, når input har ingen lyd](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Deaktivere automatisk deaktivere interlace](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Kun med lyd forudindstillinger](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Sammenkæde to eller flere videofiler](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Beskær videoer med Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Indsætte en video Registrer, når input ikke har en video](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [Rotere en video](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>Kodning med Media Services .NET SDK

Følgende kodeeksempel bruger Media Services .NET SDK til at udføre følgende opgaver:

- Oprette et kodning job.
- Få en reference til Media Encoder Standard encoder.
- Indlæse den brugerdefinerede XML- eller JSON forudindstillet. Du kan gemme XML eller JSON (for eksempel [XML](media-services-custom-mes-presets-with-dotnet.md#xml) eller [JSON](media-services-custom-mes-presets-with-dotnet.md#json) i en fil og brug følgende kode at indlæse filen.

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- Føje en kodning opgave til jobbet. 
- Angiv input aktivet kodes.
- Oprette et output aktiv, der indeholder den kodet aktiv.
- Tilføje en hændelseshandler for at kontrollere jobstatus status.
- Sende jobbet.
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
                
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
                
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
                
                    return job.OutputMediaAssets[0];
                }
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
        
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
        
                            // Display or log error details as needed.
                            break;
                        default:
                            break;
                    }
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
            }
        }


##<a name="support-for-relative-sizes"></a>Understøttelse af relative størrelser

Når der genereres miniaturer af det, behøver du ikke altid angive output bredde og højde i pixel. Du kan angive dem i procent i området [1%,..., 100%].

###<a name="json-preset"></a>JSON forudindstillet 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>XML-format
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>Generere miniaturer

Dette afsnit viser, hvordan du tilpasser en forudindstillet, som genererer miniaturer. Forudindstillet defineret nedenfor indeholder oplysninger om, hvordan du vil kode dine filer samt oplysninger, der bruges til at generere miniaturer. Du kan udføre nogle af de MES forudindstillinger dokumenterede [her](https://msdn.microsoft.com/library/mt269960.aspx) og tilføje kode, der genererer miniaturer.  

>[AZURE.NOTE]Indstillingen **SceneChangeDetection** i følgende foruddefinerede kan kun være indstillet til sand, hvis du kodning til en enkelt bithastighed video. Hvis du er kode til en video med flere bithastighed og **SceneChangeDetection** indstillet til sand, returnerer encoder en fejl.  


Du kan finde oplysninger om skema [i dette](https://msdn.microsoft.com/library/mt269962.aspx) emne.

Sørg for at læse afsnittet [overvejelser i forbindelse med](media-services-custom-mes-presets-with-dotnet.md#considerations) .

###<a id="json"></a>JSON forudindstillet


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>XML-format


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>Overvejelser i forbindelse med

Overvej følgende gælder:

- Brug af eksplicitte tidsstempler for Start/trin/område antager, at inputkilden er mindst 1 minut.
- Png-jpg/BmpImage elementer skal starte, trin, og interval strengattributter – disse kan fortolkes som:

    - Ramme tal, hvis de er ikke-negative heltal, for eksempel "Start": "120"
    - Relative til kilde varighed, hvis udtrykt som % efterstilles, for eksempel "Start": "15%", eller
    - Tidsstempel, hvis udtrykt som HH:MM:SS... formatere, for eksempel "Start": "00: 01:00"

    Du kan mix og match notation som du skal du.
    
    Desuden understøtter Start også en speciel makro: {bedste}, der forsøger at finde ud af det første "interessante" billede af indhold BEMÆRKNINGEN: (trin og området ignoreres, når Start er indstillet til {bedste})
    
    - Standardindstillinger: Start: {bedste}
- Outputformat skal angives eksplicit for hvert billede-format: Png-Jpg/BmpFormat. Når der forekommer MES svarer til JpgVideo til JpgFormat og så videre. OutputFormat introducerer en ny bestemt billede-codec-makro: "Index", som skal være præsentere (én gang og kun én gang) for billede outputformater.

##<a id="trim_video"></a>Trimme en video (skærmklip)

Dette afsnit taler om ændring af forudindstillingerne encoder for at klippe eller trimme input videoen hvor input er et papirark mezzanine fil eller en fil efter behov. Encoder kan også bruges til at klippe eller trimme et aktiv, som er hentet eller arkiveres fra en live stream-oplysninger om dette er tilgængelige i [denne blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Hvis du vil trimme dine videoer, kan du udføre nogle af de MES forudindstillinger dokumenterede [her](https://msdn.microsoft.com/library/mt269960.aspx) og redigere elementet **kilder** (som vist nedenfor). Værdien af starttidspunkt skal svare til den absolutte tidsstempler af input videoen. Eksempelvis hvis det første billede af input videoen er et tidsstempel af 12:00:10.000, derefter starttidspunkt skal være mindst 12:00:10.000 og større. I eksemplet herunder antager vi, at input videoen har et første tidsstempel nul. **Kilder** skal placeres i starten af forudindstillingen. 
 
###<a id="json"></a>JSON forudindstillet
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>XML-format
    
Hvis du vil trimme dine videoer, kan du udføre nogle af de MES forudindstillinger dokumenterede [her](https://msdn.microsoft.com/library/mt269960.aspx) og redigere elementet **kilder** (som vist nedenfor).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>Oprette en overlejring

Media Encoder Standard kan du overlejring af et billede til en eksisterende video. I øjeblikket, der understøttes i følgende formater: png, jpg, gif, og bmp. Forudindstillet defineret nedenfor er et grundlæggende eksempel på en video overlejring.

Ud over definerer en forudindstillet fil, har du også at lade Media Services ved hvilken fil i aktivet er overlejring billedet, og hvilken fil er kilden video, som du vil overlejring af billedet. Videofilen skal være den **primære** fil. 

.NET eksemplet ovenfor definerer to funktioner: **UploadMediaFilesFromFolder** og **EncodeWithOverlay**. Funktionen UploadMediaFilesFromFolder overfører filer fra en mappe (for eksempel BigBuckBunny.mp4 og Image001.png) og angiver mp4-fil, der skal være den primære fil i aktivet. Funktionen **EncodeWithOverlay** bruger den brugerdefinerede forudindstillede fil, der blev sendt til den (for eksempel på forudindstillet, der følger efter) til at oprette opgaven kodning. 

>[AZURE.NOTE]Aktuelle begrænsninger:
>
>Indstillingen overlejring ugennemsigtig understøttes ikke.
>
>Video kildefilen og billedfilen overlejring skal være i det samme aktiv, og videofilen skal angives som den primære fil i dette aktiv.

###<a name="json-preset"></a>JSON forudindstillet
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>XML-format
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>Indsætte en uovervåget lydspor, når input har ingen lyd

Som standard, hvis du sender en input til encoder, der indeholder kun video, og ingen lyd står output aktivet filer, der indeholder kun video data. Visse afspillere muligvis ikke kunne håndtere sådanne outputstreams. Du kan bruge denne indstilling til at gennemtvinge encoder til at føje en uovervåget lydspor til output i det pågældende scenarie.

For at tvinge encoder at producere et aktiv, der indeholder en uovervåget lydspor, når input har ingen lyd, skal du angive værdien "InsertSilenceIfNoAudio".

Du kan udføre nogle af de MES forudindstillinger dokumenterede [her](https://msdn.microsoft.com/library/mt269960.aspx), og Foretag følgende ændringer:

###<a name="json-preset"></a>JSON forudindstillet

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>XML-format

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Deaktivere automatisk deaktivere interlace

Kunder behøver ikke at gøre noget, hvis de have interlace indholdet skal være automatisk deaktivere interlaced. Når automatisk deaktivere interlace er slået til (standard) på MES sker automatisk registrering af interlaced rammer og kun deaktivere interlaces rammer, der er markeret som interlaced.

Du kan slå automatisk deaktivere interlace fra. Denne indstilling kan ikke anbefales.

###<a name="json-preset"></a>JSON forudindstillet
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>XML-format
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>Kun med lyd forudindstillinger

Dette afsnit viser to kun med lyd MES forudindstillinger: AAC lyd- og lyd AAC god kvalitet.

###<a name="aac-audio"></a>AAC-lyd 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>AAC god lydkvalitet

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>Sammenkæde to eller flere videofiler

I følgende eksempel vises, hvordan du kan oprette et format for at sammenkæde to eller flere videofiler. De mest almindelige scenarie er, når du vil tilføje et sidehoved eller kærrer til primære videoen. Den tilsigtede brug er, når de videofiler, bliver redigeret sammen dele egenskaber (skærmopløsning, ramme rente, lydspor Tæl osv.). Du bør tager sig ikke for at blande videoer af anden ramme satser eller med forskellige antal lydspor.

###<a name="requirements-and-considerations"></a>Kravene samt de overvejelser

- Indtast videoer bør kun have én lydspor.
- Indtast videoer skal alle have samme rammehastighed.
- Du skal overføre dine videoer til separat aktiver og angive videoerne, som den primære fil i hvert aktiv.
- Du skal kende varigheden af videoerne.
- Forudindstillede eksemplerne nedenfor antages det, at alle input videoer starter med et tidsstempel nul. Du har brug at ændre værdierne starttidspunkt, hvis videoerne har forskellige første tidsstempel, som det er typisk er tilfældet med live Arkiver.
- JSON forudindstillet gør eksplicitte referencer til emneid værdierne i de input aktiver.
- Eksempelkoden antages, er blevet gemt JSON forudindstillet til en lokal fil, som "C:\supportFiles\preset.json". Det antages, at to aktiver, som er oprettet ved at uploade to videofiler, og at du ved de resulterende emneid værdier.
- Kodestykke og JSON forudindstillet viser et eksempel på at sammenkæde to videofiler. Du kan udvide den til mere end to videoer efter:

    1. Opkald opgave. InputAssets.Add() gentagne gange for at tilføje flere videoer i rækkefølge.
    2. Foretage svarer redigerer til "Kilder" elementet i JSON, ved at tilføje flere poster, i samme rækkefølge. 


###<a name="net-code"></a>.NET kode

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>JSON forudindstillet

Opdater din brugerdefinerede forudindstillede med id'erne for de aktiver, du vil sammenkæde, og med segmentet passende tidspunkt for hver video.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="crop"></a>Beskær videoer med Media Encoder Standard

Se emnet [Beskær videoer med Media Encoder Standard](media-services-crop-video.md) .

##<a id="no_video"></a>Indsætte en video Registrer, når input ikke har en video

Som standard, hvis du sender en input til encoder, der indeholder kun lyd, og ingen video står output aktivet filer, der indeholder kun lyd data. Nogle afspillere, herunder Azure Media Player (se [denne](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) muligvis ikke kunne håndtere sådanne streams. Du kan bruge denne indstilling til at gennemtvinge encoder til at føje en sort-hvid video Registrer til output i det pågældende scenarie. 

>[AZURE.NOTE]At tvinge encoder til at indsætte en video Registrer output øges størrelsen på output aktiv, og dermed omkostninger, der er påløbet for kodning opgaven. Du skal køre test for at bekræfte, at denne resulterende forøgelse har en mindre indvirkning på månedlige gebyrer.

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Indsætte video i kun den laveste bithastighed

Antag, at du bruger en flere bithastighed kodning forudindstillede såsom ["H264 flere bithastighed 720p"](https://msdn.microsoft.com/library/mt269960.aspx) til at kode hele input kataloget til streaming, som indeholder en blanding af videofiler og kun med lyd filer. I dette scenarie, når input ikke har en video, kan du tvinge encoder til at indsætte en sort-hvid video registrering på den laveste bithastighed i modsætning til at indsætte video i hver output bithastighed. Hvis du vil arkivere dette, skal du angive flaget "InsertBlackIfNoVideoBottomLayerOnly".

Du kan udføre nogle af de MES forudindstillinger dokumenterede [her](https://msdn.microsoft.com/library/mt269960.aspx), og Foretag følgende ændringer:

#### <a name="json-preset"></a>JSON forudindstillet

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-format

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>Indsætte video overhovedet output bitrates

Antag, at du bruger en flere bithastighed kodning forudindstillede såsom ["H264 flere bithastighed 720p](https://msdn.microsoft.com/library/mt269960.aspx) til at kode hele input kataloget til streaming, som indeholder en blanding af videofiler og kun med lyd filer. I dette scenarie, når input ikke har en video, kan du tvinge encoder til at indsætte en sort-hvid video Registrer overhovedet output bitrates. Dette sikrer, at din output Aktiver er alle ensartet i forhold til antallet af video spor og lydspor. Hvis du vil arkivere dette, skal du angive flaget "InsertBlackIfNoVideo".

Du kan udføre nogle af de MES forudindstillinger dokumenterede [her](https://msdn.microsoft.com/library/mt269960.aspx), og Foretag følgende ændringer:

#### <a name="json-preset"></a>JSON forudindstillet

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-format
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>Rotere en video

[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) understøtter rotationen af vinklerne af 0/90/180/270. Standardfunktionen er "Automatisk", hvor den forsøger at registrere rotation metadataene i indgående videofilen og kompensation for den. Omfatte følgende **kilder** element til en af de forudindstillinger defineret [her](http://msdn.microsoft.com/library/azure/mt269960.aspx):

### <a name="json-preset"></a>JSON forudindstillet

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>XML-format

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Se også [i dette](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation) emne kan finde flere oplysninger under Sådan encoder fortolker indstillingerne bredde og højde i formatet, når rotation kompensation udløses.

Du kan bruge værdien "0" for at vise encoder at ignorere rotation metadata, hvis de findes, i input video. 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også 

[Media Services kodning oversigt](media-services-encode-asset.md)
