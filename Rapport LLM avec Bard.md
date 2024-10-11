# Introduction

Ce rapport présente une évaluation approfondie de la qualité des articles extraits automatiquement, en se concentrant sur l'analyse de données textuelles bruyantes. L'objectif principal de ce projet est d'explorer différentes méthodes de correction d'erreurs en utilisant des outils variés tels que le LanguageTool et des modèles de langage de grande taille (LLM), notamment le modèle Bard de Google (Gemini).

Dans un premier temps, nous examinerons la qualité des articles à l'aide de métriques de performance telles que le Word Error Rate (WER) et le Character Error Rate (CER), permettant d'évaluer les erreurs présentes dans les textes extraits par OCR. Ensuite, nous testerons l'efficacité de LanguageTool en tant que méthode de correction d'erreurs et analyserons ses résultats par rapport aux données d'origine.

Enfin, nous évaluerons l'application de modèles LLM pré-entraînés pour la correction des articles, en mettant l'accent sur l'optimisation des prompts afin d'améliorer les performances. Ce rapport se conclura par une analyse comparative des méthodes utilisées, mettant en lumière les forces et les faiblesses de chaque approche dans le contexte de la correction automatique des textes.


# Rapport du Projet :

J'ai fait l'évaluation du dataset sans aucun changement, l'évaluation du dataset avec LanguageTool et l'évaluation du LLM Bard de Google (Gemini).


## 1. Évaluer la qualité des articles automatiquement extraits (OCR actuel)

### Définitions :
- **WER (Word Error Rate) :** mesure le taux d'erreur en termes de mots. Il est calculé en comptant le nombre total d'insertions, de suppressions et de substitutions nécessaires pour transformer la transcription automatique en la transcription de référence, puis en le divisant par le nombre total de mots dans la transcription de référence. Il est exprimé en pourcentage.

- **CER (Character Error Rate) :** mesure le taux d'erreur en termes de caractères individuels. Il est calculé de manière similaire au WER, mais au lieu de compter les mots, il compte les caractères individuels.

# 

Pour cette partie, j'ai chargé le dataset. Ensuite, j'ai défini une fonction qui prend en entrée le dataset et qui retourne une liste des valeurs de CER et une autre des valeurs de WER pour chaque tuple de (texte bruité, texte corrigé)

Le code de la fonction ci-dessous : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/63849a59-3724-47cf-94ba-0db81bff7258.png)

J'ai ensuite inséré deux colonnes au dataset (CER et WER) afin d'afficher à côté de chaque texte, les résultats obtenus : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/989cb493-301e-4690-a2c9-13d15a4e3fa8.png)

J'ai calculé la moyenne de chaque liste de valeurs : 

Mean CER (OCR actuel) : 8.53%
Mean WER (OCR actuel) : 20.57%

## 2. Evaluer une méthode de base de correction d’erreur (LanguageTool)

J'ai importé la libraire **LanguageTool** et j'ai chargé la fonction avec la langue française : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/e97cf90e-eb27-477a-870b-ded185c9a7da.png)

Ensuite, j'ai fait une boucle sur les textes du dataset en calculant directement le CER et le WER entre la correction du tool et le texte déjà corrigé du dataset : 

![](https://storage.teklia.com/tools-hedgedoc-uploads/uploads/15f34735-b703-446a-a482-ef1f80393ed9.png)

Les moyennes de CER et WER obtenues sont : 
Mean CER (LanguageTool) : 9.79%
Mean WER (LanguageTool) : 22.47%

On remarque que le résultat est plus mauvais que le résultat obtenu sans aucune correction. La conclusion est que le LanguageTool n'est pas très performant sur ce dataset.

## 3. Evaluer des modèles LLM pré-entraînés pour la correction des articles

Pour cette partie, j'ai choisi un modèle de LLM à évaluer. J'ai fait l'évaluation avec Bard de Google (Gemini) en variant le prompt.

Pour le premier prompt, j'ai choisi cette phrase : 
**"Ceci est un texte bruité, avec des erreurs. Corrigez-le, afin d'effacer le plus d'erreurs possibles :"**

J'ai utilisé ce prompt pour tous les textes, sauf pour le texte 7, j'ai rajouté une précision pour que le modèle me rédige la liste entière du classement (ce qu'il ne fait pas automatiquement).

Les résultats obtenus avec ce prompt sont : 
Mean CER (Bard) : 10.5%
Mean WER (Bard) : 20.9%

Le résultat n'est toujours pas meilleur que le résultat obtenu sur les textes originaux. Donc, j'ai décidé de changer le prompt en donnant plus de précision concernant la structure du texte et en précisant que je souhaitais une correction de la grammaire et de l'orthographe.

Le nouveau promt est : 
**Corrigez le bruit du texte ci-dessous en ne changeant pas la structure et juste corriger les erreurs d'orthographe et grammaticales:**

Les résultats obtenus avec ce nouveau prompt sont : 
Mean CER (Bard) : 8.3%
Mean WER (Bard) : 17.65%

J'ai essayé de varier le prompt une troisième fois, en précisant la présence d'erreur de frappe dans les textes (Prompt : **La personne qui a écrit ce texte a fait des erreurs de frappe, corrigez les erreurs en gardant au max le sens des phrases que la personne voulait exprimer :**), mais ça ne donnait pas de meilleurs résultats. 

En conclusion, le meilleur prompt pour le modèle Bard est : 

**Corrigez le bruit du texte ci-dessous en ne changeant pas la structure et juste corriger les erreurs d'orthographe et grammaticales:**

# 


## 4. Entraîner des modèles pour la correction des articles

Voici les étapes de l'entraînement d'un modèle génératif de texte.

### Les étapes d'entraînement d'un modèle LLM 

L'entraînement d'un modèle LLM pour la correction d'articles se compose de 5 étapes. 

### a. Prétraitement des données

Vu que l'objectif est d'entraîner un modèle sur la tâche de correction d'articles, il faut donc un dataset comme celui utilisé dans la première partie du projet.
Si on décide de travailler avec le dataset LeMonde, nous avons donc la ground truth. Il manque les textes bruités. Pour les obtenir, il faut une fonction de bruitage de texte. Cette fonction rajoute des fautes de frappe, des erreurs d'orthographe, des mots coupés, des lettres, des ponctuations manquantes, etc.

Ensuite, il y a la tokenization du texte avec le tokenizer **t5-base**.

### b. Division du dataset

Pour l'entraînement, je prends aléatoirement 80% des textes du dataset Le Monde. Et les 20% restantes seront consacrés au test.

### c. Entraînement et Fine-Tuning 

- Chargement du modèle pré-entraîné.

- Définir une fonction de perte appropriée. Pour ce problème, l'idéal c'est de prendre une fonction qui minimise à la fois les erreurs de caractères et de mots. On utilise la fonction de perte de Cross-Entropy pour calculer la perte caractère par caractère entre le texte correct et le texte bruité, et aussi qui calcule la perte mot par mot pour chaque tuple (texte bruité, texte correct).

- Combinaison des deux pertes calculées (**Character-Level** Loss et **Word-Level Loss**). Cette combinaison se ferait simplement en faisant la somme des deux pertes en pondérant les pertes pour équilibrer leur contribution à la fonction de perte globale.

- Optimiser en utilisant Adam pour mettre à jour les poids du modèle lors de l'entraînement.

### d. Evaluation du modèle

Pour l'évaluation du modèle, je l'ai faite sur l'ensemble de test, en calculant le **Character Error Rate** et le **Word Error Rate**.

#

# **Conclusion :**

Les modèles LLMs n'ont pas été aussi performants que le SpellChecker. Ce dernier a donné le meilleur résultat en comparaison avec les différents LLMs et LanguageTool.
