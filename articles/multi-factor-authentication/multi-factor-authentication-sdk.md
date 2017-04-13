<properties 
    pageTitle="Integrere dine lokale identiteter med Azure Active Directory."
    description="Dette er den Azure AD-forbindelse, der beskriver den og hvorfor du skal bruge den."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Bygning Multi-Factor Authentication til brugerdefinerede Apps (SDK)

> [AZURE.IMPORTANT]  Hvis du vil hente SDK, skal du oprette en udbyder af Azure Multi-Factor Auth, selvom du har Azure MFA, AAD Premium eller EMS licenser.  Hvis du opretter en udbyder af Azure Multi-Factor Auth hertil og allerede har licenser, er det påkrævet for at oprette provideren med **Hver aktiveret bruger** modellen og sammenkæde provideren til den mappe, der indeholder Azure MFA, Azure AD Premium eller EMS licenserne.  Dette sikrer, at du ikke er faktureret medmindre du har flere forskellige brugere ved hjælp af SDK end antallet licenser, at du ejer.

Azure Multi-Factor Authentication Software Development Kit (SDK) kan du oprette telefonopkald og tekst meddelelse kontrol direkte til logon eller transaktion processer af programmer i din Azure AD-lejer.

Multi-Factor Authentication SDK er tilgængelig for C#, Visual Basic (.NET), Java, Perl, PHP og fonetisk. SDK indeholder en tynd slikpapir omkring Multi-Factor authentication. Den indeholder alt det, skal du skrive din kode, herunder kommenterede kode-kildefiler, eksempel filer og en detaljeret vigtigt-fil. Hver SDK indeholder også en certifikater og private nøgler til at kryptere transaktioner, der er entydige for din udbyder af Multi-Factor godkendelse. Så længe du har en udbyder, kan du hente SDK i så mange sprog og formater, som du vil have.

Strukturen i API'er i Multi-Factor Authentication SDK er helt enkelt. Du foretager en enkelt funktion, ringe til en API sammen med parametrene Multi-Factor indstilling som bekræftelsen tilstand og brugerdata, som telefonnummeret til at ringe til eller pinkode for at validere. API'erne oversætte funktionskald i web serviceanmodninger til skybaserede Azure Multi-Factor Authentication Service. Alle opkald skal indeholde en reference til den private certifikat, der er inkluderet i hver SDK.

Da API'erne ikke har adgang til brugere, der er registreret i Azure Active Directory, skal du angive brugeroplysninger, som telefonnumre og PIN-koder i en fil eller database. API'erne giver også ikke tilmelding eller bruger administrationsfunktioner, så du behøver at opbygge disse processer i dit program.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Hente Azure Multi-Factor Authentication SDK

Hente Azure Multi-Factor SDK kræver en [Azure Multi-Factor Auth udbyder](multi-factor-authentication-get-started-auth-provider.md).  Dette kræver et abonnement, der er fuld Azure, selvom ejes Azure MFA, Azure AD Premium eller Enterprise mobilitet Suite licenser.  For at hente SDK, skal du navigere til portalen Multi-Factor Management, enten ved at administrere provideren Multi-Factor Auth direkte eller ved at klikke på linket **"Gå til portalen"** på indstillingssiden for MFA-tjenesten.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Hente Azure Multi-Factor Authentication SDK fra Azure-portalen


1. Log på portalen Azure som Administrator.
2. Vælg Active Directory i venstre side.
3. Klik **Multi-Factor Auth udbydere** øverst på siden Active Directory
4. Klik på **Administrer** nederst
5. Der åbnes en ny side.  Klik på SDK nederst, venstre side.
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. Vælg det sprog, du vil og klikke på en tilknyttet Hent links.
7. Gem filen.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Hente Azure Multi-Factor Authentication SDK via tjenesteindstillingerne


1. Log på portalen Azure som Administrator.
2. Vælg Active Directory i venstre side.
3. Dobbeltklik på din forekomst af Azure AD.
4. Klik på **Konfigurer** øverst
5. Vælg **Administrer Tjenesteindstillinger**under Multi-Factor authentication
![hente](./media/multi-factor-authentication-sdk/download2.png)
6. Klik på **Gå til portalen**nederst på skærmen på indstillingssiden tjenester.
![Download](./media/multi-factor-authentication-sdk/download3a.png)
7. Der åbnes en ny side.  Klik på SDK nederst, venstre side.
8. Vælg det sprog, du vil og klikke på en tilknyttet Hent links.
9. Gem filen.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Indholdet af Azure Multi-Factor Authentication SDK
Inde i SDK kan du finde følgende elementer:

- **Vigtige oplysninger om**. Beskriver, hvordan du bruger Multi-Factor Authentication API'er i et nyt eller eksisterende til computeren.
- **Kilde fil(er)** til multi-Factor Authentication
- **Klientcertifikat** , du bruger til at kommunikere med tjenesten Multi-Factor Authentication
- **Privat nøgle** til certifikatet
- **Ring til resultater.** En oversigt over opkald resultatet koder. For at åbne denne fil skal du bruge et program med tekstformatering, f.eks. Bruge opkald resultatet koder til at teste og udføre fejlfinding på implementeringen af Multi-Factor Authentication i programmet. De er ikke godkendelse statuskoder.
- **Eksempler på.** Eksempel på kode for en grundlæggende arbejde implementering af Multi-Factor Authentication.


>[AZURE.WARNING]Klientcertifikat er et entydigt privat certifikat, der blev oprettet især for dig. Undlad at dele eller mister denne fil. Det er din nøgle til at sikre sikkerheden for din kommunikation med tjenesten Multi-Factor Authentication.

## <a name="code-sample-standard-mode-phone-verification"></a>Eksempel på kode: Standardtilstand telefon bekræftelse

Eksempel på denne kode viser, hvordan du bruger API'er i Azure Multi-Factor Authentication SDK føje standardtilstand stemme opkald bekræftelsen til dit program. Standardtilstand er et telefonopkald, som brugeren reagerer på ved at trykke på tasten #.

I dette eksempel bruges C# .NET 2.0 Multi-Factor Authentication SDK i en grundlæggende ASP.NET-program med C# serversiden logik, men processen minder meget for enkel installationer på andre sprog. Fordi SDK indeholder kildefiler, ikke eksekverbare filer, kan du oprette filerne og referere til dem eller medtage dem direkte i dit program.

>[AZURE.NOTE]Når du implementerer Multi-Factor Authentication, kan du bruge flere faktorer som sekundær eller tertiær bekræftelse som supplement til din primære godkendelsesmetode. Disse metoder er ikke beregnet som skal bruges som primær godkendelsesmetoder.

### <a name="code-sample-overview"></a>Kode eksempel oversigt
Denne eksempelkode for en meget simpel demo webprogram bruger et telefonopkald med en # tasternes til at fuldføre på brugergodkendelse. Denne telefonopkald faktor kendes i Multi-Factor Authentication som standard-tilstand.

Koden klientsiden omfatter ikke nogen Multi-Factor Authentication-specifikke elementer. Da de yderligere godkendelse faktorer uafhængigt af den primære godkendelse, kan du tilføje dem uden at ændre den eksisterende sign-on – brugergrænseflade. API'er i Multi-Factor SDK gør det muligt at tilpasse brugeroplevelsen, men du muligvis ikke ændre noget overhovedet.

Serversiden koden tilføjer godkendelse i standard-tilstand i trin 2. Det opretter et PfAuthParams objekt med de parametre, der kræves til standard-tilstand bekræftelse: brugernavn, ringe til nummeret, og tilstand og stien til det klientcertifikat (CertFilePath), som kræves i hvert kald. Du kan finde en demonstration af alle parametre i PfAuthParams eksempelfil i SDK.

Koden overfører derefter objektet PfAuthParams til funktionen pf_authenticate(). Returværdien angiver lykkes eller mislykkes godkendelsen. Parametrene out, callStatus og errorID, indeholder oplysninger om yderligere kald resultat. Opkald resultatet koderne er beskrevet i filen opkald resultater i SDK.

Denne minimale implementering kan skrives i en blot nogle få linjer. Men i fremstilling kode, du vil medtage mere avancerede fejlhåndtering, yderligere database kode og en forbedret brugeroplevelse.

### <a name="web-client-code"></a>Web-klienten kode

Følgende er web klient kode for en demo side.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Serversiden kode

I den følgende kode i serversiden er Multi-Factor Authentication konfigureret og køre i trin 2. Standardtilstand (MODE_STANDARD) er et telefonopkald, som brugeren svarer ved at trykke på tasten #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
