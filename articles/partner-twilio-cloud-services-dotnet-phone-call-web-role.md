<properties 
    pageTitle="Hvordan du kan foretage et telefonopkald fra Twilio (.NET) | Microsoft Azure" 
    description="Lær at foretage et telefonopkald og sende en SMS-meddelelse med tjenesten Twilio API på Azure. Kodeeksempler, der er skrevet i .NET." 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Hvordan du kan foretage et telefonopkald ved hjælp af Twilio i en web rolle på Azure

Denne vejledning viser, hvordan du bruger Twilio til at foretage et opkald fra en webside, der er hostet i Azure. Programmet resulterende beder brugeren om telefonopkald værdier, som vist på følgende skærmbillede.

![Azure opkald formular ved hjælp af Twilio og ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Forudsætninger

Du skal gøre følgende for at bruge koden i dette emne:

1. Få fat på en Twilio konto og godkendelse token. For at komme i gang med Twilio skal du tilmelde dig på [https://www.twilio.com/try-twilio][try_twilio]. Du kan altid evaluere priser på [http://www.twilio.com/pricing][twilio_pricing]. Finde oplysninger om API leveres af Twilio, se [http://www.twilio.com/voice/api][twilio_api].
2. Føje Twilio .NET-bibliotek til din web rolle. Se "Sådan Tilføj Twilio biblioteker til projektet web rolle" senere i dette emne.

Du bør have kendskab til at oprette en grundlæggende web rolle på Azure.

## <a name="howtocreateform"></a>Sådan: oprette en webformular til at foretage et opkald

<a id="use_nuget"></a>Sådan føjes Twilio biblioteker til projektet web rolle:

1.  Åbn din løsning i Visual Studio.
2.  Højreklik på **referencer**.
3.  Klik på **Administrer NuGet pakker**.
4.  Klik på **Online**.
5.  Skriv *twilio*i søgefeltet online.
6.  Klik på **Installer** på Twilio-pakke.

Følgende kode viser, hvordan du kan oprette en webformular for at hente brugerdata til at foretage et opkald. I dette eksempel oprettes en ASP.NET web rolle, med navnet **TwilioCloud** .

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Sådan: oprette koden for at foretage opkaldet
Følgende kode, der kaldes, når brugeren er afsluttet formularen, opretter meddelelsen opkald og danner opkaldet. I dette eksempel køres koden i VedKlik hændelseshandler af knappen i formularen. (Brug din Twilio konto og godkendelse token i stedet for de pladsholder værdier, der er tildelt til **accountSID** og **authToken** i koden nedenfor).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

Opkaldet foretages, og Twilio slutpunktet, API-version og opkaldsstatus vises. Følgende skærmbillede viser output fra et eksempel på Kør.

![Azure opkald svar ved hjælp af Twilio og ASP.NET][twilio_dotnet_basic_form_output]

Du kan finde flere oplysninger om TwiML på [http://www.twilio.com/docs/api/twiml][twiml]. Du kan finde flere oplysninger om &lt;sig&gt; og andre Twilio verber kan findes på [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Næste trin
Denne kode er angivet til at vise dig grundlæggende funktionalitet ved hjælp af Twilio i en ASP.NET web rolle på Azure. Før du anvender til Azure fremstilling, kan du vil tilføje flere fejlhåndtering eller andre funktioner. Eksempel:

* I stedet for ved hjælp af en webformular, kan du bruge Azure Blob-lager eller en forekomst af Azure SQL-Database til at gemme telefonnumre og ringe til tekst. Oplysninger om brug af BLOB i Azure, se, [hvordan du bruger tjenesten Azure Blob storage i .NET][howto_blob_storage_dotnet]. Oplysninger om brug af SQL-Database, se, [hvordan du bruger Azure SQL-Database i .NET-programmer][howto_sql_azure_dotnet].
* Du kan bruge RoleEnvironment.getConfigurationSettings til at hente Twilio konto-ID og godkendelse token fra din installation konfigurationsindstillinger, i stedet for hårde-kodning værdierne i formularen. Finde oplysninger om klassen RoleEnvironment, se [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Læs retningslinjerne sikkerhed Twilio på [https://www.twilio.com/docs/security][twilio_docs_security].
* Få flere oplysninger om Twilio på [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Se også
* [Sådan bruges Twilio til tale- og SMS-funktioner fra Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
