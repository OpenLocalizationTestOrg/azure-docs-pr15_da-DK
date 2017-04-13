# <a name="pull-request-comment-automation"></a>Trække anmodning kommentar automatisering

Vi bruger kommentar automatisering i hente anmodning om kommentarer til at tillade bidragydere og forfattere at tildele navne, der styrer hente anmodningen Gennemse proces.

| Kommentar | Hvad gør den | Tilgængelighed|
| -------- |-------------|-------------|
|#Afslut | Når forfatteren af en artikel skriver **#sign-fra** kommentaren kommentar strømmen, tildeles den **klar til at flette** etiket. | Offentlige og private|
|#Afslut | Hvis en bidragyder, der ikke findes vises forfatter forsøger at logge på en offentlig hente anmodning ved hjælp af **#sign-fra** kommentaren, skrives en meddelelse til den hente anmodning angiver etiketten kan tildeles kun af forfatteren. | Offentlige |
|#Hold fra | Hvis du skriver **#hold fra** i en hente anmodning kommentar, fjerner **klar til at flette** etiket -, hvis du skifter mening eller laver en fejl. I den private repo tildeler dette etiketten **gør ikke brevfletning** . | Offentlige og private |
| #Tak-slut | Forfattere kan skrive kommentaren **please #-slut** i kommentar strømmen af en hente anmodning om at lukke den, hvis du beslutter dig for ikke at har de ændringer, der er flettet. | Offentlige |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Fejlfinding i forbindelse med log starter i den offentlige repo

Logge på offentlige repo fra automatisering er kan kun forfatteren til at logge af. Nogle manuel undtagelse behandling det kan være nødvendigt:

- **Artikel forfattere**: Hvis du vil bruge offentlige lager kommentar automatisering, kontoen faktisk GitHub skal svare nøjagtigt til kontoen GitHub er angivet i artikel metadata. Store bogstaver på din konto har betydning. Hvis du blokeres fra logge på grund af dette problem, skal du sende e-mail til azdocprs alias.

- **Andre medarbejdere**: Kontakt azdocprs med linket hente anmodning, hvis du er en medarbejder der registreres på vegne af forfatteren, og du er blokeret af automatisering. Angive, hvem du er--PMs på samme produktteamet, kolleger på skriver team og skrive teamledere betragtes som pålidelige kilder.



## <a name="related"></a>Relateret

- [Hente anmodning etikette og bedste praksis for Microsoft bidragydere](contributor-guide-pull-request-etiquette.md)

- [Gennemse kvalitetskriterier for hente anmodning](contributor-guide-pr-criteria.md)
