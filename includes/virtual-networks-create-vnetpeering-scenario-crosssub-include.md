## <a name="peering-across-subscriptions"></a>Peering på tværs af abonnementer

I dette scenarie skal du oprette en peering mellem to VNets tilhører forskellige abonnementer.

![Cross sub scenario](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet peering er afhængig af rollebaseret adgangskontrol (RBAC) til godkendelse. I tværs abonnementer scenarie skal du først tildeler tilstrækkelige tilladelser til brugere, som opretter linket peering:

> [AZURE.NOTE] Hvis den samme bruger har rettigheder over begge abonnementer, kan du springe trin 1-4 nedenfor.
