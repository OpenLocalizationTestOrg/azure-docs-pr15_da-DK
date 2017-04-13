<properties
   pageTitle="Fejlfinding i forbindelse med Microsoft Power BI integrerede Preview"
   description="Fejlfinding i forbindelse med Microsoft Power BI integrerede Preview"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Fejlfinding i forbindelse med Microsoft Power BI integrerede Preview
I denne artikel indeholder svar til, hvordan du udfører fejlfinding af **Power BI integreret**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Konfiguration af SQL Server-forbindelsesstrenge
Hvis du vil angive en SQL Server, der forbinder streng, skal du følge et bestemt format. Nedenfor vises et eksempel forbindelsesstreng til SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Hvis du vil vide mere om SQL Server-forbindelsesstrenge skal du se følgende artikler:

-   [SQL Server-forbindelsesstrenge](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Angive legitimationsoplysninger
I de tilfælde, hvor du har legitimationsoplysninger for et udvikling eller testmiljø, såsom brugernavn og adgangskode, du muligvis opdatere legitimationsoplysninger, der svarer til en fremstilling løsning.

## <a name="see-also"></a>Se også
- [Komme i gang med eksempel](power-bi-embedded-get-started-sample.md)
- [Hvad er Power BI integrerede](power-bi-embedded-what-is-power-bi-embedded.md)
