Nogle pakker kan ikke installeres med pip når kører på Azure.  Blot kan det være, at pakken ikke er tilgængelig under Python pakke indekset.  Det kan være der kræves en compiler (en compiler er ikke tilgængelig på den computer kører WebApp i Azure App Service).

I dette afsnit, vil vi se på måder at løse dette problem.

### <a name="request-wheels"></a>Anmode om hjul

Hvis installationen af kræver en compiler, skal du prøve at kontakte ejeren af pakke for at anmode om, at rullehjulet gøres tilgængelige til pakken.

Med den seneste tilgængeligheden af [Microsoft Visual C++ Compiler til Python 2.7][]er det nu lettere at opbygge pakker, der har oprindelig kode til Python 2.7.

### <a name="build-wheels-requires-windows"></a>Opbygge rullehjulet (kræver Windows)

Bemærk:, Når du bruger denne indstilling, Sørg for at samle pakken ved hjælp af en Python miljø, der svarer til den platform/arkitektur/version, der bruges på web app i Azure App Service (Windows/32-bit/2.7 eller 3.4).

Hvis pakken ikke installeres, fordi den kræver en compiler, kan du installere compileren på din lokale computer og opbygge et hjul til pakken, som du derefter vil medtage i din lager.

Mac/Linux-brugere: Hvis du ikke har adgang til en Windows-computer, under [oprette et virtuelt, der kører Windows][] til at oprette en VM på Azure.  Du kan bruge det til at opbygge hjul, føje dem til lageret og Slet VM, hvis du ønsker. 

Du kan installere [Microsoft Visual C++ Compiler til Python 2.7][]Python 2.7.

Python 3.4, kan du installere [Microsoft Visual C++ 2010 Express][].

For at opbygge rullehjulet, skal du pakken hjulet:

    env\scripts\pip install wheel

Du skal bruge `pip wheel` til at oprette en afhængighed:

    env\scripts\pip wheel azure==0.8.4

Dette opretter en .whl fil i mappen \wheelhouse.  Føje \wheelhouse mappe og hjulet filer til din lager.

Redigere din requirements.txt at tilføje den `--find-links` indstilling øverst. Det fortæller pip til at søge efter en identisk værdi i den lokale mappe, før du skifter til python pakke indekset.

    --find-links wheelhouse
    azure==0.8.4

Hvis du vil medtage alle dine afhængigheder i mappen \wheelhouse og ikke bruger python pakke indekset overhovedet, kan du fremtvinge pip at ignorere pakke indekset ved at tilføje `--no-index` til toppen af din requirements.txt.

    --no-index

### <a name="customize-installation"></a>Tilpasse installationen

Du kan tilpasse scriptet installation for at installere en pakke i det virtuelle miljø ved hjælp af en alternativ installer, såsom nemt\_installere.  Se deploy.cmd for et eksempel, der er kommenterede ud.  Sørg for, at sådanne pakker ikke er angivet i requirements.txt, for at forhindre, at pip installerer dem.

Tilføje denne værdi i scriptet installation:

    env\scripts\easy_install somepackage

Du kan også kunne bruge nemt\_Installer til installation fra et exe installationsprogram (nogle er zip-kompatible, så du let\_Installer understøtter dem).  Tilføje installationsprogrammet til dit lager og kalde nemt\_installere ved at overføre stien til den eksekverbare fil.

Tilføje denne værdi i scriptet installation:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Medtage det virtuelle miljø i lageret (kræver Windows)

Bemærk: Når du bruger denne indstilling, Sørg for, at du bruger et virtuelt miljø, der svarer til den platform/arkitektur/version, der bruges på web app i Azure App Service (Windows/32-bit/2.7 eller 3.4).

Hvis du medtager det virtuelle miljø i lageret, kan du forhindre installation scriptet i at gøre virtuelt miljø administration på Azure ved at oprette en tom fil:

    .skipPythonDeployment

Vi anbefaler, at du sletter den eksisterende virtuelt miljø på appen for at forhindre overskydende filer fra når det virtuelle miljø blev administrerede automatisk.


[Oprette en virtuel maskine, der kører Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler til Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
