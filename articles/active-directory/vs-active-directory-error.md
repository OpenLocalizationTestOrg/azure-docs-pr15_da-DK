<properties 
    pageTitle="Fejl under registrering af godkendelse" 
    description="Guiden active directory forbindelse fundet en kompatibel godkendelsestype" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="error-during-authentication-detection"></a>Fejl under registrering af godkendelse

Guiden registreret under registrering af forrige godkendelse kode, en inkompatible godkendelsestype.   

##<a name="what-is-being-checked"></a>Hvad er der markeret?

**Note:** For at registrere korrekt forrige godkendelse kode i et projekt, skal der oprettes projektet.  Hvis du er stødt på denne fejl, og du ikke har en tidligere godkendelse kode i dit projekt, genopbygge og prøve igen.

###<a name="project-types"></a>Projekttyper

Guiden kontrollerer, hvilken type projekt, du udvikler så den kan indsætte den rigtige godkendelse logik i projektet.  Hvis der er en hvilken som helst controller, der stammer fra `ApiController` i projektet, kan projektet betragtes som et WebAPI projekt.  Hvis der er kun de enheder, der stammer fra `MVC.Controller` i projektet, kan projektet betragtes som et MVC projekt.  Andet understøttes ikke af guiden.  WebForms projekter understøttes ikke i øjeblikket.

###<a name="compatible-authentication-code"></a>Kompatible godkendelse kode

Guiden kontrollerer også for godkendelsesindstillinger, der tidligere er konfigureret med guiden eller er kompatible med guiden.  Det betragtes som en med indlejret sag og guiden åbnes og viser indstillingerne, hvis alle indstillinger er til stede.  Hvis der kun nogle af indstillingerne er til stede, betragtes det som en fejl sag.

I et projekt MVC kontrollerer guiden på grund af følgende indstillinger, der fremkommer forrige brug af guiden:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Desuden kontrollerer guiden på grund af følgende indstillinger i et Web API-projekt, der fremkommer forrige brug af guiden:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Inkompatible godkendelse kode

Til sidst skal forsøger guiden at registrere versioner af godkendelse kode, der er konfigureret med tidligere versioner af Visual Studio. Hvis du har modtaget denne fejl, betyder det projektet indeholder en inkompatible godkendelsestype. Guiden registrerer følgende typer af godkendelse fra tidligere versioner af Visual Studio:

* Windows-godkendelse 
* Individuelle brugerkonti 
* Organisationskonti 
 

Hvis du vil registrere Windows-godkendelse i et MVC projekt, guiden søger efter den `authentication` element fra **web.config** -filen.

<pre>
    &lt;konfiguration af&gt;
        &lt;sektionsgruppen&gt;
            <span style="background-color: yellow">&lt;godkendelsestilstand = "Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Hvis du vil registrere Windows-godkendelse i et Web API-projekt, guiden søger efter den `IISExpressWindowsAuthentication` element fra dit projekt **som indeholder .csproj** fil:

<pre>
    &lt;Project&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;aktiveret&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/projekt        &gt;
</pre>

Guiden søger for at registrere individuelle brugerkonti godkendelse for elementet pakke fra filen **Packages.config** .

<pre>
    &lt;pakker&gt;
        <span style="background-color: yellow">&lt;pakke id="Microsoft.AspNet.Identity.EntityFramework" version = "2.1.0" targetFramework = "net45" /&gt;</span>
    &lt;/pakker&gt;
</pre>

Hvis du vil finde en gamle formular Organisationskonto godkendelse, søger guiden efter følgende element fra **web.config**:

<pre>
    &lt;konfiguration af&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;føje nøgle = "ida: startdomænet" værdi = "***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Hvis du vil ændre godkendelsestypen, fjerne den inkompatible godkendelsestype og køre guiden igen.

Du kan finde flere oplysninger [Godkendelse scenarier, hvor Azure AD](active-directory-authentication-scenarios.md).