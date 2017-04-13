
Koden for alle funktionerne i en given funktion app findes i en rodmappen, der indeholder en host konfigurationsfil og en eller flere undermapper, hver især indeholder kode for en separat funktion, som i følgende eksempel

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Filen *host.json* indeholder nogle runtime-specifikke konfiguration og er placeret i rodmappen af funktionen app. Du kan finde oplysninger om indstillinger, der er tilgængelige, [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) i WebJobs.Script lager wiki.

Hver funktion har en mappe, der indeholder en eller flere kodefiler, function.json konfiguration og andre afhængigheder.