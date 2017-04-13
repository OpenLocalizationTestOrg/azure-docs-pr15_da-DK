<properties
    pageTitle="Sådan beskæres video | Microsoft Azure"
    description="I denne artikel viser, hvordan du beskære videoer med Media Encoder Standard."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Beskær videoer med Media Encoder Standard

Du kan bruge Media Encoder Standard (MAER) til at beskære inputområdet video. Beskæring er processen med at vælge et rektangulært vindue i videorammen og kodning lige pixel i vinduet. I det følgende diagram hjælper med at illustrere processen.

![Beskær en video](./media/media-services-crop-video/media-services-crop-video01.png)

Antag, at du har som input en video, der har en opløsning på 1920 x 1080 pixel (16:9 højde-breddeforhold), men har sorte kanter (søjle bokse) på venstre og højre, så kun et 4:3-vindue eller 1440 x 1080 pixel indeholder aktiv video. Du kan bruge MAER til at redigere ud af de sorte linjer og kode 1440 x 1080 område.

En forbehandling fase, er beskæring i MES, så beskæring parametrene i kodning forudindstillet gælder for den oprindelige input video. Kodning er en efterfølgende fase og indstillingerne for bredde/højde anvendes til på *forhånd behandles* video og ikke til den oprindelige video. Når du designer dit forudindstillet skal du gøre følgende: (a) Vælg Beskær parametrene baseret på den oprindelige input video, og (b) Vælg din kode indstillinger baseret på det beskårne video. Hvis du ikke matcher din kode indstillinger til beskårne videoen, output bliver ikke som forventet.

I [følgende](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) emne viser, hvordan du opretter et kodning job med MES og at angive en brugerdefineret forudindstillede for kodning opgaven. 

## <a name="creating-a-custom-preset"></a>Oprette et brugerdefineret format

I eksemplet i diagrammet:

1. Oprindelige input er 1920 x 1080
1. Det skal være beskåret til output fra 1440 x 1080, som er centreret i input rammen
1. Det betyder, at en X forskydning på (1920 – 1.440) / 2 = 240 og et Y forskyder nul
1. Bredden og højden på det beskårne rektangel er 1440 og 1080, henholdsvis
1. I fasen kodes Spørg er at producere tre lag, er løsninger 1440 x 1080 960 x 720 og 480 x 360, henholdsvis

###<a name="json-preset"></a>JSON forudindstillet


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


##<a name="restrictions-on-cropping"></a>Begrænsninger for beskæring

Funktionen beskæring er beregnet til at være manuelt. Du har brug at indlæse din Skriv video i et passende redigering værktøj, hvor du kan markere billeder af interesse, placere markøren for at afgøre forskydning til beskæring rektanglet, for at bestemme den kodning forudindstillet, der er tilpasset til den bestemt videoer osv. Denne funktion er ikke meningen, at aktivere ting som: automatisk registrering og fjernelse af sort letterbox/pillarbox kanter i inputområdet video.

Følgende begrænsninger gælder for funktionen beskæring. Hvis dette ikke er opfyldt, kan kodes opgave mislykkes eller medføre uventede output.

1. Koordinater og størrelsen på det beskårne rektangel har til at passe ind i en video
1. Som nævnt ovenfor, skal bredde og højde i indstillingerne for kodes svarer til det beskårne video
1. Beskæring gælder for videoer, registreres i liggende retning (det vil sige gælder ikke for videoer, der er registreret med en smartphone holdes lodret eller i stående tilstand)
1. Fungerer bedst med gradvist video hentes med firkantede pixel

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Næste trin
 
Se Azure Media Services læringsstier kan hjælpe dig med at få mere at vide om fantastiske funktioner, som AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
