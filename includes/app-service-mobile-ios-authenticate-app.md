**Mål-C**: 

1. Åbn _QSTodoListViewController.m_ i Xcode på din Mac, og Tilføj følgende metode. Ændre _google_ til _microsoftaccount_, _twitter_, _facebook_eller _windowsazureactivedirectory_ , hvis du ikke bruger Google som din identitetsudbyder. Hvis du bruger Facebook, [skal du whitelist Facebook domæner i din app](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Erstatte `[self refresh]` i `viewDidLoad` i _QSTodoListViewController.m_ med følgende:

            [self loginAndGetData];

3. Tryk på _køre_ skal starte appen og derefter logge på. Når du er logget på, skal du kunne få vist listen opgaveliste og foretage opdateringer.

**Swift**:

1. Åbn _ToDoTableViewController.swift_ i Xcode på din Mac, og Tilføj følgende metode. Ændre _google_ til _microsoftaccount_, _twitter_, _facebook_eller _windowsazureactivedirectory_ , hvis du ikke bruger Google som din identitetsudbyder. Hvis du bruger Facebook, [skal du whitelist Facebook domæner i din app](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Fjerne stregerne `self.refreshControl?.beginRefreshing()` og `self.onRefresh(self.refreshControl)` i slutningen af `viewDidLoad()` i _ToDoTableViewController.swift_. Tilføje et opkald til `loginAndGetData()` i stedet:

            loginAndGetData()

3. Tryk på _køre_ skal starte appen og derefter logge på. Når du er logget på, skal du kunne få vist listen opgaveliste og foretage opdateringer.
