1. Højreklik på projektet i Visual Studio **Solution Explorer**, og vælg **Tilføj > Docker Support** i kontekstmenuen.

    ![Tilføje Docker Support genvejsmenu](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Føje understøttelse af Docker til en ASP.NET-Core resulterer web project i for at tilføje flere Docker-relaterede filer blive føjet til projektet, herunder Docker skriver filer, Windows PowerShell-scripts, installation og Docker egenskaben filer. 

    ![Docker filer, der er føjet til projektet](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Hvis bruger [Docker for Windows Beta](https://beta.docker.com), åbne Properties\Docker.props, fjerne standardværdien og genstart Visual Studio for værdien kan træde i kraft.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
