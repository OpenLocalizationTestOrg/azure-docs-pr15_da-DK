<properties 
    pageTitle="Sådan konfigureres TLS fælles godkendelse for WebApp" 
    description="Lær, hvordan du konfigurerer din online for at bruge klientcertifikat på TLS." 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Sådan konfigureres TLS fælles godkendelse for WebApp

## <a name="overview"></a>Oversigt ##
Du kan begrænse adgangen til din Azure online ved at aktivere forskellige typer godkendelse for den. En metode til at gøre det er at godkende ved hjælp af et klientcertifikat, når anmodningen er over TLS/SSL. Denne funktion hedder TLS fælles godkendelse eller klientcertifikat godkendelse og i denne artikel indeholder oplysninger om hvordan du konfigurerer din online for at bruge certifikatgodkendelse for klient.

> **Note:** Hvis du har adgang til webstedet over HTTP- og ikke HTTPS, modtager du ikke en hvilken som helst klientcertifikat. Hvis programmet kræver klientcertifikater skal du ikke tillader anmodninger i dit program via HTTP.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>Konfigurere WebApp til klientcertifikat ##
Du konfigurerer din online for at kræve klientcertifikater, skal du tilføje indstillingen clientCertEnabled websted for din online og indstillet til sand. Denne indstilling er ikke tilgængelig via management oplevelsen i portalen, og REST-API skal bruges til at udføre dette.

Du kan bruge [ARMClient værktøj](https://github.com/projectkudu/ARMClient) til gør det nemt at udforme REST-API opkaldet. Når du logger på med værktøjet skal du udstede følgende kommando:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
erstatte alt i {} med oplysninger til din online og oprettelse af en fil med navnet enableclientcert.json med følgende JSON indhold:

> {"placering": "Min App webplacering"   
>   "Egenskaber": {}  
>     "clientCertEnabled": SAND}}  

Sørg for at ændre værdien af "placering" til det sted din online er placeret fx North Central os eller Vest USA osv.

> **Note:** Hvis du kører ARMClient fra Powershell, skal du gå på @ symbol for JSON-fil med en tilbage aksemærker '.

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Få adgang til klientcertifikatet fra din online ##
Hvis du bruger ASP.NET og konfigurere din app for at bruge certifikatgodkendelse for klient, er certifikatet, der bliver tilgængelig via egenskaben **HttpRequest.ClientCertificate** . For andre programmer-stakke bliver klienten cert tilgængelig i din app gennem en base64-kodet værdi i overskriften "X-ARR-ClientCert" anmodning. Programmet kan oprette et certifikat fra denne værdi og derefter bruge den til godkendelse og autorisation formål i programmet.

## <a name="special-considerations-for-certificate-validation"></a>Særlige overvejelser om at godkende certifikater ##
Klientcertifikat, der sendes til programmet går ikke gennem en hvilken som helst validering af platformen Azure Web Apps. Validering af dette certifikat er ansvar af WebApp. Her er eksempel ASP.NET kode, der har valideret egenskaber for certifikat til godkendelsesformål.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
