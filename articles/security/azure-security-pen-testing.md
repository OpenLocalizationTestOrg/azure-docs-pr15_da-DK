<properties
   pageTitle="Test af pen | Microsoft Azure"
   description="I artiklen indeholder en oversigt over markedsindtrængning testen (pentest), og hvordan udfører pentest mod dine apps, der kører i Azure infrastruktur."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Test af pen

En af de store fordele ved hjælp af Microsoft Azure til programmet test og implementering er, at du ikke behøver at udarbejde en lokal infrastruktur for at udvikle, teste og installere dine programmer. Alle infrastruktur er taget sig af i Microsoft Azure platformstjenester. Du behøver ikke at bekymre dig om rekvirering, indhente, og "omstikning og stabling" din egen lokale hardware.

Dette er velegnet – men du stadig har brug at sikre, at du udfører normal sikkerhed passende omhu, således. En af de ting, du skal gøre er markedsindtrængning teste de programmer, du installerer i Azure.

Du kan allerede ved, at Microsoft foretager [markedsindtrængning test af vores Azure-miljø](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Dette hjælper os med at forbedre vores platform og hjælper vores handlinger med hensyn til forbedring af sikkerhedskontrol, Introduktion til nye sikkerhedskontrol og forbedre vores sikkerhedsprocesser.

Vi ikke pen testprogram for dig, men vi forstår, at du ønsker og skal udføre pen forsøg på dine egne programmer. Skyldes det et godt, når du forbedre sikkerheden for dine programmer, du kan gøre hele Azure økosystemet mere sikkert.

Når du pen tester dine programmer, kan den se sådan et angreb til os. Vi [løbende overvåge](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) for at finde angreb mønstre og starter en hændelsen svar proces, hvis vi har brug for. Det hjælpe ikke dig, og det hjælpe ikke os, hvis vi udløse en hændelsen svar på grund af din egen forfaldsdato omhu pen test.

Sådan gør du?

Når du er klar til pen teste dine Azure-hostede programmer, du har brug at fortælle os. Når vi ved, at du skal udføre bestemte test, ikke vi ved et uheld lukke du (såsom blokerer IP-adressen, som du tester fra), så længe test er i overensstemmelse med Azure pen test vilkår og betingelser.
Standard test, som du kan udføre, omfatter:

- Test på slutpunkterne til at afdække [Åbn Web Application sikkerhed projekt (OWASP) øverste 10 svagheder](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Fuzz test](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) af slutpunkterne
- [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) af slutpunkterne

En type test, som du ikke kan udføre er alle slags [Nægtelse af Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) -angreb. Dette omfatter start af et DoS-angreb sig selv, eller udføre relaterede test, der kan finde ud af, vise eller simulere en hvilken som helst type DoS angreb.

Er du klar til at komme i gang med pen teste dine programmer, Microsoft Azure som vært? Hvis dette er tilfældet, skal du hoved på over til [Markedsindtrængning Test oversigt](https://security-forms.azure.com/penetration-testing/terms) siden (og klikke på knappen Opret et test anmode om nederst på siden. Du kan også finde flere oplysninger på pennen test vilkår og betingelser og nyttige links på, hvordan du kan rapportere sikkerhedsbrist, der er relateret til Azure eller en anden Microsoft-tjeneste.
