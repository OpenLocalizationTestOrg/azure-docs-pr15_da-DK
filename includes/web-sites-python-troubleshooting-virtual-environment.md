Scriptet installation springe oprettelse af det virtuelle miljø på Azure, hvis der registreres, allerede findes et kompatible virtuelt miljø.  Dette kan fremskynde installation betydeligt.  Pakker, der er installeret i forvejen udelades ved pip.

I visse tilfælde kan du overveje at gennemtvinge slette pågældende virtuelt miljø.  Du skal gøre dette, hvis du beslutter dig for at medtage et virtuelt miljø som en del af dit lager.  Du kan også vil gøre dette, hvis du har brug at slippe af med visse pakker eller test ændringer til requirements.txt.

Der er nogle indstillinger til at administrere eksisterende virtuelt miljø på Azure:

### <a name="option-1-use-ftp"></a>Mulighed 1: Bruge FTP

Oprette forbindelse til serveren, og du kan slette mappen env med en FTP-klient.  Bemærk, at nogle FTP-klienter (såsom webbrowsere) kan være skrivebeskyttet og ikke kan bruges til at slette mapper, så det er vigtigt at sikre, at du bruger en FTP-klient med denne funktion.  FTP-værtsnavn og bruger vises i din online blade på [Azure-portalen](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Mulighed 2: Skift runtime

Her er en alternativ tekst, der udnytter fakultet, scriptet installation Slet mappen env, når den ikke stemmer overens med den ønskede version af Python.  Dette vil effektivt Slet det eksisterende miljø, og Opret en ny.

1. Skifte til en anden version af Python (via runtime.txt eller bladet **Programindstillinger** i portalen Azure)
1. ciffer push ændringer (Ignorer pip Installer fejl, hvis mindst ét)
1. Skift tilbage til første version af Python
1. ciffer push nogle ændringer igen

### <a name="option-3-customize-deployment-script"></a>Mulighed 3: Tilpasse installation script

Hvis du har tilpasset scriptet installation, kan du ændre koden i deploy.cmd tvinge det til at slette mappen env.
