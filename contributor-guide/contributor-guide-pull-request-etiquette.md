# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Trække anmodning etikette og bedste praksis for Microsoft bidragydere til Azure dokumentation

Hvis du vil publicere ændringer af dokumentation, kan du sende anmodninger om hente fra din forgreningsovergang. Alle hente anmodninger skal gennemses før flettes. Læs denne artikel for at forstå, hvordan du skal arbejde med hente anmodning korrekturlæsere, og hvordan du kan oprette hente anmodninger, der er nemmere og hurtigere at gennemgå så hente anmodningskøen fungerer bedre for alle.

## <a name="working-with-pull-request-reviewers"></a>Arbejde med hente anmodning korrekturlæsere

Her er de grundlæggende funktioner, du bør vide om at arbejde med hente anmodning korrekturlæsere. 

- <b>Forstå hente anmodning korrekturlæser rolle. Korrekturlæser:</b>
  - Sikrer, at grundlæggende kvaliteten af indholdet
  - Forhindrer regressioner i lageret
  - Giver feedback før fletning

  Hente anmodning korrekturlæsere er i en indhold styring rolle. Primære hensigten er ikke at flette blot det sendes så hurtigt som muligt. Forvent feedback, der kræver, at du foretage opdateringer, især til nye og stærkt reviderede artikler.

- <b>Planlægge med din hente anmodning korrekturlæser:</b>
  - Høj prioritet hente anmodninger om
  - Hente anmodninger om fik/dateret versioner
  - Hente anmodninger, ændre eller tilføje masser af filer

- <b>SLA for hente anmode om Gennemse</b>

  I den private lager forsøger hver gang hente anmodningen indsætter hente anmodningskøen med navnet er du klar til at flette teamet at gennemse hente anmodningen inden for 12 timer (M + F, 8 AM til 5 PM) og give feedback eller flette Hvis ingen feedback er påkrævet. Denne SERVICENIVEAUAFTALE gælder for kunsten at gennemgå kurs, ikke fletning af den. PRs flettes, når de opfylder [kriterierne til fletning](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Gøre hente anmodningskøen fungere bedre for alle

Der er to grundlæggende realiteter i køen kurs:

- Hente anmodninger, der er små i omfang og, der indeholder minder meget om ændringer tage mindre tid til at gennemgå. 
- Hente anmodninger, der er stort omfang eller, der indeholder forskellige, blandede typer ændringer tage længere tid at gennemse.

Du kan gøre hente anmodningskøen fungere bedre ved at følge disse bedste fremgangsmåder:

- Separate mindre opdateringer til eksisterende artikler fra nye artikler eller overordnede de. Arbejde på disse ændringer i separate arbejde forgreninger. 

- Når du sletter artikler eller billeder, ikke blande sletningerne med indhold tilføjelser eller opdateringer. Håndtere ændringer/nyt indhold i en separat arbejde forgrening.

- Versioner eller opstod af indhold, skal du planlægge videre med dine kurs korrekturlæser. Du skal muligvis hans eller hendes hjælp til at oprette en version gren eller til at koordinere Flet gange med publicering af gange, så dit indhold er blevet publiceret på det rette tidspunkt.

- Hvis du forsøger at koordinere opdateringer, der er foretaget i ACOM repo (ie, skifter til venstre navigationsrude skal lander sider, omdirigeringer eller learning kort) med ændringer, du foretager i azure-indhold-kurs lager, skal du være koordineret, der fungerer forvejen med din kurs korrekturlæser. Ellers risikerer du har en masse brudte kæder.

## <a name="criteria-for-expedited-pull-requests"></a>Kriterier for hurtig hente anmodninger

- Kontakt azdocprs at fremskynde PRs kun, når det er absolut nødvendigt. Du kan anmode om hurtig kurs håndtering af til rød Zone, beskyttelse af personlige oplysninger, juridiske og sikkerhedsproblemer; for virkelig brudte kunde oplevelser; og til ledelsen eskaleringer. 
- Indhold i funktion versioner kvalificerer ikke til hurtig håndtering af - funktion release indhold kræver forudgående planlægning eller den skal håndteres via kø standard prioritet.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>I en fart? Sende PRs, der kan accepteres automatisk

Brug PRMerger automatisering regler til at få flere af dine daglige PRs flettede automatisk.

PRMerger kan acceptere din kurs automatisk, hvis:
* Det påvirker 10 filer eller færre.
* Den indeholder 15 anvendelser eller færre.
* Mindre end 20% af tekstændringer.
* Vælgere/switchers ikke er opdateret.
* Ingen filer er slettet eller tilføjet.
* Ingen billeder er nye, ændret eller slettet.

Hvis mødeindkaldelsen hente ikke opfylder kriterierne, tildeles etiketten "PRmerger kan ikke flette" automatisk så du ved, at det kræver vurdering af en human kurs korrekturlæser.

### <a name="need-to-make-a-lot-of-little-changes"></a>Brug at sikre en masse lille ændringer?

Tage dine kø fra ovenstående PRMerger automatisering regler, og gør følgende:
* Sende artikler med light ændringer sammen i en kurs med 10 eller færre filer.
* Oprette en separat kurs til artikler i hvilke billeder eller vælgere ændring. Dette kræver med mennesker på Gennemse.
* Oprette en separat kurs for nye eller slettede artikler. Dette kræver med mennesker på Gennemse.

## <a name="related"></a>Relateret

- [Gennemse kvalitetskriterier for hente anmodning](contributor-guide-pr-criteria.md)

- [Trække anmodning kommentar automatisering](contributor-guide-pull-request-comments.md)
