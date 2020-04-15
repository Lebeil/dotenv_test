Cours : Dotenv
  
1. Introduction
Aujourd'hui, tu vas apprendre à manipuler des clés d'API. Les clés d'API sont en quelque sorte tes identifiants et mots de passe pour faire communiquer tes programmes avec un programme tiers (Twitter, Google Drive, Spotify, etc.). Le hic, c'est que si tu écris tes identifiants dans un programme que tu vas ensuite push sur GitHub (où il est public et donc lisible par tous), c'est le meilleur moyen de faire usurper ton identité sur les API.
Il existe d'ailleurs moult bots qui s'amusent à aller sur tout GitHub et récupérer les identifiants des développeurs maladroits. En les utilisant, ils peuvent alors profiter des services à ta place (surtout si ce sont des services payants) ou commettre des délits en ton nom. Sympa non ? Bienvenue sur le net. 🏴‍☠️

Dans cette ressource, nous allons te montrer comment arriver à manipuler très facilement des clés d'API, tout en les gardant secrètes, grâce à la gem Dotenv !

2. Historique
Un moussaillon s'est retrouvé avec une facture Amazon de 16 000€ pour avoir mis ses clefs d’API sur GitHub sans passer par Dotenv (Amazon Web Services exige souvent une carte bleue avant de te donner un accès). OK, c'est plutôt une anecdote mais j'avais pas d'historique à te proposer sur Dotenv 😇.

3. La ressource
Dotenv est une gem qui permet de stocker des clés d'API dans un joli fichier qui va rester secret, car il ne sera jamais push sur GitHub. Via la gem, ton programme pourra faire appel au contenu de ce fichier pour utiliser les clés.

3.1. Comment ça marche ?
On va mettre toutes les clés d'API dans un fichier dont le nom complet est .env, puis on pourra les appeler en faisant ENV["NOM_DE_LA_CLÉ"] dans notre programme Ruby. Puis afin d'éviter de pousser le bousin sur GitHub, on va rajouter le fichier .env dans notre .gitignore.

📚 INSTANT CULTURE GÉ
En programmation, il est courant de manipuler des fichiers dont le nom commence par .. Ainsi quand je parle du fichier .env, c'est son nom complet et pas un façon de dire "un fichier ayant l'extension .env".

Souvent, les fichiers commençant par . sont, par défaut, cachés. Si tu vas dans un dossier avec l'explorateur de fichier ou si tu fais la commande ls dans ton terminal, les fichiers commençant par . ne te seront pas affichés. Pour les visualiser, fait plutôt ls -a.

3.2. Exemple avec un dossier Ruby simple
Crée un petit dossier de test dotenv_test avec l'architecture suivante :

dotenv_test
├── lib
│   └── app.rb
├── README.md
├── Gemfile
├── .env
└── .gitignore
Puis ajoute la gem dans le Gemfile :

gem 'dotenv'
Lance un petit bundle install qui va donc rajouter un fichier Gemfile.lock à ton arborescence et rendre la gem disponible dans tout le dossier.

Maintenant, on va compléter le fichier .env pour lui faire stocker des clés d'API ou autres éléments secrets :

TWITTER_API_KEY="my-twitter-api-key"
TWITTER_API_SECRET="this_is_soooo_secret"

BEST_WEBSITE_EVER="thehackingproject"
Dernière étape : accéder aux infos ultra-confidentielles de .env depuis /lib/app.rb. Pour cela, voici le contenu de app.rb :

require 'dotenv'# Appelle la gem Dotenv

Dotenv.load('.env') # Ceci appelle le fichier .env (situé dans le même dossier que celui d'où tu exécute app.rb)
# et grâce à la gem Dotenv, on importe toutes les données enregistrées dans un hash ENV

# Il est ensuite très facile d'appeler les données du hash ENV, par exemple là je vais afficher le contenu de la clé TWITTER_API_SECRET
puts ENV['TWITTER_API_SECRET']

#Autre exemple 
puts ENV['BEST_WEBSITE_EVER']
Grâce à tout cela, si tu vas dans ton dossier dotenv_test et que tu exécutes $ ruby lib/app.rb, tu devrais voir les 2 contenus secrets s'afficher. On a bien réussi à lire le fichier .env !

🤓 QUESTION RÉCURRENTE
Si maintenant tu te places dans dotenv_test/lib et que tu exécutes $ ruby app.rb, tu devrais avoir une erreur : il nous trouve plus le fichier .env... Pourquoi ?

Tout est lié à la ligne Dotenv.load('.env') dans ton fichier app.rb. C'est cette ligne qui va dire au programme où se trouve .env et si tu écris juste Dotenv.load('.env'), en gros tu lui dis "il est dans le dossier courant". Le dossier courant, c'est celui depuis lequel tu vas exécuter le programme.

Du coup ça devient logique : si tu es (sur ton terminal) dans dotenv_test et que tu exécutes $ ruby lib/app.rb, il trouve .env. Par contre si tu es dans dotenv_test/lib, il va le chercher dans /lib/ et ne le trouvera pas.

Pour les curieux, sachez qu'en faisant Dotenv.load('../.env'), on arrive à faire fonctionner la lecture de .env en étant dans /lib. Mais ne vous prenez pas trop la tête avec ces considérations pour le moment.

⚠️ ALERTE ERREUR COMMUNE
Ouiiiin, Dotenv ça marche paaaas ! 😭

Voici les principales coquilles que les moussaillons font et qui empêchent la gem de bien faire son boulot :

Le fichier .env est mal nommé ou pas situé à la racine du dossier.
Dans le fichier .env, les clés d'API ne sont pas définies comme des strings. Il faut les déclarer ainsi : Nom_de_la_clef = "contenu de la clé". Si vous oubliez les guillemets, ça foire.
Dans ton programme, la clé d’API n'est pas bien appelée. C'est un string à appeler sur le hash ENV pour en obtenir la valeur. Avec l'exemple précédent, je dois écrire ENV['Nom_de_la_clef'] (encore une fois, n'oubliez pas les guillemets).
3.3. On push maintenant sur Github
Dernière étape très importante : être capable de push sur Github ton code mais pas tes clefs API super secrètes !

Pour cela, on va rajouter la ligne .env (oui, juste ces 4 caractères suffisent) dans le fichier .gitignore et le sauvegarder.

Fait le test à présent en pushant ton dossier dotenv_test sur Github. Si jamais un fichier .env apparaît, c'est perdu : tes clefs d'API sont compromises (quelqu'un les a peut-être déjà lues). Recommence le process à zéro en cherchant ton erreur !

Si tu veux partager tes clefs d'API, par exemple avec une autre personne de ton équipe, ça n'est donc pas via Github : il faudra les envoyer par e-mail ou par Slack.

⚠️ ALERTE ERREUR COMMUNE
N'oublie pas cette étape d'ajout dans le .gitignore ! Sinon tout ça n'aura servi à rien et tu vas push .env sur GitHub … Et les hackers te disent remercient.

4. Points importants à retenir
Pour utiliser Dotenv, ajoute gem 'dotenv' à ton Gemfile, puis créé un fichier .env qui contiendra tes clés d'API et qui sera dans ton .gitignore.

Tu auras juste à require 'dotenv' et Dotenv.load pour avoir accès au contenu du fichier dans tes programmes Ruby.

Une cheatsheet Dotenv est disponible ici. https://github.com/codeunion/dotenv-example

5. Pour aller plus loin
Tu peux jeter un œil sur le repo de la gem Dotenv. https://github.com/bkeepers/dotenv