<properties
    pageTitle="Selvstudium: Kryptere og dekryptere BLOB i Microsoft Azure-lager, med Azure-tasten samling | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem hvordan til at kryptere og dekryptere en blob med klientsiden kryptering til Microsoft Azure-lager med Azure-tasten samling."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Selvstudium: Kryptere og dekryptere BLOB i Microsoft Azure-lager, med Azure-tasten samling

## <a name="introduction"></a>Introduktion

Dette selvstudium omhandler, hvordan du kan gøre brug af klientsiden lagerplads kryptering med Azure-tasten samling. Det vejleder dig gennem hvordan til at kryptere og dekryptere en blob i et console til computeren ved hjælp af disse teknologier.

**Anslået tid at gennemføre:** 20 minutter

Oversigt oversigtsoplysninger om Azure-tasten samling, du [Hvad er Azure-tasten samling?](../key-vault/key-vault-whatis.md).

Se Oversigt over oplysninger om klientsiden kryptering til Azure-lager, [klientsiden kryptering og Azure nøgle samling til Microsoft Azure-lager](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium, skal du have følgende:

- En Azure-lager-konto
- Visual Studio 2013 eller nyere
- Azure PowerShell


## <a name="overview-of-client-side-encryption"></a>Oversigt over klientsiden kryptering

Se en oversigt over klientsiden kryptering til Azure-lager, [klientsiden kryptering og Azure nøgle samling til Microsoft Azure-lager](storage-client-side-encryption.md)

Her er en kort beskrivelse af, hvordan kryptering på klientsiden fungerer:

1. Azure-lager klienten SDK genererer en indhold krypteringsnøgle (CEK), som er en én gang gang symmetriske nøgle.
2. Kundedata er krypteret ved hjælp af denne CEK.
3. CEK er derefter ombrudt (krypteret) ved hjælp af vigtige krypteringsnøglen (KEK). KEK kan er identificeret med en nøgle-id'et og være en asymmetrisk vigtige par eller en symmetriske nøgle og kan administreres lokalt eller gemt i Azure-tasten samling. Lagerplads klienten selve har aldrig adgang til KEK. Det aktiverer lige algoritmen vigtige ombrydning, som leveres af nøgle samling. Kunder kan vælge at bruge brugerdefinerede udbydere i nøgle ombrydning/udpakning Hvis de vil..
4. De krypterede data derefter er overført til tjenesten Azure-lager.


## <a name="set-up-your-azure-key-vault"></a>Konfigurere din Azure-tasten samling
For at fortsætte med dette selvstudium, skal du gøre følgende fremgangsmåde, som er beskrevet i selvstudiet [Introduktion til Azure nøgle samling](../key-vault/key-vault-get-started.md):

- Oprette en vigtige samling af legitimationsoplysninger.
- Tilføje en nøgle eller hemmeligt til den vigtige samling af legitimationsoplysninger.
- Registrere et program med Azure Active Directory.
- Godkend program tilladelse til at bruge tasten eller hemmeligt.

Noter af ClientID og ClientSecret, der blev oprettet, da registrering af et program med Azure Active Directory.

Oprette begge nøgler i den vigtige samling af legitimationsoplysninger. Vi antager for resten af selvstudiet, at du har brugt følgende navne: ContosoKeyVault og TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Oprette et console-program med pakker og AppSettings

Oprette et nyt console-program i Visual Studio.

Tilføj det er nødvendigt nuget pakker i pakke Manager-konsollen.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Føje AppSettings til App.Config.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Tilføj følgende `using` sætninger, og Sørg for at tilføje en reference til system.Configuration i stor til projektet.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Tilføje en metode til at få et token console-program

Følgende metode bruges af nøgle samling klasser, der skal godkende giver adgang til dine vigtige samling.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Få adgang til lager og nøgle samling i dit program

Tilføj følgende kode i funktionen Main.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Vigtige samling objektmodeller
>
>Det er vigtigt at forstå, at der er faktisk to nøgle samling objektmodeller skal være opmærksom på: en er baseret på REST-API (KeyVault navneområde) og den anden er en udvidelse til klientsiden kryptering.

> Tasten samling klienten skal arbejde sammen med REST-API og forstår JSON Web nøgler og hemmeligheder for de to typer af ting, der er indeholdt i nøgle samling.

> Tasten samling udvidelser er klasser, der virker oprettet specifikt for klientsiden kryptering i Azure-lager. De indeholder en brugergrænseflade for nøgler (IKey) og klasser, der er baseret på en tast Resolver begrebet. Der er to IKey, du skal kende-installationer: RSAKey og SymmetricKey. Nu de foretager stemme overens med de ting, der er indeholdt i en samling af legitimationsoplysninger nøgle, men på dette tidspunkt de er uafhængig klasser (så nøgle og hemmeligt bliver hentet af nøgle samling klienten ikke implementerer IKey).


## <a name="encrypt-blob-and-upload"></a>Kryptere blob og overføre
Tilføj følgende kode for at kryptere en blob og overføre den til kontoen Azure-lager. Metoden **ResolveKeyAsync** , der bruges returnerer en IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


Følgende er et skærmbillede fra [Azure klassisk Portal](https://manage.windowsazure.com) for en blob, der er krypteret ved hjælp af klientsiden kryptering med en nøgle, der er gemt i nøgle samling. Egenskaben **KeyId** er URI for nøglen i nøgle samling af legitimationsoplysninger, der fungerer som KEK. Egenskaben **EncryptedKey** indeholder den krypterede version af CEK.

![Skærmbillede, der viser Blob metadata, der indeholder kryptering metadata][1]

> [AZURE.NOTE] Hvis du ser på BlobEncryptionPolicy parametre, får du vist, at det kan acceptere en nøgle og/eller en resolver. Vær opmærksom på, der lige nu du ikke kan bruge en resolver til kryptering, fordi det ikke er i øjeblikket ikke understøtter en standardnøgle.



## <a name="decrypt-blob-and-download"></a>Dekryptere blob og hente
Dekryptering er virkelig når ved hjælp af Resolver klasser mening. Tasten bruges til kryptering ID er knyttet til blob i dens metadata, så der er ingen grund til at hente den pågældende nøgle og huske tilknytningen mellem nøgle og blob. Du behøver kun at sikre, at tasten forbliver i nøgle samling.   

Privat nøgle for en RSA-nøgle forbliver i nøgle samling, så til dekryptering skal udføres, tasten krypteret fra de blob-metadata, der indeholder CEK sendes til tasten samling til dekryptering.

Tilføj følgende for at dekryptere blob, som du lige har overført.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Der er et par andre typer af oversættelsesfunktionerne at gøre det nemmere, herunder key management: AggregateKeyResolver og CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Brug tasten samling hemmeligheder
Den måde at bruge et hemmeligt med klientsiden kryptering er via klassen SymmetricKey, fordi en hemmeligheden er grundlæggende en symmetriske nøgle. Men som nævnt ovenfor, er ikke knyttet et hemmeligt i nøgle samling nøjagtigt til en SymmetricKey. Der er et par ting at forstå:


- Nøglen i en SymmetricKey skal være en fast længde: 128, 192, 256, 384 eller 512 bit.
- Nøglen i en SymmetricKey skal være Base64-kodet.
- En samling af legitimationsoplysninger nøgle hemmeligt, der skal bruges som en SymmetricKey skal have en indholdstype for "programmet/octet-stream" i nøgle samling.

Her er et eksempel i PowerShell for at oprette et hemmeligt i nøgle samling af legitimationsoplysninger, der kan bruges som en SymmetricKey.
Bemærk: Værdien hårdt kodet, $key, er til demonstration formål kun. I din egen programkode får du vil generere denne tast.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

I dit console-program, kan du bruge det samme opkald som før til at hente denne hemmeligt som en SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Det var det. Nyd!

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om brug af Microsoft Azure-lager med C#, [Microsoft Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Du kan finde flere oplysninger om Blob REST-API, [Blob Service REST-API](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Gå til [Microsoft Azure lagerplads-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)for de seneste oplysninger om Microsoft Azure-lager.


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
