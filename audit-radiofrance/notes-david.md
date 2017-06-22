Ce document résume ce que nous avons observé les 15 et 16 juin dans les locaux de la Direction du Numérique chez Radio France

# Déroulé chronologique

## jeudi 15 juin

* Arrivée et accueil
* Matinée : présentation par l'équipe API ("cruiser") de l'architecture du back-end web et de la communication entre les micro-services.
* Présence à la "Foire au Cruiser" l'après-midi
* Discussions avec les différents membres de l'équipe API

## vendredi 16 juin

* Restitution des différents axes d'amélioration que nous avons entendu et discussion avec l'équipe API pour la priorisation de ces points
* Discussions avec d'autres équipes sur le même plateau
    * UX
    * SEO
    * France Culture
    * (discussion avec Terry, mais quelle était son équipe ?)


# Observation et conseils

## Observations générales

Nous sommes ressortis avec un sentiment très positif suite à ce que nous avons pu observer à la Direction du Numérique.

Tous les signaux ont l'air d'être au vert : 
* Concernant la technique, les choix sont simples, cohérents en soi et entre eux, pas de gros risques sur des technologies, un parti pris sur l'indépendance logicielle (pas de services en SaaS).
    * Le premier matin, la clarté de l'explication de l'architecture était en elle-même un signe de la qualité de l'archtecture.
    * Ne pas hésiter à payer pour du support spécifique quand un problème apparait et qu'un manque de compétence est identifié est une très bonne décision aussi
* Nous ne connaissons pas les détails financiers, mais les ambitions ont l'air en adéquation avec les moyens à disposition
* Les équipes sont petites et ont l'air soudées et de bien communiquer en interne et entre elles. La communcation a l'air fluide, simple, efficace.
* Les équipes s'organisent comme elles le souhaitent et ont l'air de s'organiser de manière efficace.
* Les individus ont l'air épanouis et de prendre plaisir à leur travail
* Il y a un climat général de confiance entre les équipes et apparemment avec la Direction du Numérique qui laisse aux équipe l'autonomie nécessaire.

Si nous devions donner un premier conseil général, ça serait celui de garder, chérir et protéger tous ces morceaux de contextes techniques, organisationels et humains qui mènent mécaniquement à un service rendu de qualité.

## Observations spécifiques

### équipe API

Il s'agit de l'équipe avec laquelle nous avons passé le plus de temps. Nous avons listé les sujets suivants :

(liste déjà publiée par Thomas)

#### rf-pg-connector

Concernant les limitations de `rf-pg-connector`, David Bruant a passé un peu de temps avec Florian de l'équipe API. La conclusion semble être que le problème est identifié et qu'il faut juste passer du temps à le résoudre. Afin de rendre la transition douce, il est recommandé d'utiliser les numéros de versions de dépendance pour `rf-pg-connector`, et faire la transition de ses consommateurs un par un (Florian a indiqué que cette procédure est connue et a déjà été appliquée).
Il est possible qu'utiliser un langage avec vérification statique comme TypeScript ou Flow aide aussi à ce genre de refactoring.

Concernant les requêtes SQL, il est conseillé d'utiliser un [query builder](https://github.com/brianc/node-sql) ou [un autre](https://www.npmjs.com/package/sql-template-strings). Celà augmenterait la lisibilité du code SQL qui se cache à l'intérieur du JavaScript.
Nous avons vu que le module [node-sql](https://github.com/brianc/node-sql) ne supporte pas les `LATERAL JOIN`. Une option peut être de contribuer à cette bibliothèque si elle est choisie.

Apparemment, le choix des "lateral join" (plutôt que des requêtes imbriquées) a été fait sur un conseil d'un expert pour raison de performances. Une idée pourrait être de définir une méthodologie de test de cette hypothèse sur les requêtes qui ont lieu effectivement chez Radio France.

Florian a évoqué un questionnement sur le fait d'avoir du SQL à 3 endroits (`rf-pg-connector`, `rf-models` et dans chaque modèle). Ce choix a l'air raisonnable. Ces 3 endroits correspondent à 3 strates de spécificités qu'il n'est pas mauvais de garder séparés.


### Equipes chaînes

Nos discussions avec les différentes équipes ont fait émergé un problème commun concernant la gestion du code JavaScript côté client.
Chaque site de chaîne a son processus de build, ses outils, ses habitudes, ses façons de faire le JavaScript front-end. Ceci se traduit naturellement en beaucoup de travail dupliqué et difficilement réutilisé.

Un conseil ici serait d'avoir une équipe transverse spécifique sur à sujets, de la même manière qu'il existe une équipe transverse sur le framework PHP qui interroge l'API interne. 
Il existe l'équipe cockpit, mais notre compréhension à ce stade est que cette équipe ne fait que créer et maintenir quelques composants front-end réutilisés par tous les sites.
Il faudrait soit étendre les sujets couverts par cette équipe, soit en créer une autre.


### Equipe UX

Beaucoup de sujets ont été évoqués, dont celui d'une charte graphique (voire d'un styleguide) en travail.
Ce sujet a aussi été évoqué par une équipe chaîne. Un rapprochement pourrait être utile afin de mieux coordonner les efforts sur ce sujet.


## Actions à mettre en œuvre

### ESLint

Trouver un problème de qualité de code, trouver s'il existe la règle correspondante, créer la règle si ce n'est pas le cas.