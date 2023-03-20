---
title: "Computer Vision for the Humanities: An Introduction to Deep Learning for Image Classification (Part 1)"
collection: lessons
layout: lesson
slug: computer-vision-deep-learning-pt1
date: 2022-08-17
authors:
- Daniel van Strien
- Kaspar Beelen
- Melvin Wevers
- Thomas Smits
- Katherine McDonough
reviewers:
- Michael Black
- Catherine DeRose
editors:
- Nabeel Siddiqui
- Alex Wermer-Colan
review-ticket: https://github.com/programminghistorian/ph-submissions/issues/342
difficulty: 3
activity: analyzing
topics: [python, machine-learning]
abstract: This is the first of a two-part lesson introducing deep learning based computer vision methods for humanities research. Using a dataset of historical newspaper advertisements and the fastai Python library, the lesson walks through the pipeline of training a computer vision model to perform image classification. 
mathjax: true
avatar_alt: An illustration of a camera on top of a wooden stand with a dark cloth.
next: computer-vision-deep-learning-pt2
series_total: 2 lessons
sequence: 1
doi: 10.46430/phen0101

---

{% include toc.html %}




# Introduction

Si la plupart des historiens s'accordent à dire que la **représentation** (moderne) est façonnée par les médias multimodaux &mdash; c'est-à-dire les médias, tels que la presse, la télévision ou l'internet, qui combinent plusieurs modes &mdash; les domaines des humanités numériques et de l'histoire numérique restent dominés par les médias textuels et la grande variété de méthodes disponibles pour leur analyse[^1]. Les historiens modernes ont souvent été accusés de négliger les formes de représentation non textuelles, et les humanistes numériques, en particulier, se sont consacrés à l'exploration des sources textuelles. Beaucoup ont utilisé l'OCR ([Optical Character Recognition](https://perma.cc/3VJ2-6RWL)), une technologie qui rend les textes numérisés lisibles par machine, ainsi que des techniques issues du domaine du [traitement automatique du langage naturel](https://perma.cc/6ZBJ-ZYY8) (TAL), pour analyser le contenu et le contexte du langage dans des vastes documents. La combinaison de ces deux éléments a donné naissance à l'innovation méthodologique centrale du domaine de l'histoire numérique : la capacité de «&#xA0;lire à distance&#xA0;» de grands corpus et de découvrir des modèles à grande échelle[^2].

Au cours des dix dernières années, le domaine de la vision par ordinateur, qui cherche à obtenir une compréhension de haut niveau des images à l'aide de techniques informatiques, a connu une innovation rapide. Ainsi, les modèles de vision par ordinateur peuvent localiser et identifier des personnes, des animaux et des milliers d'objets inclus dans des images, ce avec une grande précision. Cette avancée technologique promet de faire pour la reconnaissance d'images ce que la combinaison des techniques OCR/NLP a fait pour les textes. En d'autres termes, la vision par ordinateur ouvre à l'analyse à grande échelle une partie des archives numériques qui est restée pratiquement inexplorée : les millions d'images contenues dans les livres, journaux, périodiques et documents historiques numérisés. Par conséquent, les historiens seront désormais en mesure d'explorer le «&#xA0;côté visuel du tournant numérique dans la recherche historique&#xA0;» [^3].

Cette leçon en deux parties fournit des exemples de la manière dont les techniques de vision par ordinateur peuvent être appliquées pour analyser de nouvelle façon de grands corpus visuels historiques et comment construire des modèles de vision par ordinateur personnalisés. Outre l'identification du contenu des images et leur classification par catégorie - deux tâches axées sur les caractéristiques visuelles - les techniques de vision par ordinateur peuvent également être utilisées pour déterminer les (dis)similitudes stylistiques entre les images.

Il convient toutefois de noter que les techniques de vision par ordinateur posent aux historiens un ensemble de défis théoriques et méthodologiques. Tout d'abord, toute application des techniques de vision par ordinateur aux corpus historiques doit partir d'une question historique soigneusement formulée et, par conséquent, inclure une discussion sur **l'échelle** de l'analyse. En bref : pourquoi est-il important de répondre à la question et pourquoi les techniques de vision par ordinateur sont-elles nécessaires pour y répondre ?

Deuxièmement, à la suite des discussions dans le domaine de l'éthique de l'apprentissage automatique[^4],[^5], qui cherchent à aborder la question des biais dans l'apprentissage automatique (ML, *machine learning*), les historiens devraient être conscients du fait que les techniques de vision par ordinateur éclairent certaines parties des corpus visuels, mais peuvent négliger, mal identifier, mal classer ou même laisser dans l'ombre d'autres parties. En tant qu'historiens, nous sommes depuis longtemps conscients que nous regardons le passé à partir de notre propre époque, et par conséquent, toute application des techniques de vision par ordinateur devrait inclure une discussion sur un éventuel «&#xA0;biais historique&#xA0;». Comme (la plupart) des modèles de vision par ordinateur sont entraînés sur des données contemporaines, nous courons le risque de projeter les biais temporels de ces données sur les archives historiques. Bien qu'il ne soit pas possible, dans le cadre de cette leçon en deux parties, d'explorer pleinement la question du biais, il convient de la garder à l'esprit.


## Objectifs de la leçon 

Cette leçon en deux parties a pour but de :

- Fournir une introduction aux méthodes de vision par ordinateur basées sur l'[apprentissage profond](https://perma.cc/55WW-NY4U) pour la recherche en sciences humaines. L'apprentissage profond est une branche de l'apprentissage automatique (nous en parlerons plus en détail dans les leçons).
- Donnez un aperçu des étapes de l'entraînement d'un modèle d'apprentissage profond.
- Discuter de certaines considérations spécifiques concernant l'utilisation de l'apprentissage profond/la vision par ordinateur pour la recherche en sciences humaines.
- vous aider à décider si l'apprentissage profond peut constituer un outil répondant à vos besoins.

Cette leçon n'a pas pour but de :

- Reproduire d'autres introductions plus générales à l'apprentissage profond, bien qu'elle en partage _une partie_ des objectifs pédagogiques.
- Couvrir tous les détails de l'apprentissage profond et de la vision par ordinateur ; il s'agit de vastes sujets, qu'il n'est pas possible de traiter en profondeur ici.



## Compétences préalables suggérées

- La connaissance de [Python](https://perma.cc/HPP5-A3VR) ou d'un autre langage de programmation sera importante pour suivre ces leçons. Plus précisément, il serait bénéfique de comprendre comment utiliser les variables, l'indexation, et d'avoir une certaine familiarité avec l'utilisation de méthodes provenant de bibliothèques externes.
- Nous supposons que vous êtes familier avec les [carnets Jupyter (*notebooks*)](https://perma.cc/4FVJ-MUZ2), c'est-à-dire que vous savez comment exécuter le code qu'ils contiennent. Si vous n'êtes pas familier avec les carnets, vous trouverez en notre [Introduction aux carnets Jupyter](/fr/lecons/introduction-aux-carnets-jupyter-notebooks) une ressource utile en préalable à la présente leçon.
- Ce tutoriel fait appel à des bibliothèques Python externes, mais il n'est pas nécessaire d'en avoir une connaissance préalable car les étapes de l'utilisation de ces bibliothèques seront expliquées au fur et à mesure de leur utilisation.


## Configuration de la leçon

Nous vous suggérons d'aborder cette leçon en deux parties en deux temps :

- Tout d'abord, lisez les informations de cette page, pour vous familiariser avec les questions conceptuelles clés et le flux de travail global pour l'entraînement d'un modèle de vision par ordinateur.
- Ensuite, exécutez le code proposé dans une version carnet Jupyter de chaque leçon sur Kaggle (voir ci-dessous).

Dans cette leçon en deux parties, nous allons utiliser une approche de la vision par ordinateur basée sur l'apprentissage profond. Le processus de mise en place d'un environnement pour l'apprentissage profond est devenu plus facile mais peut encore être complexe. Nous avons essayé de conserver ce processus de configuration aussi simple que possible, et nous proposons un cheminement le plus direct possible pour commencer à exécuter le code de la leçon.

### Carnets

Cette leçon en deux parties est disponible sous forme de carnets (*notebooks*) Jupyter. Nous vous recommandons d'exécuter le code de cette leçon à l'aide de ces carnets compagnons, ce qui convient parfaitement à la nature exploratoire que nous allons mettre en œuvre.

### Exécuter les carnets 

Vous pouvez utiliser les carnets de différentes manières. Nous vous encourageons vivement à utiliser les instructions de configuration dans le cloud plutôt que de configurer un environnement local. Et ce, pour plusieurs raisons :

- Le processus de configuration de l'apprentissage profond dans un environnement cloud peut être beaucoup plus simple que la configuration locale. De nombreux ordinateurs portables et personnels ne disposent pas de ce type de matériel et le processus d'installation des pilotes logiciels nécessaires peut prendre beaucoup de temps.
- Le code de cette leçon s'exécutera beaucoup plus rapidement si un type spécifique de carte graphique ([GPU](https://perma.cc/PW3J-BVHZ)) est disponible. Cela permettra une approche interactive du travail avec les modèles et leurs résultats.
- Les GPU sont plus efficaces sur le plan [énergétique](https://doi.org/10.1109/BDCloud-SocialCom-SustainCom.2016.76) pour certaines tâches comparés aux unités centrales de traitement ([CPU](https://perma.cc/2P2P-EL4V)), y compris pour le type de tâches sur lesquelles nous allons travailler dans ces leçons.

### Kaggle

[Kaggle](https://perma.cc/9H6M-PDB6) est un site Web qui héberge des jeux de données, organise des concours de science des données et fournit des ressources pédagogique. Kaggle héberge également des carnets Jupyter, y compris avec accès à des GPU.

Pour exécuter le code de la leçon sur Kaggle, vous devrez :

- Créer un compte sur [Kaggle](https://www.kaggle.com) (vous devrez fournir un numéro de téléphone), ou vous connecter à votre compte existant.
- Aller sur [https://www.kaggle.com/code/davanstrien/progamming-historian-deep-learning-pt1](https://www.kaggle.com/code/davanstrien/progamming-historian-deep-learning-pt1). Les données utilisées dans cette leçon sont fournies avec ces carnets.
- Cliquez sur le bouton "Copy&Edit" pour créer une copie du carnet.
- Réglez l'option "Accelerator" sur "GPU" ; vous trouverez cette option dans l'onglet "Settings".

{% include figure.html filename="kaggle_gpu.png" alt="Screenshot showing the Accelerator option set to GPU" caption="The Kaggle notebooks settings menu" %}

- L'interface des carnets Kaggle devrait vous être familière si vous avez déjà utilisé des carnets Jupyter. Pour exécuter une cellule contenant du code, cliquez sur le bouton en forme de triangle pointant vers la droite ou, si la cellule est sélectionnée, utilisez "Maj + Entrée".
- N'oubliez pas de fermer votre session une fois que vous avez fini de travailler avec les notebooks. Vous pouvez le faire en accédant au menu déroulant "Run" en haut d'un carnet Kaggle.

Kaggle dispose d'une [documentation sur l'utilisation de ses carnets](https://perma.cc/YF2N-C94Q) ainsi que de conseils sur [l'utilisation efficace des GPU](https://perma.cc/V8CZ-WZQ4).


### Travailler en local 

Si vous ne souhaitez pas utiliser une configuration dans le cloud, vous pouvez suivre les [instructions de configuration locale de cette leçon](https://perma.cc/7WC9-VAC2).



# Courte introduction à l'apprentissage machine

Avant de passer au premier exemple pratique, il peut être utile de rappeler brièvement ce que l'on entend par «&#xA0;apprentissage automatique&#xA0;». [L'apprentissage automatique](https://perma.cc/V3DM-E8SF) vise à permettre aux ordinateurs d'«&#xA0;apprendre&#xA0;» à partir de données au lieu d'être explicitement programmés pour faire quelque chose. Par exemple, si nous voulons filtrer les [spams](https://perma.cc/F7ZW-52YR), nous pouvons adopter plusieurs approches différentes. L'une d'elles consiste à lire des exemples de courriels «&#xA0;spam&#xA0;» et «&#xA0;non spam&#xA0;» pour voir si nous pouvons identifier des [signaux](https://perma.cc/NE75-DHCX) indiquant qu'un courriel est un spam. À cet effet, nous pourrions par exemple trouver des mots-clés qui, selon nous, sont susceptibles d'indiquer un spam. Nous pourrions ensuite écrire un programme qui ferait quelque chose comme ceci pour chaque courriel reçu :

```
count number spam_words in email:
    if number spam_words >= 10:
        email = spam
```

En revanche, une approche par apprentissage automatique entraînerait un [algorithme](https://perma.cc/PFX7-WB6J) d'apprentissage automatique  sur des exemples étiquetés de courriels qui sont des «&#xA0;spam&#xA0;» et «&#xA0;non spam&#xA0;». Cet algorithme, après une exposition répétée aux exemples, «&#xA0;apprendrait&#xA0;» des modèles qui indiquent le type de courriels. Il s'agit d'un exemple [d'«&#xA0;apprentissage supervisé&#xA0;»](https://perma.cc/TFY2-YT7A), un processus dans lequel un algorithme est exposé à des données étiquetées, et c'est ce sur quoi ce tutoriel va se concentrer. Il existe différentes approches pour gérer ce processus d'apprentissage, dont certaines seront abordées dans cette leçon. Un autre type d'apprentissage automatique qui ne nécessite pas d'exemples étiquetés est [l'«&#xA0;apprentissage non supervisé&#xA0;»](https://perma.cc/S7QE-8D3T).

```
ajouter un exemple computer vision ? 
```

L'apprentissage automatique présente des avantages et des inconvénients. Dans notre exemple de courrier électronique, il est notamment avantageux de ne pas avoir à identifier manuellement ce qui indique si un courrier électronique est un spam ou non. Cela est particulièrement utile lorsque les signaux peuvent être subtils ou difficiles à détecter. Si les caractéristiques des courriers électroniques non sollicités devaient changer à l'avenir, vous n'auriez pas besoin de réécrire l'ensemble de votre programme, mais vous pourriez réentraîner votre modèle avec de nouveaux exemples. Parmi les inconvénients, citons la nécessité de disposer d'exemples étiquetés, dont la création peut prendre beaucoup de temps. L'une des principales limites des algorithmes d'apprentissage automatique est qu'il peut être difficile de comprendre comment ils ont pris une décision, c'est-à-dire pourquoi un courriel a été étiqueté comme spam ou non. Les implications de ce phénomène varient en fonction du «&#xA0;pouvoir&#xA0;» accordé à l'algorithme dans un système. À titre d'exemple, l'impact négatif potentiel d'un algorithme qui prend des décisions automatisées concernant une demande de prêt est probablement beaucoup plus élevé que celui d'un algorithme qui fait des recommandations de contenus sur un service de streaming. 


## Entraînement d'un modèle de classification d'images

Maintenant que nous avons une compréhension générale de l'apprentissage automatique, nous allons passer à notre premier exemple d'utilisation de l'apprentissage profond pour la vision par ordinateur. Dans cet exemple, nous allons construire un classificateur d'images qui affecte les images à l'une des deux catégories ciblées, en fonction de données d'entraînement étiquetées.

### Les données : classer des images de presse ancienne 

Dans cette leçon, nous allons travailler avec un jeu de données dérivés du projet ["Newspaper Navigator"](https://perma.cc/8U7H-9NUS). Ce jeu de données est constitué du contenu visuel extrait de 16 358 041 pages de journaux historiques numérisés provenant de la [bibliothèque du Congrès](https://perma.cc/8YJ6-KKFS) [Chronicling America collection](https://perma.cc/P98H-P3WS).


Les données du projet Newspaper Navigator ont été créées à l'aide d'un modèle d'apprentissage profond pour la [détection d'objets](https://perma.cc/3DPY-P4A8). Ce modèle a été entraîné sur des pages annotées de Chronicling America datant de la Première Guerre mondiale, dont des annotations faites par les volontaires du projet de crowdsourcing [Beyond Words](https://perma.cc/ZBP2-US4H).[^6] Il a permis de classer ces images dans sept catégories, dont photographie et publicité.

Si vous souhaitez en savoir plus sur la façon dont cet ensemble de données a été créé, reportez-vous à l'[article](https://perma.cc/AU7E-WRU4) qui décrit ce travail, ou consulter le [dépôt GitHub](https://perma.cc/CFT7-RUJR) qui contient le code et les données d'entraînement. Nous ne reproduirons pas ce modèle. Nous allons plutôt utiliser la sortie de ce modèle comme point de départ pour créer les données que nous utilisons dans ce tutoriel. Puisque les données du Newspaper Navigator sont prédites par un modèle d'apprentissage automatique, elles contiendront des erreurs ; pour l'instant, nous accepterons que les données avec lesquelles nous travaillons soient imparfaites. Un certain degré d'imperfection et d'erreur est souvent le prix à payer si nous voulons travailler avec des collections "à l'échelle" en utilisant des méthodes informatiques.


### Classer des publicités

Pour notre première application de l'apprentissage profond, nous allons nous concentrer sur la classification d'images prédites comme des publicités (n'oubliez pas que ces données sont basées sur des prédictions et qu'elles contiendront quelques erreurs). Plus précisément, nous allons travailler avec un échantillon de publicités couvrant les années 1880-1885.


#### Détecter si les publicités contiennent des illustrations

Si vous regardez les images des publicités, vous verrez que certaines d'entre elles ne contiennent que du texte, tandis que d'autres comportent une sorte d'illustration.

Une publicité illustrée [^7] :

{% include figure.html filename="illustrated_ad.jpg" alt="Une image en noir et blanc d'une publicité de journal. L'image contient une illustration d'une boîte à café sur la gauche de l'annonce." caption="Un exemple de publicité illustrée" %}

Une annonce textuelle [^8]:

{% include figure.html filename="non_illustrated_ad.jpg" alt="Une image en noir et blanc d'une publicité de journal. La publicité ne contient que du texte et concerne une assurance incendie, avec l'adresse de la compagnie d'assurance." caption="Un exemple de publicité sans illustration" %}

Notre classificateur sera entraîné à prédire à quelle catégorie appartient une publicité. Nous pourrions l'utiliser pour automatiser la recherche de publicités comportant des illustrations en vue d'une analyse «&#xA0;manuelle&#xA0;» plus poussée. Nous pourrions également utiliser ce classificateur plus directement pour quantifier le nombre d'annonces contenant des illustrations au cours d'une année donnée et découvrir si ce nombre a évolué dans le temps, ainsi que l'influence d'autres facteurs tels que le lieu de publication. L'utilisation prévue de votre modèle aura un impact sur les étiquettes sur lesquelles vous choisissez de l'entraîner et sur la manière dont vous choisissez d'évaluer si un modèle est suffisamment performant. Nous approfondirons ces questions au fil de cette leçon en deux parties.

## Introduction à la bibliothèque fastai

[fastai](https://perma.cc/EG22-5FGB) est une bibliothèque Python pour l'apprentissage profond «&#xA0;qui fournit aux praticiens des composants de haut niveau pouvent rapidement et facilement fournir des résultats de pointe dans des domaines d'apprentissage profond standard, et fournit aux chercheurs des composants de bas niveau qui peuvent être assemblés et assortis pour construire de nouvelles approches&#xA0;» [^9]. La bibliothèque est développée par [fast.ai](https://perma.cc/FY9M-LJMG), un organisme de recherche qui vise à rendre l'apprentissage profond plus accessible. Outre la bibliothèque fastai, fast.ai organise également des cours gratuits et mène des recherches.

La bibliothèque fastai a été choisie pour ce tutoriel pour plusieurs raisons :

- Elle s'attache à rendre l'apprentissage profond accessible, notamment par la conception de l'API de la bibliothèque. 
- Elle facilite l'utilisation de techniques qui ne nécessitent pas une grande quantité de données ou de ressources de calcul.
- De nombreuses bonnes pratiques sont mises en œuvre par "défaut", ce qui permet d'obtenir de bons résultats.
- Il existe différents niveaux d'interaction avec la bibliothèque, en fonction de l'importance des modifications à apporter aux détails de niveau inférieur.
- La bibliothèque s'appuie sur [PyTorch](https://perma.cc/U5US-FLSV), ce qui facilite l'utilisation du code existant.

Although this tutorial focuses on fastai, many of the techniques shown are applicable across other frameworks too.

Bien que ce tutoriel se concentre sur fastai, de nombreuses techniques présentées sont également applicables à d'autres frameworks IA.

### Creéer un classifieur d'images avec fastai

La section suivante décrit les étapes de la création et de l'apprentissage d'un modèle de classification permettant de prédire si une publicité est composée uniquement de texte ou contient également une illustration. Brièvement, les étapes seront les suivantes :

1. Charger les données
2. Créer un modèle
3. Entraîner le modèle

Ces étapes seront abordées assez rapidement ; ne vous inquiétez pas si vous avez l'impression de ne pas tout suivre dans cette section, la leçon reviendra sur ce qui se passe de manière plus détaillée lorsque nous arriverons à la section [le flux de travail d'un problème de vision par ordinateur](#the-workflow-of-a-supervised-computer-vision-problem).

La première chose que nous allons faire est d'importer les modules nécessaires de la bibliothèque fastai. Dans ce cas, nous importons `vision.all` puisque nous travaillons sur une tâche de vision par ordinateur.[^10]

```python
from fastai.vision.all import *
```
Nous importons également [Matplotlib](https://perma.cc/AX3V-X4EC), une bibliothèque permettant de créer des visualisations en Python. Nous demanderons à Matplotlib d'utiliser un autre [style](https://perma.cc/37DF-7WKS) en utilisant la méthode `style.use`.


```python
%matplotlib inline
import matplotlib.pyplot as plt
plt.style.use('seaborn')
```

## Charger les données 

Les données peuvent être chargées de plusieurs façons à l'aide de la bibliothèque `fastai`. Les données des publicités consistent en un dossier qui contient les fichiers image, et un fichier CSV qui contient une colonne avec les chemins vers les images, ainsi que l'étiquette associée :

<div class="table-wrapper" markdown="block">
  
| file                                                                      | label     |
| ------------------------------------------------------------------------- | --------- |
| kyu_joplin_ver01_data_sn84037890_00175045338_1900060601_0108_007_6_97.jpg | text-only |
  
</div>

Il existe plusieurs façons de charger ce type de données en utilisant `fastai`. Dans cet exemple, nous allons utiliser `ImageDataLoaders.from_csv`. Comme son nom l'indique, la méthode `from_csv` de `ImagDataLoaders` charge les données depuis un fichier CSV. Nous devons préciser à fastai comment charger les données lorsque l'on utilise cette méthode :

- Le chemin vers le dossier où les images et le fichier CSV sont stockés.
- Les colonnes dans le fichier CSV qui contiennent les étiquettes.
- Un 'item transform' `Resize()` pour redimensionner toutes les images à une taille standard.

Nous allons créer une variable `ad_data` qui sera utilisée pour stocker les paramètres de chargement de ces données :

```python
ad_data = ImageDataLoaders.from_csv(
    path="ads_data/",  # chemin vers le dossier des fichiers CSV et images
    csv_fname="ads_upsampled.csv/",  # le nom de notre fichier CSV 
    folder="images/",  # le dossier où nos images sont stockées
    fn_col="file",  # la colonne 'fichier' dans notre CSV 
    label_col="label",  # la colonne 'étiquette' dans notre CSV
    item_tfms=Resize(224, ResizeMethod.Squish),  # redimensionnement des images à 224x224 pixels
    seed=42,  # choix d'une valeur de graine fixe pour rendre les résultats plus reproductibles
)
```

Il est important de s'assurer que les données ont été chargées correctement. Une façon de le vérifier rapidement est d'utiliser la méthode `show_batch()` sur nos données. Cela va afficher les images et les étiquettes associées pour un échantillon de nos données. Les exemples que vous recevrez en retour seront légèrement différents de ceux présentés ici.

```python
ad_data.show_batch()
```

{% include figure.html filename="show_batch_1.png" alt="La sortie de show batch. Le résultat est une grille 3x3 d'images de publicités de journaux avec des étiquettes indiquant si les publicités sont 'illustrées' ou textuelles" caption="La sortie de 'show_batch'" %}

C'est un moyen utile de vérifier que vos étiquettes et vos données ont été chargées correctement. Vous pouvez voir ici que les étiquettes (`text-only` et `illustration`) ont été associées conformément à la façon dont nous voulons classer ces images. 


## Créer le modèle

Maintenant que fastai sait comment charger les données, l'étape suivante consiste à créer un modèle avec celles-ci. Pour créer un modèle adapté à la vision par ordinateur, nous allons utiliser la fonction `cnn_learner`. Cette fonction va créer un ['Convolutional Neural Network'](https://perma.cc/UH8L-L6MR), un type de modèle d'apprentissage profond souvent utilisé pour les applications de vision par ordinateur. Pour utiliser cette fonction, vous devez passer (au minimum) :

- Les données que le modèle utilisera comme données d'entraînement
- Le type de modèle que nous souhaitons utiliser

C'est suffisant pour créer un modèle de vision par ordinateur avec fastai, mais vous pouvez aussi vouloir passer certaines métriques à suivre pendant l'entraînement. Cela vous permettra d'avoir une meilleure idée de la façon dont votre modèle effectue la tâche sur laquelle vous l'entraînez. Dans cet exemple, nous allons utiliser `accuracy` comme métrique.

Créons ce modèle et assignons-le à une nouvelle variable `learn` :

```python
learn = cnn_learner(
    ad_data,  # les données sur lesquelles le modèle sera entraîné
    resnet18,  # le type de modèle cible
    metrics=accuracy,  # la métrique à suivre
)
```

### Entraîner le modèle

Bien que nous ayons créé un modèle `cnn_learner`, nous n'avons pas encore entraîné le modèle. Ceci est fait en utilisant la méthode `fit`. L'entraînement est le processus qui permet au modèle de vision par ordinateur d'apprendre à prédire les étiquettes correctes pour les données. Il existe différentes façons d'entraîner (ajuster) ce modèle. Pour commencer, nous allons utiliser la méthode `fine_tune`. Dans cet exemple, la seule chose que nous allons passer à la méthode est le nombre d'époques (*epoch*) pour s'entraîner. Chaque passage à travers le jeu de données complet est une epoch. Le temps d'entraînement du modèle dépendra du contexte d'exécution de ce code et des ressources disponibles.  Nous traiteron en détail de  ces éléments ci-après.



```python
learn.fine_tune(5)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0.971876</td>
      <td>0.344096</td>
      <td>0.860000</td>
      <td>00:06</td>
    </tr>
  </tbody>
</table>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0.429913</td>
      <td>0.394812</td>
      <td>0.840000</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.271772</td>
      <td>0.436350</td>
      <td>0.853333</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.170500</td>
      <td>0.261906</td>
      <td>0.913333</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.125547</td>
      <td>0.093313</td>
      <td>0.946667</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.107586</td>
      <td>0.044885</td>
      <td>0.980000</td>
      <td>00:05</td>
    </tr>
  </tbody>
</table>

Lorsque vous exécutez cette méthode, vous verrez une barre de progression indiquant la durée de l'entraînement du modèle et le temps restant estimé. Vous verrez également un tableau qui affiche d'autres informations sur le modèle, comme la métrique de précision que nous avons suivie. Vous pouvez voir que dans cet exemple, nous avons obtenu une précision supérieure à 90 %. Si vous exécutez le code vous-même, le score obtenu peut être légèrement différent.


## Résultats

While deep learning techniques are commonly perceived as needing large amounts of data and extensive computing power, our example shows that for many applications smaller datasets suffice. In this example, we could have potentially used other approaches; the aim here was not to show the best solution with this particular dataset but to give a sense of what is possible with a limited number of labeled examples.

Alors que les techniques d'apprentissage profond sont généralement perçues comme nécessitant de grandes quantités de données et une puissance de calcul importante, notre exemple montre que pour de nombreuses applications, des ensembles de données de petite taille suffisent. Dans cet exemple, nous aurions pu utiliser d'autres approches ; le but n'était pas de montrer la meilleure solution avec ce jeu de données particulier, mais de donner une idée de ce qui est possible lorsque l'on dispose d'un nombre limité d'exemples étiquetés.


# An In-Depth Guide to Computer Vision using Deep Learning

Now that we have an overview of the process we'll go into more detail about how this process works.

## The Workflow of a Supervised Computer Vision Problem

This section will start to dig into some of the steps involved in the process of creating a deep learning based computer vision model. This process involves a range of steps, only some of which are directly about training models. A high-level illustration of a supervised machine learning pipeline might look like this:

{% include figure.html filename="deep-learning-pipeline-simple.png" alt="A diagram showing a workflow of a machine learning pipeline. The pipeline contains three boxes, 'data preparation', 'deep learning' and 'analysis'. An arrow moves across these three boxes. Within the 'data preparation' box are three boxes from left to right: 'sampling', 'labels', 'annotation'. For the box 'deep learning' there are three smaller boxes with arrows moving between them: 'training data', 'model', 'predictions'. The box 'analysis' contains three smaller boxes 'metrics' and 'interpretation'." caption="A high level illustration of a supervised machine learning pipeline" %}

We can see that there are quite a few steps before and after the model training phase of the workflow. Before we get to training a model, we need data. In this lesson, image data has already been prepared so you didn't need to worry about this step. However, when you move to using computer vision for your own research questions, it is unlikely that there will an existing dataset for your exact use case. As a result, you will need to create this data yourself. The process of getting access to data will vary depending on the type of images you are interested in working with and where they are held. Some heritage collections are making bulk collections of images data available, whilst others largely make images available only via a 'viewer'. The increasing adoption of the [IIIF standard](https://perma.cc/27EM-N36U) is also making the process of working with images held by different institutions simpler.

Once you have a collection of images to work with, the next step (if using supervised learning) will be to create some labels for this data and train the model. This process will be discussed in more detail below. Once a model has been trained you will get out some predictions. These predictions are 'scored' using a range of potential metrics, some of which we'll explore further in [Part 2](/en/lessons/computer-vision-deep-learning-pt2) of this lesson.

Once a model has reached a satisfactory score, its outputs may be used for a range of 'interpretative" activities. Once we have predictions from a deep learning model there are different options for what to do with these. Our predictions could directly inform automated decisions (for example, where images are to be displayed within a web collection), but it is more likely that those predictions will be read by a human for further analysis. This will particularly be the case if the intended use is to explore historical phenomena.

## Training a Model

Zooming in on the deep learning part of the workflow, what does the training process look like?

{% include figure.html filename="training-loop.jpg" alt="A diagram showing a workflow of training a deep learning model. The pipeline contains two boxes, 'prepare training batch' and 'model training'. An arrow moves across these two boxes to a free standing box with the text 'metrics' inside. Inside the 'prepare' training batch' is a workflow showing an image and a label going through a transform, and then put in a batch. Following this under the 'model training' heading' the workflow moves through a model, predictions, and a loss. This workflow has an arrow indicating it is repeated. This workflow also flows to the metrics box"  caption="The deep learning training loop" %}

A high-level summary of the training loop for supervised learning: start with some images and labels, do some preparation to make the input suitable for a deep learning model, pass the data through the model, make predictions for the labels, calculate how wrong the predictions are, update the model with the aim of generating better predictions next time. This process is repeated a number of times. During this training loop, metrics are reported which let the human training the model evaluate how well the model is doing.

This is obviously a high-level summary. Let's look at each step in the training loop one at a time. Although the next section will show these steps using code, don't worry too much if it doesn't all sink in at first.

## Input Data

Starting with the inputs, we have images and labels. Although deep learning takes some inspiration from how human cognition works, the way a computer 'sees' is very different from a human. All deep learning models take numbers as inputs. Since images are stored on a computer as a matrix of pixel values, this process is relatively simple for computer vision models. Alongside these images, we have a label(s) associated with each image. Again, these are represented as numbers inside the model.

### How Much Data?

It is often believed that you need huge amounts of data to train a useful deep learning model, however, this is not always the case. We assume that if you are trying to use deep learning to solve a problem, you have enough data to justify not using a manual approach. The real problem is the amount of labelled data you have. It is not possible to give a definitive answer to "how much data?", since the amount of training data required is dependent on a broad range of factors. There are a number of things which can be done to reduce the amount of training data required, some of which we will cover in this lesson.

The best approach will likely be to create some initial training data and see how well your model does on it. This will give you a sense of whether a problem is possible to tackle. Furthermore, the process of annotating your data is valuable in itself. For a simple classification task, it might be possible to begin assessing whether a model is worth developing with a few hundred labelled examples (though you will often need more than this to train a robust model).

### Preparing Mini Batches

When we use deep learning, it is usually not possible to pass all of our data into the model in one go. Instead, data is split into batches. When using a GPU, data is loaded into GPU memory one batch at a time. The size of this batch can impact the training process but is more often determined by the computational resources you have available.

The reason we use a GPU for training our model is that it will almost always be quicker to train a model on a GPU compared to a CPU due to its ability to perform many calculations in parallel. 

Before we can create a batch and load it onto the GPU, we usually need to make sure the images are all the same size. This allows the GPU to run operations effectively. Once a batch has been prepared, we may want to do some additional transformations on our images to reduce the amount of training data required.

## Creating a Model

Once we have prepared data so it can be loaded one batch at a time, we pass it to our model. We already saw one example of a model in our first example `resnet18`. A deep learning model architecture defines how data and labels are passed through a model. In this two-part lesson, we focus on a specific type of deep learning that uses 'Convolutional Neural Networks' (CNN).

{% include figure.html filename="3-layer-network.png" alt="A simplified diagram of a three layer neural network. The diagram shows an input image on the left moving through three layer of the neural network. Each layer has sections highlighted illustrating these areas being activated. The diagram then points to two images on representing an illustrated advert the other a text only advert. In this diagram the image shown has an illustration so the arrow pointing to the illustrated label is highlighted." caption="A three layer neural network" %}

This diagram gives a crude overview of the different components of a CNN model. In this type of model, an image is passed through several layers, before predicting an output label for the image ('text only' in this diagram). The layers of this model are updated during training so that they "learn" which features of an image predict a particular label. So for example, the CNN we trained on adverts will update the parameters known as "weights" for each layer, which then produces a representation of the image that is useful for predicting whether an advert has an illustration or not.

[Tensorflow playground](https://perma.cc/625S-TNS6) is a useful tool for helping to develop an intuition about how these layers capture different features of input data, and how these features, in turn, can be used to classify the input data in different ways.

The power in CNNs and deep learning comes from the ability of these layers to encode very complicated patterns in data.[^11] However, it can often be a challenge to update the weights effectively.

### Using an Existing Model?

When considering how to create our model we have various options about what to use. One option is to use an existing model which has already been trained on a particular task. You might for example use the [YOLO](https://perma.cc/4BPF-LLQT) model. This model is trained to predict [bounding boxes](https://perma.cc/JT6Y-F2EF) for a number of different types of objects in an image. Although this could be a valid starting point, there are a number of limitations to this approach when working with historical material, or for humanities questions more broadly. Firstly, the data these models were trained on might be very different from the data you are using. This can impact your model's performance on your data and result in biases towards images in your data which are similar to the training data. Another issue is that if you use an existing model without any modification, you are restricted to identifying the labels the original model was trained on.

Although it is possible to directly define a CNN model yourself by defining the layers you want your model architecture to include, this is usually not where you would start. It is often best to start with an existing model architecture. The development of new model architectures is an active area of research, with models proving to be well-suited for a range of tasks and data. Often, these models are then implemented by machine learning frameworks. For example, the [Hugging Face](https://perma.cc/D39N-DBK4) [Transformers library](https://perma.cc/QJ4P-8PHQ) implements many of the most popular model architectures. 

Often, we want a balance between starting from scratch and leveraging existing models. In this two-part lesson, we show an approach which uses existing model architectures but modifies the model slightly to allow it to predict new labels. This model is then trained on new data so it becomes better suited to the task we want it to perform. This is a technique known as ['transfer learning'](https://perma.cc/62A8-5PZ8) which will be explored in the [appendix](#appendix-a-non-scientific-experiment-assessing-transfer-learning) section of this lesson.

## Training

Once a model has been created and data prepared, the training process can begin. Let's look at the steps of a training loop:

1. A model is passed data and labels, one batch at a time. Each time an entire dataset has been passed through a model is known as an 'epoch'. The number of epochs used to train a model is one of the variables that you will need to control.

2. The model makes predictions for these labels based on the given inputs, using a set of internal weights. In this CNN model, the weights are contained within the layers of the CNN.

3. The model calculates how wrong the predictions are, by comparing the predictions to the actual labels. A ['loss function'](https://perma.cc/JUD5-J6MQ) is used to calculate how 'wrong' the model was in its predictions.

4. The model changes internal parameters to try to do better next time. The loss function from the previous step returns a 'loss value', often just referred to as the 'loss', which is used by the model to update the weights.

A 'learning rate' is used to determine how much a model should update based on the calculated loss. This is another one of the important variables that can be manipulated during the training process. In [Part 2 of this lesson](/en/lessons/computer-vision-deep-learning-pt1), we will see one potential way of trying to identify a suitable learning rate for your model.

## Validation Data

When we train a deep learning model, we usually do so to make predictions on new unseen data which doesn't contain labels. For example, we might want to use our advert classifier across all of images for a particular time period to count how many of each type of advert (illustrated or not) appeared in this corpus. We, therefore, don't want a model that only does well at learning how to classify the training data it is shown. Consequently, we almost always use some form of 'validation data'. This is data which is used to check that the weights a model is learning on the training data also translate to new data. In the training loop, the validation data is only used to 'test' the model's predictions. The model does not directly use to update weights. This helps ensure we don't end up 'overfitting' our model. 

'Overfitting' refers to when a model becomes very successful at making predictions on the training data but these predictions don't generalise beyond the training data. In effect, the model is 'remembering' the training data rather than learning more general features to make correct predictions on new data. A validation set prevents this by allowing you to see how well the model is doing on data it hasn't learned from. Sometimes, an additional split is made of the data which is used to make predictions only at the end of training a model. This is often known as a 'test' set. A test set is used to validate model performance for data science competitions, such as those hosted on Kaggle, and to validate the performance of models created by external partners. This helps ensure a model is robust in situations where validation data has deliberately or accidentally been used to 'game' the performance of a model.

## Transfer Learning

In our first advert classifier, we used the `fine_tune()` method on our `learner` for training. What was this doing? You will have seen that the progress bar output two parts. The first epoch was training only the final layers of the model, after this the lower layers of the model were also trained. This is one way in which we can do transfer learning in fastai. The importance of transfer learning has already been discussed in the previous sections. As a reminder, transfer learning uses the 'weights' that a model has previously learned on another task on a new task. In the case of image classification, this usually means a model has been trained on a much larger dataset. Often this previous training dataset is ImageNet.

ImageNet is a large database of images which is heavily used in computer vision research. ImageNet currently contains ["14,197,122" images](https://perma.cc/U48T-WA6E) with over 20,000 different labels. This dataset is often used as a [benchmark](https://perma.cc/KM95-DXTR) for computer vision researchers to compare their approaches. Ethical issues related to the labels and production of ImageNet are explored in _[The Politics of Images in Machine Learning Training Sets](https://perma.cc/NE8D-P6AW)_ by Crawford and Paglen.[^4]

### Why Does Transfer Learning Often Help?

As we have seen, transfer learning works by using a model trained on one task to perform a new task. In our example, we used a model trained on ImageNet to classify images of digitised nineteenth century newspapers. It might seem strange that transfer learning works in this case, since the images we are training our model on are very different from the images in ImageNet. Although ImageNet does have a [category for newspapers](https://perma.cc/K79W-TMDE), these largely consist of images of newspapers in the context of everyday settings, rather than images cropped from the pages of newspapers. So why is using a model trained on ImageNet still useful for a task which has different labels and images to those in ImageNet?

When we looked at the diagram of a CNN model we saw that it is made of different layers. These layers create representations of the input image which pick up on particular features of an image for predicting a label. What are these features? They could be 'basic' features, for example simple shapes. Or, they could be more complex visual features, such as facial features. Various techniques have been developed to help visualise the different layers of a neural network. These techniques have found that the earlier layers in a neural network tend to learn more 'basic' features, for example they learn to detect basic shapes like circles, or lines, whilst layers further into the network contain filters which encode more complex visual features, such as eyes. Since many of these features capture visual properties useful for many tasks, starting with a model that is already capable of detecting features in images will help detect features which are important for the new task, since these new features are likely to be variants on the features the model already knows rather than new ones.

When a model is created in the fastai library using the `cnn_learner` method, an existing architecture is used as the “body” of the model. The deeper layers added are known as the model's "head". The body uses the weights (parameters) learned through training on ImageNet by default. The “head” part takes the output of the body as input before moving to a final layer which is created to fit the training data you pass to `cnn_learner`. The `fine_tune` method first trains only the head part of the model i.e. the final few layers of the model, before 'unfreezing' the lower layers. When these layers are 'unfrozen' the weights of the model are updated through the process discussed above under 'training'. We can also take more active control of how much we train different layers of the model, something we will see as we move through a full pipeline of training a deep learning model.

## Suggested Experiments

It is important to develop a sense of what happens when you make changes to the training process. We suggest making a copy of the lesson notebook and seeing what happens if you make changes. Here are some suggestions:

- Change the size of the input images defined in the `Resize` item transform in the `ImageDataLoaders`.
- Change the model used in `cnn_learner` from `resnet18` to `resnet34`.
- Change the 'metrics' defined in `cnn_learner`. Some metrics included in fastai can be found in the [documentation](https://perma.cc/K4BE-BF3W).
- Change the number of 'epochs' used in the `fine_tune` method.

If something 'breaks', don't worry! You can return to the original notebook to get back to a working version of the code. In the next part of the lesson, the components of a deep learning pipeline will be covered in more detail. Investigating what happens when you make changes will be an important part of learning how to train a computer vision model.

# Part One Conclusion

In this lesson we:

- Gave a high-level overview of the distinction between rule-based and machine learning-based approaches to tackling a problem.
- Showed a basic example of how to use fastai to create an image classifier with relatively little time and training data.
- Presented an overview of the steps of a deep learning pipeline and identified points in this pipeline where humanities scholars should pay particular attention.
- Ran a crude experiment to try and verify if transfer learning is useful for our computer vision classifier.

In the next part of this lesson, we will build on these points and dive into more detail.

# Appendix: A Non-Scientific Experiment Assessing Transfer Learning

The use of deep learning in the context of working with heritage data has not been extensively researched. It is therefore useful to 'experiment' and validate whether a particular technique is effective. For example, let's see if transfer learning will prove to be helpful when training a model to classify nineteenth century newspaper adverts into two categories: those containing images and those without images. To do this, we'll create a new `learner` with the same parameters as before but with the `pretrained` flag set to `False`. This flag tells fastai not to use transfer learning. We'll store this in a variable `learn_random_start`.

```python
learn_random_start = cnn_learner(ad_data, resnet18, metrics=accuracy, pretrained=False)
```

Now that we have created a new learner, we'll use the same `fine_tune` method as before and train for the same number of `epochs` as last time.

```python
learn_random_start.fine_tune(5)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1.303890</td>
      <td>0.879514</td>
      <td>0.460000</td>
      <td>00:04</td>
    </tr>
  </tbody>
</table>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0.845569</td>
      <td>0.776279</td>
      <td>0.526667</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.608474</td>
      <td>0.792034</td>
      <td>0.560000</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.418646</td>
      <td>0.319108</td>
      <td>0.853333</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.317584</td>
      <td>0.233518</td>
      <td>0.893333</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.250490</td>
      <td>0.202580</td>
      <td>0.906667</td>
      <td>00:05</td>
    </tr>
  </tbody>
</table>

The best acurracy score we achieve when we randomly initialise the weights is ~90%. In comparison, if we go back to our original model, which is stored in a variable `learn`, and use the `validate()` method, we get the metrics (in this case accuracy) calculated on the validation set:

```python
learn.validate()
```

```

    (#2) [0.04488467052578926,0.9800000190734863]
```

We see that there is a fairly big difference between the two models' performance. We kept everything the same except the `pretrained`flag, which we set to `False`. This flag determines if the model starts from the weights learned from training on ImageNet or starts from 'random' weights.[^12] This doesn't conclusively prove that transfer learning works, but it does suggest a sensible default for us to use.

# Endnotes

[^1]: Romein, C. Annemieke, Max Kemman, Julie M. Birkholz, James Baker, Michel De Gruijter, Albert Meroño‐Peñuela, Thorsten Ries, Ruben Ros, and Stefania Scagliola. ‘State of the Field: Digital History’. History 105, no. 365 (2020): 291–312. [https://doi.org/10.1111/1468-229X.12969](https://doi.org/10.1111/1468-229X.12969).

[^2]: Moretti, Franco. Distant Reading. Illustrated Edition. London ; New York: Verso Books, 2013.

[^3]: Wevers, Melvin, and Thomas Smits. ‘The Visual Digital Turn: Using Neural Networks to Study Historical Images’. Digital Scholarship in the Humanities 35, no. 1 (1 April 2020): 194–207. [https://doi.org/10.1093/llc/fqy085](https://doi.org/10.1093/llc/fqy085).

[^4]: Crawford, K., Paglen, T., 2019. Excavating AI: The Politics of Training Sets for Machine Learning. [https://www.excavating.ai](https://perma.cc/NE8D-P6AW) (accessed 2.17.20).

[^5]: Jo, Eun Seo, and Timnit Gebru. ‘Lessons from Archives: Strategies for Collecting Sociocultural Data in Machine Learning’. In Proceedings of the 2020 Conference on Fairness, Accountability, and Transparency, 306–316. FAT\* ’20. New York, NY, USA: Association for Computing Machinery, 2020. [https://doi.org/10.1145/3351095.3372829](https://doi.org/10.1145/3351095.3372829).

[^6]: These annotations include a 'bounding box' around images, along with information about the type of image is contained within that bounding box. This object detection model was trained on this data and subsequently used to make predictions across the whole Chronicling America collection. The model extracts images from the page and classifies them into _one_ of seven categories. Lee, Benjamin Charles Germain, Jaime Mears, Eileen Jakeway, Meghan Ferriter, Chris Adams, Nathan Yarasavage, Deborah Thomas, Kate Zwaard, and Daniel S. Weld. ‘The Newspaper Navigator Dataset: Extracting And Analyzing Visual Content from 16 Million Historic Newspaper Pages in Chronicling America’. ArXiv:2005.01583 [Cs], 4 May 2020. [https://doi.org/10.48550/arXiv.2005.01583](https://doi.org/10.48550/arXiv.2005.01583).

[^7]: Arizona republican. [volume] (Phoenix, Ariz.) 1890-1930, March 29, 1895, Page 7, Image 7. Image provided by Arizona State Library, Archives and Public Records; Phoenix, AZ. [https://chroniclingamerica.loc.gov/lccn/sn84020558/1895-03-29/ed-1/seq-7/](https://perma.cc/M5G5-CRDK).

[^8]: The Indianapolis journal. [volume] (Indianapolis [Ind.]) 1867-1904, February 06, 1890, Page 8, Image 8. Image provided by Indiana State Library. [https://chroniclingamerica.loc.gov/lccn/sn82015679/1890-02-06/ed-1/seq-8/](https://perma.cc/W2HA-YCSZ).

[^9]: Howard, Jeremy, and Sylvain Gugger. ‘Fastai: A Layered API for Deep Learning’. Information 11, no. 2 (16 February 2020): 108. [https://doi.org/10.3390/info11020108](https://doi.org/10.3390/info11020108).

[^10]: Using 'star imports' is generally discouraged in Python. However, fastai uses [`__all__`](https://perma.cc/3GHR-V8RN) to provide a list of packages that should be imported when using star import. This approach is useful for exploratory work but you may still want to change your imports to be more explicit.

[^11]: Neural Networks are able to theoretically approximate any function. The mathematical proof of this exists in a number of forms under the heading of the ["Universal Approximation theorem"](https://perma.cc/2J3Q-PDTC). These proofs are not things you will need to know to practically use deep learning. However, if you are interested, a good overview of the idea can be found in a [YouTube video](https://youtu.be/Ijqkc7OLenI).

[^12]: This initialisation isn't actually random in the fastai framework, and instead uses [Kaiming initialization](https://perma.cc/2Y74-MB47). 
