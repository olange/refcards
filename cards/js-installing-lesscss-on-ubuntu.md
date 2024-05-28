# Installing LessCSS on Ubuntu

LessCSS permet de compiler les scripts `.less` en scripts CSS équivalents. Ce compilateur enrichit la syntaxe CSS avec des primitives qui facilitent la réutilisation de fragments de code CSS. On en trouve un exemple éloquent dans le framework [Twitter Bootstrap](https://github.com/twitter/bootstrap/)

## Installation

1. Installer NodeJS

   ```
   sudo apt-get install python-software-properties
   sudo add-apt-repository ppa:chris-lea/node.js
   sudo apt-get update
   sudo apt-get install nodejs
   ```

2. Installer NPM

   ```
   curl http://npmjs.org/install.sh | sh
   ```
  
3. Installer LessCSS (l'option `--global` permet d'utiliser `lessc` en ligne de commande, en plaçant un lien vers l'exécutable de LessCSS dans `/usr/local/bin` plutôt que dans `~/node_modules/.bin`)

   ```
   npm install --global less
   ```

## Usage
  
LessCSS peut alors être invoqué comme suit:

```
lessc SOURCE_LESS_FILE > DEST_CSS_FILE
```
    
## Voir aussi

* [Installing NodeJS with a package manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)

(initially published on my [Makandra Cards › JavaScript › Installing LessCSS on Ubuntu](https://makandracards.com/js/7813-installing-lesscss-on-ubuntu))
