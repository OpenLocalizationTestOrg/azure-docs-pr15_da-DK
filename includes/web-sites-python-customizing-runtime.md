Azure bestemmer versionen af Python skal bruges til dens virtuelt miljø med følgende prioritetsrækkefølge:

1. version, der er angivet i runtime.txt i rodmappen
1. version, der er angivet af Python indstilling i sektionen web app-konfiguration ( **Indstillinger for** > **Programindstillinger** blade for din online i portalen Azure)
1. Python 2.7 er standard, hvis ingen af ovenstående er angivet

Gyldige værdier for indholdet af 

    \runtime.txt

er:

- Python 2.7
- Python 3.4

Hvis den micro version (tredje ciffer) er angivet, ignoreres.
