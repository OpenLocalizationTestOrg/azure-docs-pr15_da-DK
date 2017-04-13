<properties 
    pageTitle="Brug af Axinom til at levere Widevine licenser til Azure Media Services | Microsoft Azure" 
    description="I denne artikel beskrives, hvordan du kan bruge Azure Media Services (AMS) til at levere en stream, krypteres dynamisk ved AMS med både PlayReady og Widevine DRMs. PlayReady licens stammer fra Media Services PlayReady licensserveren og Widevine licens er leveret af Axinom licensserver." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Brug af Axinom til at levere Widevine licenser til Azure Media Services  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Oversigt

Azure Media Services (AMS) har tilføjet Google Widevine dynamisk beskyttelse (se [Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) få mere at vide). Desuden Azure Media Player (AMP) også har tilføjet Widevine support (se [AMP dokument](http://amp.azure.net/libs/amp/latest/docs/) få mere at vide). Dette er en overordnet hædersbeviser i streaming TANKESTREG indhold, der er beskyttet af CENC med MFA-native-DRM (PlayReady og Widevine) i moderne browsere, der er udstyret med MSE og EME.

Media Services, der starter med Media Services .NET SDK version 3.5.2, kan du konfigurere Widevine licens skabelon og få Widevine licenser. Du kan også bruge de følgende AMS partnere kan hjælpe dig med at levere Widevine licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/).

I denne artikel beskrives, hvordan du kan integrere og teste Widevine licensserveren administreres af Axinom. Præcist dækker den:  

- Konfiguration af dynamiske almindelige kryptering med flere-DRM (PlayReady og Widevine) med tilsvarende licens acquisition URL-adresser;
- Generering af et JWT token for at opfylde server licenskravene;
- Udvikling af Azure Media Player app som håndterer af licenser med JWT token godkendelse;

Med hele systemet og strømmen af indhold, der er vigtige, vigtige ID vigtige frø, JTW token og dens krav kan bedst beskrives efter i det følgende diagram.

![TANKESTREG og CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Indholdsbeskyttelse

Konfiguration af dynamisk beskyttelse og vigtige levering politik, skal du se Mingfei's blog: [hvordan du konfigurerer Widevine emballagen med Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Du kan konfigurere dynamisk CENC beskyttelse med flere DRM for TANKESTREG streaming har begge af følgende:

1. PlayReady beskyttelse MS Edge og IE11, der kan have en token godkendelse begrænsninger. Politikken token begrænset skal være ledsaget af et token, der er udstedt af en tjeneste STS (Secure Token), som Azure Active Directory.
1. Widevine beskyttelse med Chrome, kan det kræver godkendelse, token med token udstedt af en anden STS. 

Se [JWT Token generering af](media-services-axinom-integration.md#jwt-token-generation) sektion til hvorfor Azure Active Directory kan ikke bruges som en STS til Axinom's Widevine licensserveren.

###<a name="considerations"></a>Overvejelser i forbindelse med

1. Du skal bruge den angivne Axinom vigtige frø (8888000000000000000000000000000000000000) og din genererede eller markerede key ID til at generere tasten indhold til konfiguration af vigtige leveringstjenesten. Axinom licensserveren udsteder alle licenser, der indeholder indhold taster, der er baseret på den samme vigtige frø, som er gyldige for både test og fremstilling.
1. Widevine licens acquisition URL-adressen til test: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Både HTTP- og HTTS er tilladt.

##<a name="azure-media-player-preparation"></a>Azure Media Player forberedelse

AMP v1.4.0 understøtter afspilning af AMS indhold, som er dynamisk pakket med både PlayReady og Widevine DRM.
Hvis Widevine licensserveren ikke kræver token godkendelse, er der noget ekstra du skal gøre for at teste en TANKESTREG indhold, der er beskyttet af Widevine. Et eksempel indeholder AMP teamet et simpelt [eksempel](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), hvor du kan se det til at fungere i kant og IE11 med PlayReady og Chrome med Widevine.
Licensserveren Widevine leveres af Axinom kræver JWT token godkendelse. JWT tokenet skal sendes med licens anmodningen via en HTTP-header "X-AxDRM-meddelelsen". Hertil skal du tilføje følgende javascript på den webside, der er vært for AMP før du angiver kilden:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Resten af AMP kode er standard AMP API som AMP dokument [her](http://amp.azure.net/libs/amp/latest/docs/).

Bemærk, at den ovenstående javascript for indstillingen Brugerdefineret godkendelse sidehoved er stadig en kort sigt tilgang før den officielle langtidsaftale tilgang i AMP er udgivet.

##<a name="jwt-token-generation"></a>JWT generering af sikkerhedstoken

Axinom Widevine licensserveren til test kræver JWT token godkendelse. Desuden er en af krav i JWT tokenet af en kompleks objekttype i stedet for enkle datatype.

Desværre kan Azure AD kun udstede JWT tokens med primitive typer. På samme måde, .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler og JwtPayload) kun giver dig mulighed at skrive komplekse objekttype som krav. Påstande er dog stadig serialiseret som streng. Vi kan derfor ikke bruge en af to til at oprette JWT token for anmodning om Widevine licens.


John Sheehan [JWT Nuget pakke](https://www.nuget.org/packages/JWT) opfylder behovene, så vi skal bruge denne Nuget pakke.

Nedenfor vises koden for generere JWT token med de nødvendige krav efter behov ved Axinom Widevine licensserveren til test:

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Axinom Widevine licensserveren

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Overvejelser i forbindelse med

1.  Selvom AMS PlayReady licens leveringstjenesten kræver "bæreren =" før et godkendelse token, Axinom Widevine licensserveren ikke bruger den.
2.  Tasten Axinom kommunikation bruges som logge nøgle. Bemærk, at nøglen er en hex streng, men den skal behandles som en serie af byte, der ikke en streng, når kodning. Dette opnås ved hjælp af metoden ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Hente nøgle-ID

Du har muligvis bemærket, at kode til at oprette en JWT token, vigtige ID er påkrævet. Da JWT token skal være klar før indlæsning AMP player primære skal ID hentes for at oprette JWT token.

For at der er flere måder at få hold af nøgle-id. For eksempel en kan gemme key ID sammen med indholdsmetadata i en database. Eller du kan hente key ID fra TANKESTREG MPD (medier præsentation beskrivelse) fil. Nedenstående kode er for disse.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Oversigt

Med seneste tilføjelse af Widevine support i både Azure Media Services indholdsbeskyttelse og Azure Media Player er vi kunne implementere streaming TANKESTREG + MFA-native-DRM (PlayReady + Widevine) med begge PlayReady-tjenesten i AMS og Widevine licensserveren fra Axinom for følgende moderne browsere licenser:

- Chrome
- Microsoft Edge i Windows 10
- Internet Explorer 11 i Windows 8.1 og Windows 10
- Både Firefox (computer) og Safari på Mac (ikke iOS) understøttes også via Silverlight og den samme URL-adresse med Azure Media Player

Følgende parametre er påkrævet i licensserveren Mini løsning genbrug Axinom Widevine. Med undtagelse af nøgle-ID, resten af parametre, der leveres af Axinom baseret på deres Widevine server-konfiguration.


Parameter|Hvordan de anvendes
---|---
Kommunikation key ID|Der skal medtages som værdien af krav "com_key_id" i JWT token (se [dette](media-services-axinom-integration.md#jwt-token-generation) afsnit).
Kommunikation nøgle|Skal bruges som den signerende nøgle i JWT token (se [dette](media-services-axinom-integration.md#jwt-token-generation) afsnit).
Vigtige frø|Skal bruges til at oprette indhold nøgle med det angivne indhold key ID (se [dette](media-services-axinom-integration.md#content-protection) afsnit).
Widevine licens acquisition URL-adresse|Skal bruges i konfigurerer aktiv levering politik for TANKESTREG streaming (se [dette](media-services-axinom-integration.md#content-protection) afsnit).
Indhold nøgle-ID|Der skal medtages som en del af værdien af ret meddelelse gør krav på JWT token (se [dette](media-services-axinom-integration.md#jwt-token-generation) afsnit). 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Bekræftelse 

Vi vil gerne bekræfte de følgende personer, der har bidraget for at skabe dette dokument: Kristjan Jõgi af Axinom, Mingfei Yan og Amit Rajput.
