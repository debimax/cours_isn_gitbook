# Programmation web avec Brython</div>

Cours issue de la [documentation Brython](https://www.brython.info/static_doc/fr/intro.html)  
Pour débogguer vos script avec le navigateur:
- firefox: Outils-> Développement web->consol web (Ctrl+Maj+k))

## Introduction



### Qui fait quoi?


lorsque vous saisissez une URL dans votre navigateur, que vous validez cette dernière, votre navigateur envoie une "requête" au serveur concerné afin qu'il nous renvoie une page web.  
Tout d'abord, on nomme vulgairement l'échange de données entre votre navigateur et le serveur qui fournit les pages web un échange ***client / serveur***.   
Le client représente votre navigateur.

<object type="image/svg+xml" data="https://github.com/debimax/cours-debimax/raw/master/images/client-serveur.svg" width="400">
    Le navigateur ne peut lire ce kiwi
</object>


<center>
<img width=400px src="https://github.com/debimax/cours-debimax/raw/master/images/client-serveur.png" alt="client serveur sur internet"   />
</center>


Normalement coté client c'est javascript qui est utilisé pour programmer.  
Il existe des lirairies pour facilité la programmation ***javascript*** comme ***jquery, p5js***.  N'ayant pas le temps de vous apprendre à coder en javascript nous utiliseront ***Brython***  qui nous permet de coder en python. La libraire Brython transforme ce code python en javascript (compréhensible par votre navigateur).

Vous devez bien faire faire la différence le programme exécuté coté client (javascript ou brython) et le programme exécuté coté serveur (php ou python).

En fait le code python est convertit en javascript qui compréhensible pour votre navigateur internet


```python
from IPython.core.display import SVG
SVG(url='https://github.com/debimax/cours-debimax/raw/master/images/client-serveur.svg')
```




    <IPython.core.display.SVG object>



### Que peut on faire avec Brython?


On peut faire la même chose qu'avec javascript

- Accéder aux DOM (Document Object Model)
- Modifier le DOM
- Réagir aux évènements de la souris, du clavier etc...


### Préparation des fichiers  


Il est necessaire d'utiliser d'utilser deux fichiers ***brython.js***  et ***brython_stdlib.js***  
Comme internet au lycée  n'est pas toujours fiable je vous conseille de télécharger ces deux fichiers

- Dans votre dossier créez le dossier ***isn-internet*** puis ***isn-internet/static***
- Téléchargez puis déposer dans le dossier *isn-internet/static* le fichier [brython.js](https://www.brython.info/src/brython.js).
- Téléchargez puis déposer dans le dossier *isn-internet/static* le fichier [brython_stdlib.js](https://www.brython.info/src/brython_stdlib.js)

### Exemple 1 (Hello World)

Créez le fichier ***exo1.html*** dans le dossier *isn-internet* avec votre éditeur de texte (pyzo, geany, idle ...)

```html
<html>
<head>
  <meta charset="utf-8" />
  <script type="text/javascript" src="static/brython.js"></script>
  <script type="text/javascript" src="static/brython_stdlib.js"></script>
  <title>Clock</title>
</head>
<body onLoad="brython()">
<h1>Exemple 1:</h1>
<div>
  <button id="bouton" type="button">Cliquez!</button>
  <label id='hi'>Un texte</label>
</div> 
<script type="text/python">
from browser import document
def affiche(ev):
    document['hi'].text="Hello Word"

document["bouton"].bind("click", affiche)
</script>
</body>
</html>
```

Il ne reste plus qu'à ouvrir ce fichier avec votre navigateur préféré.

Un peu d'explication pour le code ci-dessus.

***document["bouton"]***  est l'élément qui a pour ***id*** "bouton", donc on définit l'évènement click sur le button d'id "bouton" qui déclenche la fonction ***affiche***.  
Remarque: C'est la même syntaxe que pour tkinter.

Il est aussi possible d'envoyer des arguments à la fonction ***affiche***  en utilisant les fonctions ***lamda***.  
Modifer le code comme ci-dessous.

```python
<script>
from browser import document
def affiche(message,ev):
    document['hi'].text=message

message="Hello World"
document["bouton"].bind("click", lambda ev: affiche(message,ev))
</script>
```

La syntaxe avec la fonction lambda sera identique avec tkinter.

Modifions encore le code pour cette fois intéragir avec le clavier. La touche ***Esc*** a pour keycode 27.

```python
<div>
  <label id='hi'>Appyer sur la touche esc</label>
</div> 
<script type="text/python">
from browser import document
def affiche(message,ev):
    if int(ev.keyCode)==27 :
        document['hi'].text=message

document.bind('keydown', lambda ev :  affiche("Hello World", ev))
document.bind('keyup',   lambda ev :  affiche("Appyer sur la touche esc", ev))
</script>
```

On associe à l'évènement 'keydown' (appuyer sur une touche) le déclenchement de la fonction affiche avec les paramètre "Hello World" et ***ev*** où ev signifie ***event (évènement)***.

### Exemple 2 (Afficher l'heure)

C'est le module ***time*** qui gère l'heure en python. Créer le fichier ***exo2.html***.

```html
<html>
<head>
<meta charset="utf-8" />
<script type="text/javascript" src="brython.js"></script>
<script type="text/javascript" src="brython_stdlib.js"></script>
<title>Clock</title>
</head>
<body onLoad="brython()">
<h1>exemple 2: Obtenir la date et l'heure</h1>
<div id="date"></div>
<script type="text/python">
from browser import document
import time
document["date"].text=time.strftime('Nous somme le %d %m %y, il est %H:%M:%S ',localtime)
</script>
</body>
</html>
```

Vous voyez tout de suite ce qui ne va pas.  Il faudrait rafraichir toutes les secondes.  
On modifie donc le script comme ci-dessous.  

```python
<script type="text/python">
from browser import document as doc
from browser  import timer  #https://www.brython.info/static_doc/en/timer.html
import time
def getClock():
	localtime = time.localtime(time.time())
	document["date"].text=time.strftime('Nous somme le %d %m %y, il est %H:%M:%S ',localtime)

timer = timer.set_interval(getClock,1000)
</script>
```

## Accéder aux éléments de la page



Pour accéder à un élément, on peut utiliser plusieurs méthodes. La plus courante est de se servir de son identifiant, c'est-à-dire de son attribut *id* : si on a une zone de saisie définie par  
```html
    <input id="data">
```

on peut obtenir une référence à ce champ par  
```python
    from browser import document
    data = document["data"]
```

L'objet document du module browser référence le document HTML. Il se comporte comme un dictionnaire dont les clés sont les identifiants des éléments de la page. Si aucun élément ne possède l'identifiant spécifié, le programme déclenche une exception *KeyError*

On peut aussi récupérer tous les éléments d'un certain type, par exemple tous les liens hypertexte (balise HTML A), en utilisant la syntaxe  

```python
from browser import html
links = document[html.A]
```

Enfin, tous les éléments de la page possèdent une méthode ***get()*** qui permet de rechercher des éléments de plusieurs façons :

``` elt.get(name=N)``` retourne une liste avec tous les éléments descendant de *elt* dont l'attribut name est égal à N.  
``` elt.get(selector=S)``` retourne une liste avec tous les élements descendant de *elt* dont le sélecteur CSS correspond à S.  

Quelques exemples :

```python
document.get(selector='.foo')       # éléments avec la classe "foo"
document.get(selector='form')       # liste des balises "<form>"
document.get(selector='H1.bar')     # balises H1 avec la classe "bar"
document.get(selector='#container') # liste avec l'élément dont l'id vaut "container", similaire à [document["container"]]
document.get(selector='a[title]')   # balises A avec un attribut "title"
```


## Attributs et méthodes des éléments

Les éléments de la page possèdent des attributs et des méthodes qui dépendent du type de l'objet ;  
On peut les trouver sur de nombreux [sites Internet](https://developer.mozilla.org/fr/docs/Web/HTML/Attributs).

Comme le nom des attributs peut être différent d'un navigateur à l'autre, Brython définit des attributs supplémentaires qui fonctionnent dans tous les cas :

<table style="font-size: small">
<tr><th> Nom </th><th>Type </th><th> Description</th><th> L = lecture seule<br />
L/E = lecture + écriture</th></tr>
<tr><td> abs_left </td><td> entier </td><td> position de l'élément par rapport au bord gauche de l'écran </td><td> L </td></tr>
<tr><td> abs_top </td><td> entier </td><td> position de l'élément par rapport au bord supérieur de l'écran </td><td> L </td></tr>
<tr><td> children </td><td> liste </td><td> les éléments "descendants" de l'élément </td><td> L </td></tr>
<tr><td> class_name </td><td> chaine </td><td> le nom de la classe de l'élément (attribut class de la balise) </td><td> L/E</td></tr>
<tr><td> clear </td><td> méthode </td><td> elt.clear() supprime tous les descendants de l'élément </td><td> - </td></tr>
<tr><td> height </td><td> entier </td><td> hauteur de l'élément en pixels (2) </td><td> L/E </td></tr>
<tr><td> html </td><td> chaine </td><td> le code HTML contenu dans l'élément </td><td> L/E </td></tr>
<tr><td> inside </td><td> méthode </td><td> elt.inside(autre) teste si elt est contenu dans l'élément autre </td><td> - </td></tr>
<tr><td> left </td><td> entier </td><td> la position de l'élément par rapport au bord gauche du premier parent positionné (1) </td><td> L/E </td></tr>
<tr><td> parent </td><td> instance de DOMNode </td><td> l'élément parent de l'élément (None pour document) </td><td> L </td></tr>
<tr><td> text </td><td> chaine </td><td> le texte contenu dans l'élément </td><td> L/E </td></tr>
<tr><td> top </td><td> entier </td><td> la position de l'élément par rapport au bord supérieur du premier parent positionné (1) </td><td> L/E </td></tr>
<tr><td> width </td><td> entier </td><td> largeur de l'élément en pixels (2) </td><td> L/E </td></tr>
</table>

## Les évènements

- Un ***événement (event***) est la survenue d’une action (clavier, souris) dont votre application a besoin d’être informée.
- Un ***gestionnaire d'événement (event handler)*** est une fonction de votre application qui a vocation a être appelée lorsqu’un certain événement se produira.

### Evénements souris

#### Les événements  relatifs à la souris

<table  style="font-size: small">
<tr><th> évènement</th><th></th>  </tr>
<tr><td> mouseenter </td> <td> la souris entre dans la zone couverte par l'élément, ou un de ses descendants </td></tr>
<tr><td> mouseleave </td> <td> la souris sort de la zone couverte par l'élément et par ses descendants </td></tr>
<tr><td> mouseover </td> <td> la souris entre dans la zone couverte par l'élément </td></tr>
<tr><td> mouseout </td> <td> la souris quitte la zone couverte par l'élément </td></tr>
<tr><td> mousemove </td> <td> la souris se déplace sur l'élément </td></tr>
<tr><td> mousedown </td> <td> appui sur le bouton gauche de la souris </td></tr>
<tr><td> mouseup </td> <td> relâchement du bouton gauche de la souris </td></tr>
<tr><td> click </td> <td> appui puis relâchement du bouton gauche de la souris </td></tr>
<tr><td> dblclick </td> <td> double clic </td></tr>
</table>

#### Attributs de l'objet DOMEvent

Pour les événements souris, l'instance de DOMEvent possède les attributs suivants

<table  style="font-size: small">
<tr><th> attributs </th><th></th></tr>
<tr><td> button </td><td> le numéro du bouton sur lequel on a appuyé </td><tr>
<tr><td> buttons </td><td> indique sur quels boutons de la souris on a appuyé pour déclencher l'événement.<br />  Chaque bouton sur lequel on peut appuyer est représenté par un entier donné (1 : bouton gauche, 2 : bouton droit, 4   : roue).<br  />Si on appuie sur plus d'un bouton, la valeur de buttons est combinée pour produire un nouveau nombre.<br />Par exemple, si on appuie sur le bouton droit (2) et sur la roue (4), la valeur est égale à 2+4, soit 6 </td><tr>
<tr><td> x </td><td> la position de la souris par rapport au bord gauche de la fenêtre (en pixels) </td><tr>
<tr><td> y </td><td> la position de la souris par rapport au bord haut de la fenêtre (en pixels) </td><tr>
<tr><td> clientX </td><td> la position de la souris par rapport au bord gauche de l'élément dans lequel la souris se trouve au moment du clic (en pixels) </td><tr>
<tr><td> clientY </td><td> la position de la souris par rapport au bord haut de l'élément dans lequel la souris se trouve au moment du clic (en pixels) </td><tr>
<tr><td> screenX </td><td> comme x </td><tr>
<tr><td> screenY </td><td> comme y </td><tr>
</table>

### Evénements clavier

#### Les événements relatifs au clavier

<table  style="font-size: small">
<tr><th>évènement</th><th></th></tr>
<tr><td> input </td><td> déclenché quand la valeur d'un élément &lsaquo;input&rsaquo; ou &lsaquo;textarea&rsaquo; est modifié, ou quand le contenu d'un élément contenteditable est modifié</td></tr>
<tr><td> keydown </td><td> appui sur une touche quelconque du clavier</td></tr>
<tr><td> keypress </td><td> appui sur une touche du clavier qui produit un caractère.<br />Par exemple, quand on entre Ctrl+C au clavier, l'événement keypress n'est déclenché qu'au moment où on appuie sur C, alors que keydown est déclenché dès l'appui sur Ctrl</td></tr>
<tr><td> keyup </td><td> relâchement d'une touche enfoncée</td></tr>
</table>

####  Attributs de l'objet DOMEvent

L'instance de ***DOMEvent*** possède les attributs suivants

<table style="font-size: small">
<tr><td>***altKey***</td><td> booléen, indique si la touche Alt (ou Option sur Mac) était enfoncée quand l'événement clavier a été déclenché<br />
Cet attribut n'est pas disponible pour l'événement *input*<br />
Il est normalement utilisé avec *keypress*, pour pouvoir tester si on a entré Alt+&lsaquo;key&rsaquo; ou seulement &lsaquo;key&rsaquo;</td><tr>
<tr><td>***charCode***</td><td> Le numéro de référence Unicode pour la touche<br />
Cet attribut n'est utilisable que pour l'événement keypress</td><tr>
<tr><td>***ctrlKey***</td>
    <td> booléen, indique si la touche *Ctrl* était enfoncée quand l'événement clavier a été déclenché<br />

    Cet attribut n'est pas disponible pour l'événement *input* 

    Il est normalement utilisé avec *keypress*, pour pouvoir tester si on a entré Ctrl+&lsaquo;key&rsaquo; ou seulement &lsaquo;key&rsaquo; 
<tr><td>***shiftKey***</td><td>
    booléen, indique si la touche Majuscule était enfoncée quand l'événement clavier a été déclenché<br />
    Cet attribut n'est pas disponible pour l'événement *input* <br />
    Il est normalement utilisé avec keypress, pour pouvoir tester si on a entré Shift+&lsaquo;key&rsaquo; ou seulement &lsaquo;key&rsaquo; </td><tr>
 <tr><td>***which***</td><td>
    un code numérique dépendant du système et de l'implémentation, caractérise la clé enfoncée <br />
    noter que le résultat n'est pas le même selon qu'on gère les événements *keydown, keyup* et *keypress* </td><tr>
</table>
 
### Les événement focus:

<table style="font-size: small">
<tr><td>***blur***</td><td>un élément a perdu le focus</td></tr>
<tr><td>***focus***</td><td>un élément a reçu le focus</td></tr>
</table>


Il existe aussi l'événement ***[glisser-déposer](https://www.brython.info/static_doc/fr/drag_events.html)***

## Exercices



### Calculer votre IMC

Nous traiterons des échanges client serveur dans une prochaine leçon avec flask pour programmer en python coté serveur.

On imagine une page html qui vous demande votre *masse*, votre *taille* en cm dans deux champs de données et lorsqu'on click sur bouton on obtienne notre *icm*.  
Dans un deuxième temps vérifiez que les données des champs *taille* et *masse* soient des nombres positifs.

Je rappelle que la formule pour calculer l'IMC est $$IMC=\dfrac{\text{Masse en kg}}{\text{Taille en m}}$$

Voici la page html, je vous laisse compléter le script

```html
<html>
<head>
<meta charset="utf-8" />
<script type="text/javascript" src="brython.js"></script>
<title>calcule IMC</title>
</head>
<body onLoad="brython()">
<h1>calcule IMC</h1>
<div>
<p>Entrer votre poids en kg: <input type="text" id="masse" /></p>
<p>Entrer votre taille en cm: <input type="text" id="taille" /></p>
<button id="calculer">calculer</button>
</div>
<div>réponse: <label id="reponse"></label></div>

<script type="text/python">
from browser import document
..................
.................
document["calculer"].bind("click", calculImc)
```
</script>
</body>
</html>

### Afficher les dimensions de l'ecran

Créer une page html qui affiche les dimensions de l'ecran.


```python
html="<div id='date'></div>"
```


```python
%%brython -h html -c zone
from browser import document
from browser  import timer
import time

def getClock():
	localtime = time.localtime(time.time())
	document["date"].text=time.strftime('Nous somme le %d %m %y, il est %H:%M:%S ',localtime)
timer = timer.set_interval(getClock,1000)
```


  <script id="874923" type="text/python">
from browser import document
from browser  import timer
import time

def getClock():
	localtime = time.localtime(time.time())
	document["date"].text=time.strftime('Nous somme le %d %m %y, il est %H:%M:%S ',localtime)
timer = timer.set_interval(getClock,1000)
  </script>
  <div id="zone"><div id='date'></div></div>
  <script type="text/javascript">brython({debug:1, static_stdlib_import: false, ipy_id: ["874923"]});</script>




<div id="titre">Programmation web avec Brython</div>

Cours issue de la [documentation Brython](https://www.brython.info/static_doc/fr/intro.html)  
Pour débogguer vos script avec le navigateur:
- firefox: Outils-> Développement web->consol web (Ctrl+Maj+k))

## Introduction



### Qui fait quoi?


lorsque vous saisissez une URL dans votre navigateur, que vous validez cette dernière, votre navigateur envoie une "requête" au serveur concerné afin qu'il nous renvoie une page web.  
Tout d'abord, on nomme vulgairement l'échange de données entre votre navigateur et le serveur qui fournit les pages web un échange ***client / serveur***.   
Le client représente votre navigateur.

<object type="image/svg+xml" data="https://github.com/debimax/cours-debimax/raw/master/images/client-serveur.svg" width="400">
    Le navigateur ne peut lire ce kiwi
</object>


<center>
<img width=400px src="https://github.com/debimax/cours-debimax/raw/master/images/client-serveur.png" alt="client serveur sur internet"   />
</center>


Normalement coté client c'est javascript qui est utilisé pour programmer.  
Il existe des lirairies pour facilité la programmation ***javascript*** comme ***jquery, p5js***.  N'ayant pas le temps de vous apprendre à coder en javascript nous utiliseront ***Brython***  qui nous permet de coder en python. La libraire Brython transforme ce code python en javascript (compréhensible par votre navigateur).

Vous devez bien faire faire la différence le programme exécuté coté client (javascript ou brython) et le programme exécuté coté serveur (php ou python).

En fait le code python est convertit en javascript qui compréhensible pour votre navigateur internet


```python
from IPython.core.display import SVG
SVG(url='https://github.com/debimax/cours-debimax/raw/master/images/client-serveur.svg')
```




    <IPython.core.display.SVG object>



### Que peut on faire avec Brython?


On peut faire la même chose qu'avec javascript

- Accéder aux DOM (Document Object Model)
- Modifier le DOM
- Réagir aux évènements de la souris, du clavier etc...


### Préparation des fichiers  


Il est necessaire d'utiliser d'utilser deux fichiers ***brython.js***  et ***brython_stdlib.js***  
Comme internet au lycée  n'est pas toujours fiable je vous conseille de télécharger ces deux fichiers

- Dans votre dossier créez le dossier ***isn-internet*** puis ***isn-internet/static***
- Téléchargez puis déposer dans le dossier *isn-internet/static* le fichier [brython.js](https://www.brython.info/src/brython.js).
- Téléchargez puis déposer dans le dossier *isn-internet/static* le fichier [brython_stdlib.js](https://www.brython.info/src/brython_stdlib.js)

### Exemple 1 (Hello World)

Créez le fichier ***exo1.html*** dans le dossier *isn-internet* avec votre éditeur de texte (pyzo, geany, idle ...)

```html
<html>
<head>
  <meta charset="utf-8" />
  <script type="text/javascript" src="static/brython.js"></script>
  <script type="text/javascript" src="static/brython_stdlib.js"></script>
  <title>Clock</title>
</head>
<body onLoad="brython()">
<h1>Exemple 1:</h1>
<div>
  <button id="bouton" type="button">Cliquez!</button>
  <label id='hi'>Un texte</label>
</div> 
<script type="text/python">
from browser import document
def affiche(ev):
    document['hi'].text="Hello Word"

document["bouton"].bind("click", affiche)
</script>
</body>
</html>
```

Il ne reste plus qu'à ouvrir ce fichier avec votre navigateur préféré.

Un peu d'explication pour le code ci-dessus.

***document["bouton"]***  est l'élément qui a pour ***id*** "bouton", donc on définit l'évènement click sur le button d'id "bouton" qui déclenche la fonction ***affiche***.  
Remarque: C'est la même syntaxe que pour tkinter.

Il est aussi possible d'envoyer des arguments à la fonction ***affiche***  en utilisant les fonctions ***lamda***.  
Modifer le code comme ci-dessous.

```python
<script>
from browser import document
def affiche(message,ev):
    document['hi'].text=message

message="Hello World"
document["bouton"].bind("click", lambda ev: affiche(message,ev))
</script>
```

La syntaxe avec la fonction lambda sera identique avec tkinter.

Modifions encore le code pour cette fois intéragir avec le clavier. La touche ***Esc*** a pour keycode 27.

```python
<div>
  <label id='hi'>Appyer sur la touche esc</label>
</div> 
<script type="text/python">
from browser import document
def affiche(message,ev):
    if int(ev.keyCode)==27 :
        document['hi'].text=message

document.bind('keydown', lambda ev :  affiche("Hello World", ev))
document.bind('keyup',   lambda ev :  affiche("Appyer sur la touche esc", ev))
</script>
```

On associe à l'évènement 'keydown' (appuyer sur une touche) le déclenchement de la fonction affiche avec les paramètre "Hello World" et ***ev*** où ev signifie ***event (évènement)***.

### Exemple 2 (Afficher l'heure)

C'est le module ***time*** qui gère l'heure en python. Créer le fichier ***exo2.html***.

```html
<html>
<head>
<meta charset="utf-8" />
<script type="text/javascript" src="brython.js"></script>
<script type="text/javascript" src="brython_stdlib.js"></script>
<title>Clock</title>
</head>
<body onLoad="brython()">
<h1>exemple 2: Obtenir la date et l'heure</h1>
<div id="date"></div>
<script type="text/python">
from browser import document
import time
document["date"].text=time.strftime('Nous somme le %d %m %y, il est %H:%M:%S ',localtime)
</script>
</body>
</html>
```

Vous voyez tout de suite ce qui ne va pas.  Il faudrait rafraichir toutes les secondes.  
On modifie donc le script comme ci-dessous.  

```python
<script type="text/python">
from browser import document as doc
from browser  import timer  #https://www.brython.info/static_doc/en/timer.html
import time
def getClock():
	localtime = time.localtime(time.time())
	document["date"].text=time.strftime('Nous somme le %d %m %y, il est %H:%M:%S ',localtime)

timer = timer.set_interval(getClock,1000)
</script>
```

## Accéder aux éléments de la page



Pour accéder à un élément, on peut utiliser plusieurs méthodes. La plus courante est de se servir de son identifiant, c'est-à-dire de son attribut *id* : si on a une zone de saisie définie par  
```html
    <input id="data">
```

on peut obtenir une référence à ce champ par  
```python
    from browser import document
    data = document["data"]
```

L'objet document du module browser référence le document HTML. Il se comporte comme un dictionnaire dont les clés sont les identifiants des éléments de la page. Si aucun élément ne possède l'identifiant spécifié, le programme déclenche une exception *KeyError*

On peut aussi récupérer tous les éléments d'un certain type, par exemple tous les liens hypertexte (balise HTML A), en utilisant la syntaxe  

```python
from browser import html
links = document[html.A]
```

Enfin, tous les éléments de la page possèdent une méthode ***get()*** qui permet de rechercher des éléments de plusieurs façons :

``` elt.get(name=N)``` retourne une liste avec tous les éléments descendant de *elt* dont l'attribut name est égal à N.  
``` elt.get(selector=S)``` retourne une liste avec tous les élements descendant de *elt* dont le sélecteur CSS correspond à S.  

Quelques exemples :

```python
document.get(selector='.foo')       # éléments avec la classe "foo"
document.get(selector='form')       # liste des balises "<form>"
document.get(selector='H1.bar')     # balises H1 avec la classe "bar"
document.get(selector='#container') # liste avec l'élément dont l'id vaut "container", similaire à [document["container"]]
document.get(selector='a[title]')   # balises A avec un attribut "title"
```


## Attributs et méthodes des éléments

Les éléments de la page possèdent des attributs et des méthodes qui dépendent du type de l'objet ;  
On peut les trouver sur de nombreux [sites Internet](https://developer.mozilla.org/fr/docs/Web/HTML/Attributs).

Comme le nom des attributs peut être différent d'un navigateur à l'autre, Brython définit des attributs supplémentaires qui fonctionnent dans tous les cas :

<table style="font-size: small">
<tr><th> Nom </th><th>Type </th><th> Description</th><th> L = lecture seule<br />
L/E = lecture + écriture</th></tr>
<tr><td> abs_left </td><td> entier </td><td> position de l'élément par rapport au bord gauche de l'écran </td><td> L </td></tr>
<tr><td> abs_top </td><td> entier </td><td> position de l'élément par rapport au bord supérieur de l'écran </td><td> L </td></tr>
<tr><td> children </td><td> liste </td><td> les éléments "descendants" de l'élément </td><td> L </td></tr>
<tr><td> class_name </td><td> chaine </td><td> le nom de la classe de l'élément (attribut class de la balise) </td><td> L/E</td></tr>
<tr><td> clear </td><td> méthode </td><td> elt.clear() supprime tous les descendants de l'élément </td><td> - </td></tr>
<tr><td> height </td><td> entier </td><td> hauteur de l'élément en pixels (2) </td><td> L/E </td></tr>
<tr><td> html </td><td> chaine </td><td> le code HTML contenu dans l'élément </td><td> L/E </td></tr>
<tr><td> inside </td><td> méthode </td><td> elt.inside(autre) teste si elt est contenu dans l'élément autre </td><td> - </td></tr>
<tr><td> left </td><td> entier </td><td> la position de l'élément par rapport au bord gauche du premier parent positionné (1) </td><td> L/E </td></tr>
<tr><td> parent </td><td> instance de DOMNode </td><td> l'élément parent de l'élément (None pour document) </td><td> L </td></tr>
<tr><td> text </td><td> chaine </td><td> le texte contenu dans l'élément </td><td> L/E </td></tr>
<tr><td> top </td><td> entier </td><td> la position de l'élément par rapport au bord supérieur du premier parent positionné (1) </td><td> L/E </td></tr>
<tr><td> width </td><td> entier </td><td> largeur de l'élément en pixels (2) </td><td> L/E </td></tr>
</table>

## Les évènements

- Un ***événement (event***) est la survenue d’une action (clavier, souris) dont votre application a besoin d’être informée.
- Un ***gestionnaire d'événement (event handler)*** est une fonction de votre application qui a vocation a être appelée lorsqu’un certain événement se produira.

### Evénements souris

#### Les événements  relatifs à la souris

<table  style="font-size: small">
<tr><th> évènement</th><th></th>  </tr>
<tr><td> mouseenter </td> <td> la souris entre dans la zone couverte par l'élément, ou un de ses descendants </td></tr>
<tr><td> mouseleave </td> <td> la souris sort de la zone couverte par l'élément et par ses descendants </td></tr>
<tr><td> mouseover </td> <td> la souris entre dans la zone couverte par l'élément </td></tr>
<tr><td> mouseout </td> <td> la souris quitte la zone couverte par l'élément </td></tr>
<tr><td> mousemove </td> <td> la souris se déplace sur l'élément </td></tr>
<tr><td> mousedown </td> <td> appui sur le bouton gauche de la souris </td></tr>
<tr><td> mouseup </td> <td> relâchement du bouton gauche de la souris </td></tr>
<tr><td> click </td> <td> appui puis relâchement du bouton gauche de la souris </td></tr>
<tr><td> dblclick </td> <td> double clic </td></tr>
</table>

#### Attributs de l'objet DOMEvent

Pour les événements souris, l'instance de DOMEvent possède les attributs suivants

<table  style="font-size: small">
<tr><th> attributs </th><th></th></tr>
<tr><td> button </td><td> le numéro du bouton sur lequel on a appuyé </td><tr>
<tr><td> buttons </td><td> indique sur quels boutons de la souris on a appuyé pour déclencher l'événement.<br />  Chaque bouton sur lequel on peut appuyer est représenté par un entier donné (1 : bouton gauche, 2 : bouton droit, 4   : roue).<br  />Si on appuie sur plus d'un bouton, la valeur de buttons est combinée pour produire un nouveau nombre.<br />Par exemple, si on appuie sur le bouton droit (2) et sur la roue (4), la valeur est égale à 2+4, soit 6 </td><tr>
<tr><td> x </td><td> la position de la souris par rapport au bord gauche de la fenêtre (en pixels) </td><tr>
<tr><td> y </td><td> la position de la souris par rapport au bord haut de la fenêtre (en pixels) </td><tr>
<tr><td> clientX </td><td> la position de la souris par rapport au bord gauche de l'élément dans lequel la souris se trouve au moment du clic (en pixels) </td><tr>
<tr><td> clientY </td><td> la position de la souris par rapport au bord haut de l'élément dans lequel la souris se trouve au moment du clic (en pixels) </td><tr>
<tr><td> screenX </td><td> comme x </td><tr>
<tr><td> screenY </td><td> comme y </td><tr>
</table>

### Evénements clavier

#### Les événements relatifs au clavier

<table  style="font-size: small">
<tr><th>évènement</th><th></th></tr>
<tr><td> input </td><td> déclenché quand la valeur d'un élément &lsaquo;input&rsaquo; ou &lsaquo;textarea&rsaquo; est modifié, ou quand le contenu d'un élément contenteditable est modifié</td></tr>
<tr><td> keydown </td><td> appui sur une touche quelconque du clavier</td></tr>
<tr><td> keypress </td><td> appui sur une touche du clavier qui produit un caractère.<br />Par exemple, quand on entre Ctrl+C au clavier, l'événement keypress n'est déclenché qu'au moment où on appuie sur C, alors que keydown est déclenché dès l'appui sur Ctrl</td></tr>
<tr><td> keyup </td><td> relâchement d'une touche enfoncée</td></tr>
</table>

####  Attributs de l'objet DOMEvent

L'instance de ***DOMEvent*** possède les attributs suivants

<table style="font-size: small">
<tr><td>***altKey***</td><td> booléen, indique si la touche Alt (ou Option sur Mac) était enfoncée quand l'événement clavier a été déclenché<br />
Cet attribut n'est pas disponible pour l'événement *input*<br />
Il est normalement utilisé avec *keypress*, pour pouvoir tester si on a entré Alt+&lsaquo;key&rsaquo; ou seulement &lsaquo;key&rsaquo;</td><tr>
<tr><td>***charCode***</td><td> Le numéro de référence Unicode pour la touche<br />
Cet attribut n'est utilisable que pour l'événement keypress</td><tr>
<tr><td>***ctrlKey***</td>
    <td> booléen, indique si la touche *Ctrl* était enfoncée quand l'événement clavier a été déclenché<br />

    Cet attribut n'est pas disponible pour l'événement *input* 

    Il est normalement utilisé avec *keypress*, pour pouvoir tester si on a entré Ctrl+&lsaquo;key&rsaquo; ou seulement &lsaquo;key&rsaquo; 
<tr><td>***shiftKey***</td><td>
    booléen, indique si la touche Majuscule était enfoncée quand l'événement clavier a été déclenché<br />
    Cet attribut n'est pas disponible pour l'événement *input* <br />
    Il est normalement utilisé avec keypress, pour pouvoir tester si on a entré Shift+&lsaquo;key&rsaquo; ou seulement &lsaquo;key&rsaquo; </td><tr>
 <tr><td>***which***</td><td>
    un code numérique dépendant du système et de l'implémentation, caractérise la clé enfoncée <br />
    noter que le résultat n'est pas le même selon qu'on gère les événements *keydown, keyup* et *keypress* </td><tr>
</table>
 
### Les événement focus:

<table style="font-size: small">
<tr><td>***blur***</td><td>un élément a perdu le focus</td></tr>
<tr><td>***focus***</td><td>un élément a reçu le focus</td></tr>
</table>


Il existe aussi l'événement ***[glisser-déposer](https://www.brython.info/static_doc/fr/drag_events.html)***

## Exercices



### Calculer votre IMC

Nous traiterons des échanges client serveur dans une prochaine leçon avec flask pour programmer en python coté serveur.

On imagine une page html qui vous demande votre *masse*, votre *taille* en cm dans deux champs de données et lorsqu'on click sur bouton on obtienne notre *icm*.  
Dans un deuxième temps vérifiez que les données des champs *taille* et *masse* soient des nombres positifs.

Je rappelle que la formule pour calculer l'IMC est $$IMC=\dfrac{\text{Masse en kg}}{\text{Taille en m}}$$

Voici la page html, je vous laisse compléter le script

```html
<html>
<head>
<meta charset="utf-8" />
<script type="text/javascript" src="brython.js"></script>
<title>calcule IMC</title>
</head>
<body onLoad="brython()">
<h1>calcule IMC</h1>
<div>
<p>Entrer votre poids en kg: <input type="text" id="masse" /></p>
<p>Entrer votre taille en cm: <input type="text" id="taille" /></p>
<button id="calculer">calculer</button>
</div>
<div>réponse: <label id="reponse"></label></div>

<script type="text/python">
from browser import document
..................
.................
document["calculer"].bind("click", calculImc)
```
</script>
</body>
</html>

### Afficher les dimensions de l'ecran

Créer une page html qui affiche les dimensions de l'ecran.
