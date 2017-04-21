# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Trin til at følge, når du går på pension eller ændre navnet på en ACOM tekniske artikel

Denne vejledning er for små og mellemstore virksomheder, der er angivet som forfatter af en artikel, der skal udgå fra sektionen tekniske dokumentation for azure.microsoft.com. Fremgangsmåden gælder også, hvis filen er blevet omdøbt.

Hvis du er medlem af vores Azure community, og du mener, at en artikel skal udgå for en eller anden grund, skal du skrive en kommentar i Disqus kommentar strømmen til artiklen for at lade forfatteren vide noget er der galt med artiklen.

Små og mellemstore Virksomheders forfattere skal følge flere trin for at trække problemfrit indhold, så brugere af webstedet ikke har en forkert oplevelse, når vi går på pension indhold fra webstedet. Slette artiklen eller ændre dets navn skal være det sidste, der sker!

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Trin 1: Angiv artiklen til Nej-indeks/Nej-opfølgning og genudgive det (anbefales)

Det første, du skal gøre, er genpublicere artiklen som ingen indeks/Nej-følge et par uger, før du slette den. Det betragtes som bedste praksis "foreløbig arbejdet" for pension indhold. Dette fjerner artiklen fra program søgeindeks, så folk ikke kan finde artiklen i Søg. [Se den interne wiki få mere at vide.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Trin 2: Administrere indgående links (påkrævet)

Finde ud af, om der er en ikke-Microsoft indgående links til dit indhold. Ofte, blogge, fora og andet indhold på internettet peger på artikler. Ofte, kan du arbejde med blog ejere, du vil ændre disse links, og du kan fjerne eller opdatere links fra forumindlæg. Web analytics-værktøjer kan fortælle dig, hvis der er stor trafik indgående kæder det være nødvendigt at administrere på denne måde.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Trin 3: Fjerne alle crosslinks til artiklen fra tekniske indhold lageret (påkrævet)

Ikke stole på omdirigeringer til tager sig af crosslinks fra andre artikler. Opdater eller fjern krydset henvisninger til artiklen du slette eller omdøbe, herunder i artikler ejes af andre personer.

1. Sikre, at du arbejder i en opdateret lokale gren – Kør `git pull upstream master` (eller den relevante variation denne kommando.

2.  Scanne mappen azure-indhold-kurs/artikler og mappen azure-indhold-kurs/omfatter til en artikel og omfatter med hyperlinks til en artikel, du vil trække tilbage, og enten at fjerne crosslinks eller erstatte dem med en passende nye crosslinks. Du kan bruge en Søg og Erstat for at finde crosslinks, hvis du har en installeret. Hvis du ikke gør det, kan du bruge Windows PowerShell gratis! Her er, hvordan du kan bruge PowerShell til at finde crosslinks:

 en. Start Windows PowerShell.

 b. Ændre til mappen azure-indhold-pr\articles PowerShell for kommandoprompten:

 `cd azure-content-pr\articles`

 c. Skriv følgende kommando, som viser alle filer, der indeholder en reference til i artiklen, du vil slette:

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Hvis du foretrækker at sende på listen over filnavne til en tekstfil (i dette tilfælde, navngivet psoutput.txt), kan du:

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Tilføje og acceptere alle ændringerne, overføre dem til din forgreningsovergang og hente anmode om at flytte dine ændringer fra din forgreningsovergang til den overordnede gren i den primære lager.

## <a name="step-4-update-the-fwlink-tool-required"></a>Trin 4: Opdatere værktøjet FWLink (påkrævet)

Se værktøjet FWLink for en hvilken som helst FWLinks, der muligvis henviser til artiklen. Peg en hvilken som helst FWLinks erstatning indhold; Hvis du ikke er på det alias, der ejer linket, kan du deltage i den. Hvis ejerne ikke opdatere linket, skal du fil en brugertilladelse med MSCOM have linket ændres. Flere oplysninger - [interne wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Trin 5: Fjerne alle crosslinks til artiklen fra andre sider på azure.microsoft.com og oprette en omdirigering for siden udgåede Hvis relevante (påkrævet)

Du skal have til at arbejde med den person, der vedligeholder og opdaterer landingssiden dokumentation til din tjeneste for denne del. Kontakt din partner indhold team, hvis du ikke ved, hvem der vedkommende er. Den person, der vedligeholder og opdaterer landingssiden dokument skal gøre to ting:

1. Gennemse **hele** ACOM web løsningen til krydsreferencer til den fil, der går på pension i Visual Studio. Fjern de krydshenvisning, eller erstatte dem med en opdateret krydsreference. Du skal fjerne HTML-links samt de relaterede ressourcestrenge for HTML-links. Flere oplysninger - se [interne wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Hvis der findes en artikel til erstatning, kan du oprette en omdirigering. Flere oplysninger - se [interne wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Markér feltet ændringer til lageret.

## <a name="step-6-retire-the-article"></a>Trin 6: Går på pension i artiklen

Når du har fuldført de foregående trin, og disse ændringer er direkte, kan du slette artiklen fra lageret. 

**Vigtige:** Når du sletter filer, skal du bruge den `git add --all` kommandoen.

## <a name="step-7-remove-links-from-msdn-required"></a>Trin 7: Fjern links fra MSDN (påkrævet)

Gennemse indhold QA værktøjet for brudte hyperlinks til emnet udgået eller omdøbt, og fjern/fix linkene i alle MSDN-emner, der påvirkes.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Trin 8: Fjern cachelagrede sider fra søgemaskiner (kun hvis det er absolut nødvendigt)

Gøre denne kun, hvis indholdet skal fjernes hurtigt på grund af juridiske og dårlige kundeproblemer. Per bedste fremgangsmåder fra Google skal normal prioritet siden sletninger lige håndteres af neutralt Søg program processer. Gå til disse websider til at fjerne cachelagrede websider fra søgemaskiner:

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Bidragydere vejledning links

- [Oversigt over artikel](./../README.md)
- [Indeks over vejledning artikler](./contributor-guide-index.md)
