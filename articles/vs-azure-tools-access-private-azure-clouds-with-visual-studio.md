<properties 
   pageTitle="Få adgang til private Azure skyer med Visual Studio | Microsoft Azure"
   description="Lær at få adgang til private skyen ressourcer ved hjælp af Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Få adgang til private Azure skyer med Visual Studio

##<a name="overview"></a>Oversigt

Som standard understøtter Visual Studio offentlige Azure skyen RESTEN slutpunkter. Det kan være et problem, dog, hvis du bruger Visual Studio til en privat Azure sky. Du kan bruge certifikater til at konfigurere Visual Studio for at få adgang til private Azure skyen RESTEN slutpunkter. Du kan finde disse certifikater via din Azure publicere indstillingsfil.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Adgang til en privat Azure skyen i Visual Studio

1. Hent filen Publicer indstillinger i [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885) til private skyen, eller kontakt din administrator for en fil med Publicer indstillinger. På den offentlige version af Azure er linket for at hente denne [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Den fil, du har hentet skal have filtypenavnet .publishsettings).

1. Vælg noden **Azure** **Server Explorer** i Visual Studio, og vælg kommandoen **Administrere abonnementer** på i genvejsmenuen.

    ![Administrere abonnementer kommandoen](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Vælg fanen **certifikater** i dialogboksen **Administrer Microsoft Azure abonnementer** , og vælg derefter knappen **Importér** .

    ![Import af Azure certifikater](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Gå til den mappe, hvor du har gemt filen Publicer indstillinger, og Vælg fil, og vælg derefter knappen **Importér** i dialogboksen **Importér Microsoft Azure-abonnementer** . Dette importerer certifikaterne i indstillingsfil Udgiv i Visual Studio. Du bør nu kunne interagere med dine private skyen ressourcer.

    ![Importere publicere indstillinger](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Næste trin

[Publicere på en Azure skybaseret tjeneste fra Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Sådan: Hent og Importer udgive abonnementsoplysninger og indstillinger](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

