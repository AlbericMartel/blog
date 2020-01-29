### Introduction

### Niveau de tests

- Pyramide

#### Tests unitaires

- Tests de méthodes privées

#### Tests de composants

#### Tests d'intégration

#### Tests end to end

### Types de tests

- Smoke tests : Des tests qui couvrent les uses cases les plus importants. Ils sont utilisés pour tester des pannes systèmes suffisamment graves pouvant bloquer une release par exemple.

#### Mutation testing

En java : http://pitest.org/

#### Property based testing

En java : https://jqwik.net/

### TDD

Processus :

- Red : fail ou ne compile pas
- Green : le plus petit bout de code requis pour implémenter une feature
- Refactor : Eliminer les duplications (dans le code de production et/ou le code de test)

#### Stratégies d'implementation

- Fake it : retourner une constante et graduellement remplacer les constantes par des variables jusqu'à arriver au code final
- Obvious implementation : implémentation réelle. Si le test ne passe pas, alors y aller par étapes plus petites
- Triangulation : quand on n'est pas sûrs de comment refactorer, écrire plusieurs assertions dans le test pour rendre l'implémentation évidente
- One to many : quand on veut tester une méthode qui doit gérer une collection, commencer avec un élément unique. Quand le test passe, 
ajouter une collection en argument utilisant l'élement unique précedent, puis changer l'implémentation pour ne gérer que la collection, 
et supprimer l'argument simple et ajouter plus d'éléments dans la collection dans le test.

#### Process patterns (Quand, où écrire un test et quand s'arrêter)

- One step test : Le prochain test à écrire devrait être celui permettant d'aller une étape vers le but global. Choisir un test qui avec une couverture supplémentaire
 et que l'on peut faire fonctionner rapidement
- Explanation test : Demander une explication sur ce qui est attendu avec des exemples et commencer par écrire les tests avec ça
- Another test : Quand une idée annexe arrive, l'écrire à coté pour un futur test et continuer pour garder le focus
- Regression test : Ecrire un test pour chaque bug rapporté et penser à comment on aurait pu l'écrire pendant l'implémentation de la feature
- Do over : Si on est bloqué, peut être jeter ce qui a été fait et recommencer. En pair programming, on peut changer de partenaire, expliquer ce qu'on a fait, et ce dernier pourrait apporter
 un nouvel angle d'attaque

#### Techniques de testing

- Mock object : Pour mocker les dépendances externes et coûteuses. Les mocks ont l'avantage de ne retourner que des données minimales requises, encouragent à etre précautionneux sur la visibilité
 de chaque objet pour réduire le couplage. Le risque est d'utiliser des données qui ne correspondent à ce qui serait attendu en réalité. Dans ce cas, utiliser des données réelles attendues.
- Self shunt : Pour tester qu'un objet communique bien avec un autre, on peut faire communiquer cete objet avec la classe de test. Par exemple, l'objet A doit parler avec B, 
alors il pourrait être plus simple que B parle avec une interface. Cette interface peut aussi être implémentée par la classe de test et passée à l'objet. Préférer des interfaces réduites au strict nécessaire.
- Crash test dummy : C'est comme un mock mais sans avoir besoin de mocker l'objet complet. Par exemple, juste créer une inner class avec une méthode spécifique levant une exception à tester.
- Broken test : En sortant d'une session de programmation en solo, on peut laisser un test en échec pour faciliter notre retour où on en était sans réfléchir à ce qu'on était en train de faire.
- Clean check-in : En sortant d'une session de programmation en équipe, être certain de commit des tests passants. Si un test est en échec lorsqu'on récupère le code distant, 
cela peut vouloir dire que l'on avait pas la bonne approche et qu'on peut jeter ce qu'on faisait pour recommencer. Cela encourage de prendre des étapes plus petites et commit plus souvent.

Quoi tester : 

- Méthodes privées : peut être fait temporairement dans un legacy quand il n'est pas possible de tester correctement une méthode publique, puis refactorer jusqu'à ce que ce soit possible 
et que tous les embranchements de méthodes privées ait été testées puis supprimer les tests sur les méthodes privées.

Necessités pour que ça fonctionne bien : feedback de test rapide et design avec faible couplage pour faciliter les tests

Avec le TDD, les bugs apparaissent moins souvent. Conséquence : on ne subit plus mais on est proactifs, on peut impliquer les clients dans le dev de tous les jours, réactivité de l'équipe, on peut livrer tous les jours.

Le TDD est un moyen de gérer la peur, l'incertitude.

Données de test :

- Ne pas disséminer les données de partout. Il devrait être facile de savoir quelles données un test utilise.
- Utiliser le nombre minimal de données nécessaires aux tests. Ne pas utiliser plein d'objects si seulement 3 conduisent au même design et implémentation

#### Sources

Test driven development by example, Kent Beck
https://blog.octo.com/la-pyramide-des-tests-par-la-pratique-1-5/
