# batterie
### I) L'alimentation
##### A) Cahier des charges 

Il est nécessaire d'éditer un cahier des charges avant chaque début de projet. En effet, chacun d'eux est constitué de différents composants qui ont tous des caractéristiques d'alimentation, et de courant délivré qui leur sont propre. 

Nous avons donc répertorié tous les composants suivant leur datasheet


- Lorawan      5V 
               111 mA (transmission)
               2,1 µA (veille)
               6,7mA (Réception)

- STM32        3,3V
               50mA

- GPS          3,3v
               <25mA

- Accéléromètre 3,3V
                3,9mA

- Capteur humidité 5V
                   <1,5mA

- Capteur PH          5V

- Capteur température 3,3V
                      <100mA
                     
                 
##### B) Les piles 

L'avantage de notre projet actuel est que l'on a utilisé des piles non rechargeables qui nous délivraient les 6V voulus, tandis que les piles non rechargeables, elles ne peuvent délivrer que 1,2V chacune ce qui ne fera pas exactement 6V en tension d'entrée. Cependant, qui dit piles non rechargeables dis moins d'autonomie; ce qui ne coincidera pas avec les attentes de l'acheteur si l'on commercialise le projet.

### II) Développement du PCB
##### A) Régulateur Buck
##### Pourquoi ? 

Nous avons plusieurs composants à alimenter. Les deux tensions à délivrer sont le 5V et le 3,3V. 
Pour cela, nous avions deux choix de régulateurs; soit un Buck (tension de sortie, du régulateur, abaissée) ou un régulateur Boost (le contraire; tension de sortie du régulateur augmentée). 
Boost inverse la tension
c’était plus simple. 

Sachant que, pour le composte, la batterie délivre 12V, le choix du régulateur Buck était alors une évidence. 
Nous avons, de plus, pris la décision de rester sur ce type de régulateur pour les deux autres projets même si nous pouvions utiliser un Boost. (tension d’entrée de 6V délivrée avec les piles). 

Nous avons, donc, opté pour un MC34063A/E
capable du Boost, du Buck, courant de sortie supérieur à 1,5mA, large plage de tension d’entrée (3V-40V), sortie (0,8V-18V)
Moins cher, ampérage de sortie 1,5 A l’autre 3A. 


 ##### L'environnement
 
Cependant, afin d’abaisser la tension, un simple régulateur dans un circuit électrique ne suffit pas. Il a besoin d’un environnement pour mener à bien sa mission. 
En effet, d’après la datasheet, de nombreux composants sont nécessaires.

En se référant à la datasheet du régulateur. 

<img width="626" alt="Régulateur" src="https://user-images.githubusercontent.com/102949845/172059604-825a0a68-938c-40e6-9af4-39334f3eaabe.PNG">


Détaillons chacun d’eux : 

Un résistance Rsc de 0,33 Ohm . Celle-ci permet de contrôler le courant maximal arrivant dans la pin Switch. 
Un condensateur C2 (100µF) polarisé ; filtrage de parasites 
Un condensateur C1(470µF) polarisé; filtrage de tension de sortie
Une bobine ( 220µH); filtrage de courant de sortie. *
bobine en série et capa en parallèle = lissage 
Un diode (40V) reliée à la masse, à la sortie de la switch. Elle agit comme une sécurité. En effet, si la tension dépasse 40V, alors tout passe par la diode et va à la masse. Cette valeur de tension peut paraître inatteignable de prime abord, car nous délivrons au maximum 12V en entrée, mais le On Off de la switch augmente la tension et peut atteindre ces 40V.
Un condenstaur C3 (470pF) non polarisé; capa de synchronisation de charge et décharge du régulateur.
2 résistances R1 et R2 associées en pont diviseur de tension, vont déterminées la tension de sortie du régulateur. En effet, lorsque l’on regarde l’intérieur du régulateur; nous avons un comparateur de 1,25V référence régulateur qui comparé avec la tension R1 et R2 agit sur la tension de sortie. 
L’association de C2, C1 et la bobine permet au régulateur d’être le plus stable possible. 

(Chaque valeur des composants est déterminée suivant la datasheet).  

##### B) Régulateur linéaire
###### Pourquoi?
Tout comme le montre le cahier des charges du travailleur isolé, on a besoin d’alimenter la
carte STM32 ainsi que les deux capteurs GPS et Accéléromètre avec une tension de 3,3V.
Pour avoir cette tension de sortie, on pourra refaire le même régulateur BUCK pour passer
du 6V au 3,3V ou bien du 5V au 3,3V.
Pour des raisons de coût ainsi que d’optimisation d’espace sur le PCB, on a plutôt opté pour
un régulateur linéaire.

Contraintes:
Les limites d'utilisation du régulateur linéaire sont liées aux contraintes thermiques, car ce
dernier chauffe. Et il peut chauffer beaucoup ! La dissipation thermique du composant est
proportionnelle à la différence de tension qui règne entre son entrée et sa sortie, et du
courant qui le parcourt.
=> Donc est ce que celà peut nous causer un problème? Dans notre cas, pour éviter ce
soucis, on a relié le régulateur linéaire avec le BUCK pour avoir comme tension d’entrée de
ce régulateur un 5V au lieu de 6V. Ainsi, on minimise la différence de tension entre son
entrée et sa sortie. De plus, concernant le courant; le régulateur choisi ne fournit que 300mA
et on ne consomme 100mA maximum.
On choisit alors:

![](images/reglin.png)

Ainsi grâce à ce choix: 
  -Le régulateur nous délivre une tension de sortie de 3,3V pour alimenter les différents composants.

  -Il nous délivre un courant de 300mA suffisant (sachant que la STM32 consomme du 50mA; l'accéléromètre du 3,9mA; et le GPS un courant inférieur à 25mA).

  -On a eu une livraison rapide vu que le composant était en stock.

  -On a optimisé de l’espace vu que ce régulateur ne nécessite pas beaucoup de composants contrairement au BUCK.

###### Alimentation:
En se référant sur la datasheet du NCV33375:

![](images/datareglin.png)

L’une des caractéristiques de ce régulateur est:

-Les deux condensateurs Cin et Cout servent à linéariser respectivement la tension d’entrée et de sortie.

-Si la pin ON/OFF reste flottante alors le régulateur ne marche pas, il est nécessaire de lui appliquer une tension. Pour éviter toute contrainte, on relie cette pin à Vin.  

##### C)Points Tests

Afin de vérifier le bon fonctionnement du module alimentation, nous avons créé des points tests sur le PCB. Ceux-ci vont permettre de tester les tensions de sortie de chaque régulateur. Ils apparaissent comme des débuggueurs de régulateurs. 
Pour se faire nous avons inséré une résistance nulle à la sortie du régulateur Buck. Celle-ci pouvant être soudée et dessoudée en fonction de ce que l’on souhaite faire. En effet, si elle est dessoudée, nous pouvons vérifier la tension de sortie du régulateur grâce à un multimètre. En la soudant, elle va permettre de lier les deux régulateurs entre eux. En fait, elle se comporte comme un lien entre les deux régulateurs. 

Nous avons également choisi de mettre 3 pins dans le PCB. Une pin Masse, une pin 3,3V et une pin 5V. Chaque pin étant reliée à la sortie du régulateur correspondant. 


<img width="582" alt="points test" src="https://user-images.githubusercontent.com/102949845/172060186-3f57f2eb-60b0-4677-a741-d96b9df2ec3e.PNG">


Nous verrons plus tard, que lors de l'association sur le PCB de l'alimentation avec les autres modules (capteurs, lorawan...), la résistance nulle nous aura été bien utile pour vérifier le bon fonctionnement du régulateur Buck et ainsi résoudre quelques problèmes. 

### III) Résultats:
##### A) PCB de l’alimentation 
En suivant la datasheet de chaque régulateur et avec quelques recherches, nous avons réalisé notre PCB sous Eagle et une fois reçu l’avons soudé.

![](images/PCB.png)

En vert le régulateur BUCK

En orange le régulateur linéaire


###### Nos erreurs : 

Dans cette partie de rapport nous avons répertorié les quelques erreurs et remarques que nous avons faites lors du projet. 

- Ne pas omettre le plan de masse sur Eagle
- Prêter attention à la largeur des pistes car la machine de l’Ensea ne peut en faire des trop petites. C’est pourquoi le premier PCB n’était pas exploitable 
- Le via a proximité des pins SV1 n’était pas connecté au plan de masse; on a soudé un fil entre le via et celui-ci. 
- Ne pas confondre Masse et Terre 
- Ne pas confondre stabilité du régulateur Buck et linéarité du régulateur linéaire. 

NB : Avoir de bons yeux pour souder les résistances en 0603 :) 

##### B) résultat du 6V

![](images/resultat6V.png)

En alimentant notre PCB avec les 4 piles AAA, soit 6V, nous avons bien obtenu les tensions de sorties souhaitées. 
##### C) résultat du 12V 
