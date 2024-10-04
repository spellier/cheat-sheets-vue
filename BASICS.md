# Basics

<!-- TOC -->
* [Basics](#basics)
  * [package.json](#packagejson)
    * [Installation de dépendances](#installation-de-dépendances)
      * [Difference entre tilde (~) et caret (^) dans package.json](#difference-entre-tilde--et-caret--dans-packagejson)
      * [dependencies vs devDependencies vs peerDependencies](#dependencies-vs-devdependencies-vs-peerdependencies)
    * [Lancement des tests](#lancement-des-tests)
  * [Traitement des données](#traitement-des-données)
    * [String interpolation](#string-interpolation)
    * [Binding](#binding)
  * [Les hook du cycle de vie](#les-hook-du-cycle-de-vie)
    * [*Pour aller plus loin*](#pour-aller-plus-loin)
<!-- TOC -->

## package.json

### Installation de dépendances

`npm install` => Installe toutes les dépendances du fichier **package.json** (et historise les versions récupérées dans le **package-lock.json**)  
`npm install @training/vue` => Installe la dépendance vue de training en version latest  
`npm install @training/vue@1.1.0` => Installe la dépendance vue de training en version 1.1.0

#### Difference entre tilde (~) et caret (^) dans package.json

La syntaxe des versions npm est au format suivant :
> Major.Minor.Patch

- Major : Breaking changes
- Minor : nouvelles fonctionnalités, Refactor, Depreciation d'anciennes fonctionnalités, ...
- Patch : Correction de bugs

| Notation  | Conséquence                                  | Exemple                                           | Note                                       |
|-----------|----------------------------------------------|---------------------------------------------------|--------------------------------------------|
| Tilde (~) | Met à jour les versions *Patch*              | ~1.2.0 => 1.2.1, 1.2.2, 1.2.5,...,1.2.x           | Permet de récupérer des corrections de bug |
| Caret (^) | Met à jour les versions *Minor* (et *Patch*) | ^1.2.4 => 1.2.5, 1.3.0, 1.3.1, 1.4.0, 1.x.y       | Nouvelles fonctionnalités rétrocompatibles |
| Rien      | Conserve la version dans le fichier          | 1.2.4 => N'ira chercher aucune version supérieure | -                                          |


#### dependencies vs devDependencies vs peerDependencies

| Dependencies                                                                     | devDependencies                                                                                        | peerDependencies                                                                                                                                                      |
|----------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Librairie dont un projet a besoin pour fonctionner efficacement.                 | Packages dont un développeur a besoin pendant le développement.                                        | spécifie que notre package est compatible avec une version particulière d'un package npm.                                                                             |
| Si le package n'existe pas dans le *node_module*, il sera automatiquement ajouté | Lorsque vous installez un package, npm installera automatiquement les dépendances de développement.    | Pas automatiquement installés. Vous devez modifier manuellement votre fichier package.json                                                                            |
| Librairies dont vous avez besoin lorsque vous exécutez votre code.               | Peuvent être nécessaires à un moment donné du processus de développement, mais pas pendant l'exécution | les peerDependencies n'existent que lorsque vous publiez votre propre package, c'est-à-dire lorsque vous développez du code qui sera utilisé par d'autres programmes. |
| `npm install <package name> `                                                    | `npm install <package name> --save-dev`                                                                | Changer le package.json manuellement                                                                                                                                  |

### Lancement des tests

> *TODO*
> Utilisation de Vitest pour les TU
> Utilisation de Cypress pour les E2E

## Traitement des données

### String interpolation

La string interpolation avec les doubles accolades  `{{ }}`  permet d'insérer la valeur d'une propriété TypeScript dans le template.

### Binding

> *TODO*
> props et emits

| Propriété | Définition                                                                                                                 | Exemple                         | Binding                           | 
|-----------|----------------------------------------------------------------------------------------------------------------------------|---------------------------------|-----------------------------------|
| Props     | Propriété d'entrée sur un composant Vue. Il permet à un parent de passer une valeur à un enfant via une propriété liée     | `defineProps(['title'])`        | `:myProp="myData"`                |
| Emit      | Définit un événement de sortie sur un composant Vue. Il permet à un enfant de communiquer avec son parent via un événement | `defineEmits(['enlarge-text'])` | `@myEmit="onClickButton($event)"` |


## Les hook du cycle de vie


| Hook                | Description                                              |
|---------------------|----------------------------------------------------------|
| `setup()`           | Point d'entrée pour la composition API.                  |
| `onMounted()`       | Exécuté après que le composant a été monté.              |
| `onUpdated()`       | Exécuté après que le composant a été mis à jour.         |
| `onUnmounted()`     | Exécuté avant que le composant soit démonté.             |
| `onActivated()`     | Exécuté lorsque le composant est activé (keep-alive).    |
| `onDeactivated()`   | Exécuté lorsque le composant est désactivé (keep-alive). |
| `onBeforeMount()`   | Exécuté juste avant le montage du composant.             |
| `onBeforeUpdate()`  | Exécuté juste avant la mise à jour du composant.         |
| `onBeforeUnmount()` | Exécuté juste avant le démontage du composant.           |
| `watch()`           | Surveille les changements d'une propriété réactive.      |
| `watchEffect()`     | Exécute une fonction réactive à chaque changement.       |
| `provide()`         | Fournit une valeur à des composants descendants.         |
| `inject()`          | Injecte une valeur fournie par un ancêtre.               |
| `computed()`        | Crée une propriété calculée réactive.                    |


---

### *Pour aller plus loin*

- [Vue - Doc officielle](https://fr.vuejs.org/guide/essentials/template-syntax.html)
- [Grafikart - Apprendre Vue.js](https://grafikart.fr/tutoriels/syntax-vuejs-2226#autoplay)