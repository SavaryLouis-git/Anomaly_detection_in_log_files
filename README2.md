# Détection d'Anomalies dans les Fichiers de Logs

Ce package est conçu pour la détection d'anomalies dans des fichiers de logs. Il se compose de deux branches :
- Un **arbre de décision** qui permet de détecter les comportements anormaux à partir de séquences de logs.
- Un **modèle de détection d'anomalie log par log**, basé sur **LanoBERT** pour analyser chaque nouvelle entrée de log.

## Prérequis
De mutiple librairie son nécéssaire pour faire tourner le modèle.
Vous pouvez les installer automatiquement avec un environement conda par le fichier:
- environment.yml

avec la commande suivante:
```bash
conda env create -f environment.yml
```

## Structure du projet

- **programme/** : Contient le programme principal qui surveille les fichiers de logs et déclenche la détection d'anomalies.
  - `start.py` : Script principal qui surveille en permanence un fichier de logs. Ce programme utilise deux variables importantes :
    - `log_file` : Chemin vers le fichier de logs à surveiller.
    - `python_program` : Chemin vers le modèle à utiliser pour l'analyse (LanoBERT ou autre).
    
  Dès qu'une nouvelle entrée est ajoutée au fichier de logs, le modèle choisi est lancé pour analyser la nouvelle ligne.

- **lanobert/inference/** : Contient le code pour l'analyse des logs à l'aide du modèle **LanoBERT**.
  - `detection3.py` : Code qui utilise LanoBERT pour analyser la dernière ligne du fichier de logs. 
    - `n_logs` : Variable qui détermine combien de lignes de logs doivent être analysées simultanément. Par défaut, elle est à `1`,
                 car le programme `start.py` appelle le modèle à chaque nouvelle ligne de log.
    - `fichier_de_log` : Chemin vers le fichier de logs à analyser. Il doit être le même que celui défini dans `log_file` de `start.py`.

## Paramétrages des modèles

1. **Arbre de décision** :
    - Dans le fichier `programme/start.py`, définissez :
      - La variable `log_file` avec le chemin complet vers le fichier de logs à surveiller.
      - La variable `python_program` avec le chemin vers le modèle (ex. : `lanobert/inference/detection3.py`).
    
2. **Modèle LanoBERT** :
    - Dans `lanobert/inference/detection3.py` :
      - Modifiez la variable `fichier_de_log` pour qu'elle corresponde au chemin du fichier de logs défini dans `start.py`.
      - La variable `n_logs` est initialement à `1` pour analyser la dernière ligne de logs. Vous pouvez l'ajuster selon vos besoins.
      - La variable `threshold` est initialement à 0.5 c'est la probabilité à la quelle notre modèle tranche la probabilité résultante de la prédéction normale/anormale.
# Résultat en Sortie

- 'detection3.py' renvoie "aucune erreur detecter" si la log est normale,
          et renvoie "La log suivante est une anomalie:" puis la log concerné.


## Exemple d'exécution
Une fois les chemins de fichier correctement paramétrés.

```bash
python programme/start.py
```

## Principe de fonctionnement

**Enssemble des programmes** :

**Modèle LanoBERT** :
Le modèle LanoBERT utilise une approche de masquage et de prédiction pour détecter les anomalies dans les logs. À chaque étape, un token du log est masqué, et le modèle tente de prédire ce que serait ce token si le log était normal, en retournant la probabilité associée à cette prédiction. Le processus est répété pour chaque token masqué, et le programme analyse la distribution des probabilités obtenues. En se basant sur la moyenne basse de ces probabilités, il peut déterminer si le log est considéré comme normal ou anormal.
![Schéma fonctionnement](Lanobert_décision.png)

