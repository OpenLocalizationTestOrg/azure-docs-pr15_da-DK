Nu hvor du har tilføjet en udløser, interessant dens tid til at gøre noget med de data, der er oprettet af udløseren. Følg disse trin for at tilføje en handlingen **SFTP - Udpak mappe** . Denne handling udtrække indholdet af en fil, hvis de definerede betingelser er opfyldt. 

Konfigurere denne handling, skal du angive følgende oplysninger. Du vil bemærke, at det er let at bruge data, der genereres af udløseren som input til nogle af egenskaberne for den nye fil:

|SFTP - Udpak mappe egenskab|Beskrivelse|
|---|---|
|Kilde arkiv filsti|Dette er stien til den fil, der trækkes. Du kan markere en token fra en tidligere handling eller gennemse SFTP serveren for at finde stien til filen.|
|Stien til destination|Dette er den sti, hvor de udpakkede filer skal placeres. Du kan vælge en token fra en tidligere handling som stien til destinationsmappen eller gennemse SFTP serveren og vælge en sti.|
|Vil du overskrive?|Angiver, hvis der findes en fil med samme navn som den udpakkede fil i stien til destinationsmappen, hvis den eksisterende fil skal overskrives eller ej.|

Lad os komme i gang Tilføj handling Hvis du vil udtrække filerne, hvis den betingelse, der er defineret tidligere evalueres til *Sand*. 

1. Vælg **Tilføj en handling**.        
![Billede af SFTP handling betingelse 6](./media/connectors-create-api-sftp/condition-6.png)   
- Vælg handlingen, **SFTP - Udpak mappe**      
![Billede af SFTP handling betingelse 7](./media/connectors-create-api-sftp/condition-7.png)   
- Vælg **kilde arkiv filsti**              
![Billede af SFTP handling betingelse 9](./media/connectors-create-api-sftp/condition-9.png)   
- Vælg **filsti** tokenet. Dette angiver, at du vil bruge filstien til den fil, udløseren fandt som kilde arkiv filstien.           
![Billede af SFTP handling betingelse 10](./media/connectors-create-api-sftp/condition-10.png)   
- Vælg **stien destinationsmappen**           
![Billede af SFTP handling betingelse 11](./media/connectors-create-api-sftp/condition-11.png)   
- Vælg **filsti** tokenet. Dette angiver, at du vil bruge filstien til den fil, udløseren fandt som destinationsstien til de udpakkede filer.   
- Angiv *\ExtractedFile* i kontrolelementet **stien destinationsmappen** . Gør dette lige efter filen sti token i kontrolelementet Destination mappe sti.         
![Billede af SFTP handling betingelse 12](./media/connectors-create-api-sftp/condition-12.png)   
- Angiv *Sand* i den **Overskriv?* kontrolelement til at angive, at eksisterende filer skal overskrives, hvis de har samme navn som de udtrukne filer.      
![Billede af SFTP handling betingelse 13](./media/connectors-create-api-sftp/condition-13.png)   
- Gemme ændringerne i arbejdsprocessen  
