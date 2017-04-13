<properties
    pageTitle="Konfigurere et domænenavn til din Blob storage slutpunkt | Microsoft Azure"
    description="Lær at knytte et brugerdefinerede domæne til Blob storage slutpunkt for en Azure-lager-konto i portalen Azure klassisk."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Konfigurere et brugerdefineret domænenavn til Blob storage første eller sidste ark

## <a name="overview"></a>Oversigt

Du kan konfigurere et brugerdefineret domæne til at få adgang til blob-data i kontoen Azure-lager. Standardslutpunkt for Blob-lager er `<storage-account-name>.blob.core.windows.net`. Hvis du knytte et brugerdefineret domæne og underdomæne som **www.contoso.com** til blob slutpunkt for kontoen lagerplads, og klik derefter dine brugere kan også access blob-data i din lagerplads konto ved hjælp af domænet.

>[AZURE.IMPORTANT] Azure-lager understøtter ikke endnu HTTPS med brugerdefinerede domæner. Vi har kendskab, kunder er interesseret i denne funktion, og det vil være tilgængelig i en senere version.

Der er to måder til at pege blob slutpunkt for kontoen lagerplads dit eget domæne. Den nemmeste måde er at oprette en tilknytning af dit eget domæne og underdomæne til blob slutpunktet CNAME-post. En CNAME-post er en DNS-funktion, der knytter et kilde domæne til en destinationsdomænet. I dette tilfælde kildedomænet er dit brugerdefinerede domæne og underdomæne – Bemærk, at underdomænet er altid er påkrævet. Destinationsdomænet er din Blob tjenesteslutpunkt.

Processen med at tilknytte dit brugerdefinerede domæne til din blob slutpunkt kan dog resultere i en kort periode nedetid for domænenavn, mens du registrerer for domænet på [Azure klassisk Portal](https://manage.windowsazure.com). Hvis dit brugerdefinerede domæne understøtter i øjeblikket et program med en serviceniveauaftale (SLA), der kræver, at der ikke nogen nedetid, kan du bruge Azure **asverify** underdomænet til at levere en mellemliggende registrering trin, så brugerne kan få adgang til dit domæne under DNS tilknytning finder sted.

Den følgende tabel viser eksempel URL-adresser for at få adgang til blob-data i en lagerplads konto med navnet **mystorageaccount**. Dit eget domæne, der er registreret for kontoen lagerplads er **www.contoso.com**:

Ressourcetype|URL-adressen formater
---|---
Lagerplads konto|**Standard URL-adresse:** http://mystorageaccount.blob.core.windows.net<p>**Brugerdefineret domæne URL-adresse:** http://www.contoso.com</td>
BLOB|**Standard URL-adresse:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**Brugerdefineret domæne URL-adresse:** http://www.contoso.com/mycontainer/myblob
Rod objektbeholder|**URL-adresse:** http://mystorageaccount.blob.core.windows.net/myblob eller http://mystorageaccount.blob.core.windows.net/$ rod/myblob<p>**Brugerdefineret domæne URL-adresse:** http://www.contoso.com/myblob eller http://www.contoso.com/$ rod/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registrere et brugerdefineret domæne til kontoen lagerplads

Brug denne procedure til at registrere dit eget domæne, hvis du ikke har problemer, hvordan du får domænet, være kortvarigt er ikke tilgængelige for brugere, eller hvis dit eget domæne ikke er i øjeblikket er vært for et program.

Hvis dit brugerdefinerede domæne understøtter i øjeblikket et program, der ikke kan have en hvilken som helst nedetid, skal du bruge fremgangsmåden i <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">registrere et brugerdefineret domæne til din lagerplads konto ved hjælp af mellemliggende asverify underdomæne</a>.

Hvis du vil konfigurere et brugerdefineret domænenavn, skal du oprette en ny CNAME-post med din domæneregistrator. CNAME-posten angiver et alias for et domænenavn i dette tilfælde knytter den adressen på dit eget domæne til Blob storage slutpunkt for kontoen lagerplads.

Hver registrator har en ligner, men nogle lidt andre metode til at angive en CNAME-post, men som er den samme. Bemærk, at mange grundlæggende domæne registrering pakker ikke leverer DNS-konfiguration, så du skal muligvis opgradere dit domæne registreringspakke, før du kan oprette CNAME-posten.

1.  Gå til fanen **Storage** i [Azure klassisk Portal](https://manage.windowsazure.com).

2.  Klik på navnet på lagerplads konto, du vil tilknytte dit eget domæne i fanen **Storage** .

3.  Klik på **Konfigurer** .

4.  Klik på **Manage Domain** for at få vist dialogboksen **Administrer brugerdefinerede domæne** i bunden af skærmen. I feltet tekst øverst i dialogboksen, får du vist oplysninger om at oprette CNAME-posten. Ignorere den tekst, der refererer til **asverify** underdomænet for denne procedure.

5.  Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Du kan finde det i en sektion som **Domænenavn**, **DNS**eller **Name Server Management**.

6.  Find sektionen til administration af CNAMEs. Du kan være nødvendigt at gå til en side, avancerede indstillinger, og se efter ordene **CNAME** **Alias**eller **underdomæner**.

7.  Oprette en ny CNAME-post, og Giv et underdomæne alias, som **www** eller **billeder**. Giv dernæst et værtsnavn, som er dit Blob tjenesteslutpunkt, i formatet **mystorageaccount.blob.core.windows.net** (hvor **mystorageaccount** er navnet på din lagerplads konto). Værtsnavnet for at bruge er angivet for dig i teksten i dialogboksen **Administrer brugerdefinerede domæne** .

8.  Når du har oprettet CNAME-posten, vende tilbage til dialogboksen **Administrer eget domæne** , og skriv navnet på dit eget domæne, herunder underdomænet, i feltet **Brugerdefinerede domænenavn** . Eksempelvis hvis domænet er **contoso.com** og et underdomæne er **www**, angive **www.contoso.com**; Hvis dit underdomæne er **fotos**, skal du angive **photos.contoso.com**. Bemærk, at underdomænet er påkrævet.

9. Klik på knappen **Registrer** for at registrere dit eget domæne.

    Hvis registreringen er gået igennem, får du vist meddelelsen **dit eget domæne er aktiv**. Brugerne kan nu få vist blob oplysninger om dit eget domæne, så længe de har de nødvendige tilladelser.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registrere et brugerdefineret domæne til din lagerplads konto ved hjælp af mellemliggende asverify underdomæne

Brug denne procedure til at registrere dit brugerdefinerede domæne Hvis dit brugerdefinerede domæne understøtter i øjeblikket et program med en SERVICENIVEAUAFTALE, der kræver, at der ingen nedetid. Ved at oprette en CNAME, der peger fra asverify. &lt;underdomæne&gt;. &lt;customdomain&gt; til asverify. &lt;storageaccount&gt;. blob.core.windows.net, du kan allerede har registreret dit domæne med Azure. Du kan derefter oprette en anden CNAME, der peger fra &lt;underdomæne&gt;. &lt;customdomain&gt; til &lt;storageaccount&gt;. blob.core.windows.net, hvorefter trafik til dit eget domæne, blive omdirigeret til dit blob slutpunkt.

Asverify underdomæne er en speciel underdomæne genkendes af Azure. Ved prepending **asverify** til din egen underdomæne, du giver tilladelse Azure at genkende dit brugerdefinerede domæne uden at ændre DNS-posten for domænenavn. Når du ændrer DNS-posten for domænenavn, kan det være knyttet til blob slutpunktet uden nedetid.

1.  Gå til fanen **Storage** i [Azure klassisk Portal](https://manage.windowsazure.com).

2.  Klik på navnet på lagerplads konto, du vil tilknytte dit eget domæne i fanen **Storage** .

3.  Klik på **Konfigurer** .

4.  Klik på **Manage Domain** for at få vist dialogboksen **Administrer brugerdefinerede domæne** i bunden af skærmen. I feltet tekst øverst i dialogboksen, får du vist oplysninger om at oprette CNAME-posten ved hjælp af **asverify** underdomæne.

5.  Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Du kan finde det i en sektion som **Domænenavn**, **DNS**eller **Name Server Management**.

6.  Find sektionen til administration af CNAMEs. Du kan være nødvendigt at gå til en side, avancerede indstillinger, og se efter ordene **CNAME** **Alias**eller **underdomæner**.

7.  Oprette en ny CNAME-post, og Giv et underdomæne alias, der indeholder asverify underdomænet. For eksempel være det underdomæne, du angiver i formatet **asverify.www** eller **asverify.photos**. Giv dernæst et værtsnavn, som er dit Blob tjenesteslutpunkt, i formatet **asverify.mystorageaccount.blob.core.windows.net** (hvor **mystorageaccount** er navnet på din lagerplads konto). Værtsnavnet for at bruge er angivet for dig i teksten i dialogboksen **Administrer brugerdefinerede domæne** .

8.  Når du har oprettet CNAME-posten, vende tilbage til dialogboksen **Administrer eget domæne** , og skriv navnet på dit eget domæne i feltet **Brugerdefinerede domænenavn** . Eksempelvis hvis domænet er **contoso.com** og et underdomæne er **www**, angive **www.contoso.com**; Hvis dit underdomæne er **fotos**, skal du angive **photos.contoso.com**. Bemærk, at underdomænet er påkrævet.

9.  Klik på afkrydsningsfeltet, hvor der står **Avanceret: Brug 'asverify' underdomæne til at preregister mit eget domæne**.

10. Klik på knappen **Registrer** for at preregister dit eget domæne.

    Hvis forudregistrering er gået igennem, får du vist meddelelsen **dit eget domæne er aktiv**.

11. På dette tidspunkt dit eget domæne er blevet godkendt af Azure, men er ikke dirigeres trafik til dit domæne til kontoen lagerplads. Gå tilbage til DNS-udbyderens websted for at fuldføre processen, og Opret en anden CNAME-post, der knytter et underdomæne til din Blob tjenesteslutpunkt. For eksempel angive underdomænet som **www** eller **fotos**og hostname som **mystorageaccount.blob.core.windows.net** (hvor **mystorageaccount** er navnet på din lagerplads konto). Registrering af dit eget domæne er færdig med dette trin.

12. Til sidst, kan du slette den CNAME-post, du har oprettet ved hjælp af **asverify**, som den var nødvendige kun som en mellemliggende trin.

Brugerne kan nu få vist blob oplysninger om dit eget domæne, så længe de har de nødvendige tilladelser.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Kontrollér, at dit eget domæne henviser Blob service første eller sidste ark

Oprette en blob i en offentlig objektbeholder i kontoen lagerplads for at bekræfte, at dit brugerdefinerede domæne faktisk er knyttet til din Blob tjenesteslutpunkt. Brug derefter i en webbrowser, en URI i følgende format til at få adgang til blob:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

For eksempel kan du bruge følgende URI at få adgang til en webformular via et **photos.contoso.com** brugerdefinerede underdomæne, der er tilknyttet en blob i din **myforms** beholder:

-   http://Photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Fjerne registreringen af et brugerdefineret domæne fra din lagerplads konto

For at fjerne registreringen af et brugerdefineret domæne, skal du følge disse trin: 

1. Log på [Azure klassisk Portal](https://manage.windowsazure.com). 

2. Klik på **lager**i navigationsruden. 

3. Klik på navnet på lagerplads konto til at vise dashboard på siden **lagerplads** . 

5. Klik på **Manage Domain**på båndet. 

6. Klik på **fjernelse af registrering**i dialogboksen **Administrer brugerdefinerede domæne** . 


## <a name="additional-resources"></a>Yderligere ressourcer

-   [Sådan tilknyttes brugerdefineret domæne til indhold levering netværk (CDN) slutpunkt](../cdn/cdn-map-content-to-custom-domain.md)
