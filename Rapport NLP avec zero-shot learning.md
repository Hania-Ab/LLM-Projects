# Introduction

Dans le cadre de ce projet, j'ai exploré l'application de modèles de classification de texte à l'aide de la méthode de zero-shot learning, en m'appuyant sur la bibliothèque **Hugging Face**. L'objectif principal était de classifier des articles issus du journal Le Monde en utilisant divers modèles pré-entraînés, sans nécessiter d'échantillons étiquetés spécifiques pour chaque classe cible.

J'ai d'abord préparé les données en séparant les caractéristiques et les cibles, puis j'ai évalué plusieurs modèles de la bibliothèque pour identifier celui offrant les meilleures performances. Après une série de tests, j'ai choisi le modèle **deberta-v3-base-zeroshot-v1**, qui a démontré une précision satisfaisante et un temps d'exécution considérablement réduit par rapport aux autres modèles. Ce travail met en lumière l'efficacité du zero-shot learning dans la classification de texte, tout en soulignant les défis associés à l'évaluation des performances des modèles dans des contextes réels.

## 1. Classifier un exemple de texte d'un article du Monde en utilisant un modèle de zero-shot learning avec la bibliothèque Hugging Face

Pour commencer, j'ai découpé les données en features (X) et target (y).

Ensuite, j'ai créé une fonction qui charge le modèle. Elle prend en entrée le nom du modèle et éventuellement le nom du tokenizer, charge le modèle puis le retourne.

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/f8c568b4-ac43-4e99-a875-0d344e9315b1.png)

Pour finir, voici la fonction qui calcule les métriques. Elle prend en entrée le nombre de secondes, la ground truth et y_pred. Elle affiche le temps d'exécution (minutes et secondes), le rapport de classification et l'accuracy score.

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/1309e713-5836-4f75-880d-d4e88260042d.png)

## 2. Le choix du modèle

J'ai effectué des tests sur 5 modèles de la librairie Hugging Face à la recherche d'un bon modèle. Les modèles que j'ai testés sont : 

* facebook/bart-large-mnli
* MoritzLaurer/DeBERTa-v3-base-mnli-fever-anli
* MoritzLaurer/deberta-v3-base-zeroshot-v1
* cmarkea/distilcamembert-base-nli
* vicgalle/xlm-roberta-large-xnli-anli

L'évaluation a été effectué sur 10 textes tirés au hasard du dataset. 
J'ai donc tiré au hasard 10 textes du dataset en utilisant la fonction **sample(n=10)**.

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/ad367332-1d5a-4300-bd0d-655bd5d5438d.png)

Ensuite, j'ai fait une boucle sur ces 10 textes afin de calculer le y_pred pour chaque texte, en donnant ce dernier et la liste des catégories au classifier.

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/9f41ba33-74a4-43fb-b684-d0d60e7ccc1a.png)

La ground truth étant la liste des catégories des 10 textes tirés : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/4209a211-08e6-4706-83f4-9383d00dbbea.png)


J'ai calculé l'accuracy pour l'output de chaque modèle, ainsi que le rapport de classification.

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/26975c53-8f50-4cbc-b98e-171341874d5d.png)

Le tableau ci-dessous illustre les résultats pour chacun des modèles testés:


| Modèle | facebook/bart-large-mnli | MoritzLaurer/DeBERTa-v3-base-mnli-fever-anli | MoritzLaurer/deberta-v3-base-zeroshot-v1 | cmarkea/distilcamembert-base-nli | vicgalle/xlm-roberta-large-xnli-anli |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Accuracy Score    | 0.0 | 0.5     | 0.5     | 0.1     | 0.4     |
| Precision Macro avg | 0.0 | 0.54     | 0.46     | 0.05    | 0.43     |
| Precision weighted avg | 0.0 | 0.62     | 0.53    | 0.07     | 0.5    |
| Temps d'exécution | 15min 58sec | 13min 17sec | 3min 4sec    | 54sec     | 6min 20sec     |

D'après le tableau ci-dessus, le meilleur résultat que j'ai obtenu sur 10 textes c'est 50% d'accuracy. Ce résultat a été obtenu par deux modèles : **DeBERTa-v3-base-mnli-fever-anli** et **deberta-v3-base-zeroshot-v1**. En ce qui concerne les autres modèles, j'ai obtenu un résultat à peu près correct avec le modèle **xlm-roberta-large-xnli-anli** et de mauvais résultats avec **distilcamembert-base-nli** et **bart-large-mnli**.

Pour la suite, entre les deux meilleurs modèles, j'ai sélectionné le modèle **deberta-v3-base-zeroshot-v1**, car son temps d'exécution est largement inférieur.

## 3. Choisir une formulation pour les classes à prédire 
Pour la formulation des classes de prédiction, j'ai opté pour les mots entiers au lieu de juste les diminutifs. Voici ci-dessous, les classes à prédire que je donne en entrée au modèle.

J'ai créé un dictionnaire de catégories : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/dd17cf9f-ca5b-416a-aba3-cbcbe49d8c4e.png)

J'ai fait un dictionnaire seulement pour faire le mapping entre les catégories de base du dataset et les mots par lesquels je les ai remplacées.

Et je donne donc au classifier les clés du dictionnaire, comme suit : 
![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/ef788652-1898-42e7-a82d-e8b18fe4d98e.png)

## 4. Prédiction de classe du texte par le modèle 

J'ai exécuté le modèle **deberta zeroshot** sur le text 0 du dataset : 
![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/016ae07d-5e52-4203-8c34-e4b3cc4302cc.png)

Voici le résultat obtenu : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/9502cc41-fd0d-4026-a66b-bb997a726f74.png)

Le modèle a retourné une liste de classes et une liste des probabilités que ce soit la bonne classe. Sur le screenshot ci-dessus, le modèle a prédit que le text 0 du dataset avait la catégorie Sports à 74.3%, la catégorie international à 23.4% et le reste à moins de 2%.

Le modèle a prédit la bonne classe avec la plus grande probabilité, donc le résultat est correct. Voici ci-dessous la vraie catégorie du texte 0 : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/54db51ba-50a4-4171-be94-4242bae61d21.png)

## **5- Evaluation de la performance du modèle sur 100 articles (a test set) :** 

J'ai fait l'évaluation du modèle **deberta-v3-base-zeroshot-v1** sur 100 textes tirés aléatoirement. Les résultats obtenus sont sur le screenshot ci-dessous : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/762f4a56-260b-4f6e-bcf6-5ec78e269617.png)

L'accuracy score est de 50%. C'est équivalent à l'accuracy score de l'évaluation sur 10 textes. 
Le temps d'exécution a été de 24 minutes et 59 secondes.


J'ai quand même essayé l'autre modèle de Deberta (**DeBERTa-v3-base-mnli-fever-anli**). J'ai obtenu une accuracy de 46.48% pour un temps d'exécution de 2h 45minutes.

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/6ea7b4f3-2127-4505-8d61-8b4fa07bcd68.png)

Comme illustré sur la capture d'écran ci-dessus, le macro avg et weighted avg sont meilleurs pour ce modèle.

### **Conclusion :** 
En raison du temps d'exécution et de la performance, le modèle **deberta-v3-base-zeroshot-v1** est le meilleur modèle sélectionné.
