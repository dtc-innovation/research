# Audit Radio France - Equipe Cockpit

dtc innovation continue son audit chez Radio France.
Les 11 et 12 septembre, David Bruant est venu pour rencontrer l'équipe "Cockpit" qui s'occupe des différentes bibliothèques front-end communes aux sites des différentes chaînes.


## Contexte général

L'équipe Cockpit comporte 4 développeurs et un PO. Elle est née d'une *mitose* d'avec l'équipe Cruiser qui ne garda alors que la responsabilité des données. L'équipe cockpit a commencé avec la responsabilité de maintenir le *player*. De nouveaux projets sont apparus au cours du temps : kami (l'éditeur pour les back-office), damlib (un outil pour inclure des média), un front-end pour l'API, Kaizer (qui contient notamment l'outil pour la grille des programmes) et déjavu (outil qui est un wrapper autour de instaclick + Blazy).

Le temps a mené à une spécialisation où chaque projet est très bien connu d'un développeur (typiquement qui l'a écrit en premier) et très peu des autres.


## Objectifs

Les objectifs de l'audit avec l'équipe Cockpit sont les suivants :
- Evaluer le *[bus factor](https://vimeo.com/187568897)*
- Evaluer si les différents repos peuvent être distribués en open source
- Evaluer la facilité pour un nouveau-venu à rentrer dans le code des repos Cockpit


## Méthodologie

David Bruant a procédé à des entretiens avec les membres de l'équipe Cockpit (sauf Jérémy, le PO qui était absent ces jours-là)


## Avis général

Sans grosse surprise et en continuité de l'avis émis lors du premier morceau d'audit, la première conclusion est que les voyants sont au vert au sein de l'équipe Cockpit. 
Notamment, l'équipe était déjà consciente des dangers du bus factor et avait déjà mis des actions en places avant l'arrivée de David Bruant pour y remédier.

Aussi, si des efforts supplémentaires seraient à faire du point de vue de la documentation, le fait que les différents repos aient une même *stack* technique (React + redux, modules ES6, etc.) fait qu'il est déjà assez facile pour un débutant pour s'acclimater aux différents projets. Pareillement, la bonne gestion des dépendances permet que les projets pertinents soient quasiment déjà "open-sourçables".


## Sujets spécifiques

### Bus factor

Consciente du problème de la spécialisation issu de l'histoire de cette équipe, l'équipe Cockpit avait déjà commencé à mettre en œuvredes pratiques pour limiter le bus factor comme de la transmission d'informations entre deux personnes.

D'autres pratiques pourraient être mises en place et généralisées pour pallier ce problème :
* *[Pair programming](https://en.wikipedia.org/wiki/Pair_programming)*. Il arrivait que Kevin et Kamal la pratique, mais changer les paires pourrait aider à mieux distribuer la connaissance
* *[Mob Programming](https://en.wikipedia.org/wiki/Mob_programming)* ou revue de code en équipe. L'idée ici est de faire à plusieurs une tâche que l'on peut faire seul. Un peu de temps est perdu à court terme pour faire la tâche, mais tout le monde apprend dans la foulée et a l'occasion de poser toutes les questions qui viennent
* Distribuer les revues de code aléatoirement. La personne faisant la revue la fera en plus de temps, mais cela lui donne l'occasion de poser toutes les questions utiles à comprendre le code revu.


### Open source

Tous les projets n'ont pas vocation à être open sourcés ; en effet, certains ont un métier très spécifique à Radio France, comme certains onglets de Kaizer. Le projet dejavu a aussi peu de valeur ajoutée par rapport aux projets qu'il encapsule (BLazy et instantclick).

Toutefois, pour les autres projets, les repos sont bien découpés et ne dépendent que très peu de dépendances spécifiques à Radio France. Il sera donc assez facile de les isoler et les mettre en *open source*.


### Open source et nouveaux-venus

Le fait que les technologies utilisées soient relativement les mêmes aide beaucoup à ce qu'un nouveau-venu (open source ou pas) puisse plonger rapidement dans un projet. 

#### Automatisation

Les process de *build* sont basés sur les [npm script](http://substack.net/task_automation_with_npm_run) ; ce qui évite d'avoir à connaître des outils ésotériques. Toutefois, les noms de tâches sont parfois inconsistants (`npm run dev` ou `npm run build-dev`). Une tâche à mener serait de décider de conventions de nommage de tâches, de les documenter et de les appliquer sur tous les repos existants en copiant la documentation ou en faisant un lien vers elle.

Normaliser les conventions de nommage de tâche aiderait.

#### Readme

Pour l'open source comme pour les nouveaux-venus, il serait bon d'avoir des *readme* un peu plus riches, spécifiquement :

* Expliquer clairement (un tweet ou 2 lignes max) l'objectif et le périmètre du projet
* Expliquer les technologies majeures de chaque projet (TS + Draft + MegaDraft sur Kami) ou les choix majeurs (architecture en plugins)
* Mettre le lien direct du chat (exemple https://chat.dnm.radiofrance.fr/channel/rfplayer pour rfPlayer, le chat de Cockpit pour les autres)

#### Tests

Augmenter la couverture des tests serait aussi une bonne manière d'améliorer la confiance d'un nouveau venu à contribuer en étant assez sûr de ne pas casser l'existant. La couverture de code est un métrique discutable, mais elle a l'avantage d'être tangible et de pouvoir se donner un objectif... objectif. 

Une option pourrait être de mettre en place l'infrastructure de couverture de code, mais de ne pas la regarder en intégration continue, mais plutôt une fois de temps en temps et se donner des objectifs progressifs. Par exemple, à la mise en place, viser 30% sur tous les repos et oublier le sujet pour un moment. Quelques mois plus tard, re-regarder et se forcer à augmenter à 35%. Puis 40%, etc. mais sans se mettre de pression si d'autres tâches mènent à ce que ce pourcentage baisse ponctuellement.
Faire systématiquement des tests de non-regression peut aider aussi.

#### ESLint

Pareillement, ESLint est utilisé dans certains projets, mais pas configuré de la même manière. les actions à mener sont :
* activer ESLint sur tous les projets
* partager une configuration sur tous les projets et l'appliquer


#### Plus de mutualisation

Il faudrait aussi partager la configuration Babel afin de s'assurer qu'exactement la même syntaxe est utilisée sur tous les projets.

Normaliser les polyfills et demander aux chaines de les charger (via polyfill.io ou autre) plutôt que de les rajouter dans le bundle via `babel-polyfill` (lecteur-commun/rfplayer/blob/master/src/Main.js)




### Lien avec les équipes chaînes

#### Réorganiser certaines responsabilités ?

Joachim faisait remonter le fait que les chaînes ont des rythmes assez différents quant à la mise à jour des versions des bibliothèques produites par l'équipe Cockpit. Ceci se traduit généralement par un soin particulier à s'assurer que tout changement est rétro-compatible.

Après avoir discuté avec plusieurs équipes chaînes, celles-ci ont fait remonté qu'elles souhaiteraient des changelog mieux documentés, notamment pour savoir à quoi faire attention. Les chaînes transmettent aussi des indications concernant quoi tester aux QA ; ce changelog les aiderait à mieux formuler ces indications. Utiliser le bouton [`New Tag`](https://gitlab.dnm.radiofrance.fr/js-commons/rf-react-components/tags) permet de rentrer des notes riches. 

Un dernier sujet quant à l'utilisation des bibliothèques de l'équipe Cockpit est que cette équipe est parfois obligée de produire des artefacts pre-packagé (player avec le bon ensemble de plugins) pour chaque chaine. Cette solution est la conclusion notamment du fait que chaque chaîne a une stratégie différente quant à sa gestion des modules et des dépendances JavaScript front-end. Cet hétérogénéité des pratiques du front-end des chaînes est un problème qui est revenu plusieurs fois.

Kevin faisait remarquer que l'équipe Cockpit est une des rares équipes n'avoir aucun objectif fonctionnel et seulement des objectifs techniques. Une solution pourrait donc être d'avoir une nouvelle équipe dédiée au player. Cette équipe aura pour responsabilité de :
* maintenir le code du player et des plugins (déjà la responsabilité de l'équipe Cockpit)
* mettre à jour le code JavaScript de toutes les chaînes qui utilisent le player (aujourd'hui sous la responsabilité de chaque chaîne)

Cette équipe aurait donc un pied dans le code de chaque chaîne et un pied dans du code commun. Cette équipe pourrait, à cette occasion, commencer à diffuser des pratiques communes sur le front-end auprès des équipes des chaines et former ces équipes progressivement aux bonnes pratiques modernes du front-end.

Cette nouvelle organisation permettrait de résoudre les problèmes cités ci-dessus dans le même mouvement. Tous les projets actuellement gérés par l'équipe cockpit et communs aux chaînes pourraient avoir une telle organisation.



## Conclusions

Les sujets de bus factor, open source et arrivée d'un nouveau venu sont en bon état déjà aujourd'hui. 

Des pistes d'améliorations sont possibles. Pour le bus factor, il faut favoriser les pratiques collectives. Pour l'open source et les nouveaux-venus, il faut travailler aux sujets transverses (mutualiser les noms des tâches npm, les configurations eslint, babel, compléter et normaliser les readme).

Concernant les petits points de friction technique entre l'équipe Cockpit et les équipes des chaines et afin de diffuser des pratiques communes sur le JavaScript front-end, une solution peut être de réorganiser un peu l'équipe Cockpit autour de projets qu'ils produiraient puis diffuseraient auprès des chaines.



# Annexe : notes brutes de revue de code

### lecteur-commun/rfplayer

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer/

`"lint": "eslint ./src/main.js",` : ???

`https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer/blob/master/.eslintrc` pas partagé entre projets ?

`globals` peut être remplacé par des [envs](https://eslint.org/docs/user-guide/configuring#specifying-environments)
`browser` + `mocha`


Je n'arrive pas à chercher "object-assign" dans gitlab (HTTP 502)

Les npm script `dev` et `dev-all` n'existent plus => `npm start`

Pourquoi versionner les fichiers `bundle-` ?
=> Les chaînes ne gèrent pas elles-mêmes leurs bulnde avec plugins. 
=> L'objectif à long terme est que les chaines utilisent rfPlayer comme lib (comme une dépendance NPM)
Pour pouvoir lister en dépendance, on pourrait renseigner le `main` pour que le package puisse être `import` (ou `require`)

à quoi sert le dossier `dist` ? `release` ? les `bundle-` dans `src` ?

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer/blob/master/src/models/Source.js#L30
=> Chaine qui mériterait d'être une constante

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer/blob/master/src/actions/PlayerActions.js
=> Faire plusieurs exports


### lecteur-commun/rfplayer-commons

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer-commons/blob/a77cec541cbd70462263cfdad51ab80271c647e7/src/utils.js#L61
=> pas besoin de `item !== null`, déja ouvert par `item &&`

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer-commons/blob/a77cec541cbd70462263cfdad51ab80271c647e7/src/utils.js#L42-50
=> Utiliser [find](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array/find)
`return TYPES.find(t => t === ext)`


https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer-commons/blob/a77cec541cbd70462263cfdad51ab80271c647e7/src/utils.js#L30
=> [path](https://www.npmjs.com/package/path).extname() https://nodejs.org/docs/latest/api/path.html#path_path_extname_path
commons

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer-commons/blob/a77cec541cbd70462263cfdad51ab80271c647e7/src/utils.js#L21
=> y'a des modules pour ça

mergeDeep
=> Y'a sûrement une solution facile avec lodash


rfplayer-commons et rfplayer-constants peuvent être fusionnés dans rfPlayer. Les projets dépendants peuvent utiliser
`require('rfplayer/commons')` ou `require('rfplayer/constants')` [si ce sont les seuls morceaux utilisés](https://nodejs.org/api/modules.html#modules_loading_from_node_modules_folders)




### Kaiser

L'erreur liée au fait de ne pas avoir de `config.local.json` est chiante. Le fichier peut être versionné puis mis dans le .gitignore. Comme ça, il est là et c'est cool, mais les changements ne sont pas versionnés.

Le [pattern singleton](https://gitlab.dnm.radiofrance.fr/croiseur/kaiser/blob/6c47780ed097fcf283a9fb53c609a8113bbbb33e/www/src/services/Booster.js#L14-20) n'est pas utile en JavaScript. Ne créer qu'un seul objet. (Utilisé dans plein de fichiers)


### rf-gdp-client

Il manque un readme utile.

https://gitlab.dnm.radiofrance.fr/croiseur/rf-gdp-client/blob/2471eda7db6bcd4dcb0c6f65b3b23dd2621d6899/src/client/services/ApiClient.js#L52-62
=> Utiliser `Date.now()`

https://gitlab.dnm.radiofrance.fr/croiseur/rf-gdp-client/blob/2471eda7db6bcd4dcb0c6f65b3b23dd2621d6899/src/client/middleware/api.js#L11
=> `=== undefined`


### Kami

https://gitlab.dnm.radiofrance.fr/cockpit/kami/blob/master/docs/Types.md 
=> Super cool !

https://gitlab.dnm.radiofrance.fr/cockpit/kami
=> Pourquoi un Makefile ?

Avantage de TypeScript : si les équipes chaine s'y mettent, elles peuvent profiter des types comme une première info sur si qqch a cassé


### rf-react-components

https://gitlab.dnm.radiofrance.fr/js-commons/rf-react-components/blob/29270262ab9d263bd59a4788e7735fa45d31972c/src/components/Menu.tsx#L15
Un peu bizarre. Utiliser [children](https://facebook.github.io/react/docs/composition-vs-inheritance.html#containment) semble plus adapté

Il faudrait donner de meilleurs noms aux interface (DefaultProps, Props). Et peut-être les exporter, ça sera mieux pour l'open source


### rfplayer-plugin-xiti

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer-plugin-xiti/blob/13ee8791197efac7afefa0545106fe89bacaf30d/src/index.js#L20
=> Rajouter un commentaire sur pourquoi 

### rfplayer-plugin-binaural

https://gitlab.dnm.radiofrance.fr/lecteur-commun/rfplayer-plugin-binaural/blob/0f1065813132cd40728144f77779af02acc88021/src/Convolver.js#L125
=> Une technique utilisée dans d3.js consiste à créer une variable
`const π = Math.PI`. Ou juste `pi`. 
Destructuring sur les math aussi `const {cos, sin, round} = Math`.

Il faudrait une doc ou un papier scientifique
