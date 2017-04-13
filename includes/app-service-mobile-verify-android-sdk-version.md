På grund af igangværende udvikling, kan den Android SDK-version, der er installeret i Android Studio ikke svarer til versionen i koden. Android SDK refereres til i dette selvstudium er version 23, senest på tidspunktet for skrivning. Versionsnummeret kan øge som nye versioner af SDK vises, og det anbefales at bruge den nyeste version.

To symptomer på uoverensstemmelse er:

1. Når du opretter eller genopbygge projektet, kan du få Gradle fejlmeddelelser som "**kunne ikke finde target Google Inc.:Google APIs:n**".

2. Standard Android objekter i kode, der skal løse baseret på `import` sætninger kan generere fejlmeddelelser.

Hvis en af disse vises, kan versionen af Android SDK installeret i Android Studio ikke overens med SDK destinationen af hentede projektet.  Hvis du vil kontrollere versionen, skal du foretage følgende ændringer:


1. Klik på **Funktioner**i Android Studio => **Android** => **SDK Manager**. Hvis du ikke har installeret den nyeste version af SDK-platformen, klik derefter på for at installere den. Skal du notere versionsnummeret.

2. Åbn filen i fanen Projektstifinder under **Gradle Scripts**, **build.gradle (modeule: app)**. Sørg for, at **compileSdkVersion** og **buildToolsVersion** er indstillet til den nyeste SDK-version, der er installeret. Koderne kan se sådan ud:
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. Højreklik på projektnoden i Android Studio Projektstifinder, Vælg **Egenskaber**, og vælg **Android**i kolonnen yderst til venstre. Sørg for, at **Project opbygge mål** er indstillet til den samme version som SDK som **targetSdkVersion**.

4. Manifestfilen bruges ikke længere i Android Studio til at angive mål SDK og SDK minimumversion, i modsætning til tilfældet med Eklipse.
