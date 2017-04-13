<properties
   pageTitle="Pålidelige Services beskeder | Microsoft Azure"
   description="Grundlæggende dokumentation til tjenesten strukturerede pålidelig Services meddelelser"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Pålidelige Services-beskeder

Beskeder tillader klienter at spore de ændringer, der foretages til et objekt, som de er interesseret i. To typer objekter understøtter beskeder: *Stabil tilstand overordnet* og *Pålidelig ordbog*.

Almindelige årsager til brug af meddelelser er:

- Bygning indtruffet visninger som sekundære indeks eller aggregeret filtrerede visninger af replikaens tilstand. Et eksempel er et sorteret indeks over alle nøgler i pålidelige ordbog.
- Afsendelse overvågningsdata, som antallet brugere, der er tilføjet i den seneste time.

Meddelelser, der er udløses, som en del af at anvende handlinger. På grund af, at håndteres meddelelser så hurtigt som muligt, og synkron begivenheder ikke bør omfatte en hvilken som helst dyrt.

## <a name="reliable-state-manager-notifications"></a>Pålidelig tilstand Manager beskeder

Pålidelig tilstand Manager leverer beskeder til følgende hændelser:

- Transaktion
    - Anvend
- Stat manager
    - Genopbygge
    - Tilføjelse af en pålidelig tilstand
    - Fjernelse af en pålidelig tilstand

Pålidelig tilstand Manager holder styr på de aktuelle inflight transaktioner. Den eneste ændring i tilstanden for posteringen, der får en meddelelse til at udløse er en post, der er anvendt.

Pålidelig tilstand Manager vedligeholder en samling af pålidelig stater som pålidelig ordbog og pålidelig kø. Pålidelig tilstand Manager udløses beskeder, når denne samling ændres: en pålidelig tilstand er tilføjet eller fjernet eller hele samlingen genopbygges.
Samlingen stabil tilstand Manager genopbygges i tre tilfælde:

- Gendannelse: Når en replika starter, genoprettes den forrige tilstand fra disken. I slutningen af gendannelse bruges **NotifyStateManagerChangedEventArgs** forespørgselsreglen en begivenhed, der indeholder sæt af gendannet stabil tilstand.
- Fuld kopi: før en replika kan deltage i konfigurationssættet, der er skal anlægges. Nogle gange kan kræver dette en fuldstændig kopi af stabil tilstand-chefens tilstand fra den primære replika skal anvendes på den inaktive sekundær replika. Pålidelig tilstand Manager på den sekundære replika bruger **NotifyStateManagerChangedEventArgs** forespørgselsreglen en begivenhed, der indeholder sæt af pålidelig stater, som den hentet fra den primære replika.
- Gendannelse: I genoprettelse efter nedbrud scenarier replikaens tilstand kan gendannes fra en sikkerhedskopi via **RestoreAsync**. I så fald bruger stabil tilstand Manager på den primære replika **NotifyStateManagerChangedEventArgs** forespørgselsreglen en begivenhed, der indeholder sæt af pålidelig stater, som det gendannet fra sikkerhedskopien.

Hvis du vil registrere for beskeder om og/eller tilstand manager meddelelser, skal du registrere med **TransactionChanged** eller **StateManagerChanged** begivenheder på pålidelige tilstand Manager. Et almindelige sted til at registrere med disse hændelseshandlere er parametre med høj sikkerhed-tjenesten. Når du har registreret på parametre, kan du ikke går glip af alle meddelelser, der er forårsaget af en ændring i **IReliableStateManager**levetid.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Hændelseshandler **TransactionChanged** bruger **NotifyTransactionChangedEventArgs** til at give oplysninger om begivenheden. Den indeholder egenskaben action (for eksempel **NotifyTransactionChangedAction.Commit**), der angiver typen ændring. Den indeholder også transaktionsegenskaben, der indeholder en reference til den post, der har ændret.

>[AZURE.NOTE] I dag, udløses **TransactionChanged** hændelser kun, hvis posteringen er anvendt. Handlingen svarer derefter til **NotifyTransactionChangedAction.Commit**. Men fremover, kan være opløftet begivenheder til andre typer Postændringer tilstand. Vi anbefaler kontrollere handlingen og hændelsen behandles kun, hvis det er et, som du forventer.

Følgende er en eksempel **TransactionChanged** hændelseshandler.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Hændelseshandler **StateManagerChanged** bruger **NotifyStateManagerChangedEventArgs** til at give oplysninger om begivenheden.
**NotifyStateManagerChangedEventArgs** har to underklasser: **NotifyStateManagerRebuildEventArgs** og **NotifyStateManagerSingleEntityChangedEventArgs**.
Du kan bruge egenskaben action i **NotifyStateManagerChangedEventArgs** til konverteres **NotifyStateManagerChangedEventArgs** til den korrekte underklasse:

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** og **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Følgende er et eksempel **StateManagerChanged** meddelelse handler.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Pålidelige ordbog beskeder

Pålidelige ordbog leverer beskeder til følgende hændelser:

- Genopbyg: Kaldes, når **ReliableDictionary** er gendannet dens tilstand fra en gendannet eller kopierede lokale tilstand eller et sikkerhedskopi.
- Ryd: Kaldes, når tilstanden for **ReliableDictionary** er ryddet via metoden **ClearAsync** .
- Tilføj: Kaldes, når et element er blevet føjet til **ReliableDictionary**.
- Opdater: Kaldes, når et element i **IReliableDictionary** er blevet opdateret.
- Fjern: Kaldes, når et element i **IReliableDictionary** er blevet slettet.

For at få besked via pålidelige ordbogen, skal du registrere med hændelseshandler **DictionaryChanged** på **IReliableDictionary**. Et almindelige sted til at registrere med disse hændelseshandlere er i **ReliableStateManager.StateManagerChanged** tilføje meddelelse.
Registrering, når **IReliableDictionary** føjes til **IReliableStateManager** sikrer, at du ikke går glip af alle meddelelser.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** er metoden eksempel, der ringer op til det foregående eksempel **OnStateManagerChangedHandler** .

Den foregående kode angiver grænsefladen **IReliableNotificationAsyncCallback** sammen med **DictionaryChanged**. Fordi **NotifyDictionaryRebuildEventArgs** indeholder en **IAsyncEnumerable** grænseflade – skal optælles asynkront – genopbygge meddelelser er udløses gennem **RebuildNotificationAsyncCallback** i stedet for **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] I den foregående kode, som en del af behandling af meddelelsen Genopbyg først tilstanden bevares aggregeret ikke er markeret. Fordi samlingen pålidelig genopbygges med en ny tilstand, er alle tidligere meddelelser irrelevante.

Hændelseshandler **DictionaryChanged** bruger **NotifyDictionaryChangedEventArgs** til at give oplysninger om begivenheden.
**NotifyDictionaryChangedEventArgs** har fem underklasser. Bruge egenskaben handling i **NotifyDictionaryChangedEventArgs** til konverteres **NotifyDictionaryChangedEventArgs** til den korrekte underklasse:

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** og **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Anbefalinger

- *Behøver* at udføre meddelelseshændelser så hurtigt som muligt.
- *Send ikke* udføre en hvilken som helst dyrt (for eksempel i/o-handlinger) som en del af synkron begivenheder.
- Handlingstypen tjekke *skal* , før du behandler begivenheden. Ny handlingstyper kan tilføjes i fremtiden.

Her er nogle ting, du skal huske på:

- Meddelelser, der er udløses, som en del af udførelse af en handling. For eksempel udløses en meddelelse om Gendan som det sidste trin i en gendannelse. En gendannelse kan ikke afsluttes, før hændelsen meddelelse behandles.
- Fordi meddelelser, der er udløses, som en del af de handlinger, der anvender, se klienter kun beskeder for lokalt anvendt handlinger. Og fordi handlinger er garanti for kun at være lokalt anvendt (logget med andre ord), de kan eller kan ikke fortrydes i fremtiden.
- En enkelt meddelelse udløses på vej Annuller Fortryd for hver anvendte handling. Det betyder, at hvis transaktion T1 omfatter Create(X), Delete(X) og Create(X), får du vist en meddelelse om oprettelse af X, en for at sletningen, og et til oprettelse igen, i rækkefølgen.
- Handlinger anvendes til transaktioner, der indeholder flere handlinger, i den rækkefølge, hvori de blev modtaget på den primære replika fra brugeren.
- Som en del af behandling af falsk fremdrift, kan nogle handlinger ikke fortrydes. Meddelelser er opløftet til sådanne Fortryd handlinger, ruller tilstanden for replikaen tilbage til en stabil punkt. En vigtig forskel på Fortryd meddelelser er, at hændelser, der har dublerede nøgler samles. Eksempelvis hvis transaktion T1 er der fortrydes, får du vist en enkelt meddelelse til Delete(X).

## <a name="next-steps"></a>Næste trin

- [Pålidelige af websteder](service-fabric-work-with-reliable-collections.md)
- [Pålidelige Services Hurtig start](service-fabric-reliable-services-quick-start.md)
- [Pålidelige Services sikkerhedskopiering og gendannelse (nedbrud)](service-fabric-reliable-services-backup-restore.md)
- [Udviklerreference for pålidelig af websteder](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
