<properties 
    pageTitle="Bruge Emoji humørikoner i Azure Mobile aftale" 
    description="Sådan bruger du Emoji humørikoner i din pushmeddelelser"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Brug Emoji humørikon i Push-beskeder

Du kan medtage Emoji humørikoner i du push meddelelse i et par nemme trin: 

1. Første skal du finde Emoji vil du sende i meddelelsen. Sørg for, at den Emoji, du vælger være understøttes af target enheden, som enhed producerer gå et stykke tid at føje nyligt godkendte Emojis til Enhedsplatforme. 

2. I **Windows** - kan du gå til dette [link](http://apps.timwhitlock.info/emoji/tables/unicode) og kopiere ikonet 'Oprindelig'.

    ![][7] 

3. Du kan finde Emojis i ordbogen program under Rediger -> Emoji og symboler på **Mac** .

    ![][6] 

4. Nu kan du gå til fanen **Når** på portalen Azure Mobile aftale. Vælg typen af din push-meddelelse (meddelelsen undersøger osv). I dette eksempel vælger vi opslagsnål en meddelelse.

5. Angiv de forskellige felter i meddelelsen om, indtil du når teksten i meddelelsen. Dette er, hvor du vil tilføje humørikonet Emoji. Du kan vælge at placere det i titlen, meddelelsen eller begge dele. Skal du trække og slippe eller kopiere den Emoji, som du finder fra placeringer ovenfor. 

    ![][1]

6. Udfylde de andre felter for meddelelsen, og Gem den. 

7. Når du kører en test eller aktivere meddelelsen, der vises en meddelelse med det ønskede humørikon som angivet.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

