<properties 
    pageTitle="Licensering Microsoft® jævn Streaming klient konvertere Kit" 
    description="Få mere at vide om, hvordan til Microsoft® udglattede Streaming klient konvertere Kit-licenser." 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licensering Microsoft® jævn Streaming klient konvertere Kit

##<a name="overview"></a>Oversigt

Microsoft udglattede Streaming klient konvertere Kit (**SSPK** til kort) er en jævn Streaming klient implementering, der er optimeret til at hjælpe producenter af integrerede enheder, kabel og mobiludbydere, indhold tjenesteudbydere, håndsæt producenter, uafhængige softwareleverandører (softwareudviklere) og løsningsudbydere for at oprette produkter og tjenester til streaming tilpasset streaming indhold i udglattede Streaming format. SSPK er en uafhængig enhed og platform implementering af udglattede Streaming klient, der kan overføres ved licens til en hvilken som helst enhed og platform. 

Medtaget under er en højt niveau arkitektur og IIS udglattede Streaming konvertere Kit er jævne Streaming klient implementering leveres af Microsoft og omfatter alle core logik til afspilning af udglattede Streaming indhold. Dette er derefter overføres af partnere til en bestemt enhed eller platform ved at implementere relevante grænseflader. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>Beskrivelse

SSPK er givet i licens på betingelser, der tilbyder fremragende forretningsværdi. SSPK licens indeholder branche med:

- Problemfri Streaming konvertere Kit kilde i C++ 
  - implementerer funktionalitet udglattede Streaming klienter
  - Tilføjer format parsing af heuristik, bufferen logik osv.
- Program til afspilning af API'er 
  - Programming Interface for interaktion med et program til afspilning af medier
- Platform fremstilling lag (ven) Interface 
  - Programming Interface for interaktion med operativsystemet (tråde, sockets)
- Hardware fremstilling lag (HAL) grænseflade 
  - Programming Interface for interaktion med hardware A / V dekodere (kodning, gengivelse)
- Digital Rights Management (DRM) grænseflade 
  - Programming Interface til håndtering af DRM gennem DRM fremstilling lag (DAL)
  - Microsoft PlayReady konvertere Kit leveres særskilt men integrerer gennem denne grænseflade. Du kan finde flere oplysninger om Microsoft PlayReady Device licensering, skal du klikke på [her](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Implementering eksempler 
  - Eksempel på ven implementering for Linux
  - Eksempel på HAL implementering for GStreamer

##<a name="licensing-options"></a>Licensmulighederne

Microsoft udglattede Streaming klient konvertere Kit er gjort tilgængelig for indehavere under to forskellige license-aftaler: et til udvikling af udglattede Streaming klient midlertidige produkter og en anden til at distribuere udglattede Streaming klient endelige produkter til slutbrugere.
 
- En Microsoft udglattede Streaming klient konvertere Kit **Midlertidige produktlicens** skal udføres for chipset producenter, systemintegratorer eller uafhængige softwareleverandører (softwareudviklere) der skal bruge en kildekode konvertere kit for at udvikle midlertidige produkter.
- Microsoft udglattede Streaming klient konvertere Kit **Endelige produktlicens** skal udføres for producenter af enheder eller softwareudviklere, der kræver fordeling rettigheder til jævn Streaming klient endelige produkter til slutbrugere.

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft jævne Streaming klient konvertere Kit midlertidige produktlicens

Under denne licens tilbyder Microsoft en jævn Streaming klient konvertere pakke og de nødvendige intellektuelle ejendomsrettigheder til at udvikle og distribuere udglattede Streaming klient midlertidige produkter til andre udglattede Streaming klient konvertere Kit enhed licenshavere, distribuerer udglattede Streaming klient endelige produkter.

####<a name="fee-structure"></a>Gebyr struktur

Et USA 50.000 enkeltstående licens gebyr giver adgang til jævn Streaming klient konvertere Kit. 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft jævne Streaming klient konvertere Kit færdig produktlicens

Microsoft tilbyder under denne licens alle nødvendige intellektuel ejendomsrettigheder til at modtage udglattede Streaming klient midlertidige produkter fra andre udglattede Streaming klient konvertere Kit licenshavere og til at distribuere virksomhed-mærkede udglattede Streaming klient endelige produkter til slutbrugere.

####<a name="fee-structure"></a>Gebyr struktur

Det bløde Streaming klient endelige produkt tilbydes under en royalty model som under:

- $0.10 per enhed implementering afsendt
- Royalty er maksimalt 50.000 hvert år
- Ingen royalty til første 10.000 enhed installationer hvert år 

##<a name="licensing-procedure-and-sspk-access"></a>Licensering Procedure og SSPK adgang

Skal du sende [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) for alle licenser forespørgsler.

[SSPK fordeling portalen](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) er tilgængelige for registrerede midlertidige indehavere.

Foreløbige og endelige SSPK indehavere kan sende tekniske spørgsmål til [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft jævne Streaming klient midlertidig produkt aftale indehavere

- Adroit Business Solutions, Inc.
- Avanceret Digital udsendelse Systemadministratorens
- AirTies Kablosuz Iletism Sanayive ES Ticaret A.S.
- Albis teknologier Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- AVC multimedier Software Co., Ltd.
- Cavium, Inc.
- EchoStar købe Corporation
- Enseo, Inc.
- Fluendo S.A.
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Irdeto USA Inc.
- Frit globale Services BV
- MediaTek Inc.
- MStar Co, Ltd
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Safran Digital begrænset
- Sichuan Changhong elektrisk Co., Ltd
- SoftAtHome
- Sony Corporation
- Tatung Technology Inc.
- TCL Technoly Electronics (Huizhou) Co., Ltd.
- Vestel Elektronik Sanayi Gem Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft jævne Streaming klient endelige produkt aftale indehavere

- Avanceret Digital udsendelse Systemadministratorens
- AirTies Kablosuz Iletism Sanayive ES Ticaret A.S.
- Albis teknologier Ltd.
- Amazon Digital Services, Inc.
- AmTRAN teknologi Co., Ltd.
- Arcadyan Technology Corporation
- ATMACA ELEKTRONİK SAN. GEM TİC. A.Ş
- British Sky udsendelse begrænset
- CastPal Technology Inc., Shenzhen
- Compal Electronics, Inc.
- Dongguan Digital AV teknologi Corp., Ltd.
- EchoStar købe Corporation
- Enseo, Inc.
- Filmflex film begrænset
- Fluendo S.A.
- Gibson innovation begrænset
- Haier oplysninger Applicantion S.R.L
- HANDAN BroadInfoCom Co., Ltd.
- Homecast Co., Ltd
- Hon Hai præcision branche Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Orange Systemadministratorens
- Safran Digital begrænset
- Sagemcom bredbånd SAS
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou elektrisk Co., Ltd
- Shenzhen Skyworth Digital teknologi Co., Ltd
- Sichuan Changhong elektrisk Co., Ltd.
- Skardin industrielle Corp.
- Sky Deutschland Fernsehen GmbH og Co. KG
- SmarDTV S.A.
- SoftAtHome
- Sony Corporation
- Begrænset TCL oversøiske Marketing (Offshore kommerciel Macao)
- Mange levering teknologier, SAS
- Tongfang Global Ltd.
- Toshiba livsstil produkter og tjenester Corporation
- Universel medier Corporation /Slovakia/ s.r.o.
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
