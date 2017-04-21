<properties
   pageTitle="Sidetitel, der vises i browseren fanen og søgeresultaterne"
   description="Artikel beskrivelse, der skal vises på lander sider og i de fleste søgeresultater"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Tabsbeløb skabelon (artikel overskrift 1 eller H1): overskrift i toppen af artiklen

Kopiere i artiklen i din lokale repo for at kopiere tabsbeløb fra denne skabelon, eller klik på knappen rå i GitHub UI, og Kopiér tabsbeløb.

  ![Alternativ tekst må ikke være tom. Beskriv billedet.][8]

Indledende afsnit: en dolor amet, adipiscing elit. Phasellus interdum nulla risus, lacinia porta nisl imperdiet sed. Mauris dolor mauris, tempus sed lacinia nec, euismod ikke felis. Nunc semper porta ultrices. Maecenas neque nulla condimentum vitae ipsum sidde amet, dignissim aliquet nisi.

## <a name="heading-2-h2"></a>Overskrift 2 (H2)

Aenean sidde amet leo nec purus placerat fermentum ac gravida odio. Aenean tellus lectus, faucibus i rhoncus i faucibus sed urna.  volutpat mi id purus ultrices iaculis nec ikke neque. [Teksten i linket til linket uden for azure.microsoft.com](http://weblogs.asp.net/scottgu). Nullam dictum dolor på aliquam pharetra. Vivamus ac hendrerit mauris [eksempel linktekst for link til en artikel i en Tjenestemappe](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Jeg får 10 gange mere trafik fra [Google]  [ gog] end fra [Yahoo]  [ yah] - eller [MSN] [msn].

> [AZURE.NOTE] Indrykkede notetekst.  Ordet 'note' tilføjes under publikation. Ut EU-pretium lacus. Nullam purus est, iaculis sed est å det niveau, euismod vehicula odio. Curabitur lacinia erat tristique iaculis rutrum erat SEM'en sodales nisi, og EU-condimentum turpis nisi en purus.

1. Aenean sidde amet leo nec **Purus** placerat fermentum ac gravida odio.

2. Aenean tellus lectus, faucius i **Rhoncus** i faucibus sed urna. Suspendisse volutpat mi id purus ultrices iaculis nec ikke neque.

    ![Alternativ tekst må ikke være tom. Indsamler bil i racing rød.][5]

3. Nullam dictum dolor på aliquam pharetra. Vivamus ac hendrerit mauris. Sed dolor dui, condimentum et varius en vehicula på nisl.

    ![Alternativ tekst der må ikke være tom][6]


Suspendisse volutpat mi id purus ultrices iaculis nec ikke neque. Nullam dictum dolor på aliquam pharetra. Vivamus ac hendrerit mauris. Otrus informatus: [1 Link til et andet azure.microsoft.com dokumentation emne](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Overskrift (H2)

Ut EU-pretium lacus. Nullam purus est, iaculis sed est å det niveau, euismod vehicula odio.

1. Curabitur lacinia erat tristique iaculis rutrum erat SEM'en sodales nisi, og EU-condimentum turpis nisi en purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum levende ipsum primis i faucibus orci luctus et ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam ikke <i>nisl molestie</i> pharetra eget en est. [Link 2 til et andet azure.microsoft.com dokumentation emne](web-sites-custom-domain-name.md)


Quisque commodo eros å det niveau lectus euismod auctor eget sidde amet leo. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Overskrift 2 (H2)

1. Maecenas sed condimentum nisi. Suspendisse potenti.

  + Fusce
  + Malesuada
  + SEM'en

2. Nullam i massa EU-tellus tempus hendrerit.

    ![Alternativ tekst må ikke være tom. Eksempel på en kanal 9 video.][7]

3. Quisque felis enim, fermentum ut aliquam nec, pellentesque pulvinar magna.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Vestibul levende ipsum primis i faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit purus diam pretium eros, vitae tincidunt nulla en sed turpis: [3 Link til et andet azure.microsoft.com dokumentation emne](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
