<properties 
    pageTitle="Introduktion til Azure Active Directory og Visual Studio forbundne tjenester (MVC projekter) | Microsoft Azure" 
    description="Sådan Introduktion til brug af Azure Active Directory i MVC projekter, efter at oprette forbindelse til eller oprette en Azure AD ved hjælp af Visual Studio forbundne tjenester" 
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

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Introduktion til Azure Active Directory og Visual Studio forbundne tjenester (MVC projekter)

> [AZURE.SELECTOR]
> - [Kom godt i gang](vs-active-directory-dotnet-getting-started.md)
> - [Hvad skete der](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Kræve godkendelse til access-enheder 

Alle enheder i dit projekt er adorned med attributten **godkende** . Denne attribut kræver, at brugeren skal godkendes, før at få adgang til disse controller. For at tillade controller til at få adgang til anonymt, skal du fjerne denne attribut fra controlleren. Hvis du vil angive tilladelser på en mere detaljeret niveau, skal du anvende attributten til hver enkelt metode, der kræver godkendelse i stedet for at anvende den på klassen controller.
 
##<a name="adding-signin--signout-controls"></a>Tilføje logon / logge af kontrolelementer 

Tilføje en logon/logge af kontrolelementerne til din visning, du kan bruge visningen **_LoginPartial.cshtml** delvis føje funktionen til en af dine visninger. Her er et eksempel på de funktioner, der er føjet til visningen standard **_Layout.cshtml** . (Bemærk det sidste element i division med klasse navigationslinje / Skjul):

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[Lær mere om Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 
