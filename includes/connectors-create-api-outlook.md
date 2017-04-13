## <a name="connect-to-outlookcom"></a>Oprette forbindelse til Outlook.com

### <a name="prerequisites"></a>Forudsætninger
- En Outlook.com-konto

Før du kan bruge din Outlook.com-konto i en logik app, skal du tillade logik app til at oprette forbindelse til din Outlook.com-konto. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen. 

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til din Outlook.com-konto:

1. Alle logik apps skal startes af en udløser, så når du opretter din logik app, Forespørgselsdesigner åbnes og viser en liste over udløser, som du kan bruge til at starte din logik app:

  ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Skriv "outlook" i søgefeltet. Bemærk, på listen er filtreret for at få vist alle udløsere med "Outlook" i navn:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Vælg **Office 365 Outlook - på ny mail**.   
  Hvis du ikke har oprettet en forbindelse til Outlook før, får du bedt om at angive din Outlook.com-legitimationsoplysninger. Disse legitimationsoplysninger bruges til at tillade, at din logik app til at oprette forbindelse til, og få adgang til din Outlook.com-konto data:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Angive dine legitimationsoplysninger til Outlook, og log på:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
  Det var det. Du har nu oprettet en forbindelse til Outlook. Denne forbindelse bliver tilgængelig til brug i en anden logik app, du opretter.


