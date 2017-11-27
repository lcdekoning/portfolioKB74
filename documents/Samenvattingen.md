## Analyzing gait pathologies using a depth camera
Hoang Anh Nguyen, Edouard Auvinet, Max Mignotte, Jacques A. de Guise and Jean Meunier
 
In dit artikel wordt een onderzoek beschreven dat met behulp van de Kinect probeert te bepalen of er sprake is van een asymmetrie in de manier van lopen van een persoon. Ze doen dit door de hoek te berekenen tussen het boven- en het onderbeen, terwijl een persoon op een loopband loopt.
Allereerst maken ze van de output van de Kinect, die bestaat uit een 2-dimensionale array, opnieuw een 3D constructie. Elk punt in de array bevat een horizontale coördinaat, een verticale coördinaat en een diepte waarde. Met behulp van parameters die bekend zijn voor de camera (focal distance en image centre coördinates) bepalen ze de corresponderende 3D coördinaten aan de hand van ‘perspective transformation’ (formules staan in het artikel). De afbeeldingen zijn opgenomen met 30 frames per seconde en een resolutie van 640x480 pixels.
Vervolgens bepalen ze waar de schouders van de persoon zich bevinden door van boven naar beneden de breedte van de ‘slices’ te bekijken; deze worden na de nek veel breder en blijven daarna ongeveer even breed, waardoor de schouders gevonden kunnen worden. Op basis waarvan ze de rest van het skelet reconstrueren. Ze gebruiken hierbij de eerder gemeten lengten van de ledematen van de proefpersonen
De resultaten plaatsen ze eerst in een grafiek met op de x-as de tijd en op de y-as de hoek tussen het onder- en bovenbeen. In deze grafiek staan een aantal stappen (loopstappen) na elkaar. Omdat elke stap vaak enigszins verschilt, maken ze van alle stappen die gezet zijn één ‘mean’ stap, dit vormt de tweede grafiek. 


## 3D-Skeleton-based human action classification: A survey
Liliana Lo Presti n, Marco La Cascia

(Samen met Robin)
 
Dit artikel is voornamelijk een hele grote samenvatting met verwijzingen naar bestaande literatuur over het herkennen van (skeletten en) bewegingen met 3D camera’s. Is dus waarschijnlijk pas relevant als we toekomen aan de vierde milestone.
#### Depth maps and related technologies
De drie meest populaire technologieën die gebruikt worden voor het verkrijgen van een ‘depth map’ zijn: stereo cameras, 3D Time of Flight cameras (Kinect 2) en Structured-light 3D scanners (Kinect 1).
####  Body pose estimation
Er zijn verschillende methoden om een skelet te schatten: motion capture, intensity data en depth maps (OpenNI) en passive stereo vision (meerdere camera’s).
#### Pre-processing of skeletal data
(wat doe je met de data zodat het met elkaar vergeleken kan worden, bijvoorbeeld bij verschillende snelheden in beweging, andere lichaamsbouw, etc.)
Data normalization and biometric differences
Keuze van referentie coördinaten system is belangrijk. Zo worden de ene keer hoeken tussen twee ledematen gebruikt, of bovenlichamen en schouders worden uitgelijnd, soms wordt de data omgevormd (om te kunnen vergelijen) vóór het opslaan, soms erna.
##### Dealing with varying temporal duration
Geldt voornamelijk wanneer een beweging herkend moet worden (bijv. herkennen dat iemand loopt), wellicht bruikbaar om te herkennen wélke oefening gedaan wordt. Echter wordt hierin vaak de beweging ‘vloeiend’ gemaakt, wat wij juist niet willen.
####  Action representation and classification
##### Joint-based representation
Probeert de (relatieve) gewrichten te vinden. Heeft ook nog 3 subcategorieën:
##### Spatial descriptors
Zoekt gewrichten door het meten van alle mogelijke paarsgewijze afstanden (op een gegeven moment of door de tijd heen) of de covariantie matrices.
##### Geometric descriptors
##### Key-pose based descriptors
Een set van key-posities is beschikbaar en er wordt gekeken op welke key-positie een positie (voor de camera) het meest lijkt.
##### Mined joint-based descriptors
Bewegingen bestaan vaak uit het gebruik van verschillende gewrichten, door te kijken welke gewrichten gebruikt worden, kan op convergerende wijze gezocht worden naar de beweging die wordt uitgevoerd.
##### Dynamics-based descriptors
#### Fusing skeletal data with other sensors’ data
Soms wordt data van verschillende sensoren gebruikt, bijvoorbeeld een combinatie van skeletal data, RGB beelden en depth maps.
#### Datasets and validation protocol
- UCF
- MHAD
- MSRA3D
- UTKA
- MSR daily activity 3D
- Further benchmarks

