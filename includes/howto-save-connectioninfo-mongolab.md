Selvom det er muligt at indsætte en MongoLab URI i din kode, anbefaler vi, at konfiguration af det i miljøet for at lette i administration. Denne måde, hvis URI ændres, kan du opdatere den via Azure administrationsportalen uden at gå til koden.


1. Vælg **Web Apps**i Azure-portalen.
1. Klik på navnet på web app på listen Web Apps.  
![WebAppEntry][entry-website]  
Viser dashboardet Web App.

1. Klik på **Konfigurer** i menulinjen.  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Rul ned til sektionen strenge.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Angiv MONGOLAB_URI for **navn**.
1. Som **værdi**skal du indsætte forbindelsesstrengen vi der fås i forrige afsnit.
1. Vælg **brugerdefineret** på listen **Type** ned (i stedet for standard **SQLAzure**).
1. Klik på **Gem** på værktøjslinjen.  
![SaveWebApp][button-website-save]

**Note:** Azure tilføjer den **CUSTOMCONNSTR\_ ** præfiks til denne variabel, som er grunden koden over referencer **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
