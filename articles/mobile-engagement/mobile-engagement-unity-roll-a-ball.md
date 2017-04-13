<properties
    pageTitle="Enhed filmrulle et kuglen selvstudium"
    description="Trin til at oprette en enhed klassisk Brug en kuglen game som er en foreløbig nødvendige til alle Mobile aftale enhed selvstudier"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Oprette enhed Brug en kuglen game

Dette selvstudium vejleder gennem de vigtigste trin til en ændret en smule [enhed filmrulle et kuglen selvstudium](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Dette eksempel game består af et sfærisk 'player' objekt som styres af app brugeren og formålet med game er ' indsamle ' collectible objekter ved at støde player objekt med disse collectible objekter. Dette forudsætter grundlæggende kendskab til enhed editor miljø. Hvis du støder på problemer skal du henvise til det fulde selvstudium. 

### <a name="setting-up-the-game"></a>Konfiguration af game
Følgende er fra [enhed selvstudium](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Åbn **Enhed Editor** , og klik på **Ny**. 
    
    ![][51] 
    
2. Angiv et **Projektnavn** & **placering**, Vælg **3D** og klikke på **Opret projekt**.
    
    ![][52]

3. Gemme standard scenen lige oprettet som en del af det nye projekt som med navnet **minispil** i et nyt ** \_scener** mappe under **Aktiver** mappe:
    
    ![][53]

4. Oprette en- **3D-objekt -> plan** som feltet afspilning og omdøbe denne plan objekt som **bunden**

    ![][1]

5. Nulstil komponenten transformation for dette **bunden** objekt, så den er ved nulpunktet. 

    ![][3]

6. Fjern markeringen i **Vis gitter** **er** menuen til **bunden** objektet.

    ![][4]

7. Opdatere komponenten **skala** til **bunden** objektet skal være [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Føje en ny- **3D-objekt -> kugle** til projektet, og Omdøb dette kugle objekt som **Player**. 

    ![][6]

9. Markér objektet **Player** , og klik på **Nulstil transformere** ligner objektet plan. 

10. Opdater **transformation -> Position -> Y koordinere** komponent Player y som 0,5.  

    ![][7]

11. Oprette en ny mappe kaldet **materialer** i projektet, hvor vi opretter materiale farvelægge afspilleren. 

12. Oprette et nyt **materiale** kaldet **baggrund** i denne mappe. 

    ![][8]

13. Opdatere farven på materialet ved at opdatere egenskaben **Albedo** for den. Du kan vælge RGB-værdier for [0,32,64]. 

    ![][9]

14. Træk dette materiale til visningen scene for at anvende farve på **bunden** objekt. 

    ![][10]

17. Til sidst opdatere den **transformation -> Rotation Y ->** til 60 på objektet, retningsbestemte Light klarhed. 

    ![][12]

### <a name="moving-the-player"></a>Flytte afspilleren
Følgende er fra [enhed selvstudium](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Føje en **RigidBody** komponent til objektet **Player** . 

    ![][13]

2. Oprette en ny mappe med navnet **Scripts** i projektet. 

3. Klik på **tilføje komponent -> Nyt Script C# Script ->**. Navngiv den **PlayerController**, og klik på **Opret og Tilføj**. Dette opretter og knytter et script til objektet Player.  

    ![][14]

5. Flytte dette script under mappen **Scripts** i projektet. 

6. Åbne script til redigering i din foretrukne Scripteditor, opdaterer scriptkoden med følgende kode, og Gem den. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Bemærk, at scriptet ovenfor bruger en **hastighed** egenskab. I editoren enhed skal du opdatere egenskaben hastighed til 10.  

    ![][15]

9. Tryk på **Afspil** i enhed Editor. Du bør nu ikke kunne styre kuglen ved hjælp af tastaturet, og de skal rotere og flytte rundt. 

### <a name="moving-the-camera"></a>Flytte kameraet
Følgende er fra [enhed selvstudium](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) og vil knytter **Main kamera** til objektet **Player** . 

1. Opdatere **Transform.Position** for at være X = 0, Y = 10.5, Z =-10.  
2. Opdatere **Transform.Rotation** for at være X = 45, Y = 0, Å = 0.  

    ![][16]

2. Tilføje et nyt script kaldet **CameraController** til **MainCamera** og flytte den under mappen Scripts. 

    ![][17]

3. Åbn scriptet til redigering og Tilføj følgende kode i den:

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. Træk Player-variablen i Player slotten for objektet Main kamera i enhed miljø - så to er knyttet til hinanden. 

    ![][18]

6. Nu hvis du klikker på Afspil i redigeringsprogrammet enhed og rotere objektet Player kuglen vises derefter kameraet følger i bevægelse.  

### <a name="setting-up-the-play-area"></a>Opsætning af området Afspil
Følgende er fra [enhed selvstudium](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vi opretter væggene omkring bunden, så objektet Player kuglen ikke indlevering afspilningsområdet i dets bevægelse. 

1. Klik på **Opret -> Opret tomt Game objekt ->** , og kald den **vægge**

    ![][19]

2. Oprette en ny- **3D-objekt -> kube** under denne vægge objekt - og kald den "Vest væg". 

    ![][20]

3. Opdater **transformation -> placering** og **transformation -> skala** til dette Vest væg objekt. 

    ![][21]

4. Duplikere Vest væg for at oprette en **Øst væg** med opdaterede transformation placering og skalering. 

    ![][22]

5. Duplikere Øst væg for at oprette en **Nord væg** med opdaterede transformation placering og skalering. 

    ![][23]

6. Duplikere nord væg, og Opret en **syd væg** med opdaterede transformation placering & skala. 

    ![][24]

### <a name="creating-collectible-objects"></a>Oprettelse af Collectible objekter
Følgende er fra [enhed selvstudium](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vi opretter nogle flot udseende objekter, der udgør sæt af collectible objekter som objektet Player kuglen skal indsamling af støde med dem. 

1. Oprette et nyt **3D-kube objekt** , og kald den afhentning. 

2. Justere den- **transformation -> Rotation** & -**transformation -> skala** for objektet enhed. 

    ![][25]

3. Opret og Vedhæft et **Nyt C# Script** kaldet **Rotator** til objektet enhed. Sørg for at flytte scriptet under mappen Scripts. 

    ![][26]

4. Åbne dette script til redigering og opdatere den for at være følgende: 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. Nu påløber rotere afspilning i redigeringsprogrammet enhed og diasshowet enhed objekt på dens akse.

6. Oprette en ny mappe med navnet **Prefabs** 

    ![][27]

7. Træk objektet **afhentning** , og Placer det i mappen Prefabs.

    ![][28]

8. Oprette et nyt **tomt Game objekt** kaldet **afhentning**. Nulstille dets position til origin og derefter trække objektet enhed under dette game objekt.  

    ![][29]

9. Duplikere objektet **afhentning** og sprede på **bunden** objektet omkring objektet **Player** ved at opdatere **Transform.Position's X & å** værdierne korrekt. 

    ![][30]

10. Oprette et **nyt materiale** kaldet **afhentning** og opdatere den for at være rød farve ved at opdatere **Albedo egenskaben** magen til hvad vi gjorde for opdatering af objektet bunden. 

    ![][31]

11. Materialet, gælder for alle 4 enhed objekter.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>Indsamling af enhed objekter
Følgende er fra [enhed selvstudium](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Vi opdaterer Windows Media Player, så det er kunne 'indsamle' enhed objekterne ved støde med dem. 

1. Åbn **PlayerController** scriptet knyttet til Player objektet til redigering og opdatere til følgende:  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Oprette et nyt **mærke** kaldet **Vælg op** (den stemme overens med nyheder i scriptet)  

    ![][33]
    
    ![][34]

3. Anvend dette **mærke** til objektet Prefab afhentning. 

    ![][35]

4. Aktivere **IsTrigger** afkrydsningsfeltet for objektet Prefab.

    ![][36]

5. Føje en hård brødteksten til afhentning Prefab objekt. Vi vil opdatere den statiske collider, som vi har brugt til et dynamisk collider til optimering af ydeevne. 

    ![][37]
  
6. Til sidst kontrollere egenskaben **IsKinematic** for objektet prefab. 

    ![][38]

7. Besøg **afspilles** i redigeringsprogrammet enhed, og du vil kunne afspille denne **Brug en kugle** game ved at flytte objektet Player ved hjælp af tasterne til retning input. 

### <a name="updating-the-game-for-mobile-play"></a>Opdatere game for mobile Afspil
I ovenstående afsnit indgået grundlæggende selvstudiet fra enhed. Nu vil vi ændre game for at gøre den mobile enhed brugervenligt. Bemærk, at vi bruges tastaturinput for game hidtil til test. Nu vil vi ændre det så vi kan styre afspilleren ved hjælp af bevægelse af telefonen dvs. Brug af Accelerometer som input. 

Åbn **PlayerController** scriptet til redigering og Opdater metoden **FixedUpdate** for at bruge bevægelse fra accelerometer til at flytte objektet Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Dette selvstudium indgår en grundlæggende game oprettelse med enhed, og du kan installere det på en enhed efter eget valg til at afspille game. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
