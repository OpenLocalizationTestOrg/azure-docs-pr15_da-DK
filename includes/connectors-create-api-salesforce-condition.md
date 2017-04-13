Denne betingelse evaluerer feltet e-mail-adresse til hver ny Salesforce-kundeemne. Hvis mailadressen indeholder *amazon.com*, bliver resultatet betingelse *Sand*.

1. Vælg **+ nyt trin**.  
![Billede af Salesforce betingelse 1](./media/connectors-create-api-salesforce/condition-1.png)   
- Vælg **Tilføj en betingelse**.    
![Billede af Salesforce betingelse 2](./media/connectors-create-api-salesforce/condition-2.png)  
- Vælg **Vælg en værdi**.    
![Billede af Salesforce betingelse 3](./media/connectors-create-api-salesforce/condition-3.png)  
- Vælg *mail* tokenet kundeemne af udløseren.    
![Billede af Salesforce betingelse 4](./media/connectors-create-api-salesforce/condition-4.png)  
- Vælg *indeholder*.      
![Billede af Salesforce betingelse 5](./media/connectors-create-api-salesforce/condition-5.png)  
- Vælg **Vælg en værdi** i bunden af kontrolelementet.     
![Billede af Salesforce betingelse 6](./media/connectors-create-api-salesforce/condition-6.png)  
- Angiv *amazon.com* som den værdi, du gerne vil evaluere e-mail-adressen på det nye kundeemne til. Hvis mailadressen indeholder *amazon.com*, betingelsen skal evalueres til *True* og andre trinnene i din logik app kan fortsætte.    
![Billede af Salesforce betingelse 7](./media/connectors-create-api-salesforce/condition-7.png)  
- Gem dine logik apps.  

