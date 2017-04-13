Lad os tilføje en udløser.

1. Angive *sftp* i søgefeltet i logik apps designer, og vælg **SFTP - når en fil er tilføjet eller ændret** udløseren derefter   
![Billede af SFTP udløser 1](./media/connectors-create-api-sftp/trigger-1.png)  
- **Når en fil er tilføjet eller ændret** kontrolelementet åbnes  
![Billede af SFTP udløser 2](./media/connectors-create-api-sftp/trigger-2.png)  
- Vælg **...** placeret på højre side af kontrolelementet. Dette åbner kontrolelementet mappe Vælg  
![Billede af SFTP udløser 3](./media/connectors-create-api-sftp/action-1.png)  
- Vælg **SFTP** til at vælge rodmappen som mappen til at overvåge for nye eller ændrede filer. Bemærk rodmappen vises nu i kontrolelementet **mappe** .  
![Billede af SFTP udløser 4](./media/connectors-create-api-sftp/action-2.png)   

På dette tidspunkt er din logik app konfigureret med en udløser, der starter en kørsel af de andre udløsere og handlinger i arbejdsprocessen, når en fil er enten ændret eller oprettet i mappen bestemte SFTP. 

>[AZURE.NOTE]Til en logik app fungerer, skal den indeholde mindst én udløser og en handling. Følg trinnene i næste afsnit for at føje en handling.  