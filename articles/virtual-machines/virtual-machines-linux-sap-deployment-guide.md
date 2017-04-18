<properties
   pageTitle="SAP NetWeaver på Linux virtuelle maskiner (VM'er) – installationsvejledning | Microsoft Azure"
   description="SAP NetWeaver på Linux virtuelle maskiner (VM'er) – installationsvejledning"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-azure-virtual-machines-vms--deployment-guide"></a>SAP NetWeaver på Azure virtuelle-maskiner (VM'er) – installationsvejledning

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (SAP NetWeaver på Linux virtuelle maskiner (VM'er) – DBMS installationsvejledning) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (cachelagring af FOS og virtuelle harddiske) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure-lager) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (strukturen i en RDBMS installation) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (høj tilgængelighed og nedbrud med Azure FOS) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 og nyere) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 og tidligere versioner) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (ved hjælp af en SQL Server-billeder af Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (generelt SQL Server for SAP på Azure oversigt) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (specifikke oplysninger til SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (lagerplads konfiguration) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (sikkerhedskopiering og gendannelse) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (overvejelser til sikkerhedskopiering og gendannelse) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (andet) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (SAP NetWeaver på Linux virtuelle maskiner (VM'er) – installationsvejledning) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP ressourcer) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (implementere en VM med et brugerdefineret billede) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (scenarie 1: implementere en VM af Azure Marketplace for SAP) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (scenarie 2: implementering af en VM med et brugerdefineret billede til SAP) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 ( Scenarie 3: Flytte en VM fra ved hjælp af en ikke-generalized Azure Virtuelle med SAP lokalt) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (installation scenarier af FOS for SAP på Microsoft Azure) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (implementerer Azure PowerShell-cmdlet'er) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download og importere SAP relevante PowerShell-cmdlet'er) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (deltage i VM til lokale domæne - kun Windows) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [installation-vejledning-4.4]: Virtual-Machines-Linux-SAP-Deployment-Guide.MD#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Hent, Installer og Aktivér Azure VM Agent) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (konfigurere Azure udvidet overvågning udvidelse til SAP) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (parathed kontrollere, om Azure udvidet overvågning for SAP) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (tilstandskontrol for Azure Overvågning af konfiguration af infrastruktur) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (yderligere fejlfinding af Azure overvågning infrastrukturen for SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (SAP NetWeaver på Linux virtuelle maskiner (VM'er) – planlægning og implementeringsvejledningen) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (ressourcer) [planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (høj tilgængelighed (HA) og nedbrud gendannelse (DR) til SAP NetWeaver kører på virtuelle Azure-computere) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (høj tilgængelighed til SAP Application Servers) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (ved hjælp af starte efter SAP forekomster) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md# 1625df66-4cc6-4d60-9202-de8a0b77f803 (skyen kun - virtuelt installationer til Azure uden afhængigheder af kundenetværket lokalt) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (kryds-Premise - installation af én eller flere SAP FOS til Azure med kravet af blive fuldt integreret i det lokale netværk) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure områder) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (fejl domæner) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (opgradere domæner) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088- f9be - 4c 97-958a - 27996255c 665 (Azure tilgængelighed sæt) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (feltet Microsoft Azure virtuelt konceptet) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (flyttes en VM fra det lokale til Azure med en ikke-generalized disk) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (implementere en VM med et bestemt billede kunde) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (forberedelse til at flytte en VM fra det lokale til Azure med en ikke-generalized disk) [ Planning-Guide-5.2.2]:Virtual-Machines-Linux-SAP-Planning-Guide.MD#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (forberedelse til implementering af en VM med et bestemt billede kunde til SAP) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (forberede FOS med SAP til Azure) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (forskellen mellem en Azure Disk og Azure billede) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (overførsel af en virtuel harddisk fra det lokale til Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (når du kopierer diske mellem Azure lagerplads konti) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md# 4efec401-91e0-40c0-8e64-f2dceadff646 (VM/Virtuelle struktur til SAP-installationer) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (indstilling automatisk tilslutning om en vedhæftet disk) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (enkelt VM med SAP NetWeaver demo/kursus scenarie) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (begreber Cloud-Only installation af SAP forekomster) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure overvågning løsning til SAP) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.

Microsoft Azure gør det muligt for virksomheder at anskaffe Beregn og lager ressourcer minimale tidspunkt uden længerevarende indkøb skifter. Azure virtuelle maskiner kan virksomheder at implementere klassisk programmer, som SAP NetWeaver baseret programmer til Azure og udvide deres pålidelighed og tilgængelighed uden yderligere ressourcer, der er tilgængelige i det lokale miljø. Microsoft Azure understøtter også tværs lokale connectivity, som gør det muligt for virksomheder at integrere aktivt virtuelle Azure-computere i deres lokale domæner, deres privat skyer og deres SAP System liggende.

Denne hvidbog beskrives trin for trin, hvordan en Azure virtuel maskine er parat til installation af SAP NetWeaver baseret programmer. Det antages, at oplysningerne i den [planlægning og implementeringsvejledningen] [-Planlægningsvejledning] kendes. Hvis ikke, skal du læse respektive dokumentet først.

Papiret supplerer SAP Installation dokumentation og SAP-noter, som repræsenterer de primære ressourcer til installationer og installationer af SAP-software på bestemte platforme.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="introduction"></a>Introduktion
Et stort antal virksomheder over hele verden bruge SAP NetWeaver baseret programmer – mest fremtrædende den SAP Business-pakke – til at køre deres opgaver kritiske forretningsprocesser. Systemtilstand er derfor et afgørende aktiv, og muligheden for at understøtte enterprise i tilfælde af en fejl, herunder ydeevne hændelser, bliver et vigtigt krav.
Microsoft Azure leverer overordnede platform instrumentation for at tage højde for alle virksomhedens vigtige applikationer supportability krav. Denne vejledning sikrer, at en Microsoft Azure virtuel maskine, der er rettet til installation af SAP-Software er konfigureret, så enterprise support kan blive tilbudt, uanset hvor måde virtuelt oprettes, skal den taget af Azure Marketplace eller bruge et bestemt billede kunde.
Trin i opsætningen af følgende, alle nødvendige er beskrevet i detaljer.

## <a name="prerequisites-and-resources"></a>Forudsætninger og ressourcer
### <a name="prerequisites"></a>Forudsætninger
Før du begynder, skal du kontrollere, at, at de forudsætninger, der er beskrevet i følgende kapitler er opfyldt.

#### <a name="local-personal-computer"></a>Lokale pc
Konfiguration af en Azure Virtual Machine til SAP softwareinstallation består af flere trin. For at administrere Windows FOS eller Linux FOS, skal du bruge en PowerShell-script og Microsoft Azure-Portal. For den pågældende er det nødvendigt at have en lokal pc, der kører Windows 7 eller nyere. Hvis du kun vil administrere Linux FOS og ønsker at bruge en Linux maskine til denne opgave, kan du også bruge Azure Command Line Interface (Azure CLI).

#### <a name="internet-connection"></a>Internetforbindelse
For at hente og udføre de nødvendige værktøjer og scripts, kræves forbindelse til internettet. Desuden kører Azure udvidet overvågning filtypenavnet Microsoft Azure Virtual Machine skal have adgang til internettet. I tilfælde af denne Azure VM er en del af en Azure virtuelt netværk eller et lokalt domæne, sørge for, at de relevante proxy-indstillinger er angivet som beskrevet i kapitel [Konfigurere Proxy] [ deployment-guide-configure-proxy] i dette dokument.

#### <a name="microsoft-azure-subscription"></a>Microsoft Azure-abonnement
Der findes allerede en Azure-konto og er blevet konstateret, sorteret efter logonoplysninger.

#### <a name="topology-consideration-and-networking"></a>Topologi overvejelser og netværk
Topologi og arkitektur af SAP-installation i Azure skal defineres. Arkitektur med hensyn til:

* Microsoft Azure-lager konti der skal bruges
* Virtuelt netværk til at udrulle SAP-systemet til
* Ressourcegruppe til at udrulle SAP-systemet til
* Azure område for at installere SAP-systemet
* SAP-konfigurationen (2-niveau eller 3-niveau)
* VM eller størrelser og antallet af yderligere virtuelle harddiske til skal oprettes forbindelse til VM(s)
* SAP Transport og rettelse systemkonfiguration

Azure-lager firmaer eller Azure virtuelle netværk skal som sådan der er oprettet og konfigureret allerede. Sådan oprettes og konfigurere dem dækkes i [Planning and implementeringsvejledningen] [-Planlægningsvejledning].

#### <a name="sap-sizing"></a>SAP-skalering
* Planlagte SAP arbejdsbelastningen er blevet bestemt, f.eks. ved hjælp af Quicksizer SAP og sorteret efter SAP'ER antallet kendes 
* Påkrævet CPU ressource og hukommelse forbrug af SAP-systemet skal være kendt
* De påkrævede i/o-operationer sekundet skal være kendt
* Den nødvendige netværksbåndbredde i eventuel kommunikation mellem forskellige FOS i Azure kendes
* Den nødvendige netværksbåndbredde mellem de lokale aktiver og Azure installeret SAP systemer kendes

#### <a name="resource-groups"></a>Ressourcegrupper
Ressourcegrupper er nye koncepter, der indeholder alle de ressourcer, der har samme livscyklus fx de oprettes og slettet på samme tid. Læs [denne artikel] [ resource-group-overview] kan finde flere oplysninger om ressourcegrupper. 

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP ressourcer
Under af konfigurationenarbejde, der er behov for følgende ressourcer:

* SAP Note [1928533]
    * på listen over Azure virtuelt størrelser, som understøttes til installation af SAP-Software 
    * oplysninger om vigtige kapacitet per Azure virtuelt størrelse
    * understøttede SAP-software og OS og DB kombination
* SAP Note [2015553] listen over forudsætninger skal understøttes af SAP, når du installerer SAP-software til Microsoft Azure.
* SAP Note [1999351] , der indeholder flere oplysninger om fejlfinding for udvidet Azure overvågning for SAP.
* SAP Note [2178632] , der indeholder detaljerede oplysninger om alle tilgængelige overvågning målepunkter til SAP på Microsoft Azure. 
* SAP Note [1409604] , der indeholder den nødvendige SAP Host Agent version til Windows på Microsoft Azure, når du installerer på den nye Azure ressource leder.
* SAP Note [2191498] , der indeholder den nødvendige SAP Host Agent version for Linux på Microsoft Azure, når du installerer på den nye Azure ressource leder.
* SAP Note [2243692] , der indeholder oplysninger om licenser til SAP på Linux på Azure
* SAP Note [1984787] , der indeholder generelle oplysninger om SUSE LINUX Enterprise Server 12
* SAP Note [2002167] , der indeholder generelle oplysninger Red Hat Enterprise Linux 7.x
* [SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , der indeholder alle kræves SAP noter til Linux
* SAP bestemte PowerShell-cmdlet'er, der er en del af [Azure PowerShell][azure-ps]
* SAP bestemte Azure CLI, der er en del af [Azure CLI][azure-cli]
* [Microsoft Azure-portalen][azure-portal]

[comment]: <> (MSSedusch Opgaveliste tilføje ARM programrettelse niveau for SAP Host Agent i SAP Note 1409604)
 
Følgende vejledninger dækker emnet af SAP på Microsoft Azure samt:

* [SAP NetWeaver på Azure virtuelle maskiner (VM'er) – planlægning og implementeringsvejledningen] [-Planlægningsvejledning]
* [SAP NetWeaver på Azure virtuelle-maskiner (VM'er) – installationsvejledning (dette dokument)] [installationsvejledning]
* [SAP NetWeaver på Azure virtuelle-maskiner (VM'er) – DBMS installationsvejledning] dbms-vejledning

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Installationsscenarier af FOS for SAP på Microsoft Azure
I dette kapitel lærer du de forskellige måder at installation og de enkelte trin for hver installationstype.

### <a name="deployment-of-vms-for-sap"></a>Installation af FOS til SAP
Microsoft Azure får flere muligheder for at installere FOS og tilknyttede diske. Dermed er det vigtigt at forstå forskellene, da forberedelser af FOS være forskellige afhænger af metode til installation. Vi ser Generelt, i følgende scenarier:

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implementere en VM af Azure Marketplace
Du kan lide at tage et Microsoft eller 3 personer, forudsat at billede af Azure Marketplace for at installere din VM. Når du har installeret din VM på Microsoft Azure, kan du følge de samme retningslinjer og værktøjer til at installere SAP softwaren i din VM, som du ville gøre i et lokalt miljø. Til installation af SAP-software i Azure VM, SAP og Microsoft anbefaler at overføre og gemme SAP installationsmedierne i Azure virtuelle harddiske eller til at oprette en Azure VM arbejde som en en filserver, som indeholder alle de nødvendige SAP installationsmedier.

[comment]: <> (MSSedusch TODO Hvorfor skal vi anbefaler, at en filstyring fx filserver eller Virtuelle? Er, så forskellige fra det lokale?)

Få mere at vide i kapitel [Scenario 1: implementere en VM af Azure Marketplace for SAP] [installation-vejledning-3,2].

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Implementere en VM med et brugerdefineret billede
På grund af bestemte programrettelseskrav med hensyn til din OS eller DBMS version, kan de medfølgende billeder af Azure Marketplace ikke opfylder dine behov. Du kan derfor skal til at oprette en VM ved hjælp af din egen 'private' OS/DB VM billede som kan installeres bagefter flere gange.
Trin til at oprette et privat billede er forskellige i en Windows Phone og et Linux billede.

___

> ![Windows][Logo_Windows] Windows
>
> For at forberede et billede af Windows, der kan bruges til at udrulle flere virtuelle maskiner skal være Windows-indstillinger (som Windows SID og hostname) udtrukket/generalized på den lokale VM. Dette kan gøres ved hjælp af sysprep, som beskrevet på <https://technet.microsoft.com/library/cc721940.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> For at forberede et Linux-billede, der kan bruges til at udrulle flere virtuelle maskiner skal være visse Linux indstillinger udtrukket/generalized på den lokale VM. Dette kan gøres ved hjælp af waagent-deprovision som beskrevet i [denne artikel] [ virtual-machines-linux-capture-image] eller i [denne artikel][virtual-machines-linux-agent-user-guide-command-line-options].

___

Du kan konfigurere din indhold i databasen ved at bruge SAP Software klargøring Manager til at installere et nyt SAP-System, gendanne en sikkerhedskopi af databasen fra en virtuel harddisk, der er knyttet til den virtuelle maskine eller direkte gendanne en sikkerhedskopi af databasen fra Azure-lager, hvis DBMS understøtter det. (se [DBMS Installation Guide][dbms-guide]). Hvis du allerede har installeret en SAP-system i din lokale VM (især for 2 lag systemer), kan du tilpasse indstillingerne for SAP system efter installationen af Azure VM gennem fremgangsmåden System omdøbe understøttes af SAP Software klargøring Manager (SAP Note [1619720]). Ellers kan du installere softwaren SAP senere efter installationen af Azure VM.

Få mere at vide i kapitel [Scenario 2: implementering af en VM med et brugerdefineret billede til SAP] [installation-vejledning-3.3].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Flytte en VM fra det lokale til Microsoft Azure med en ikke-generalized disk
Du vil flytte et bestemt SAP-system fra det lokale til Microsoft Azure. Dette kan gøres ved at uploade den virtuelle harddisk, som indeholder Operativsystemet, de SAP binære filer og eventuel DBMS binære filer samt de virtuelle harddiske med data, og log filerne af DBMS til Microsoft Azure. I modsatte til dette scenario, der er beskrevet i kapitel [implementere en VM med et brugerdefineret billede] [installation-vejledning-3.1.2] ovenfor, du beholde hostname, SAP SID og SAP-brugerkonti i Azure VM som de er konfigureret i det lokale miljø. Derfor er generalisering operativsystemet ikke nødvendigt. Dette tilfælde gælder hovedsageligt i tværs lokale situationer, hvor en del af SAP-liggende køre i det lokale miljø og dele på Microsoft Azure.

Få mere at vide i kapitel [scenarie 3: flytte en VM fra ved hjælp af en ikke-generalized Azure Virtuelle med SAP lokalt] [installation-vejledning-3.4].

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenarie 1: Implementere en VM af Azure Marketplace for SAP
Microsoft Azure er muligheden at installere en VM forekomst af Azure Marketplace, som har nogle OS standardbilleder af Windows Server og forskellige Linux salgsdistributioner. Det er også muligt at installere et billede, der indeholder DBMS lagerenheder fx SQL Server. Få mere at vide ved hjælp af disse billeder med DBMS SKU'er i referere til [DBMS installationsvejledningen til] [dbms-vejledning]

SAP bestemte sekvens af trin, der anvender en VM af Azure Marketplace ville se således:

![Rutediagram over VM installation til SAP-systemer ved hjælp af et billede af VM fra Azure Marketplace][deployment-guide-figure-100]

Følgende trin skal udføres efter rutediagrammet:

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Oprette virtuelle maskine ved hjælp af portalen Azure
Den nemmeste måde at oprette en ny virtuel maskine ved hjælp af et billede fra Azure Marketplace er via Azure-portalen. Gå til <https://portal.azure.com/#create>. Angiv typen af operativsystem, som du vil installere i søgefeltet, f.eks. vinduer, SLES eller RHEL, og vælg versionen. Sørg for at markere implementeringsmodel "Azure ressourcestyring", og klik på Opret.

Guiden fører dig gennem de krævede parametre til at oprette den virtuelle maskine sammen med alle de ressourcer, der er påkrævet som netværksgrænseflader eller lagerplads konti. Nogle af disse parametre er:

1. Grundlæggende funktioner
    1. Navn: Navnet på ressourcen, det vil sige virtuelt navn
    1. Brugernavn og adgangskode/SSH offentlig nøgle: angive brugernavn og adgangskoden for den bruger, der oprettes under klargøringen. For en Linux virtuel maskine, du kan også angive den offentlige SSH nøgle, du vil bruge til at logge på computeren ved hjælp af SSH.
    1. Abonnement: Vælg det abonnement, du vil bruge til at klargøre den nye virtuelle maskine.
    1. Ressourcegruppe: Navnet på ressourcegruppen. Du kan enten indsætte navnet på en ny ressourcegruppe eller af en ressourcegruppe, der allerede findes
    1. Placering: Vælg den placering, hvor den nye virtuelle maskine skal anvendes. Hvis du vil oprette forbindelse den virtuelle maskine til dit lokale netværk, skal du sørge for at vælge placeringen af det virtuelle netværk, der forbinder Azure til dit lokale netværk. Få mere at vide kapitlet [Microsoft Azure Networking] [ planning-guide-microsoft-azure-networking] i [Planlægningsvejledning] [-Planlægningsvejledning].
1. Størrelse: Læs SAP Note [1928533] for en liste over understøttede VM datatyper. Også Sørg for at vælge den korrekte type, hvis du vil bruge Premium-lager. Ikke alle VM datatyper understøtter Premium lagerplads. Se kapitel [lagerplads: Microsoft Azure-lager og datadisce] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] og [Azure Premium lager] [planlægning-vejledning-azure-premium-lager] i [Planlægningsvejledning] [-Planlægningsvejledning] for at få flere oplysninger.
1. Indstillinger
    1. Lagerplads konto: Du kan markere en eksisterende lagerplads-konto eller oprette en ny. Læs kapitel [Microsoft Azure-lager] [dbms-vejledning-2.3] [DBMS guide] [dbms-vejledning] få mere at vide om de forskellige lagerplads. Bemærk, at ikke alle lagerplads datatyper understøttes til kørsel af SAP-programmer.
    1. Virtuelt netværk og undernet: Vælg det virtuelle netværk, der er forbundet til dit lokale netværk, hvis du vil integrere den virtuelle maskine i dit intranet.
    1. Offentlige IP-adresse: Vælg den offentlige IP-adresse, du vil bruge, eller Angiv parametrene for at oprette en ny offentlige IP-adresse. Du kan bruge en offentlige IP-adresse at få adgang til din virtuelle maskine via internettet. Kontrollér, at du også oprette en netværk sikkerhedsgruppe for at filtrere adgang til din virtuelle maskine.
    1. Netværk sikkerhedsgruppe: se [Hvad er et netværk sikkerhed gruppe (NSG)] [ virtual-networks-nsg] kan finde flere oplysninger.
    1. Overvågning: Du kan deaktivere indstillingen diagnosticering. Det vil aktiveret automatisk, når du kører kommandoerne til at aktivere Azure udvidet overvågning, som er beskrevet i kapitel [Konfigurere overvågning][deployment-guide-configure-monitoring-scenario-1].
    1. Tilgængelighed: Vælg en Availablility sæt eller Angiv parametrene for at oprette et nyt sæt Availablility. Du kan finde flere oplysninger kapitel [Azure tilgængelighed sæt] [planlægning-vejledning-3.2.3].
1. Oversigt: Validere oplysningerne på siden Oversigt, og klik på OK.

Når du har afsluttet guiden, som din virtuelle maskine skal installeres i ressourcegruppen, du har valgt.

#### <a name="create-virtual-machine-using-a-template"></a>Oprette virtuelle computeren ved hjælp af en skabelon
Du kan også oprette en installation, ved at bruge en af SAP-skabelonerne udgivet i [azure-Hurtig start-skabeloner github lager][azure-quickstart-templates-github]. Eller du kan oprette en virtuel maskine, ved hjælp af [Azure Portal][virtual-machines-windows-tutorial], [PowerShell] [ virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] eller [Azure CLI] [ virtual-machines-linux-tutorial] manuelt.

* [skabelon til 2 lag konfiguration (kun én virtuelt)] [ sap-templates-2-tier-marketplace-image] Brug denne skabelon, hvis du vil oprette et system med 2-niveauer med kun én virtuelt.
* [skabelon til konfiguration af 3-niveau (flere virtuelle maskiner)] [ sap-templates-3-tier-marketplace-image] Brug denne skabelon, hvis du vil oprette et system med 3 niveauer ved hjælp af flere virtuelle computere.

Når du har åbnet en af de skabeloner, der er ovenfor, fører portalen Azure til panelet Rediger parametre. Angiv følgende oplysninger:

* **sapSystemId**: SAP System-ID
* **osType**: operativsystem, som du vil installere, f.eks. Windows Server 2012 R2, SLES 12 eller RHEL 7.2
    * Listen indeholder kun versioner, der understøttes af SAP på Microsoft Azure
* **sapSystemSize**: størrelsen af SAP-systemet
    * Mængden af SAP'ER give det nye system. Hvis du ikke er sikker på hvor mange SAP'ER kræver, at systemet, skal du bede din SAP Technology Partner eller Workflowsystemet
* **systemAvailability**: (kun 3 lag skabelon) systemets tilgængelighed 
    * Vælg HA for en konfiguration, som egner sig til en HA-installation. To Webdatabase servere, og der oprettes to servere for ASC'ER.
* storageType: (kun 2 lag skabelon) Type lagerplads, der skal bruges 
    * Til større systemer anbefales brugen af Premium lagerplads. Du kan finde flere oplysninger om de forskellige lagerplads læse 
        * [Microsoft Azure-lager] [dbms-vejledning-2.3] [dbms-vejledning] [DBMS guide]
        * [Premium opbevaring: High-Performance lagerplads til Azure virtuelt arbejdsmængder][storage-premium-storage-preview-portal]
        * [Introduktion til Microsoft Azure-lager][storage-introduction]
* **adminUsername** og **adminPassword**: brugernavn og din adgangskode
    * Der oprettes en ny bruger, der kan bruges til at logge på computeren.
* **newOrExistingSubnet**: bestemmer, om en ny virtuelt netværk og undernet skal der oprettes eller en eksisterende undernet skal bruges. Hvis du allerede har et virtuelt netværk, der er forbundet til dit lokale netværk, kan du vælge eksisterende.
* **subnetId**: ID'ET for det undernet, som de virtuelle maskiner skal forbindes til. Vælg undernettet af netværket VPN- eller Express rute virtuelle tilsluttes den virtuelle maskine til dit lokale netværk. ID'ET ser normalt Sådan /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Når du har angivet alle parametre, Vælg abonnementet, og den ressourcegruppe, du vil bruge. Du kan enten vælge et eksisterende ressourcegruppe eller oprette en ny ved at vælge "+ Ny" i rullemenuen. Hvis du opretter en ny ressourcegruppe, har du også at markere det område, hvor ressourcegruppen og den virtuelle maskine oprettes.

Gennemse de juridiske begreber, acceptere dem, og klik på Opret.

Vær opmærksom på, at Azure VM Agent er installeret som standard, når du bruger et billede fra Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurere indstillinger for proxyserver
Afhængigt af konfigurationen af dit lokale netværk, kan det være påkrævet for at konfigurere proxyen på din virtuelle maskine, hvis den er tilsluttet til dit lokale netværk via VPN- eller Express rute. Ellers kan den virtuelle maskine ikke længere kunne få adgang til internettet, og derfor kan ikke hente de nødvendige udvidelser eller indsamling overvågningsdata. Se kapitel [Konfigurere Proxy] [ deployment-guide-configure-proxy] af dette dokument.

#### <a name="join-domain-windows-only"></a>Oprette forbindelse til domæne (kun Windows)
I de tilfælde, at distributionen i Azure har forbindelse til lokale AD/DNS via Azure-til-websted eller Express rute (også opført som tværs lokale i den [planlægning og implementering Guide][planning-guide]) det forventes, at VM deltager i et lokalt domæne. Overvejelser om på dette trin er beskrevet i kapitel [deltage i VM til lokale domæne (kun Windows)] [installation-vejledning-4.3] i dette dokument.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurere overvågning
Konfigurere i Azure udvidet overvågning udvidelse til SAP, som beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5] i dette dokument.

Se forudsætninger for SAP overvågning til nødvendige mindste versioner af SAP kerne og SAP Host Agent i de ressourcer, der er angivet i kapitel [SAP ressourcer] [installation-guide 2.2] i dette dokument.

#### <a name="monitoring-check"></a>Overvågning af markeringen
Kontrollere, om overvågning fungerer som beskrevet i kapitel [kontrollerer og fejlfinding til slut overvågnings konfiguration for SAP på Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Indlæg installation trin
Når du har oprettet VM, som skal installeres og det er derefter på at installere alle de nødvendige software-komponenter til VM. Denne type VM installation kræver derfor softwaren skal være installeret beeing allerede findes i Microsoft Azure i nogle andre VM eller som disk, som kan knyttes. Eller vi søger i tværs lokale scenarier hvor forbindelse til de lokale Aktiver (Installer aktier) er en given.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenarie 2: Implementering af en VM med et brugerdefineret billede til SAP
Der er en metode til at forberede og oprette et brugerdefineret billede og bruge det til at oprette flere nye FOS, som beskrevet i den [planlægning og implementeringsvejledningen] [-Planlægningsvejledning] allerede findes i detaljeret vejledning. Rækkefølgen af trinnene i rutediagram ville se således:
 
![Rutediagram over VM installation til SAP-systemer ved hjælp af et billede af VM i privat Marketplace][deployment-guide-figure-300]

Følgende trin skal udføres efter rutediagrammet:

#### <a name="create-virtual-machine"></a>Oprette virtuelle maskine
Brug en af SAP-skabelonerne udgives på [azure-Hurtig start-skabeloner github lager]til at oprette en installation, ved at bruge en privat OS billede via Azure administrationsportalen[azure-quickstart-templates-github].
Du kan også oprette en virtuel maskine, ved hjælp af [PowerShell] [ virtual-machines-upload-image-windows-resource-manager] manuelt. 

* [skabelon til 2 lag konfiguration (kun én virtuelt)] [ sap-templates-2-tier-user-image] Brug denne skabelon, hvis du vil oprette et system med 2-niveauer ved hjælp af kun én virtuelt og OS billedet.
* [skabelon til konfiguration af 3-niveau (flere virtuelle maskiner)] [ sap-templates-3-tier-user-image] Brug denne skabelon, hvis du vil oprette et system med 3 niveauer ved hjælp af flere virtuelle maskiner og OS billedet.

Når du har åbnet en af de skabeloner, der er ovenfor, fører portalen Azure til panelet Rediger parametre. Angiv følgende oplysninger:

* **sapSystemId**: SAP System-ID
* **osType**: operativsystemtype, du vil installere Windows eller Linux
* **sapSystemSize**: størrelsen af SAP-systemet
    * Mængden af SAP'ER give det nye system. Hvis du ikke er sikker på hvor mange SAP'ER kræver, at systemet, skal du bede din SAP Technology Partner eller Workflowsystemet
* **systemAvailability**: (kun 3 lag skabelon) systemets tilgængelighed 
    * Vælg HA for en konfiguration, som egner sig til en HA-installation. To Webdatabase servere, og der oprettes to servere for ASC'ER.
* **storageType**: (kun 2 lag skabelon) Type lagerplads, der skal bruges 
    * Til større systemer anbefales brugen af Premium lagerplads. Du kan finde flere oplysninger om de forskellige lagerplads læse 
        * [Microsoft Azure-lager] [dbms-vejledning-2.3] [dbms-vejledning] [DBMS guide]
        * [Premium opbevaring: High-Performance lagerplads til Azure virtuelt arbejdsmængder][storage-premium-storage-preview-portal]
        * [Introduktion til Microsoft Azure-lager][storage-introduction]
* **adminUsername** og **adminPassword**: brugernavn og din adgangskode
    * Der oprettes en ny bruger, der kan bruges til at logge på computeren.
* **userImageVhdUri**: URI af private OS billede virtuelle fx https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
* **userImageStorageAccount**: navnet på kontoen lagerplads, hvor privat OS billedet er gemt fx `<accountname`> i eksemplet ovenfor URI
* **newOrExistingSubnet**: bestemmer, om en ny virtuelt netværk og undernet skal der oprettes eller en eksisterende undernet skal bruges. Hvis du allerede har et virtuelt netværk, der er forbundet til dit lokale netværk, kan du vælge eksisterende.
* **subnetId**: ID'ET for det undernet, som de virtuelle maskiner skal forbindes til. Vælg undernettet af netværket VPN- eller Express rute virtuelle tilsluttes den virtuelle maskine til dit lokale netværk. ID'ET ser normalt Sådan /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Når du har angivet alle parametre, Vælg abonnementet, og den ressourcegruppe, du vil bruge. Du kan enten vælge et eksisterende ressourcegruppe eller oprette en ny ved at vælge "+ Ny" i rullemenuen. Hvis du opretter en ny ressourcegruppe, har du også at markere det område, hvor ressourcegruppen og den virtuelle maskine oprettes.

Gennemse de juridiske begreber, acceptere dem, og klik på Opret.

#### <a name="install-vm-agent-linux-only"></a>Installere VM Agent (kun Linux)
Linux Agent skal allerede være installeret på bruger billede, hvis du vil bruge skabelonen til ovenfor. Ellers mislykkes installationen. Hent og Installer VM Agent i bruger billedet, som beskrevet i kapitel [hente, installere og aktivere Azure VM Agent] [installation-vejledning-4.4] i dette dokument.
Hvis du ikke bruge skabelonerne ovenfor, kan du også installere VM Agent bagefter.

#### <a name="join-domain-windows-only"></a>Oprette forbindelse til domæne (kun Windows)
I de tilfælde, at distributionen i Azure har forbindelse til lokale AD/DNS via Azure-til-websted eller Express rute (også opført som tværs lokale i den [planlægning og implementering Guide][planning-guide]) det forventes, at VM deltager i et lokalt domæne. Overvejelser om på dette trin er beskrevet i kapitel [deltage i VM til lokale domæne (kun Windows)] [installation-vejledning-4.3] i dette dokument.

#### <a name="configure-proxy-settings"></a>Konfigurere indstillinger for proxyserver
Afhængigt af konfigurationen af dit lokale netværk, kan det være påkrævet for at konfigurere proxyen på din virtuelle maskine, hvis den er tilsluttet til dit lokale netværk via VPN- eller Express rute. Ellers kan den virtuelle maskine ikke længere kunne få adgang til internettet, og derfor kan ikke hente de nødvendige udvidelser eller indsamling overvågningsdata. Se kapitel [Konfigurere Proxy] [ deployment-guide-configure-proxy] af dette dokument.

#### <a name="configure-monitoring"></a>Konfigurere overvågning
Konfigurere Azure overvågning udvidelse til SAP, som beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5] i dette dokument.
Se forudsætninger for SAP overvågning til nødvendige mindste versioner af SAP kerne og SAP Host Agent i de ressourcer, der er angivet i kapitel [SAP ressourcer] [installation-guide 2.2] i dette dokument.

#### <a name="monitoring-check"></a>Overvågning af markeringen
Kontrollere, om overvågning fungerer som beskrevet i kapitel [kontrollerer og fejlfinding til slut overvågnings konfiguration for SAP på Azure][deployment-guide-troubleshooting-chapter].

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenarie 3: Flytte en VM fra ved hjælp af en ikke-generalized Azure Virtuelle med SAP lokalt
Dette scenario er vælge en adresse til et SAP-system, skal du blot flyttes i den aktuelle formular og figurer fra det lokale til Azure små eller store bogstaver. Betyder, at ingen navn ændring af Windows eller Linux hostname og SAP SID eller noget med dette sker. I dette tilfælde er den virtuelle harddisk refereres til ikke som et billede under installationen, men anvendes direkte som OS disken. Med hensyn til installationen, er dette tilfælde forskellig fra de to tidligere tilfælde ved, at VM Agent ikke kan installeres automatisk under installationen. Derfor Azure VM Agent skal hentes fra Microsoft og skal være installeret og aktiveret i VM manuelt senere. Når denne opgave er fuldført, kan du fortsætte med at starte i SAP Host overvågning Azure udvidelse og dens konfiguration. Få at vide om funktionen af Azure VM Agent, skal du kontrollere i denne artikel:

[comment]: <> (MSSedusch Opgaveliste Opdater Windows linket nedenfor) 

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/Archive/2014/02/17/BgInfo-guest-Agent-Extension-for-Azure-VMS.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Brugervejledning til Azure Linux Agent][virtual-machines-linux-agent-user-guide]

___

Arbejdsprocessen over de forskellige trin ser sådan ud:
 
![Rutediagram over VM installation til SAP-systemer ved hjælp af en VM Disk][deployment-guide-figure-400]

Hvis du allerede har, disken er allerede har overført og defineret i Azure (se [planlægning og implementering Guide][planning-guide]), skal du følge disse trin

#### <a name="create-virtual-machine"></a>Oprette virtuelle maskine
Du kan oprette en installation, ved at bruge en privat OS disk via Azure-portalen ved brug af skabelonen SAP udgives på [azure-Hurtig start-skabeloner github lager][azure-quickstart-templates-github]. Du kan også oprette et virtuelt ved hjælp af den [PowerShell eller Azure CLI manuelt.

* [skabelon til 2 lag konfiguration (kun én virtuelt)][sap-templates-2-tier-os-disk]
    * Brug denne skabelon, hvis du vil oprette et system med 2-niveauer med kun én virtuelt.

Når du har åbnet skabelonen ovenfor, fører portalen Azure til panelet Rediger parametre. Angiv følgende oplysninger:

* **sapSystemId**: SAP System-ID
* **osType**: operativsystemtype, du vil installere Windows eller Linux
* **sapSystemSize**: størrelsen af SAP-systemet
    * Mængden af SAP'ER give det nye system. Hvis du ikke er sikker på hvor mange SAP'ER kræver, at systemet, skal du bede din SAP Technology Partner eller Workflowsystemet
* **storageType**: (kun 2 lag skabelon) Type lagerplads, der skal bruges 
    * Til større systemer anbefales brugen af Premium lagerplads. Du kan finde flere oplysninger om de forskellige lagerplads læse 
        * [Microsoft Azure-lager] [dbms-vejledning-2.3] [dbms-vejledning] [DBMS guide]
        * [Premium opbevaring: High-Performance lagerplads til Azure virtuelt arbejdsmængder][storage-premium-storage-preview-portal]
        * [Introduktion til Microsoft Azure-lager][storage-introduction]
* **osDiskVhdUri**: URI af private Operativsystemet disk fx https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
* **newOrExistingSubnet**: bestemmer, om en ny virtuelt netværk og undernet skal der oprettes eller en eksisterende undernet skal bruges. Hvis du allerede har et virtuelt netværk, der er forbundet til dit lokale netværk, kan du vælge eksisterende.
* **subnetId**: ID'ET for det undernet, som de virtuelle maskiner skal forbindes til. Vælg undernettet af netværket VPN- eller Express rute virtuelle tilsluttes den virtuelle maskine til dit lokale netværk. ID'ET ser normalt Sådan /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Når du har angivet alle parametre, Vælg abonnementet, og den ressourcegruppe, du vil bruge. Du kan enten vælge et eksisterende ressourcegruppe eller oprette en ny ved at vælge "+ Ny" i rullemenuen. Hvis du opretter en ny ressourcegruppe, har du også at markere det område, hvor ressourcegruppen og den virtuelle maskine oprettes.

Gennemse de juridiske begreber, acceptere dem, og klik på Opret.

#### <a name="install-vm-agent"></a>Installere VM Agent
Linux Agent skal allerede være installeret i OS disken, hvis du vil bruge skabelonen til ovenfor. Ellers mislykkes installationen. Downloade og installere VM Agent i VM, som beskrevet i kapitel [hente, installere og aktivere Azure VM Agent] [installation-vejledning-4.4] i dette dokument.

Hvis du ikke bruge skabelonerne ovenfor, kan du også installere VM Agent bagefter.

#### <a name="join-domain-windows-only"></a>Oprette forbindelse til domæne (kun Windows)
I de tilfælde, at distributionen i Azure har forbindelse til lokale AD/DNS via Azure-til-websted eller Express rute (også opført som tværs lokale i den [planlægning og implementering Guide][planning-guide]) det forventes, at VM deltager i et lokalt domæne. Overvejelser om på dette trin er beskrevet i kapitel [deltage i VM til lokale domæne (kun Windows)] [installation-vejledning-4.3] i dette dokument.

#### <a name="configure-proxy-settings"></a>Konfigurere indstillinger for proxyserver
Afhængigt af konfigurationen af dit lokale netværk, kan det være påkrævet for at konfigurere proxyen på din virtuelle maskine, hvis den er tilsluttet til dit lokale netværk via VPN- eller Express rute. Ellers kan den virtuelle maskine ikke længere kunne få adgang til internettet, og derfor kan ikke hente de nødvendige udvidelser eller indsamling overvågningsdata. Se kapitel [Konfigurere Proxy] [ deployment-guide-configure-proxy] af dette dokument.

#### <a name="configure-monitoring"></a>Konfigurere overvågning
Konfigurere Azure udvidet overvågning af udvidelse til SAP, som beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5] i dette dokument.

Se forudsætninger for SAP overvågning til nødvendige mindste versioner af SAP kerne og SAP Host Agent i de ressourcer, der er angivet i kapitel [SAP ressourcer] [installation-guide 2.2] i dette dokument.

#### <a name="monitoring-check"></a>Overvågning af markeringen
Kontrollere, om overvågning fungerer som beskrevet i kapitel [kontrollerer og fejlfinding til slut overvågnings konfiguration for SAP på Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Scenario 4: Opdatering af overvågning konfiguration til SAP
Der er tilfælde, hvor du har brug for at opdatere overvågning konfigurationen:

* Fælles MS/SAP teamet udvidet overvågningsfunktionerne og besluttede dig for at tilføje flere tællere eller slette nogle tællere. 
* Microsoft introducerer en ny version af den underliggende Azure infrastruktur levere den overvågningsdata, og i Azure udvidet overvågning udvidelse til SAP tilpasse sig disse ændringer.
* Du tilføjer yderligere virtuelle harddiske fastgøres til din Azure VM, eller du fjerner en virtuel harddisk. I dette tilfælde skal du opdatere samlingen af lagerplads relaterede data. Hvis du ændrer din konfiguration ved at tilføje eller slette slutpunkter eller tildele IP-adresser til en VM, påvirker det ikke overvågning konfigurationen.
* Du ændrer størrelsen på din Azure VM fx fra A5 til andre størrelsen på VM.
* Du tilføjer nye netværksgrænseflader til din Azure VM

For at opdatere overvågning konfigurationen, skal du gå videre på følgende måde:

* Opdatere overvågning infrastrukturen ved at følge trinnene beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5] i dette dokument. En køre igen på det script, der er beskrevet i kapitlet registrerer, at en overvågning konfiguration er installeret og foretager de nødvendige ændringer af overvågning konfigurationen. 

___

> ![Windows][Logo_Windows] Windows
>
> Ingen brugerhandlinger er påkrævet til opdatering af Azure VM Agent. VM Agent automatisk opdaterer sig selv, og kræver ikke en VM genstart.
>
> ![Linux][Logo_Linux] Linux
>
> Følg trinnene i [denne artikel] [ virtual-machines-linux-update-agent] opdatere Azure Linux Agent. 

___

## <a name="detailed-single-deployment-steps"></a>Detaljeret enkelt installation trin

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementere Azure PowerShell-cmdlet'er
* Gå til <https://azure.microsoft.com/downloads/>
* I afsnittet er "kommandolinjeparametre Tools' der en sektion med navnet 'Windows PowerShell'. Følg linket "Installer".
* Microsoft Download Manager anvender pop op-vindue med en slutter med .exe post. Vælg indstillingen "Kør".
* En pop op-menuen kommer op spørger, om at køre Microsoft Web Platform Installer. Tryk på Ja
* Der vises en skærm som denne:
 
![Installationsskærmbillede til Azure PowerShell-cmdlet'er][deployment-guide-figure-500]
<a name="figure-5"></a>

* Tryk på Installér og acceptere den slutbrugerlicensaftale (EULA).

Se ofte om PowerShell-cmdlet'er er blevet opdateret. Der er som regel opdateringer på en månedlig periode. Den nemmeste måde at gøre dette er at følge trinnene til installation, som beskrevet ovenfor til installation skærmbillede vises i [denne] [ deployment-guide-figure-5] figur. I dette skærmbillede vises Udgivelsesdato af cmdlet'erne samt det faktiske release tal. Medmindre angivet anderledes i SAP noter [1928533] eller [2015553], anbefales det at arbejde med den seneste version af Azure PowerShell-cmdlet'er.

Den aktuelle installerede version af Azure-cmdletter den skrivebord/bærbare computer kan kontrolleres med kommandoen PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

Resultatet skal vises som vist nedenfor i [denne] [ deployment-guide-figure-6] figur.

![Resultatet af Azure PS cmdlet version afkrydsningsfelt][deployment-guide-figure-600]
<a name="figure-6"></a>

Hvis den Azure cmdlet-version, der er installeret på computeren/bærbar er den aktuelle, det første skærmbillede efter start af Microsoft Web Platform Installer ser lidt anderledes sammenlignet med den, der vises i [denne] [ deployment-guide-figure-5] figur.

Skal du bemærke den røde cirkel under [figur] [ deployment-guide-figure-7] nedenfor.
 
![Installationsskærmbillede til Azure PowerShell-cmdlet'er, der angiver, at den seneste version af Azure PS cmdletter er installeret][deployment-guide-figure-700]
<a name="figure-7"></a>

Hvis skærmen ser ud som [over][deployment-guide-figure-7], der angiver, at den nyeste version af Azure cmdlet allerede er installeret, der er ikke nødvendigt at fortsætte med installationen. I dette tilfælde kan du 'afslutte' installationen på nuværende tidspunkt.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementere Azure CLI
* Gå til <https://azure.microsoft.com/downloads/>
* I afsnittet er "kommandolinjeparametre Tools' der en sektion med navnet 'Azure kommandolinjeparametre interface'. Følg linket Installer for dit operativsystem.

Se ofte om Azure CLI er blevet opdateret. Der er som regel opdateringer på en månedlig periode. Den nemmeste måde at gøre dette er at følge trinnene til installation som beskrevet ovenfor.

Den aktuelle installerede version af Azure CLI den skrivebord/bærbare computer kan kontrolleres med kommandoen:

```
azure --version
```

Resultatet skal vises som vist nedenfor i [denne] [ deployment-guide-figure-azure-cli-version] figur.

![Resultatet af Azure CLI version afkrydsningsfelt][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Deltage i VM til lokale domæne (kun Windows)
I tilfælde, hvor du installerer SAP FOS til et kryds lokale scenarie hvor lokale AD og DNS forlænges til Azure, det er meningen, at FOS er sammenføjet i et lokalt domæne. Den detaljerede vejledning til at deltage i et VM til et lokalt domæne og ekstra software, der kræves for at være medlem af et lokalt domæne er kunden afhængige. Normalt deltager i et VM til et lokalt domæne betyder installation af yderligere software som beskyttelse mod skadelig software software eller forskellige supportmedarbejdere fra sikkerhedskopien eller overvågning software.

Desuden kan du har brug at sikre, at i de tilfælde hvor internetproxyindstillinger gennemtvinges når du deltager i et domæne, der Windows lokale System Account(S-1-5-18) i gæst VM disse indstillinger samt. Easiest er tvinge proxy med domæne Gruppepolitik, der gælder for systemer i domænet.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Hente, installere og aktivere Azure VM Agent
Følgende trin er nødvendigt, når en VM for SAP webstedsarbejdsprocessen installeres fra en OS billeder, der ikke er generalized fx ikke blevet behandlet med Sysprep til Windows. Det er ikke nødvendigt at installere Agent til virtuelle maskiner implementeret fra Azure marketplace. Disse billeder indeholder allerede Azure Agent.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Hent Azure VM Agent:
    * Hente installationsprogrammet Azure VM Agent fra: <https://go.microsoft.com/fwlink/?LinkId=394789>
    * Gemme VM Agent MSI-pakken lokalt på den bærbare computer eller en server
* Installer Azure VM Agent:
    * Oprette forbindelse til udløst Azure VM med Terminal Services (RDP)
    * Åbn Windows Stifinder-vindue på VM og Åbn en destinationsmappe til MSI-fil af VM Agent
    * Træk og slip på Azure VM Agent Installer .msi-filen fra din lokale bærbar/server til destinationsmappen af VM Agent i VM
    * Dobbeltklik på MSI-fil i VM
    * For VM optaget lokale domæner, Sørg for, at eventuel internetproxyindstillinger gælder for kontoen Windows lokalt System (S-1-5-18) i VM, såvel som beskrevet i kapitel [Konfigurere Proxy][deployment-guide-configure-proxy]. VM Agent kører i denne kontekst og skal kunne oprette forbindelse til Azure.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Installer VM Agent for Linux ved hjælp af følgende kommando

- **SLES**

```
sudo zypper install WALinuxAgent
```
- **RHEL**

```
sudo yum install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurere Proxy
Trin til konfiguration af proxyen er forskellige i Windows og Linux.

#### <a name="windows"></a>Windows
Disse indstillinger skal også være gyldige for kontoen LocalSystem adgang til internettet. Hvis dine proxyindstillinger ikke er angivet af gruppepolitik, kan du konfigurere dem til LocalSystem-kontoen skal du følge disse trin for at konfigurere den.

1.  Åbn gpedit.msc
1.  Gå til konfiguration computeren –> Administrative skabeloner -> Windows-komponenter -> Internet Explorer og aktivere "gør proxyindstillinger indstillinger per computer (i stedet for hver bruger)
1.  Åbn Kontrolpanel, og gå til netværk og Internet -> Internetindstillinger
1.  Åbn fanen forbindelser, og klik på LAN-indstillinger
1.  Deaktivere "Automatisk registrering af indstillinger"
1.  Aktivere "Brug en proxyserver til LAN", og Angiv proxy-vært og port

#### <a name="linux"></a>Linux
Konfigurere de korrekte proxy i konfigurationsfilen af Microsoft Azure gæst Agent, som er placeret i /etc/waagent.conf. Følgende parametre skal angives:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Genstarte agenten, når du har ændret dens konfiguration med

```
sudo service waagent restart
```

Proxyindstillinger i /etc/waagent.conf gælder også for de nødvendige VM udvidelser. Hvis du vil bruge Azure typer lagre, skal du kontrollere, at trafik til disse typer lagre ikke vil via lokalt intranet. Hvis du har oprettet brugerdefineret omdirigerer aktivere tvunget tunnelføring, skal du sørge for at tilføje en rute dirigerer, som trafik til typer lagre direkte til internettet og ikke via forbindelsen til websted.

- **SLES** Du skal også tilføje omdirigerer for de viste i /etc/regionserverclnt.cfg IP-adresser. Et eksempel er vist i skærmbilledet nedenfor. 

- **RHEL** Du skal også tilføje omdirigerer til IP-adresserne på de værter, der er angivet i /etc/yum.repos.d/rhui-load-balancers. Et eksempel er vist i skærmbilledet nedenfor.

Se [denne artikel]kan finde flere oplysninger om brugerdefineret omdirigerer[virtual-networks-udr-overview].

![Tvungen tunnelføring][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurere Azure udvidet overvågning lokalnummer til SAP
Når VM er forberedt, som beskrevet i kapitel [installation scenarier af FOS for SAP på Microsoft Azure] [installation-vejledning-3], Azure VM Agent er installeret på computeren. Det næste vigtige trin er at installere i Azure udvidet overvågning udvidelse til SAP, der findes i Azure lokalnummer lager i den globale datacentre af Microsoft Azure. Få mere at vide, skal du kontrollere [planlægning og implementeringsvejledningen] [planlægning-vejledning-9.1]. 

Du kan bruge Azure PowerShell eller Azure CLI til at installere og konfigurere i Azure udvidet overvågning udvidelse til SAP. Læs kapitel [Azure PowerShell] [installation-vejledning-4.5.1] Hvis du vil installere filtypenavnet på en Windows eller Linux VM ved hjælp af en Windows-computer. For at installere filtypenavnet på en Linux VM ved hjælp af en Linux Læs skrivebord kapitel [Azure CLI] [installation-vejledning-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell til Linux og Windows FOS
For at udføre opgaven til at installere i Azure udvidet overvågning udvidelse til SAP, skal du udføre følgende trin:

* Sørg for, at du har installeret den nyeste version af Microsoft Azure PowerShell-cmdlet. Se kapitel [implementerer Azure PowerShell-cmdlet'er] [installation-vejledning-4.1] i dette dokument.  
* Kør følgende PowerShell-cmdlet. Kør cmdlet accepterer Get-AzureRmEnvironment for en liste over tilgængelige miljøer. Hvis du vil bruge offentlige Azure, er AzureCloud i dit miljø. Vælg AzureChinaCloud for Azure i Kina.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Når du har givet din kontodata og Azure Virtual Machine, bliver scriptet installere de nødvendige udvidelser og Aktivér de nødvendige funktioner. Det kan tage flere minutter.
Læs [Denne MSDN-artikel] [ msdn-set-azurermvmaemextension] kan finde flere oplysninger om sæt-AzureRmVMAEMExtension.
  
![Skærmbillede af resultatet i vellykket udførelse af SAP bestemte Azure cmdlet'en Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

En vellykket kørsel af sæt AzureRmVMAEMExtension vil gøre alle de trin, der er nødvendige for at konfigurere værten for overvågning af funktionaliteten for SAP. 

Output skal køre scriptet ser sådan ud:

* Bekræftelse, overvågning konfigurationen af den Base virtuelle harddisk (der indeholder OS) plus alle yderligere virtuelle harddiske er tilsluttet til VM er blevet konfigureret.
* De næste to meddelelser bekræfter konfigurationen af Lagringsmål for en bestemt lagerplads konto. 
* Én linjes output giver en status på den faktiske opdatering af overvågning konfigurationen.
* En anden vises bekræfter, at konfigurationen er blevet installeret eller opdateret.
* Den sidste linje i output er til orientering med mulighed for at teste overvågning konfigurationen.
* For at kontrollere, at alle trin i Azure udvidet overvågning er blevet udført og, Azure-Udgivelsesinfrastruktur indeholder de nødvendige data, kan du fortsætte med kontrollen er klar til Azure udvidet overvågning af udvidelse til SAP, som beskrevet i kapitel [parathed kontrollere, om Azure udvidet overvågning for SAP] [installation-vejledning-5.1] i dette dokument. 
* For at fortsætte med at gøre dette, skal du vente 15-30 minutter, indtil Azure diagnosticering har de relevante data, der indsamles.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI for Linux FOS

For at udføre opgaven til at installere i Azure udvidet overvågning udvidelse til SAP ved hjælp af Azure CLI, skal du udføre følgende trin:

1. Installer Azure CLI, som beskrevet i [denne] [ azure-cli] artikel
1. Logon med din Azure-konto

    ```
    azure login
    ```
1. Skifte til Azure ressourcestyring tilstand

    ```
    azure config mode arm
    ```
1. Aktivere Azure skærpet overvågning

    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
1. Kontrollere, at Azure udvidet overvågning er aktiv på Azure Linux VM. Kontrollér, om fil /var/lib/AzureEnhancedMonitor/PerfCounters findes. Hvis der findes, viser oplysninger der indsamles via AEM med:

    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    Så får du output som:
    
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontroller og fejlfinding til slut overvågning konfiguration for SAP på Azure
Når du har installeret din Azure VM og konfigurere den relevante Azure overvågning infrastruktur, kan du kontrollere, om alle komponenter i Azure udvidet overvågning fungerer i et stort måde. 

Derfor udføre parathed Kontrollér, om i Azure udvidet overvågning udvidelse til SAP, som beskrevet i kapitel [parathed kontrollere, om Azure udvidet overvågning for SAP] [installation-vejledning-5.1]. Hvis resultatet af denne kontrol er positivt, og du får alle relevante ydeevne tællere, er Azure overvågning konfigureret korrekt. I dette tilfælde skal du fortsætte med installationen af SAP Host Agent som beskrevet i de SAP-noter, der er angivet i kapitel [SAP ressourcer] [installation-guide 2.2] i dette dokument. Hvis resultatet af kontrollen parathed angiver manglende tællere, kan du gå videre afholde tilstandskontrol for Azure overvågning infrastrukturen, som beskrevet i kapitel [tilstandskontrol for Azure overvågning konfiguration af infrastruktur] [installation-vejledning-5.2]. I tilfælde af eventuelle problemer med konfigurationen af Azure overvågning kontrollere kapitel [yderligere fejlfinding af Azure overvågning infrastrukturen for SAP] [installation-vejledning-5.3] for yderligere hjælp til fejlfinding.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Kontrollér, om Azure skærpet overvågning for SAP der er klar
Med denne kontrol sikre du dig, at omfanget, der skal vises i dit SAP-program der leveres helt med den underliggende Azure-infrastruktur. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Udfør parathedskontrol på en Windows-VM
For at kunne udføre parathedskontrollen logon til (administratorkonto ikke er nødvendigt) Azure Virtual Machine og udføre følgende trin:

* Åbn en kommandoprompt med Windows og skifte til installationsmappen af filtypenavnet Azure overvågning for SAP C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop

Delen version, der er angivet på stien til filtypenavnet overvågning ovenfor kan variere. Hvis du får vist flere mapper for overvågning lokalnummer-versionen i installationsmappen, kontrollere konfigurationen af tjenesten Windows 'AzureEnhancedMonitoring', og Skift til den mappe, der er angivet som 'Path til den eksekverbare fil'.
 
![Egenskaber for tjenesten kører i Azure udvidet overvågning udvidelse til SAP][deployment-guide-figure-1000]

* Udføre azperflib.exe i kommandovinduet uden parametre.

> [AZURE.NOTE] Azperflib.exe kører i en løkke og opdaterer indsamlede tællere hver 60 sekunder. Luk kommandovinduet for at afslutte indstillingen Gentag.

Hvis Azure udvidet overvågning udvidelsen ikke er installeret eller tjenesten 'AzureEnhancedMonitoring' ikke kører, er filtypenavnet ikke konfigureret korrekt. I dette tilfælde skal du følge kapitel [yderligere fejlfinding af Azure overvågning infrastrukturen for SAP] [installation-vejledning-5.3] for at få detaljerede instruktioner Sådan Geninstaller filtypenavnet.

##### <a name="check-the-output-of-azperflibexe"></a>Kontrollere output fra azperflib.exe
Output fra azperflib.exe viser alle udfyldt Azure ydeevne tællere til SAP. Nederst på listen over indsamlede tællere skal finde du en oversigt over og en tilstand indikator, der angiver status for Azure overvågning. 
 
![Output fra tilstandskontrol ved at udføre azperflib.exe, der angiver, at der findes ingen problemer][deployment-guide-figure-1100]
<a name="figure-11"></a>

Kontrollér resultatet returneres for output af mængden tællere totalen som rapporteres som tomme og 'tilstandskontrol', som vist ovenfor i figur [over][deployment-guide-figure-11].

Du kan fortolke resultatværdier på følgende måde:

| Azperflib.exe resultatværdier | Azure overvågning parathed Status |
| ------------------------------|----------------------------------- |
| **Tællere total: tomme** | Følgende 2 Azure lagerplads tællere kan være tom: <ul><li>Lagerplads læse Op ventetid Server MS</li><li>Lagerplads læse Op ventetid E2E MS</li></ul>Alle andre tællere skal indeholde værdier. |
| **Tilstandskontrol** | OK, hvis retur status viser kun OK |

Hvis ikke begge dele returnere værdierne i azperflib.exe viser, at alle på forhånd tællere returneres korrekt, skal du følge vejledningen af tilstandskontrol for Azure overvågning infrastruktur konfiguration, som beskrevet i kapitel [tilstandskontrol for Azure overvågning konfiguration af infrastruktur] [installation-vejledning-5.2] nedenfor.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Udfør parathedskontrol på et Linux VM
For at kunne udføre kontrollen er klar, skal du holde kontakten med SSH til Azure Virtual Machine og udføre følgende trin:

* Kontrollere output fra Azure udvidet overvågning filtypenavnet
    * flere /var/lib/AzureEnhancedMonitor/PerfCounters
        * Bør give dig en liste over tællere i ydeevne. Filen må ikke være tom
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | GREP fejl
        * Skal returnere en linje, hvor fejlen er ingen fx 3; config; Fejl; 0; 0; **ingen**; 0; 1456416792; tst-servercs;
    * flere /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * Bør være tom eller ikke skal findes
* Hvis den første check ovenfor ikke blev fuldført, kan du udføre disse yderligere test:
    * Sørg for, at waagent er installeret og i gang
        * sudo ls-al/varians/lib/waagent /
            * skal indeholde indholdet af mappen waagent
        * PS-ax | GREP-waagent
            * bør vise én post ligner ' python /usr/sbin/waagent-daemon'
    * Sørg for, at filtypenavnet Linux Diagnostic er installeret og i gang
        * sudo vi - c ' ls-al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
            * skal indeholde indholdet af mappen Linux diagnosticering lokalnummer
        * PS-ax | GREP diagnosticering
            * bør vise én post ligner ' python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py-daemon'
    * Sørg for, at Azure udvidet overvågning filtypenavnet er installeret og i gang
        * sudo vi - c ' ls-al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
            * skal indeholde indholdet af mappen Azure udvidet overvågning lokalnummer
        * PS-ax | GREP AzureEnhanced
            * bør vise én post ligner 'python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon'
* Installere SAP Host Agent, som beskrevet i SAP Note [1031096] og kontrollere output fra saposcol
    * Udføre /usr/sap/hostctrl/exe/saposcol -d
    * Udføre lagring ccm
    * Kontrollere, om metrisk "Virtualization_Configuration\Enhanced overvågning adgang" er sand
* Hvis du allerede har en SAP NetWeaver ABAP application server, der er installeret, Åbn transaktion ST06, og Kontrollér, hvis den forbedrede overvågnings er aktiveret.

Hvis et af Kontroller over fejl, skal du følge kapitel [yderligere fejlfinding af Azure overvågning infrastrukturen for SAP] [installation-vejledning-5.3] for at få detaljerede instruktioner Sådan Geninstaller filtypenavnet.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Tilstandskontrol for Azure overvågning infrastruktur konfiguration
Hvis nogle af overvågning data ikke leveret korrekt som angivet af de test, der er beskrevet i kapitel [parathed kontrollere, om Azure udvidet overvågning for SAP] [installation-vejledning-5.1] oven udføre cmdlet'en Test-AzureRmVMAEMExtension til at afprøve, om den aktuelle konfiguration af infrastrukturen Azure overvågning og filtypenavnet overvågnings for SAP er korrekt.

For at teste overvågning konfigurationen, skal du udføre følgende rækkefølge:

* Sørg for, at du har installeret den nyeste version af Microsoft Azure PowerShell-cmdlet, som beskrevet i kapitel [implementerer Azure PowerShell-cmdlet'er] [installation-vejledning-4.1] i dette dokument.
* Kør følgende PowerShell-cmdlet. Kør cmdlet accepterer Get-AzureRmEnvironment for en liste over tilgængelige miljøer. Hvis du vil bruge offentlige Azure, er AzureCloud i dit miljø. Vælg AzureChinaCloud for Azure i Kina.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Når du har givet din kontodata og Azure Virtual Machine, test scriptet konfigurationen af den virtuelle maskine, du vælger.

 
![Indtast skærmbillede med SAP bestemte Azure cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

Når du har angivet oplysningerne om din konto og Azure Virtual Machine, afprøve scriptet konfigurationen af den virtuelle maskine, du vælger.
 
![Output fra vellykket test af Azure overvågning infrastruktur for SAP][deployment-guide-figure-1300]

Sørg for, at hvert afkrydsningsfelt er markeret med OK. Hvis nogle af Kontroller ikke ok, skal du udføre Cmdletten opdatering, som beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5] i dette dokument. Vente 15 minutter til en anden og udføre de Kontroller, der er beskrevet i kapitel [parathed kontrollere, om Azure udvidet overvågning for SAP] [installation-vejledning-5.1] og [tilstandskontrol for Azure overvågning konfiguration af infrastruktur] [installation-vejledning-5.2] igen. Hvis Kontroller angiver stadig et problem med nogle eller alle tællere, skal du fortsætte til kapitel [yderligere fejlfinding af Azure overvågning infrastrukturen for SAP] [installation-vejledning-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Yderligere fejlfinding af Azure overvågning infrastrukturen for SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure ydeevne tællere, vises ikke på alle
Samling af ydeevnen målene på Azure er udført af tjenesten Windows 'AzureEnhancedMonitoring'. Hvis tjenesten ikke er installeret korrekt, eller hvis det ikke kører i din VM, kan ingen ydeevne målepunkter indsamles overhovedet.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Installationsmappen af udvidelsen udvidet overvågning Azure er tom 

###### <a name="issue"></a>Problem
Installationsmappen C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop er tom.

###### <a name="solution"></a>Løsning
Filtypenavnet er ikke installeret. Kontroller, hvis det er et problem i proxy (som beskrevet før). Du kan være nødvendigt at genstarte computeren og/eller køre scriptet konfiguration script sæt AzureRmVMAEMExtension igen

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Tjenesten til Azure udvidet overvågning findes ikke 

###### <a name="issue"></a>Problem
Windows-tjenesten 'AzureEnhancedMonitoring' findes ikke. Azperflib.exe: Azperlib.exe output opstår en fejl, som vist i [nedenstående figur][deployment-guide-figure-14].
 
![Udførelse af azperflib.exe angiver, at tjenesten af Azure udvidet overvågning filtypenavnet for SAP ikke kører][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Løsning
Hvis tjenesten ikke findes, som vist i [skærmbilledet ovenfor][deployment-guide-figure-14], Azure overvågning udvidelse til SAP er ikke installeret korrekt. Geninstaller filtypenavnet ifølge de trin, der er beskrevet til scenariet installation i kapitel [installation scenarier af FOS for SAP på Microsoft Azure] [installation-vejledning-3]. 

Efter installation af filtypenavnet, skal du kontrollere, om tællerne i Azure ydeevne er angivet i Azure VM efter 1 time.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Tjeneste til Azure udvidet overvågning findes, men kan ikke startes 

###### <a name="issue"></a>Problem
Windows-tjenesten 'AzureEnhancedMonitoring' findes og er aktiveret, men kan ikke startes. Kontrollér programmets hændelseslog kan finde flere oplysninger.

###### <a name="solution"></a>Løsning
Forkert konfiguration. Genaktivere filtypenavnet overvågning af VM som beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Nogle tællere i Azure ydeevne mangler
Samling af ydeevnen målene på Azure er udført af tjenesten Windows 'AzureEnhancedMonitoring', som henter data fra flere kilder. Nogle konfigurationsdata indsamles lokalt, ydeevne målepunkter læses fra Azure diagnosticering og lagerplads tællere bruges fra din logføring på lagerpladsen abonnement.

Hvis fejlfinding ved hjælp af SAP Note [1999351] ikke hjælper, skal du køre scriptet til konfiguration sæt AzureRmVMAEMExtension igen. Du kan være nødvendigt at vente til en time, fordi lagerplads analytics eller diagnosticering tællere ikke kan oprettes, umiddelbart efter at de er aktiveret. Hvis problemet fortsætter, du Åbn en SAP-kunde support meddelelse på komponenten BC-OP-NT-AZR.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure ydeevne tællere, vises ikke på alle

Samling af ydeevnen målene på Azure er udført af en deamon. Hvis deamon ikke kører, kan ingen ydeevne målepunkter indsamles overhovedet.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Installationsmappen af udvidelsen udvidet overvågning Azure er tom 

###### <a name="issue"></a>Problem
Den directory/varians/bibliotek/waagent/indeholder ikke en undermappe til udvidelsen udvidet overvågning Azure.

###### <a name="solution"></a>Løsning
Filtypenavnet er ikke installeret. Kontroller, hvis det er et problem i proxy (som beskrevet før). Du kan være nødvendigt at genstarte computeren og/eller Kør igen scriptet til konfiguration sæt AzureRmVMAEMExtension

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Nogle tællere i Azure ydeevne mangler

Samling af ydeevnen målene på Azure er udført af en daemon, som henter data fra flere kilder. Nogle konfigurationsdata indsamles lokalt, ydeevne målepunkter læses fra Azure diagnosticering og lagerplads tællere bruges fra din logføring på lagerpladsen abonnement.

En komplet og opdaterede liste over kendte problemer skal du se SAP Note [1999351] , der indeholder flere oplysninger om fejlfinding for udvidet Azure overvågning for SAP.

Hvis fejlfinding ved hjælp af SAP Note [1999351] ikke hjælper, skal du køre igen scriptet sæt AzureRmVMAEMExtension til konfiguration som beskrevet i kapitel [konfigurere Azure udvidet overvågning udvidelse til SAP] [installation-vejledning-4.5]. Du kan være nødvendigt at vente til en time, fordi lagerplads analytics eller diagnosticering tællere ikke kan oprettes, umiddelbart efter at de er aktiveret. Hvis problemet fortsætter, du Åbn en SAP-kunde support meddelelse på komponent BC-OP-NT-AZR til Windows eller BC-OP-LNX-AZR for en Linux virtuel maskine.
