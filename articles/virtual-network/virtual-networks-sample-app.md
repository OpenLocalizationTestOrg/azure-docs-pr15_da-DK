<properties
   pageTitle="Eksempel på programmet til brug sammen med sikkerhed rammen miljøer | Microsoft Azure"
   description="Installere denne enkle webprogram efter oprettelse af en DMZ teste trafik flow scenarier"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor"/>

# <a name="sample-application-for-use-with-security-boundary-environments"></a>Northwind til brug sammen med sikkerhed rammen miljøer

[Gå tilbage til sikkerhed rammen bedste fremgangsmåder side][HOME]

Disse PowerShell-scripts kan køres lokalt på IIS01 og AppVM01 servere du installerer og konfigurerer en meget simpel webprogram, der viser en HTML-side fra front-end IIS01 serveren med indhold fra back end-AppVM01 serveren.

Denne app er giver en enkel testmiljø for mange af eksemplerne DMZ og hvordan ændringer slutpunkter, NSGs, UDR og Firewall regler kan effekt trafik-flow.

## <a name="firewall-rule-to-allow-icmp"></a>Firewallregel, der tillader ICMP
Denne enkle PowerShell-sætning kan køres på en hvilken som helst Windows VM til at tillade ICMP (Ping) trafik. Dette giver mulighed for nemmere test og fejlfinding ved at tillade ping-protokollen til passerer gennem windows firewall (for de fleste Linux distros ICMP er aktiveret som standard).

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Note:** Hvis du bruger den under scripts, denne firewall regel tilføjelse er den første sætning.

## <a name="iis01---web-application-installation-script"></a>IIS01 - Web Application installationsscript
Dette script skal:

1.  Åbne IMCPv4 (Ping) på lokal server windows firewall til nemmere test
2.  Installere IIS og .net Framework v4.5
3.  Oprette en ASP.NET-webside og en Web.config-fil
4.  Ændre standard-programgruppen for at gøre det nemmere adgang til filer
5.  Angiv den anonyme bruger til dit administratorkonto og din adgangskode

Denne PowerShell-script skal køres lokalt, mens RDP havde i IIS01.

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"
        
    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
        
    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
        
    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>
            
        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'
        
        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'
            
        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
    
    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
        
    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC
        
        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## <a name="appvm01---file-server-installation-script"></a>AppVM01 - fil Server installationsscript
Dette script konfigurerer back-end for denne enkle program. Dette script skal:

1.  Åbne IMCPv4 (Ping) på firewall til nemmere test
2.  Oprette en ny mappe
3.  Oprette en tekstfil, så det kan bruges fra en fjernplacering adgang ved websiden herover
4.  Angive tilladelser for mappen, og filer til anonym at give adgang
5.  Deaktivere IE forbedret sikkerhed for at tillade nemmere søgning fra denne server 

>[AZURE.IMPORTANT] **Bedste fremgangsmåde**: aldrig deaktivere IE udvidet sikkerhed på en server, fremstilling samt det er normalt en dårlig idé søge på internettet fra en fremstilling server. Åbning af filshares til anonym adgang er også en dårlig idé, men udført her til enkel.

Denne PowerShell-script skal køres lokalt, mens RDP havde i AppVM01. PowerShell er påkrævet for at være Kør som Administrator for at sikre vellykket gennemførelse.
    
    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work
    
    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
    
    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory
    
    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
    
    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl
    
    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
    
    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
    
        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host
    

## <a name="dns01---dns-server-installation-script"></a>DNS01 - DNS-Server installationsscript
Der er ingen script, der er inkluderet i dette eksempel program tilladelse til at konfigurere DNS-serveren. Hvis tester firewallregler NSG eller UDR skal medtage DNS-trafik, DNS01 serveren skal være konfiguration manuelt. Netværkskonfiguration XML-fil til begge eksempler omfatter DNS01 som den primære DNS-server og den offentlige DNS-server, der er hostet af niveau 3 som sikkerhedskopien DNS-server. Niveau 3 DNS-server er den faktiske DNS-server, der bruges til ikke-local-trafik, og med DNS01 ikke installationen, klik på ingen lokale DNS finder sted.

<!--Link References-->
[HOME]: ../best-practices-network-security.md
