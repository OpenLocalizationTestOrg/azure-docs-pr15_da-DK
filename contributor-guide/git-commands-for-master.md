<properties pageTitle="Ciffer kommandoer til at oprette en ny artikel eller opdatere en eksisterende artikel" description="Trin til at arbejde med de tekniske Azure indhold GitHub typer lagre." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>Ciffer kommandoer til at oprette en ny artikel eller opdatere en eksisterende artikel


## <a name="standard-process-working-from-master"></a>Standardprocedure (arbejde fra master)
Følg trinnene i denne artikel for at oprette en lokal arbejde gren på din computer, så du kan oprette en ny artikel til sektionen tekniske dokumentation af azure.microsoft.com eller opdatere en eksisterende artikel.

1. Start ciffer fest (eller værktøjet kommandolinjen du bruger til ciffer).

 **Note:** Hvis du arbejder i den offentlige lager, kan du ændre azure-indhold-kurs til azure-indhold i alle kommandoer.

2. Ændre til azure-indhold-kurs:

        cd azure-content-pr
3. Se den overordnede gren:

        git checkout master

4. Oprette et nyt lokale arbejde gren udledt fra den overordnede gren:

        git pull upstream master:<working branch>


5. Flytte til den nye arbejde gren:

        git checkout <working branch>

6. Lad dine forgreningsovergang ved, du har oprettet den lokale arbejde afdeling:

        git push origin <working branch>

7. Oprette din nye artikel eller foretage ændringer i en eksisterende artikel. Brug Windows Stifinder til at åbne og oprette nye tabsbeløb filer og bruge Atom (http://atom.io) som tabsbeløb editor. Når du har oprettet eller redigeret din artikel og billeder, kan du gå til næste trin.

8. Tilføje, og foretag de ændringer, du har foretaget:

        git add .
        git commit –m "<comment>"
        
   Eller du kan tilføje kun specifikke-filer, du har ændret:

        git add <file path>
        git commit –m "<comment>"

   Hvis du har slettet filer, har du brug dette:
   
        git add --all
        git commit -m "<comment>"

9. Opdatere din lokale arbejde gren med ændringer fra tidligere:

        git pull upstream master

10. Overføre ændringerne til dine forgreningsovergang på GitHub:

        git push origin <working branch>

12. Når du er klar til at sende dit indhold til den foregående master gren til at arrangere datavalidering, og/eller udgiver, i UI GitHub oprette en anmodning om hente fra din forgreningsovergang til den overordnede gren.

13. Hvis du er en medarbejder arbejde i den private lager de ændringer, du har indsendt gemmes automatisk midlertidigt og en midlertidig link er skrevet på hente anmodningen. Gennemse dine faseinddelt indhold, og logge af i hente anmodning om kommentarer ved at tilføje **#sign-fra** kommentaren.  Dette angiver, at ændringerne er klar til at blive installeret direkte.  Hvis du ikke vil hente anmodningen kan accepteres -, hvis du lige arrangere ændringer – Tilføj **#hold fra** noten til at hente anmodningen.

14. Hente anmodning acceptor gennemser hente anmodningen, indeholder feedback og/eller accepterer anmodningen hente. 

15. Du kan også kontrollere din publicerede artikel eller ændringer på

 http://Azure.Microsoft.com/Documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Publicering

- Artikler er udgivet på cirka 10:00 AM og 3:00 Pacific Time, mandag til fredag. Det kan tage op til 30 minutter efter artikler skal vises online efter udgivelse. Husk, at din anmodning om hente har skal flettes af en hente anmodning korrekturlæser, før ændringerne kan medtages i næste planlagte udgivelse køre. Du har brug at arbejde med din hente anmodning korrekturlæser forvejen at sikre, at en anmodning om hente flettet til en bestemt publicering køre. Ellers skal gennemgås PRs i den rækkefølge, de er sendt.

- Hvis du er en medarbejder arbejder i den private lager, gennemgår alle anmodninger om hente valideringsregler, der skal rettes, før hente anmodningen kan flettes. 

## <a name="working-with-release-branches"></a>Arbejde med release forgreninger

Når du arbejder med en version gren, er den bedste måde at oprette en lokal arbejde gren fra release grenen at bruge denne Kommandosyntaks:

    git checkout upstream/<upstream branch name> -b <local working branch name>

Dette opretter den lokale afdeling direkte fra den tidligere gren, undgår lokale fletning.

