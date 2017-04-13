## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Installere skabelonen ARM ved hjælp af klik til at udrulle

Du kan genbruge foruddefinerede ARM skabeloner Overfør til en github lager vedligeholdes af Microsoft og åbne til community. Skabelonerne kan installeres direkte af github, eller hentet og ændres, så de passer til dine behov. Følg nedenstående trin for at installere en skabelon, der opretter en VNet med to undernet.

1. Gå til [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)fra en browser.
2. Rul ned på listen over skabeloner, og klik på **101-vnet-to-undernet**. Markér filen **README.md** , som vist nedenfor.

    ![READEME.md fil i github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Klik på **installeres til Azure**. Hvis det er nødvendigt, kan du angive dine Azure logonoplysninger. 
4. Angiv de værdier, du vil bruge til at oprette din nye VNet i bladet **parametre** , og klik derefter på **OK**. I nedenstående figur vises værdier for vores scenario.

    ![ARM skabelonparametre](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Klik på **ressourcegruppe** og vælg en ressourcegruppe tilføje VNet til, eller klik på **Opret ny** for at føje VNet til en ny ressourcegruppe. I nedenstående figur vises ressourcen gruppeindstillinger til en ny ressourcegruppe kaldet **TestRG**.

    ![Ressourcegruppe](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Hvis det er nødvendigt, kan du ændre indstillinger for **abonnement** og **placering** for din VNet.
6. Hvis du ikke vil se VNet som et felt i **Startboard**, kan du deaktivere **Fastgør til Startboard**.
5. Klik på **Leagl betingelser**, Læs vilkårene, og klik på **Køb** for at acceptere. 
6. Klik på **Opret** for at oprette VNet.

    ![Indsendelse installation flise i preview-portalen](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Når installationen er færdig, skal du klikke på **TestVNet** > **alle indstillinger** > **undernet** at se egenskaberne undernet, som vist nedenfor.

    ![Oprette VNet preview-portalen](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)