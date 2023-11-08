# Comprendre et mettre en œuvre les fonctionnalités de la blockchain
-------------

Pour cet exercice pratique, nous allons utiliser Geth (Go Ethereum), qui est l'implémentation d'Ethereum avec le langage GO (https://geth.ethereum.org/). Il existe d'autres implémentations d'Ethereum, mais la version Go reste la plus répandue et la plus facile à apprendre.

L'autre outil important ici est le compilateur Solidity (https://solidity.readthedocs.io/en/v0.4.21/), solc. Il nous permettra de compiler les fichiers Solidity utilisés pour implémenter les smart contracts (contrats intelligents) avec Ethereum.

Les conteneurs Docker seront utilisés pour toutes ces applications pratiques. Cela présente plusieurs avantages : 1) la possibilité de déployer plusieurs nœuds avec des fonctionnalités différentes sur la même machine ; 2) la possibilité de tester rapidement différents scénarios ; et 3) l'absence de nécessité d'installer des outils autres que Docker (ET Docker Compose) sur la machine utilisée.

Il convient de noter que nous utiliserons un environnement de test, et donc une blockchain privée dans laquelle nous pouvons créer des utilisateurs, allouer des éthers, effectuer des transactions, développer et tester des contrats intelligents gratuitement et rapidement. Il existe également un certain nombre de réseaux de test Ethereum (Testnet), mais ceux-ci sont principalement destinés à tester des smart contracts et non à expérimenter des éléments de l'architecture de la blockchain.

**Important:** A la fin de la session, veuillez m'envoyer un court rapport par groupe de 2 étudiants répondant aux questions posées dans cet exercice pratique (romain.dulout@univ-eiffel.fr).

## Prérequis (15 minutes max)

**Remarque** Cette partie peut être sautée par les personnes ayant des connaissances sur les principes et commandes de base de docker (l'idée de ce TP n'étant pas de vous former à docker et au devops de manière générale mais de vous introduire quelques concepts de la blockchain). 

Qu'est-ce que docker ? 

Docker est une plateforme de conteneurisation open-source qui permet d'emballer des applications et leurs dépendances dans des conteneurs légers, portables et isolés. En d'autres termes, il s'agit de machines virtuelles beaucoup plus légères, que l'on appelle des conteneurs lors de leurs phases d'exécution. Chaque conteneur docker partage le même noyau du système d'exploitation, là où les VMs émulent un système d'exploitation complet (y compris la mémoire, le stockage et le processeur).

Les conteneurs Docker sont autonomes, ce qui signifie qu'ils incluent tout ce dont une application a besoin pour s'exécuter, y compris le code, les bibliothèques et les configurations.

D'un point de vue technique, Docker offre de nombreux avantages en simplifiant notamment le déploiement et la gestion des applications, car les conteneurs peuvent être exécutés de manière cohérente sur n'importe quelle infrastructure compatible Docker (serveur, hôtes MAC OS, windows, linux) offrant une grande portabilité. 

Enfin, Il affiche une efficacité et une flexibilité accrues, car les conteneurs peuvent être rapidement déployés, mis à jour et réduits, tout en garantissant l'isolation des applications.

Il est aujourd'hui l'une des technologies centrales de l'approche DevOps (https://about.gitlab.com/topics/devops/), que tout nouveau développeur sur le marché se doit de connaître. 

### Installation de Docker

- **Etape 1:** Installez Docker sur votre machine locale.

 Suivez le guide d'installation officiel de votre système d'exploitation (https://docs.docker.com/engine/install/ubuntu/).

  ```shell
  # Example for Linux (Ubuntu)
  sudo apt-get update
  sudo apt-get install docker-ce

```

Pour vérifier que Docker est correctement installé sur votre machine, vous pouvez utiliser la commande suivante :

```shell
docker run hello-world
```
Dans cette ligne de commande :
  1. `docker` indique à votre système d'exploitation que vous utilisez le programme docker
  2. `run` est l'une des différentes sous-commandes proposées par docker pour créer et exécuter un conteneur docker.
  3. `hello-world` est utilisé pour indiquer à docker que vous utilisez une image spécifique qui sera chargée dans le conteneur.

### 2. Télécharger (pull) et exécuter (run) une image Docker 

- **Étape 1:** Télécharger (pull) une image Docker.

  Téléchargez l'image Docker "nginx".

   ```shell
  docker pull nginx
  ```

  - **Étape 2:** Lancer un conteneur NGINX.

  Démarrez un conteneur NGINX en mode détaché.

  ```shell
  docker run -d -p 8080:80 nginx
  ```

- **Étape 3:** Accédez au serveur web NGINX.

  Ouvrez un navigateur web et naviguez jusqu'à `http://localhost:8080`.

Vous venez de lancer votre premier environnement conteneurisé, à partir de l'image NGINX (dtéléchargé depuis dockerhub, librairie en ligne qui contient l'ensemble des images docker).

Nous allons voir maintenant comment créer vos propres images Docker.

### Construire son image docker

- **Étape 1:** Créez un répertoire pour votre projet Docker.
- **Étape 2:** Créez un fichier Docker avec les instructions pour construire un serveur web personnalisé en utilisant NGINX. (exemple de fichier index.html dans ce dossier)

  ```Dockerfile
  # Utilisez l'image de base officielle de NGINX
  FROM nginx

  # Copiez vos fichiers HTML personnalisés à la racine du document NGINX
  COPY custom-html-files /usr/share/nginx/html
  ```

- **Étape 3:** Construire l'image Docker personnalisée.

  ``shell
  docker build -t my-web-server .
  ```

- **Étape 4:** Exécutez un conteneur à partir de votre image personnalisée.

  ``shell
  docker run -d -p 8081:80 mon-serveur-web
  ```

- **Étape 5:** Accédez au serveur web personnalisé.

  Ouvrez un navigateur web et naviguez jusqu'à `http://localhost:8081`.

Vous venez de créer votre image NGINX custom, et lancer ce nouvel environnement conteneurisé.
Nous allons à présent lancer des environnements plus complexes à l'aide de l'orchestrateur de conteneurs docker : docker-compose.

### Orchestration de plusieurs conteneurs docker

- **Étape 1:** Installez Docker Compose.

  Suivez le guide d'installation officiel de Docker Compose sur votre système d'exploitation (vous pouvez essayer d'exécuter le script situé dans ce dossier). 

- **Étape 2:** Créez un répertoire de projet Docker Compose.

  Créez un répertoire nommé `my-docker-app` pour votre projet Docker Compose.

- **Étape 3:** Définissez un fichier YAML pour Docker Compose.

Créez un fichier nommé `docker-compose.yml` dans le répertoire du projet et définissez-y des services (qui sont nos conteneurs). Par exemple, créez des services pour une application web et une base de données (par exemple, WordPress et MySQL).


  ```yaml
  version: '3'
  services:
    wordpress:
      image: wordpress:latest
      ports:
        - "8080:80"
      environment:
        WORDPRESS_DB_HOST: db
        WORDPRESS_DB_USER: exampleuser
        WORDPRESS_DB_PASSWORD: examplepassword
        WORDPRESS_DB_NAME: exampledb
    db:
      image: mysql:5.7
      environment:
        MYSQL_ROOT_PASSWORD: examplepassword
        MYSQL_USER: exampleuser
        MYSQL_PASSWORD: examplepassword
        MYSQL_DATABASE: exampledb
  ```

- **Étape 4:** Démarrez les services Docker Compose.

  Exécutez la commande suivante dans le répertoire du projet pour démarrer les services définis en mode détaché.

  ``Shell
  docker-compose up -d
  ```

- **Étape 5:** Accédez à l'application.

  Ouvrez un navigateur web et naviguez vers `http://localhost:8080`.

Vous venez de lancer un environnement complexe dans lequel plusieurs services (conteneurs) interdépendant sont initialisés puis exécutés. 

Vous n'aurez pas dans la suite du TP le besoin de connaître l'ensemble de ces commandes, retenez simplement la philosophie.


## A. Déploiement d'un réseau Blockchain

Le dossier dans lequel se trouve cet énoncé contient divers fichiers qui peuvent être utilisés pour lancer un réseau Blockchain, y compris 1) deux DockerFiles et 2) un fichier Docker-Compose.

Utilisez la commande `docker-compose up -d` pour lancer le réseau Blockchain.

Le réseau Blockchain que vous venez de lancer est composé de différents types de nœuds : 1) un BootNode, 2) un mineur (miner), 3) un RPC node et un utilisateur.

**QA.1 Expliquer le rôle des nœuds BootNode, Miner et RPC dans une architecture de blockchain.**

En analysant le fichier docker-compose.yaml, nous pouvons voir que les nœuds mineur et RPC s'attachent au BootNode lorsqu'ils sont lancés, ce qui semble logique étant donné le rôle de ces nœuds. En analysant ce fichier, nous pouvons également voir 1) que le nœud RPC expose un port donné et 2) que tous les nœuds sont lancés sur un sous-réseau donné.

**QA.2 Quel port le nœud RPC expose-t-il ? Quelle est la plage d'adresses du sous-réseau ?**

**QA.3 L'analyse de ce fichier révèle également qu'un NetworkID est spécifié. De quoi s'agit-il ?**

En analysant le Dockerfile des nodes (`Dockerfile.nodes`), vous pouvez maintenant voir que ces fichiers utilisent le même fichier `genesis.json` pour leur instanciation.

**QA.4 Quel est le rôle du genesis block dans une blockchain ?**

## B. Communiquer avec un réseau Blockchain

Nous allons maintenant essayer d'établir une communication avec ce réseau Blockchain. Pour ce faire, nous commencerons par utiliser des API endpoints JSON-RPC (https://geth.ethereum.org/docs/interacting-with-geth/rpc).

Pour ce faire, votre première tâche sera de mettre à jour le docker-compose pour ajouter un second nœud RPC, en associant son port 8045 au port 8046 de localhost.

Une fois cela fait, vous devriez pouvoir tester que votre implémentation fonctionne (une nouvelle fois docker-compose up) en utilisant la commande suivante depuis localhost :  `curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":67}' http://127.0.0.1:8545` 

**QB.1 Que vous montre le résultat de ce curl ?**

**QB.2 Que devez-vous changer dans cette ligne pour retourner la liste des comptes eth enregistrés sur ce réseau Blockchain ? Combien y a-t-il de comptes à l'heure actuelle ?** Pour répondre à cette question, vous pouvez utiliser le site suivant : https://www.okx.com/okbc/docs/dev/api/okbc-api/json-rpc-api#eth-accounts

**QB.3 Quelle ligne du fichier Dockerfile.nodes semble avoir créé cet utilisateur ? Dans lequel des fichiers fournis de ce TP ce mot de passe est-il stocké ? Quelle est la valeur de ce mot de passe ?** Vous pouvez récupérer ces informations à partir du fichier docker-compose.yaml

Nous allons maintenant découvrir une nouvelle façon de se connecter et d'interagir avec le nœud RPC. 

Pour cela, nous allons nous connecter au client geth, qui fait partie du réseau Docker déployé. Comme vous pouvez le voir dans son fichier docker (`Dockerfile.user`), il possède tous les outils nécessaires : geth et solc en particulier.

La ligne de connexion est la suivante :  `docker exec -it "CONTAINER_NAME" /bin/bash`. En utilisant `docker ps` vous devriez pouvoir retrouver le nom de ce conteneur.

Une fois connecté, il s'agit maintenant de s'attacher au nœud RPC. Pour ce faire, il suffit d'entrer la ligne suivante : `geth attach rpc:http://IP:port`.

**QB.4 Quels sont l'adresse IP et le port à spécifier ?**

Une fois attachée, vous pouvez maintenant vérifier que tout fonctionne correctement en affichant à nouveau la liste des comptes d'utilisateurs actuellement enregistrés dans la blockchain : `eth.accounts`

À ce stade, si vous le souhaitez, vous pouvez supprimer le deuxième nœud RPC que vous avez ajouté. Vous n'en aurez pas besoin pour la suite de ce tutoriel.

## C. Gestion des utilisateurs

Nous allons maintenant créer deux utilisateurs supplémentaires. Pour ce faire, vous devez utiliser la commande suivante : personal.newAccount(). 

**QC.1 Quelles informations devez-vous préciser à ce stade ?**

Vérifiez que les deux comptes apparaissent désormais dans la liste des comptes associés à ce réseau Blockchain.

**QC.2 En utilisant la fonction `eth.getBalance(eth.accounts[0])`, vous devriez être en mesure de vérifier le solde des utilisateurs individuels. Quel est le solde de l'utilisateur 1, de l'utilisateur 2 et de l'utilisateur 3 ? Comment l'expliquez-vous ?**

**QC.3 Que fait la fonction suivante ? À quoi pensez-vous qu'elle soit utile ?** `personal.unlockAccount(ACCOUNT, PASSWORD)`

## D. Effectuer une première transaction

Maintenant que notre réseau est opérationnel et que nous savons comment nous connecter et nous authentifier, nous allons effectuer notre premier transfert d'argent entre deux utilisateurs. C'est la fonction de base d'un réseau Blockchain.

Ces transactions seront stockées dans des blocs, la structure de base d'un système basé sur la blockchain.

**QD.1 Quel est le nombre actuel de blocs dans la blockchain ? Pour le savoir, utilisez la fonction eth.blockNumber .**

Le fait que cette valeur soit non nulle est principalement dû au fait que nous sommes dans un réseau de test.

**QD.2 La difficulté définie dans le fichier genesis.json est très faible. Qu'est-ce que cela signifie en termes de vitesse de génération des blocs ?**

Cela a pour avantage que le premier utilisateur créé est automatiquement rattaché en tant que compte au mineur, il reçoit donc de l'argent à chaque nouvelle génération de bloc. C'est ainsi que nous pourrons effectuer notre première transaction !

Pour ce faire, vous pouvez utiliser une ligne comme celle-ci : `eth.sendTransaction({from:EMITTER, to:RECEIVER, value:50000000})`

Notez que vous pouvez spécifier l'adresse d'un compte donné (EMITTER/RECEIVER) en utilisant la ligne suivante : `eth.accounts[1]`.

**QD.3 Quelle ligne devez-vous saisir pour transmettre de l'argent de l'utilisateur 1 à l'utilisateur 2 ? Quelle est l'opération à effectuer avant de le faire ?**

**QD.4 Quelles informations sur un bloc peuvent être récupérées à l'aide de la fonction eth.getBlock(NUMBER) ? Quelle est l'adresse du mineur ?**

**QD.5 Quelles informations sur une transaction peuvent être récupérées à l'aide de la fonction eth.getTransaction(NUMBER) ? Que signifie "gas" dans Ethereum ?**

## E. Deployer un premier Smart Contract

**QE.1 Qu'est ce qu'un Smart Contract?**

Pour commencer, nous allons simplement essayer de compiler et de déployer le code actuel dans le fichier tirelire.sol. Pour ce faire, nous allons d'abord compiler ce fichier en générant deux fichiers essentiels : un fichier bin, qui correspond au contrat compilé et donc au code qui sera placé dans la blockchain, et un fichier ABI (Application Binary Interface), qui nous permettra d'interagir avec le contenu hexadécimal du fichier bin dans un format compréhensible.

Utilisez votre navigateur web pour accéder à `https://chriseth.github.io/browser-solidity/#version=soljson-latest.js`, le compilateur en ligne en temps réel de Solidity. Après avoir accédé à cette page dans un navigateur, collez le contenu du fichier tirelire.sol à la place du code sur l'interface. Cela devrait avoir été compilé automatiquement et vous pouvez maintenant récupérer le code `ABI` et `bin` généré sous les noms de variables `Bytecode` et `Interface`.

Note (importante !): si vous utilisez la dernière version du compilateur, il se peut que cela ne fonctionne pas. Nous vous conseillons donc de modifier les paramètres de configuration pour utiliser une version fonctionnelle du compilateur telle que : `0.4.24+commit.e67f0147`.

```console
// Store the bin file, remembering to add 0x so that it is correctly interpreted
// as hexadecimal content and put quotation marks around it!

tirelireBin = '0x' + "<BIN from compiler!>"

// Simple storage of the ABI in a variable (JSON format)

tirelireAbi = <abi from compiler>

// Tells Geth that this variable is an ABI: transform JSON into ABI

tirelireInterface = eth.contract(tirelireAbi)

//Unlock the user to publish the smart contract

personal.unlockAccount(eth.accounts[0], "PASSWD")

//Publish the smart contract

var tirelireTx = tirelireInterface.new({from: eth.accounts[0],data: tirelireBin,gas: 1000000})

// Retrieve the hash

tirelireTxHash = tirelireTx.transactionHash

// Finally, we retrieve the contract address

publishedTirelireAddr = eth.getTransactionReceipt(tirelireTxHash).contractAddress

```

Maintenant que vous avez effectué ces différentes commandes, le contrat devrait être miné et il devrait être possible de l'utiliser ! Pour le vérifier, nous allons essayer d'utiliser les différentes fonctions définies dans le fichier tirelire.sol.

Nous allons commencer par mettre de l'argent dans notre tirelire avec la commande tirelireTx.donner({from:eth.accounts[0], value : "50000000000000000"}). Pour vérifier que cela a fonctionné, nous pouvons consulter le solde actuel de cette tirelire avec la commande eth.getBalance( tirelireTx.address). Nous pouvons maintenant allouer cet argent à l'utilisateur avec la commande tirelireTx.retirer({from:eth.accounts[0]}).

Note : Vous pouvez voir que lors de la publication du contrat intelligent, un attribut appelé gas a été spécifié. Le gas est une unité de mesure correspondant aux ressources de calcul nécessaires à l'accomplissement d'une tâche, en l'occurrence les ressources de calcul nécessaires à l'exécution d'une transaction, à l'instanciation d'un smart contract ou à l'interaction avec ce smart contract. Bien entendu, certaines transactions sont beaucoup plus simples que d'autres, et par conséquent le calcul nécessaire à leur mise en place est beaucoup plus léger. Un mineur qui consacre son temps, son électricité et sa capacité de calcul à l'exécution d'un code pour résoudre une preuve et mettre en place une transaction doit être rémunéré, et c'est le Gas qui en détermine le montant. Le Gas est donc la capacité de calcul nécessaire à l'exécution d'un smart contract ou d'une transaction. Plus la demande est complexe, plus le coût est élevé, il est donc possible de définir une limite de Gas, c'est-à-dire le montant maximum de Gas que l'on est prêt à dépenser pour une transaction/un contrat. En cas d'erreur dans le code ou de bug, la capacité de calcul pourrait être infinie, et la fixation d'une limite permet de se prémunir contre ce genre de problème. Si la limite est atteinte, la transaction est interrompue et annulée. De plus amples informations à ce sujet peuvent être trouvées ici ( https://masterthecrypto.com/ethereum-what-is-gas-gas-limit-gas-price/)

**QE.2 D'après cet article, existe-t-il des solutions pour faire passer ses transactions avant celles des autres ?**

Nous allons maintenant pousser notre contrat intelligent un peu plus loin en ajoutant des variables.

Pour cela, commencez par copier le fichier tirelire.sol dans un nouveau fichier tirelire_2.sol, sur lequel vous travaillerez jusqu'à la fin de ce TP (afin de conserver une base valide du fichier).

Ouvrez maintenant le fichier et créez une nouvelle variable uint publique nbAcces, que vous instancierez à 0 dans le constructeur. Cette fonction nous permettra de savoir combien de fois nous avons mis de l'argent dans notre tirelire, nous devrons donc l'incrémenter à chaque fois que nous appellerons la fonction give.

Une fois ces trois étapes franchies (définition, instanciation et incrémentation), nous devons répéter les étapes de la question précédente avec le fichier tirelire_2.sol avant de pouvoir l'installer sur la blockchain.

Une fois le fichier configuré (abi, bin, contrat), vérifiez que les modifications que vous venez d'apporter fonctionnent correctement. Pour cela, vérifiez d'abord la valeur de la variable publique uint nbAcces, puis déposez un peu d'argent dans votre tirelire et affichez à nouveau la valeur de nbAcces - elle devrait avoir été incrémentée.

Dans cette section, vous verrez qu'un contrat intelligent permet l'instanciation de variables dont les valeurs seront stockées dans la blockchain. Ces variables peuvent être affichées ou modifiées en appelant les fonctions et les constructeurs du contrat intelligent.

Nous allons maintenant définir une nouvelle valeur, qui correspondra à l'objectif que nous voulons atteindre, c'est-à-dire le nombre d'éthers que nous voulons avoir stocké dans notre tirelire avant de pouvoir en retirer de l'argent.

Pour ce faire, toujours dans le même fichier, nous définissons d'abord une nouvelle valeur : goal, que nous instancions à l'adresse 100000000000000000 dans le constructeur.

Une fois cela fait, nous ajouterons une nouvelle ligne à la fonction withdraw, qui devrait signifier que si le montant de notre compte d'épargne n'est pas supérieur ou égal à notre objectif (goal), nous ne pourrons pas retirer d'argent. Pour ce faire, vous pouvez utiliser la fonction assert, qui permet de vérifier si une assertion est vraie (similaire à un if).

**QE.3 Quelle ligne doit être ajoutée ? (c'est-à-dire donner la ligne de code)**

Après avoir compilé et ajouté à nouveau ce contrat à la blockchain, vérifiez que ce que nous venons de mettre en place fonctionne :
  - faites un premier transfert vers la tirelire tirelireTx.donner({from:eth.accounts[0], value : "50000000000000000"}) et affichez son solde ;
  - essayez de retirer de l'argent de la blockchain, et affichez son solde, que constatez-vous ?
  - faites un second transfert de la même valeur, et essayez à nouveau de retirer de l'argent de la tirelire, que se passe-t-il cette fois ?

En définissant cette variable et cette condition, nous pouvons voir que si les conditions d'un accord (quelles qu'elles soient) ne sont pas remplies, l'accord ne sera pas conclu. Dans ce cas, s'il n'y a pas assez d'argent, nous ne pourrons pas le retirer.

Cette section montre également qu'une fois une variable définie, s'il n'y a pas de fonction pour la modifier, il sera impossible d'interagir avec elle, par exemple, dans notre cas, pour la diminuer. Cette valeur est donc définitivement fixée, et cette condition sera toujours vraie et nécessaire.

La seule façon de modifier cette valeur serait de créer un nouveau smart contract avec une valeur de départ différente, mais c'est une fausse solution, car l'argent mis dans notre première tirelire serait perdu....

Après avoir versé de l'argent à l'un des autres utilisateurs, par exemple : eth.sendTransaction({from:eth.accounts[0], to:eth.accounts[1], value:50000000000000000}) (sans argent, cet utilisateur ne pourra pas payer le mineur et donc ne pourra pas agir sur le contrat !), déposez de l'argent sur le contrat avec l'utilisateur 1 (eth. accounts[0]) tirelireTx.donner({from:eth.accounts[0], value : "100000000000000000"}) et essayez de vider le contenu du smart contract avec l'adresse de l'utilisateur auquel vous avez effectué la première transaction : tirelireTx.retirer({from:eth.accounts[1]}) (une authentification peut être nécessaire). Combien d'argent notre tirelire contient-elle maintenant ?

Comme vous pouvez le constater, un contrat intelligent est ouvert, et toute personne possédant l'adresse du contrat peut y accéder. Ainsi, n'importe quel utilisateur peut interagir avec les fonctions publiques de notre contrat, et comme c'est le cas ici, il pourrait être facile pour une personne malveillante de voler le contenu de notre tirelire....

Pour ce faire, nous créons et instancions un nouveau propriétaire d'adresse à msg.sender dans le constructeur, et ajoutons un assert à la fonction withdraw pour vérifier que l'adresse correspond à l'adresse du créateur du smart contract.

**QE.4 A quoi correspond la ligne à ajouter ? (c'est-à-dire donner la ligne de code)**

Une fois le contrat déployé, déposez un peu plus d'argent dans notre tirelire : tirelireTx.give({from:eth.accounts[0], value : "100000000000000000"}) puis essayez d'y accéder avec un autre utilisateur. Que constatez-vous ?

## F. Vers les DApps

**QF.1 Qu'est-ce qu'une DApp ?**

La méthode web3.personal.unlockAccount n'est pas disponible dans les dernières versions de Web3.js, et il n'est pas recommandé de l'utiliser dans les applications web pour des raisons de sécurité. Le déverrouillage d'un compte de cette manière est généralement effectué dans la console du client Ethereum à des fins de développement.

Pour interagir avec les comptes Ethereum d'une manière plus sûre et plus conviviale dans une Dapp, vous devriez utiliser un fournisseur de portefeuille (wallet) comme MetaMask. MetaMask gère la gestion des comptes et la signature des transactions en toute sécurité. Les utilisateurs peuvent installer l'extension de navigateur MetaMask et la connecter à votre Dapp, offrant ainsi une meilleure expérience utilisateur.

**QF.2 Qu'est-ce que MetaMask ?**

Dans cette section, nous allons voir s'il est possible ou non de faire fonctionner une DApp localement sur notre propre réseau. 

Pour ce faire, vous devrez ajouter MetaMask à votre navigateur et le configurer pour utiliser notre réseau local. Vous pouvez suivre ce guide par exemple : https://dev.to/afozbek/how-to-add-custom-network-to-metamask-l1n 

Cependant, l'ajout du réseau Blockchain local ne sera pas suffisant pour interagir avec le réseau local : vous devrez également ajouter au moins un des utilisateurs du réseau. Pour ce faire, vous devrez utiliser la clé privée de l'utilisateur (l'utilisateur 0, par exemple).

Avec Geth, le processus de création d'un utilisateur (cf. sections précédentes) n'implique pas le stockage de cette clé privée, car cela pourrait poser des problèmes de sécurité. Seule la phrase de passe que nous avons enregistrée dans notre environnement est stockée.

Il vous faudra donc re-générer cette clé privée. Pour cela, vous devrez vous connecter à l'une des trois machines de votre réseau (bootnode, miner ou RPC) avec la commande suivante : ` docker exec -it bc-example_geth-rpc-endpoint_1 sh`.

Une fois cela fait, il vous faudra récupérer le fichier contenant les informations de l'utilisateur 0, à partir duquel vous pourrez régénérer sa clé privée. Ce fichier est situé dans : "~/.ethereum/keystore".

Une fois ce fichier récupéré, soit directement sur votre machine en installant web3, soit en l'installant sur le client geth docker, vous pourrez régénérer cette clé en utilisant la commande suivante : `web3 account extract --keyfile FILE-YOU-RETRIEVED --password USER-PASSPHRASE`.

Il ne reste plus qu'à importer ce compte (dans votre réseau local) dans MetaMask en important un nouvel utilisateur à partir d'une clé privée. À ce stade, le solde de l'utilisateur devrait apparaître. Vous pourrez également visualiser les transactions futures.

C'est tout ce qui doit être fait au niveau de MetaMask.

Ensuite, vous devez modifier le fichier dapp que nous vous avons fourni en ajoutant YOUR_CONTRACT_ADDRESS, qui correspond à l'adresse du contrat de la tirelire (vous pouvez l'obtenir auprès de geth).

Enfin, vous devrez lancer un serveur Python (ou autre) pour partager le contenu de cette page html. Pour ce faire, vous pouvez simplement utiliser la commande suivante (dans un dossier dans lequel vous avez placé le fichier DApp) : `python3 -m http.server 8000`.

Vous pouvez alors ouvrir le navigateur de votre choix et interagir avec le contrat. Veuillez noter que vous devrez CONNECTER MetaMask à cette page. Pour ce faire, cliquez simplement sur votre extension MetaMask, puis sur les trois points dans le coin supérieur droit, et vous verrez "Connected Sites", où vous pouvez ajouter ce site.

Il ne reste plus qu'à tester si tout cela fonctionne !









