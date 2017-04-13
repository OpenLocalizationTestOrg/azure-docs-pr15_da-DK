<properties
    pageTitle="Vis SAML, der returneres af Access Control Service (Java)"
    description="Få mere at vide, hvordan du får vist SAML, der returneres af Access Control Service i Java-programmer, der er hostet på Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Sådan får du vist SAML, der returneres af Azure Access Control Service

Denne vejledning viser dig, hvordan til at få vist den underliggende sikkerhed program Markup Language (SAML) tilbage til dit program ved Azure Access ACS (Control Service). Guiden bygger på emnet [Sådan godkende webbrugere med Azure Access kontrolelement tjenesten ved hjælp af Eklipse][] ved hjælp af kode, der viser SAML oplysninger. Det færdige program ser nogenlunde sådan følgende.

![Eksempel SAML output][saml_output]

Du kan finde flere oplysninger om ACS, afsnittet [Næste trin](#next_steps) .

> [AZURE.NOTE]
> Azure Access Services kontrolelement filteret er et community technology preview. Som foreløbig version – understøttes det formelt ikke af Microsoft.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre opgaverne i denne vejledning skal fuldføre eksemplet, [hvordan man kan godkende webbrugere med Azure Access kontrolelement tjenesten ved hjælp af Eklipse][] og bruge den som udgangspunkt til dette selvstudium.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Føje JspWriter bibliotek til din build sti og installation samling

Tilføj det bibliotek, der indeholder **javax.servlet.jsp.JspWriter** klassen til din build sti og installation samling. Hvis du bruger Tomcat, er biblioteket **jsp api.jar**, der er placeret i mappen Apache **bibliotek** .

1. I Eklipses Projektstifinder, skal du højreklikke på **MyACSHelloWorld**, skal du klikke på **Opret stien**, skal du klikke på **Konfigurere opbygge stien**, klik på fanen **biblioteker** og klik derefter på **Tilføj eksterne JARs**.
2. I dialogboksen **JAR markering** , gå til den nødvendige glas, markere den og derefter klikke på **Åbn**.
3. Klik på **Installation samling**med dialogboksen **Egenskaber for MyACSHelloWorld** stadig er åben.
4. Klik på **Tilføj**i dialogboksen **Web installation samling** .
5. Klik på **Java opbygge sti poster** i dialogboksen **Ny samling direktiv** , og klik derefter på **Næste**.
6. Vælg det relevante bibliotek, og klik på **Udfør**.
7. Klik på **OK** for at lukke dialogboksen **Egenskaber for MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Redigere filen JSP for at få vist SAML

Ændre **index.jsp** for at bruge følgende kode.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Køre programmet

1. Køre programmet i computer emulatoren eller installere til Azure, ved at følge trin beskrives, [hvordan man kan godkende webbrugere med Azure Access kontrolelement tjenesten ved hjælp af Eklipse][].
2. Start en browser, og åbn dit webprogram. Når du logger på i dit program, kan du se SAML oplysninger, herunder den sikkerhed-program, der leveres af identitetsudbyder.

## <a name="next-steps"></a>Næste trin

Yderligere udforske ACSS funktionalitet og for at eksperimentere med mere avancerede scenarier, se [Access Control Service 2.0][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Sådan godkende webbrugere med Azure Access Control Service ved hjælp af Eklipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 