Azure bestemmer, at dit program bruger Python **Hvis begge disse betingelser er sande**:

- requirements.txt fil i rodmappen
- en hvilken som helst .py-fil i rodmappen eller en runtime.txt, der angiver python

Når det er tilfældet, kan der skal bruges en Python bestemt installation script, som udfører den standard synkronisering af filer samt flere Python handlinger f.eks.:

- Automatisk administration af virtuelt miljø
- Installation af pakker i requirements.txt ved hjælp af pip
- Oprettelse af relevante web.config baseret på den valgte Python version.
- Indsamle statisk filerne til Django programmer

Du kan styre bestemte aspekter af standard installation trinnene uden at tilpasse scriptet.

Hvis du vil springe over alle Python bestemt installation trin, kan du oprette denne tom fil:

    \.skipPythonDeployment

Hvis du vil springe over samling af statiske filer til dit Django program:

    \.skipDjango 

Du kan tilsidesætte standard installation scriptet til mere kontrol over installation, ved at oprette følgende filer:

    \.deployment
    \deploy.cmd

Du kan bruge [Azure kommandolinjen][] til at oprette filerne.  Brug denne kommando fra projektmappen:

    azure site deploymentscript --python

Når filerne ikke findes, opretter en midlertidig installation script Azure, og du kører den.  Det er identisk med det, du opretter med kommandoen ovenfor.

[Azure kommandolinjen]: http://azure.microsoft.com/downloads/
