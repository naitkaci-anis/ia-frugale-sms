# Une question à ChatGPT, ça consomme combien ?

**Comparateur de modèles d'IA frugale.** Projet du cours « Développement soutenable », Master 1 Informatique, Université d'Évry Paris-Saclay (novembre 2026).
Équipe : Celina, Zina et Anis.

## La question

Pour une tâche simple, a-t-on besoin d'une grosse IA ? On compare trois IA de tailles très différentes sur la même tâche, **repérer des SMS arnaques**, et on mesure leurs performances, leur temps de calcul et leur consommation d'énergie.

## Les trois IA comparées

| | Modèle | Taille | Utilisation |
|---|---|---|---|
| 1 | Régression logistique (TF-IDF) | environ 7 700 poids | entraînée sur nos SMS |
| 2 | DistilBERT (`distilbert-base-uncased`) | 66 millions de paramètres | pré-entraîné, puis adapté à nos SMS |
| 3 | Mini-chatbot Qwen2.5 (`Qwen/Qwen2.5-0.5B-Instruct`) | 0,5 milliard de paramètres | utilisé tel quel : on lui pose la question pour chaque SMS |

## Les données

*SMS Spam Collection* (Almeida, Gómez Hidalgo et Yamakami, 2011), UCI Machine Learning Repository, licence CC BY 4.0 : 5 574 SMS en anglais, étiquetés normal (`ham`) ou arnaque (`spam`). Le notebook télécharge une copie du fichier officiel.

- Après suppression des doublons : 5 171 SMS, dont 653 spams.
- 80 % pour l'entraînement (4 136 SMS), 20 % pour le test (1 035 SMS), séparation stratifiée avec `random_state=42`.
- Les trois IA sont testées sur les mêmes SMS.

## Ce qu'on mesure

- **Performances** : exactitude (accuracy), précision, rappel et F1 sur la classe spam.
- **Temps** de prédiction par SMS.
- **Énergie et CO₂** avec [CodeCarbon](https://github.com/mlco2/codecarbon), avec l'électricité française, ramenés à 1 000 SMS. Le coût de l'entraînement est mesuré à part.

Sous Windows 11, CodeCarbon mesure réellement l'énergie du processeur (Windows Energy Meter Interface). La mémoire, elle, est estimée.

## Refaire l'expérience

**Prérequis** : Python 3.11 ou plus récent (testé avec Python 3.14.2 sous Windows 11), quelques Go d'espace libre et une connexion Internet pour télécharger les données et les modèles (environ 1,3 Go).

1. Récupérer le projet : bouton **Code → Download ZIP** puis décompresser, ou `git clone` de ce dépôt.
2. Dans l'Invite de commandes Windows, depuis le dossier du projet :
   ```
   py -m venv .venv
   .venv\Scripts\activate
   pip install -r requirements.txt
   jupyter notebook
   ```
   Sous macOS ou Linux : `python3 -m venv .venv` puis `source .venv/bin/activate`.
3. Ouvrir `experience_ia_frugale_v2.ipynb` et lancer toutes les cases dans l'ordre (menu **Run → Run All Cells**).

**Conditions de mesure** : les trois IA sur le même PC, branché sur secteur, avec les autres applications fermées. DistilBERT et le chatbot sont les parties les plus longues. Si le chatbot est trop lent, mettre `NB_TEST_LLM = 200` dans les paramètres : le notebook compare alors les trois IA sur ces mêmes 200 SMS.

**Fichiers produits** :
- `comparison.csv` : résultats d'inférence des trois IA ;
- `entrainement.csv` : coût de l'entraînement ;
- `exactitude_vs_energie.png` : le graphe du poster.

## Résultats

*À compléter après la séance de mesure du 13 novembre 2026.*
PC de mesure : portable Dell, Intel Core i5-8365U, 8 Go de RAM, Windows 11, sans carte graphique.

## Limites

- Un seul PC et une seule tâche.
- CodeCarbon mesure le processeur, mais estime la mémoire (puissance fixe).
- Ne sont pas comptés : le pré-entraînement de DistilBERT et l'entraînement de Qwen, faits par leurs créateurs.
- Le chiffre de ChatGPT (0,34 Wh par question) est une déclaration de Sam Altman (juin 2025), pas une mesure. Une question à ChatGPT n'est pas la même tâche que classer un SMS.

## Références

- Henderson, P., Hu, J., Romoff, J. et al. (2020). *Towards the Systematic Reporting of the Energy and Carbon Footprints of Machine Learning.* Journal of Machine Learning Research, 21, 1-43. https://www.jmlr.org/papers/v21/20-312.html
- Luccioni, A. S., Jernite, Y. et Strubell, E. (2024). *Power Hungry Processing: Watts Driving the Cost of AI Deployment?* ACM FAccT '24. https://doi.org/10.1145/3630106.3658542
- Almeida, T. A., Gómez Hidalgo, J. M. et Yamakami, A. (2011). *Contributions to the Study of SMS Spam Filtering: New Collection and Results.* ACM DocEng '11. Données : https://doi.org/10.24432/C5CC84
- CodeCarbon : https://github.com/mlco2/codecarbon
- Altman, S. (2025). *The Gentle Singularity*, billet de blog, 10 juin 2025.
