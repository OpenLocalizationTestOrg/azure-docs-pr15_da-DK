<properties
pageTitle="MailChimp | Microsoft Azure"
description="Opret logik apps med Azure App service. MailChimp er en SaaS-tjeneste, der gør det muligt for virksomheder at administrere og automatisere mail marketingaktiviteter, herunder afsendelse af marketing mails, automatiseret meddelelser og målrettede kampagner."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-mailchimp-connector"></a>Komme i gang med MailChimp forbindelsen

MailChimp er en SaaS-tjeneste, der gør det muligt for virksomheder at administrere og automatisere mail marketingaktiviteter, herunder afsendelse af marketing mails, automatiseret meddelelser og målrettede kampagner.


>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen.

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

MailChimp forbindelsen kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater.

 MailChimp forbindelsen har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="mailchimp-actions"></a>MailChimp handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Oprette en ny kampagnens baseret på en Campaign Type modtagerlisten og kampagnens indstillinger (emnelinjen, titel, from_name og reply_to)|
|[NewList](connectors-create-api-mailchimp.md#newlist)|Oprette en ny liste i kontoen MailChimp|
|[addmember](connectors-create-api-mailchimp.md#addmember)|Tilføje eller opdatere medlem liste|
|[removemember](connectors-create-api-mailchimp.md#removemember)|Slette et medlem fra en liste.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Opdatere oplysninger for et bestemt liste.|
### <a name="mailchimp-triggers"></a>MailChimp udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Når et medlem er blevet føjet til en liste|Aktiverer en arbejdsproces, når et nyt medlem er blevet føjet til en liste|
|Når der oprettes en ny liste|Udløser en arbejdsproces, når der oprettes en ny liste|


## <a name="create-a-connection-to-mailchimp"></a>Oprette en forbindelse til MailChimp
Hvis du vil oprette logik apps med MailChimp, skal du først oprette en **forbindelse** og derefter angive detaljerne for følgende egenskaber:

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive MailChimp legitimationsoplysninger|

>[AZURE.INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="reference-for-mailchimp"></a>Reference til MailChimp
Gælder for version: 1.0

## <a name="newcampaign"></a>newcampaign
Ny kampagnens: Oprette en ny kampagnens baseret på en Campaign Type modtagerlisten og kampagnens indstillinger (emnelinjen, titel, from_name og reply_to)

```POST: /campaigns```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|newCampaignRequest| |Ja|brødteksten|ingen|JSON objekt til at sende i brødteksten med de nye kampagnens anmodning om parametre|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="newlist"></a>NewList
Ny liste: Oprette en ny liste i kontoen MailChimp

```POST: /lists```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|newListRequest| |Ja|brødteksten|ingen|JSON objekt til at sende i brødteksten med de nye kampagnens anmodning om parametre|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="addmember"></a>addmember
Tilføj medlem til liste: tilføje eller opdatere medlem liste

```POST: /lists/{list_id}/members```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|streng|Ja|sti|ingen|Det entydige id for listen|
|newMemberInList| |Ja|brødteksten|ingen|JSON objekt til at sende i brødteksten med de nye medlemsoplysninger|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="removemember"></a>removemember
Fjerne medlem fra listen: slette et medlem fra en liste.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|streng|Ja|sti|ingen|Det entydige id for listen|
|member_email|streng|Ja|sti|ingen|E-mail-adressen på medlemmet for at slette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="updatemember"></a>updatemember
Opdatere medlemsoplysninger: opdatere oplysninger for et bestemt liste.

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|streng|Ja|sti|ingen|Det entydige id for listen|
|member_email|streng|Ja|sti|ingen|Medlemmet opdatere entydige mailadresse|
|updateMemberInListRequest| |Ja|brødteksten|ingen|JSON objekt til at sende i brødteksten med de opdaterede medlemsoplysninger|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="onmembersubscribed"></a>OnMemberSubscribed
Når et medlem er blevet føjet til en liste: udløser en arbejdsproces, når et nyt medlem er blevet føjet til en liste

```GET: /trigger/lists/{list_id}/members```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|streng|Ja|sti|ingen|Det entydige id for listen|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="oncreatelist"></a>OnCreateList
Når der oprettes en ny liste: udløser en arbejdsproces, når der oprettes en ny liste

```GET: /trigger/lists```

Der er ingen parametre til dette opkald
#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner

### <a name="newcampaignrequest"></a>NewCampaignRequest


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|type|streng|Ja |
|modtagere|ikke defineret|Ja |
|indstillinger|ikke defineret|Ja |
|variate_settings|ikke defineret|Nej |
|sporing|ikke defineret|Nej |
|rss_opts|ikke defineret|Nej |
|social_card|ikke defineret|Nej |



### <a name="recipient"></a>Modtager


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|list_id|streng|Ja |
|segment_opts|ikke defineret|Nej |



### <a name="settings"></a>Indstillinger


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|subject_line|streng|Ja |
|titel|streng|Nej |
|from_name|streng|Ja |
|reply_to|streng|Ja |
|use_conversation|Boolesk værdi|Nej |
|to_name|streng|Nej |
|folder_id|heltal|Nej |
|godkende|Boolesk værdi|Nej |
|auto_footer|Boolesk værdi|Nej |
|inline_css|Boolesk værdi|Nej |
|auto_tweet|Boolesk værdi|Nej |
|auto_fb_post|matrix|Nej |
|fb_comments|Boolesk værdi|Nej |



### <a name="variatesettings"></a>Variate_Settings


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|winner_criteria|streng|Nej |
|Ventetid|heltal|Nej |
|test_size|heltal|Nej |
|subject_lines|matrix|Nej |
|send_times|matrix|Nej |
|from_names|matrix|Nej |
|reply_to_addresses|matrix|Nej |



### <a name="tracking"></a>Sporing


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|åbnes|Boolesk værdi|Nej |
|html_clicks|Boolesk værdi|Nej |
|text_clicks|Boolesk værdi|Nej |
|goal_tracking|Boolesk værdi|Nej |
|ecomm360|Boolesk værdi|Nej |
|google_analytics|streng|Nej |
|clicktale|streng|Nej |
|Salesforce|ikke defineret|Nej |
|highrise|ikke defineret|Nej |
|kapsel|ikke defineret|Nej |



### <a name="rssopts"></a>RSS_Opts


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|feed_url|streng|Nej |
|frekvens|streng|Nej |
|constrain_rss_img|streng|Nej |
|tidsplan|ikke defineret|Nej |



### <a name="socialcard"></a>Social_Card


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|image_url|streng|Nej |
|Beskrivelse|streng|Nej |
|titel|streng|Nej |



### <a name="segmentopts"></a>Segment_Opts


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|saved_segment_id|heltal|Nej |
|Sammenlign|streng|Nej |



### <a name="salesforce"></a>Salesforce


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Kampagnens|Boolesk værdi|Nej |
|noter|Boolesk værdi|Nej |



### <a name="highrise"></a>Highrise


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Kampagnens|Boolesk værdi|Nej |
|noter|Boolesk værdi|Nej |



### <a name="capsule"></a>Kapsel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|noter|Boolesk værdi|Nej |



### <a name="schedule"></a>Tidsplan


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|time|heltal|Nej |
|daily_send|ikke defineret|Nej |
|weekly_send_day|streng|Nej |
|monthly_send_date|tal|Nej |



### <a name="dailysend"></a>Daily_Send


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|søndag|Boolesk værdi|Nej |
|mandag|Boolesk værdi|Nej |
|tirsdag|Boolesk værdi|Nej |
|onsdag|Boolesk værdi|Nej |
|torsdag|Boolesk værdi|Nej |
|fredag|Boolesk værdi|Nej |
|lørdag|Boolesk værdi|Nej |



### <a name="campaignresponsemodel"></a>CampaignResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|streng|Nej |
|type|streng|Nej |
|create_time|streng|Nej |
|archive_url|streng|Nej |
|status|streng|Nej |
|emails_sent|heltal|Nej |
|send_time|streng|Nej |
|content_type|streng|Nej |
|modtager|matrix|Nej |
|indstillinger|ikke defineret|Nej |
|variate_settings|ikke defineret|Nej |
|sporing|ikke defineret|Nej |
|rss_opts|ikke defineret|Nej |
|ab_split_opts|ikke defineret|Nej |
|social_card|ikke defineret|Nej |
|report_summary|ikke defineret|Nej |
|delivery_status|ikke defineret|Nej |
|_links|matrix|Nej |



### <a name="absplitopts"></a>AB_Split_Opts


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|split_test|streng|Nej |
|pick_winner|streng|Nej |
|wait_units|streng|Nej |
|Ventetid|heltal|Nej |
|split_size|heltal|Nej |
|from_name_a|streng|Nej |
|from_name_b|streng|Nej |
|reply_email_a|streng|Nej |
|reply_email_b|streng|Nej |
|subject_a|streng|Nej |
|subject_b|streng|Nej |
|send_time_a|streng|Nej |
|send_time_b|streng|Nej |
|send_time_winner|streng|Nej |



### <a name="reportsummary"></a>Report_Summary


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|åbnes|heltal|Nej |
|unique_opens|heltal|Nej |
|open_rate|tal|Nej |
|klikker på|heltal|Nej |
|subscriber_clicks|tal|Nej |
|click_rate|tal|Nej |



### <a name="deliverystatus"></a>Delivery_Status


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|aktiveret|Boolesk værdi|Nej |
|can_cancel|Boolesk værdi|Nej |
|status|streng|Nej |
|emails_sent|heltal|Nej |
|emails_canceled|heltal|Nej |



### <a name="link"></a>Link


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Rel|streng|Nej |
|linkreference|streng|Nej |
|metode|streng|Nej |
|targetSchema|streng|Nej |
|skema|streng|Nej |



### <a name="newlistrequest"></a>NewListRequest


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Ja |
|Kontakt|ikke defineret|Ja |
|permission_reminder|streng|Ja |
|use_archive_bar|Boolesk værdi|Nej |
|campaign_defaults|ikke defineret|Ja |
|notify_on_subscribe|streng|Nej |
|notify_on_unsubscribe|streng|Nej |
|email_type_option|Boolesk værdi|Ja |
|synlighed|streng|Nej |



### <a name="contact"></a>Kontakt


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|virksomhed|streng|Ja |
|Address1|streng|Ja |
|adresse 2|streng|Nej |
|by|streng|Ja |
|stat|streng|Ja |
|ZIP|streng|Ja |
|land|streng|Ja |
|telefon|streng|Ja |



### <a name="campaigndefaults"></a>Campaign_Defaults


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|from_name|streng|Ja |
|from_email|streng|Ja |
|emne|streng|Nej |
|sprog|streng|Ja |



### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|streng|Ja |
|Navn|streng|Ja |
|Kontakt|ikke defineret|Ja |
|permission_reminder|streng|Ja |
|use_archive_bar|Boolesk værdi|Nej |
|campaign_defaults|ikke defineret|Ja |
|notify_on_subscribe|streng|Nej |
|notify_on_unsubscribe|streng|Nej |
|Date_Created|streng|Nej |
|list_rating|heltal|Nej |
|email_type_option|Boolesk værdi|Ja |
|subscribe_url_short|streng|Nej |
|subscribe_url_long|streng|Nej |
|beamer_address|streng|Nej |
|synlighed|streng|Nej |
|moduler|matrix|Nej |
|Statistik|ikke defineret|Nej |
|_links|matrix|Nej |



### <a name="stats"></a>Statistik


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|member_count|heltal|Nej |
|unsubscribe_count|heltal|Nej |
|cleaned_count|heltal|Nej |
|member_count_since_send|heltal|Nej |
|unsubscribe_count_since_send|heltal|Nej |
|cleaned_count_since_send|heltal|Nej |
|campaign_count|heltal|Nej |
|campaign_last_sent|heltal|Nej |
|merge_field_count|heltal|Nej |
|avg_sub_rate|tal|Nej |
|avg_unsub_rate|tal|Nej |
|target_sub_rate|tal|Nej |
|open_rate|tal|Nej |
|click_rate|tal|Nej |
|last_sub_date|streng|Nej |
|last_unsub_date|streng|Nej |



### <a name="getlistsresponsemodel"></a>GetListsResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|lister|matrix|Nej |
|total_items|heltal|Nej |



### <a name="newmemberinlistrequest"></a>NewMemberInListRequest


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|email_type|streng|Nej |
|status|streng|Ja |
|merge_fields|ikke defineret|Nej |
|interesser|streng|Nej |
|sprog|streng|Nej |
|Vip|Boolesk værdi|Nej |
|placering|ikke defineret|Nej |
|email_address|streng|Ja |



### <a name="firstandlastname"></a>FirstAndLastName


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|FNAME|streng|Nej |
|EFTERNAVN|streng|Nej |



### <a name="location"></a>Placering


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|længde|tal|Nej |
|længdegrader|tal|Nej |



### <a name="memberresponsemodel"></a>MemberResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|streng|Nej |
|email_address|streng|Nej |
|unique_email_id|streng|Nej |
|email_type|streng|Nej |
|status|streng|Nej |
|merge_fields|ikke defineret|Nej |
|interesser|streng|Nej |
|Statistik|ikke defineret|Nej |
|ip_signup|streng|Nej |
|timestamp_signup|streng|Nej |
|ip_opt|streng|Nej |
|timestamp_opt|streng|Nej |
|member_rating|heltal|Nej |
|last_changed|streng|Nej |
|sprog|streng|Nej |
|Vip|Boolesk værdi|Nej |
|email_client|streng|Nej |
|placering|ikke defineret|Nej |
|last_note|ikke defineret|Nej |
|list_id|streng|Nej |
|_links|matrix|Nej |



### <a name="lastnote"></a>Last_Note


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|note_id|heltal|Nej |
|created_at|streng|Nej |
|created_by|streng|Nej |
|Bemærk!|streng|Nej |



### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|medlemmer|matrix|Nej |
|list_id|streng|Nej |
|total_items|heltal|Nej |



### <a name="object"></a>Objekt






### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|email_address|streng|Nej |
|email_type|streng|Nej |
|status|streng|Ja |
|merge_fields|ikke defineret|Nej |
|interesser|streng|Nej |
|sprog|streng|Nej |
|Vip|Boolesk værdi|Nej |
|placering|ikke defineret|Nej |



### <a name="getmembersresponsemodel"></a>GetMembersResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|medlemmer|matrix|Nej |
|list_id|streng|Nej |
|total_items|heltal|Nej |



### <a name="adduserresponsemodel"></a>AddUserResponseModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|streng|Ja |
|email_address|streng|Ja |
|unique_email_id|streng|Nej |
|email_type|streng|Nej |
|status|streng|Nej |
|merge_fields|ikke defineret|Ja |
|interesser|streng|Nej |
|Statistik|ikke defineret|Nej |
|ip_signup|streng|Nej |
|timestamp_signup|streng|Nej |
|ip_opt|streng|Nej |
|timestamp_opt|streng|Nej |
|member_rating|heltal|Nej |
|last_changed|streng|Nej |
|sprog|streng|Nej |
|Vip|Boolesk værdi|Nej |
|email_client|streng|Nej |
|placering|ikke defineret|Nej |
|last_note|ikke defineret|Nej |
|list_id|streng|Nej |
|_links|matrix|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)
