<properties
    pageTitle="Azure Active Directory-B2C: Tilpasninger brugergrænsefladen (UI) | Microsoft Azure"
    description="Et emne på bruger brugergrænsefladen tilpasningsfunktioner i Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory-B2C: Tilpasse Azure AD B2C brugergrænsefladen (UI)

Brugeroplevelsen er største i et consumer adgang til computeren. Det er forskellen mellem en god computerprogram og en god og mellem blot aktive forbrugere og virkelig optaget dem. Azure Active Directory (Azure AD) B2C kan du tilpasse consumer tilmeldingsprocessen logon (*Se nedenstående bemærkning*), redigering, profil og sider med pixel perfekt kontrolelement til nulstilling af adgangskode.

> [AZURE.NOTE]
I øjeblikket lokale konti logon sider, accompaning adgangskoden Nulstil sider og bekræftelse mails kan tilpasses, kun ved hjælp af [virksomhed mærkning funktion](../active-directory/active-directory-add-company-branding.md) og ikke efter funktioner, der er beskrevet i denne artikel.

I denne artikel kan du læse om:

- Siden bruger brugergrænsefladen funktionen tilpasning.
- Et værktøj, der hjælper dig med at teste funktionen siden Brugergrænsefladen tilpasning ved hjælp af vores eksempler på indhold.
- Elementer på core Brugergrænsefladen i de forskellige typer sider.
- Bedste fremgangsmåder, når du benytter denne funktion.

## <a name="the-page-ui-customization-feature"></a>Funktionen til tilpasning af Brugergrænsefladen på siden

Du kan tilpasse udseendet af forbrugere tilmelding, log på med funktionen siden Brugergrænsefladen tilpasning, nulstilling af adgangskode og redigering af profil sider (ved at konfigurere [politikker](active-directory-b2c-reference-policies.md)). Dine brugere får problemfri oplevelser, når du vil flytte mellem programmet og sider vises ved Azure AD B2C.

I modsætning til andre tjenester, hvor UI-indstillinger er begrænset eller er kun tilgængelig via API'er, bruger Azure AD B2C en moderne (og nemmere) tilgang til siden Tilpasning af Brugergrænsefladen.

Sådan virker det: Azure AD B2C kører kode i din consumer browser og bruger en moderne fremgangsmåde kaldet [Tværs Origin ressource deling (CORS)](http://www.w3.org/TR/cors/) til at indlæse indhold fra en URL-adresse, du angiver i en politik. Du kan angive forskellige URL-adresser til forskellige sider. Koden flettes brugergrænsefladeelementer fra Azure AD B2C med det indhold, der er indlæst fra din URL-adresse, og viser siden til din forbruger. Alt, du skal gøre, er:

1. Oprette korrekt udformet HTML5 indhold med en `<div id="api"></div>` element (skal være et tomt element) placeret et sted i den `<body>`. Dette element mærker, hvor Azure AD B2C indholdet indsættes.
2. Hoste dit indhold på et HTTPS-slutpunkt (med CORS tilladt).
3. Typografi elementer på Brugergrænsefladen, som indsætter Azure AD B2C i.

## <a name="test-out-the-ui-customization-feature"></a>Teste funktionen Brugergrænsefladen tilpasning

Hvis du vil afprøve funktionen Brugergrænsefladen tilpasning ved hjælp af vores eksempel HTML og CSS-indhold, har vi angivet [et simpelt hjælper værktøj](active-directory-b2c-reference-ui-customization-helper-tool.md) , overførsler og konfigurerer eksempler på indhold på dit Azure Blob-lager.

> [AZURE.NOTE]
Du kan hoste dit Brugergrænsefladen indhold hvor som helst: på Web-servere, CDNs, AWS S3, deling filsystemet osv. Så længe indholdet er placeret på et offentligt tilgængeligt HTTPS-slutpunkt (med CORS tilladt), er du klar. Vi bruger Azure Blob-lager til vejledende formål.

### <a name="the-most-basic-html-content-for-testing"></a>Den mest grundlæggende HTML-indhold til test

Vist nedenfor, er den mest grundlæggende HTML indhold, som du kan bruge til at afprøve denne egenskab. Brug den samme hjælper værktøj, der findes tidligere til at overføre og konfigurere indholdet på dit Azure Blob-lager. Du kan kontrollere, at det grundlæggende, ikke-stiliserede knapper og formularfelter på hver side er viste og funktionelle.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Core brugergrænsefladeelementer i hver type af siden

I de følgende afsnit, kan du finde eksempler på HTML5 fragmenter, der Azure AD B2C flettes ind i den `<div id="api"></div>` element, der er placeret i dit indhold. **Kan ikke indsætte disse fragmenter i HTML 5 indholdet.** Tjenesten Azure AD B2C indsætter dem i på kørselstidspunktet. Brug disse eksempler til at designe dine egne typografiark.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Azure AD B2C indhold, der er indsat på "identitet udbyder markering siden"

Denne side indeholder en liste over id-udbydere, som brugeren kan vælge mellem under tilmelding eller Log på. Dette er enten sociale identitetsudbydere som Facebook og Google + eller lokale konti (baseret på navnet for e-mail-adresse eller bruger).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Azure AD B2C indhold, der er indsat i "lokale tilmelding kontosiden"

Denne side indeholder en tilmeldingsblanket brugeren med at udfylde ved tilmelding til en lokal konto, der er baseret på en e-mailadresse eller et brugernavn. Formularen kan indeholde forskellige input kontrolelementer som tekstboks, boks til indtastning af adgangskode, alternativknappen, enkelt Vælg rullelisterne og markere flere afkrydsningsfelter.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure AD B2C indhold, der er indsat i "" sociale tilmelding kontosiden "

Denne side indeholder en tilmeldingsblanket forbrugerne med at udfylde ved tilmelding ved hjælp af en eksisterende konto fra en sociale identitetsudbyder som Facebook eller Google +. Denne side minder til den lokale konti logonsiden (vist i det forrige afsnit) med undtagelse af adgangskode TOC-felter.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Azure AD B2C indhold, der er indsat på "samlede tilmelding eller Log på siden"

Denne side håndterer både tilmelding & logon forbrugerne, der kan bruge sociale identitetsudbydere som Facebook eller Google + eller lokale konti.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Azure AD B2C indhold, der er indsat på "Multi-Factor authentication siden"

Brugere på denne side, kan du kontrollere deres telefonnumre (med tekst eller stemme) under tilmelding eller Log på.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Azure AD B2C indhold, der er indsat i "" fejlsiden"

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Ting, du skal huske, når du opretter dit eget indhold

Hvis du planlægger at bruge funktionen til tilpasning af Brugergrænsefladen på siden, kan du gennemgå følgende anbefalede fremgangsmåder:

- Ikke kopiere Azure AD B2C standardindhold og forsøger at redigere den. Det er bedst at opbygge dit HTML5 indhold fra bunden og bruge standardindhold som reference.
- I alle sider (undtagen fejlsider) served ved logon, tilmelding og redigering af profil politikker, får typografiark, du angiver, at tilsidesætte standardtypografiark, som vi tilføje i disse sider i den <head> fragmenter. I alle de sider, served ved tilmeldingen eller logon og Nulstil adgangskodepolitikker og fejlsider på alle politikker, er du nødt til at levere alle stil dig selv.
- Af sikkerhedsmæssige årsager muligt vi ikke at medtage en hvilken som helst JavaScript i dit indhold. De fleste af hvad du skal være tilgængelig af feltet. Hvis ikke, bruge [Bruger stemme](http://feedback.azure.com/forums/169401-azure-active-directory) til at anmode om nye funktioner.
- Understøttede browserversioner:
    - Internet Explorer 11
    - Internet Explorer 10
    - Internet Explorer 9 (begrænset)
    - Internet Explorer 8 (begrænset)
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0
