<properties 
    pageTitle="Oversigt over Widevine licens skabelon | Microsoft Azure" 
    description="Dette emne giver et overblik over en Widevine licens skabelon, der bruges til at konfigurere Widevine licenser." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Oversigt over Widevine licens-skabelon

##<a name="overview"></a>Oversigt

Azure Media Services nu giver dig mulighed at konfigurere og anmode om Widevine licenser. Når slutbrugerens afspilleren forsøger at afspille dine Widevine beskyttet indhold, sendes en anmodning til leveringstjenesten licens til at hente en licens. Hvis tjenesten licens godkender anmodningen, udsteder den licens, som sendes til klienten og kan bruges til at dekryptere og afspille det angivne indhold.

Anmodning om Widevine licens er formateret som en JSON-meddelelse.  

Bemærk, at du kan vælge at oprette en tom meddelelse med nogen værdier kun "{}", og der oprettes en licens-skabelon med alle standardindstillinger.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>JSON meddelelse

Navn | Værdi | Beskrivelse
---|---|---
data |Base64-kodet streng |Licens anmodningen sendes af en klient. 
content_id | Base64-kodet streng|Id, der er brugt til at oprette KeyId(s) og indhold nøgle(r) for hver content_key_specs.track_type.
udbyder |streng |Bruges til at søge efter indhold nøgler og politikker. Der er påkrævet.
policy_name | streng |Navnet på en tidligere registrerede politik. Valgfri
allowed_track_types | Optæl  | SD_ONLY eller SD_HD. Kontrolelementer, der indhold nøgler skal medtages i en licens
content_key_specs | matrix med JSON strukturer, se **Specifikationer for indhold nøgle** nedenfor | Få en bedre kornet kontrolelement på tasterne hvilket indhold for at vende tilbage. Se indhold nøgle specifikation under få mere at vide.  Kun én af allowed_track_types og content_key_specs kan angives. 
use_policy_overrides_exclusively | Boolesk værdi. SAND eller FALSK | Brug politik attributter, der er angivet af policy_overrides og udelader alle tidligere gemte politik.
policy_overrides | JSON struktur, skal du se **Politik tilsidesætter** nedenfor | Politikindstillinger for denne licens.  I tilfælde af, at dette aktiv har en foruddefineret politik, der skal bruges disse angivne værdier. 
session_init | JSON struktur, skal du se **Session initialisering** nedenfor | Valgfri data overføres til licens.
parse_only | Boolesk værdi. SAND eller FALSK | Er parset licens anmodningen, men ingen licens er udstedt. Dog værdier formular licens anmodningen returneres i svaret.  

##<a name="content-key-specs"></a>Specifikationer for indhold vigtige 

Hvis der findes en eksisterende politik, er der ikke nødvendigt at angive en eller flere af værdierne i den indhold nøgle specifikation.  Den eksisterende politik, der er knyttet til dette indhold bruges til at bestemme output beskyttelsen som HDCP og CGMS.  Hvis en eksisterende politik ikke er registreret hos licensserveren Widevine, kan udbyderen af indholdet Indsæt værdierne i licens indkaldelsen.   


Hver content_key_specs skal være angivet til alle numre, uanset indstillingen use_policy_overrides_exclusively. 


Navn | Værdi | Beskrivelse
---|---|---
content_key_specs. track_type | streng | Et Registrer typenavn. Hvis content_key_specs er angivet i licens anmodningen, skal du sørge for at angive alle registrere typer eksplicit. Fejl ved at gøre dette medfører fejl til afspilning tidligere 10 sekunder. 
content_key_specs  <br/> security_level | UInt32 | Definerer klient stabilitet krav til afspilning. <br/> 1 – software-baserede whitebox kryptering er påkrævet. <br/> 2 – software kryptering og en sløret dekoder er påkrævet. <br/> 3-nøgle materialer og kryptografi handlinger skal udføres i et hardware sikkerhedskopierede der er tillid til udførelse af miljø. <br/> 4 – kryptering og kodning af indhold skal udføres i et hardware sikkerhedskopierede der er tillid til udførelse af miljø.  <br/> 5 – det kryptografi, afkodningsværktøjet og alle ekspedition af medier (komprimeres og ikke-komprimeret format) skal håndteres i et hardware sikkerhedskopierede der er tillid til udførelse af miljø.  
content_key_specs <br/> required_output_protection.hDC | streng - en af: HDCP_NONE, HDCP_V1, HDCP_V2 | Angiver, om HDCP er kræver
content_key_specs <br/>nøgle | Base64 <br/>kodet streng|Indhold for at bruge til denne registrering. Hvis angivet, er track_type eller key_id påkrævet.  Denne indstilling kan udbyderen af indholdet skal tilføjes tasten indhold for denne Registrer i stedet for at lade Widevine licensserveren generere eller finde en nøgle.
content_key_specs.key_id| Base64-kodet streng binær, 16 byte | Entydigt id for nøglen. 


##<a name="policy-overrides"></a>Politik tilsidesætter 

Navn | Værdi | Beskrivelse
---|---|---
policy_overrides. can_play | Boolesk værdi. SAND eller FALSK | Angiver afspilningen af indholdet er tilladt. Standard er falsk.
policy_overrides. can_persist | Boolesk værdi. SAND eller FALSK |Angiver, at licensen kan bevaret til permanente lager til brug offline. Standard er falsk.
policy_overrides. can_renew | Boolesk SAND eller FALSK |Angiver, at fornyelse af denne licens er tilladt. Hvis sand, kan varigheden af licensen udvides ved godkendelse af gyldighed. Standard er falsk. 
policy_overrides. license_duration_seconds | Int64 | Angiver en tidsramme for denne specifikke licens. En værdi på 0 angiver, at der er ingen grænse for varigheden. Standardværdien er 0. 
policy_overrides. rental_duration_seconds | Int64 | Angiver en tidsramme, mens afspilning er tilladt. En værdi på 0 angiver, at der er ingen grænse for varigheden. Standardværdien er 0. 
policy_overrides. playback_duration_seconds | Int64 | Vinduet visning af tid, når afspilning starter inden for licens varighed. En værdi på 0 angiver, at der er ingen grænse for varigheden. Standardværdien er 0. 
policy_overrides. renewal_server_url |streng | Alle godkendelse gyldighed (fornyelse) anmodninger om denne licens rettes til den angivne URL-adresse. Dette felt bruges kun, hvis can_renew er sand.
policy_overrides. renewal_delay_seconds |Int64 |Hvor mange sekunder efter license_start_time, før fornyelse forsøges først. Dette felt bruges kun, hvis can_renew er sand. Standard er 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Angiver forsinkelsen i sekunder mellem efterfølgende licens fornyelse anmodninger, i tilfælde af mislykket. Dette felt bruges kun, hvis can_renew er sand. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | Vinduet periode, hvori afspilning har tilladelse til at fortsætte mens fornyelse er forsøgte, endnu mislykkes på grund af back end-problemer med licensserveren. En værdi på 0 angiver, at der er ingen grænse for varigheden. Dette felt bruges kun, hvis can_renew er sand.
policy_overrides. renew_with_usage | Boolesk SAND eller FALSK |Angiver, at licensen skal sendes til fornyelse, når brugen startes. Dette felt bruges kun, hvis can_renew er sand. 

##<a name="session-initialization"></a>Initialisering af session

Navn | Værdi | Beskrivelse
---|---|---
provider_session_token | Base64-kodet streng |I denne sessionstoken sendes tilbage på licensen og findes i efterfølgende fornyelser.  Session tokenet bevares ikke ud over sessioner. 
provider_client_token | Base64-kodet streng | Klient-token til at sende tilbage i licens svaret.  Hvis licens anmodningen indeholder en klient-token, ignoreres denne værdi. Klient-token bevares ud over licens sessioner.
override_provider_client_token | Boolesk værdi. SAND eller FALSK |Hvis falsk og licens anmodningen indeholder et klient-id, skal du bruge token fra anmodningen, selvom en klient-token er angivet i denne struktur.  Hvis sand, Brug altid det token, der er angivet i denne struktur.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Konfigurere dine Widevine licenser ved hjælp af .NET datatyper

Media Services indeholder .NET API'er, som du kan konfigurere din Widevine licenser. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasser som defineret i Media Services .NET SDK

Følgende er definitioner af disse filtyper.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Eksempel

I følgende eksempel viser, hvordan du bruger .NET API'er til at konfigurere en simpel Widevine licens.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Se også

[Ved hjælp af PlayReady og/eller Widevine dynamisk almindelige kryptering](media-services-protect-with-drm.md)
