# NLP/LLM Project

Ce projet explore l'utilisation de modèles de traitement du langage naturel (NLP) pour la classification de textes extraits d'articles du journal Le Monde. Le travail est divisé en deux parties :

- **Rapport sur la Classification de Texte :** Cette section se concentre sur l'évaluation de différents modèles de classification à l'aide de l'apprentissage par zéro-shot, en utilisant la bibliothèque **Hugging Face**. Divers modèles ont été testés pour classer des articles en fonction de leurs catégories, en mesurant l'exactitude et le temps d'exécution. Le modèle **deberta-v3-base-zeroshot-v1** a été sélectionné comme le meilleur en raison de sa performance et de son efficacité.

- **Rapport sur l'Évaluation de la Qualité des Articles :** Ce rapport traite de l'évaluation de la qualité des articles extraits par OCR, avec des corrections apportées par différents outils, y compris LanguageTool, SpellChecker, et des modèles de langage comme **Mistral**, **Gemini (Bard)**, et **GPT-3.5**. Les résultats mettent en lumière les performances des modèles et des outils de correction, en utilisant des métriques telles que le WER (Word Error Rate) et le BLEU (Bilingual Evaluation Understudy).

Ce projet illustre l'application de techniques avancées de NLP et d'apprentissage automatique pour améliorer la compréhension et la classification des textes, tout en mettant en avant l'importance de la qualité des données dans les processus d'analyse de texte.
