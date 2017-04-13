<properties
    pageTitle="Arbejde med pålidelig samlinger | Microsoft Azure"
    description="Lær de bedste fremgangsmåder til at arbejde med pålidelig af websteder."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Arbejde med pålidelig af websteder

Tjenesten strukturen tilbyder en med høj sikkerhed programming model til .NET udviklere via pålidelige af websteder. Nærmere betegnet kan indeholder Service-strukturen pålidelig ordbog og pålidelig kø klasser. Når du bruger disse klasser, er din stat opdelt (for skalerbarhed), replikeres (for tilgængelighed) og overført i en partition (for SURT semantik). Lad os se på en typisk brugen af en pålidelig dictionary-objektet, og se, hvilke dens faktisk sig.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Alle handlinger på pålidelige ordbog objekter (med undtagelse af ClearAsync, ikke kan fortrydes), kræver et ITransaction objekt. Dette objekt er knyttet til den nogen og alle ændringer, du forsøger at gøre til en hvilken som helst pålidelig ordbog og/eller pålidelige kø objekter inden for en enkelt partition. Du får fat på en ITransaction objekt ved at ringe til partitionen er Statemanager's CreateTransaction metode.
 
I ovenstående kode, er objektet ITransaction sendt til en pålidelig ordbog AddAsync metode. Internt tage ordbog metoder, der accepterer en nøgle en læser/skriver Lås, der er knyttet til tasten. Hvis metoden ændrer den nøgleværdi, metoden kræver en skrivelås på tasten, og hvis metoden læser kun fra den nøgleværdi, derefter en låst er udført på tasten. Da AddAsync ændrer nøgleværdi til den nye, overføres i værdi, benyttes på tasten skrivelås. Så, hvis 2 (eller flere) tråde forsøger at tilføje værdier med den samme nøgle ad gangen, én tråd opnår Skriv låsen og andre tråde blokerer. Som standard metoder blokere i op til 4 sekunder at låse; metoderne Udløs en TimeoutException efter 4 sekunder. Metode overloads findes, så du kan overføre en eksplicit timeoutværdien, hvis du foretrækker.
 
Som regel, skal du skrive din kode for at reagere på en TimeoutException ved fangst den og prøver hele igen (som vist i ovenstående kode). Jeg foretager blot opkald Task.Delay, der passerer 100 millisekunder hver gang i min enkel kode. Men i virkeligheden, kan du muligvis bedre tjent med en slags eksponentiel tilbage fra forsinkelse i stedet.
 
Når låsen er hentet, tilføjer AddAsync objektreferencer nøgle og værdi til en intern midlertidige ordbog, der er knyttet til objektet ITransaction. Dette er gjort for at give dig med læse-dine-ejer-skriver semantik. Det vil sige, når du har kaldt AddAsync, returnerer et nyere opkald til TryGetValueAsync (med det samme objekt ITransaction) værdien selvom du ikke har endnu er sendt posteringen. Derefter AddAsync serializes din nøgle og værdi objekter til byte matrixer og føjer disse byte matrixer til en logfil på den lokale node. Til sidst skal sender AddAsync byte matrixer til alle sekundær kopier, så de har de samme nøgleværdi/oplysninger. Selvom nøgleværdi/oplysningerne er skrevet i en logfil, betragtes oplysningerne ikke som en del af ordbogen, indtil den transaktion, som de er tilknyttet er blevet udført. 

I ovenstående kode bekræfter opkaldet til CommitAsync alle de transaktion handlinger. Nærmere betegnet kan vedhæftes logfilen på den lokale node angiver oplysninger og sender også posten Anvend til alle sekundær kopier. Når en quorum (størstedelen) af replikaerne har svaret, alle dataændringer betragtes som permanent og eventuelle låse, der er knyttet til nøgler, som blev redigeres via objektet ITransaction frigives, så andre tråde/transaktioner kan manipulere de samme taster og deres værdier.

Hvis CommitAsync ikke kaldes (som regel på grund af en undtagelse, der udløst), bliver objektet ITransaction fjernet. Ved salg et ikke-bekræftede ITransaction-objekt, Service-strukturen føjer Afbryd oplysninger til den lokale node logfil, og det ikke nødvendigt at blive sendt til en af de sekundære replikaer. Og derefter eventuelle låse, der er knyttet til nøgler, som blev redigeres via posteringen er udgivet.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Almindelige faldgruber, og hvordan du undgår dem.
Nu hvor du forstå, hvordan de pålidelige samlinger fungerer internt, Lad os se nærmere på nogle almindelige misuses stk.. Se nedenstående kode:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Når du arbejder med en almindelig .NET-ordbog, kan du føje en nøgleværdi til ordbogen og derefter ændre værdien af en egenskab (såsom LastLogin). Men denne kode fungerer ikke korrekt med en pålidelig ordbog. Husk fra den tidligere diskussion opkaldet til AddAsync serializes nøgleværdi/objekter til byte matrixer og derefter gemmer matrixer til en lokal fil og også sender dem til de sekundære replikaer. Hvis du senere ændrer en egenskab, ændrer dette værdien for egenskaben i hukommelsen. den påvirker ikke den lokale fil eller de data, der er sendt til replikaerne. Hvis processen går ned, er hvad der er hukommelse udløst ikke til stede. Når en ny proces starter, eller hvis en anden replika bliver primære, og derefter den gamle egenskabsværdi er, hvad der er tilgængeligt. 

Jeg kan ikke understrege nok hvor let, som det er at gøre slags fejl ovenstående. Og du kan kun få mere at vide om fejlen Hvis/når processen går ned. Den korrekte måde at skrive koden er ganske enkelt at fortryde de to linjer:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Her er et andet eksempel, der viser en meget almindelig fejl:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

Igen, med almindelige .NET ordbøger ovenstående kode fungerer fint og er et almindelige mønster: udvikleren anvender en nøgle til at slå en værdi op. Hvis værdien findes, ændres udvikleren en egenskabsværdi. Denne kode opfører dog med pålidelig samlinger, det samme problem, som allerede nævnt: __du må ikke redigere et objekt, når du har givet til en pålidelig samling.__
 
Den korrekte måde at opdatere en værdi i en pålidelig samling, er at få en reference til den eksisterende værdi og overveje det objekt, der henvises til i denne fast reference. Derefter kan oprette et nyt objekt, som er en nøjagtig kopi af det oprindelige objekt. Du kan nu redigere tilstanden for denne nye objekt og skrive det nye objekt i gruppen af websteder, så det bliver serialiseres til byte matrixer føjet til den lokale fil og sendes til replikaerne. Når du gemmer ændringer, har objekterne i hukommelsen, den lokale fil og alle kopier samme nøjagtige tilstand. Alle er god!

Nedenstående kode viser den korrekte måde at opdatere en værdi i en pålidelig af websteder:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definere fast datatyper for at forhindre programmer fejl

Ideelt set vil vi have til at rapportere fejl, når du kommer til at producere kode, der mutates tilstanden for et objekt, du skal overveje fast. Men compileren C# har ikke mulighed for at gøre dette. Så, for at undgå mulige programmer fejl, anbefales det, at du definerer typen du bruger med pålidelig af websteder skal være fast typer. Nærmere betegnet kan det betyder, at du overholder du core værdityper (såsom tal [Int32, UInt64 osv.], DateTime, Guid, TimeSpan og lignende). Og selvfølgelig, du kan også bruge streng. Det er bedst at undgå egenskaber for samling som serialisering og deserialisering af dem kan ofte kan reducere ydeevnen. Men hvis du vil bruge egenskaber for samling, anbefales brugen af. Nettooversigts fast samlinger-bibliotek (System.Collections.Immutable). Dette bibliotek kan downloades fra http://nuget.org. Vi anbefaler også lukning dine klasser og foretage felter skrivebeskyttet tilstand, når det er muligt.

Typen brugeroplysninger nedenfor viser, hvordan du definerer en fast type at drage fordel af ovennævnte anbefalinger.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

Typen element-id er også en fast type, som vist her:

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>Skema versionsstyring (opgraderinger)

Internt sekventielt af pålidelige websteder ved hjælp af objekterne. Nettooversigts DataContractSerializer. Fortløbende objekterne er bevaret til den primære replika lokal disk og overføres også til de sekundære replikaer. Som din tjeneste bliver større, er det sandsynligvis skal du ændre slags data (skema) kræver, at din tjeneste. Du skal løse versioner af dine data med stor forsigtighed. Først og fremmest skal du altid kunne deserialisere gamle data. Nærmere betegnet kan det betyder, at din deserialiseringen kode skal være uendelig bagudkompatible: Version 333 af din tjenestekode skal kunne anvendes på data, der er placeret i en pålidelig samling af version 1 af din tjenestekode 5 år siden.

Desuden er tjenestekode opgraderede ét opgradering domæne ad gangen. Så under en opgradering har du to forskellige versioner af din tjenestekode, der kører samtidigt. Du skal undgå, at den nye version af din tjenestekode bruge det nye skema som ældre versioner af din tjenestekode ikke muligvis kunne håndtere det nye skema. Når det er muligt, skal du designe hver version af din tjeneste skal videresende kompatible ved 1 version. Nærmere betegnet kan betyder det, at V1 af din tjenestekode skal kunne du blot vil ignorere en hvilken som helst skemaelementer den ikke håndterer eksplicit. Det skal dog ikke kunne gemme alle data, der er det ikke eksplicit vide om og blot skrive det i igen ud af, når du opdaterer en ordbog nøgle eller værdi. 

>[AZURE.WARNING] Mens du kan redigere skemaet for en nøgle, skal du sikre dig, at din nøgle hashkode og algoritmer, der er lig med er stabil. Hvis du ændrer, hvordan en af disse algoritmer betjene, kan du ikke slå tasten inden for den pålidelige ordbog nogensinde igen.
  
Du kan også udføre hvad er typisk kaldes en 2-fasen opgradering. Med en 2-fasen opgradering, skal du opgradere din tjeneste fra V1 til V2: V2 indeholder koden, der ved, hvordan du håndterer nye skemaændringen, men ikke udføre denne kode. Når koden V2 læser V1 dataene, fungerer på den og skriver V1 data. Derefter, når opgraderingen er fuldført på tværs af alle opgradering domæner, du kan en eller anden måde signal til de kørende V2 forekomster, at opgraderingen er fuldført. (En måde at signal dette er at udrulle en konfiguration opgradering og dette er, hvad gør dette en 2-fasen opgradering). Nu kan V2 forekomster læse V1 data, konvertere den til V2 data, betjene på den og skrive det som V2 data. Når andre forekomster læser V2 data, de er ikke nødvendigt at konvertere den, lige betjene på den, og skrive V2 data ud.

## <a name="next-steps"></a>Næste trin
Se [Fremadkompatible Data aftaler](https://msdn.microsoft.com/library/ms731083.aspx)for at få mere for at vide om oprettelse af videresende kompatible datakontrakter.

Få bedste fremgangsmåder i forbindelse med versionsstyring datakontrakter under [Data kontrakt versionsstyring](https://msdn.microsoft.com/library/ms731138.aspx). 

For at lære at implementere version tolerant datakontrakter, skal du se [Version Tolerant serialisering tilbagekald](https://msdn.microsoft.com/library/ms733734.aspx). 

Hvis du vil lære at angive en datastruktur, som kan arbejde sammen på tværs af flere versioner, skal du se [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
