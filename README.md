# batterie
### II) Développement du PCB
##### A) Régulateur Buck
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

### III) Résultats:
##### A) PCB de l’alimentation 
En suivant la datasheet de chaque régulateur et avec quelques recherches, nous avons réalisé notre PCB sous Eagle et une fois reçu l’avons soudé.

![](images/PCB.png)

En vert le régulateur BUCK

En orange le régulateur linéaire


###### Nos erreurs : 

Ne pas omettre le plan de masse sur Eagle

Prêter attention à la largeur des pistes car la machine de l’Ensea ne peut en faire des trop petites. C’est pourquoi le premier PCB n’était pas exploitable 

Le via a proximité des pins SV1 n’était pas connecté au plan de masse; on a soudé un fil entre le via et celui-ci. 

Ne pas confondre Masse et Terre 

NB : Avoir de bons yeux pour souder les résistances en 0603 :) 

