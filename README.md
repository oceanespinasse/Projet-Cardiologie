# Diagnostic ECG automatique à l'aide d'un réseau de neurones profonds
Scripts et modules pour la formation et le test des réseaux de neurones profonds pour la classification automatique des ECG. Code d'accompagnement de l'article "Diagnostic automatique de l'ECG à 12 dérivations à l'aide d'un réseau de neurones profonds".
 https://www.nature.com/articles/s41467-020-15432-4.

--------

Citation:
```
Ribeiro, A.H., Ribeiro, M.H., Paixão, G.M.M. et al. Automatic diagnosis of the 12-lead ECG using a deep neural network.
Nat Commun 11, 1760 (2020). https://doi.org/10.1038/s41467-020-15432-4
```

Bibtex:
```
@article{ribeiro_automatic_2020,
  title = {Automatic Diagnosis of the 12-Lead {{ECG}} Using a Deep Neural Network},
  author = {Ribeiro, Ant{\^o}nio H. and Ribeiro, Manoel Horta and Paix{\~a}o, Gabriela M. M. and Oliveira, Derick M. and Gomes, Paulo R. and Canazart, J{\'e}ssica A. and Ferreira, Milton P. S. and Andersson, Carl R. and Macfarlane, Peter W. and Meira Jr., Wagner and Sch{\"o}n, Thomas B. and Ribeiro, Antonio Luiz P.},
  year = {2020},
  volume = {11},
  pages = {1760},
  doi = {https://doi.org/10.1038/s41467-020-15432-4},
  journal = {Nature Communications},
  number = {1}
}
```
-----

## Condition

Ce code a été testé sur Python 3 avec Tensorflow `2.2`. Il existe une branche plus ancienne ([`tensorflow-v1`](https://github.com/antonior92/automatic-ecg-diagnosis/tree/tensorflow-v1)) qui contient l'implémentation du code pour Tensorflow `1.15`.

## Modèle

Le modèle utilisé dans l'article est un neurone résiduel. L'implémentation de l'architecture de réseau neuronal dans Keras est disponible dans ``model.py``. Pour imprimer un résumé des couches du modèle exécutées :
```bash
$ python model.py
```

![resnet](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41467-020-15432-4/MediaObjects/41467_2020_15432_Fig3_HTML.png?as=webp)

Le modèle reçoit un tenseur d'entrée de dimension `(N, 4096, 12)` et renvoie un tenseur de sortie de dimension `(N, 6)`, pour lequel `N` est la taille du lot.

Le modèle peut être entraîné à l'aide du script `train.py`. Alternativement, des pesées pré-entraînées pour les modèles décrits dans le document sont également disponibles sur : https://doi.org/10.5281/zenodo.3625017. Ou dans le lien de la boîte de dépôt miroir [here](https://www.dropbox.com/s/5ar6j8u9v9a0rmh/model.zip?dl=0).
A l'aide de la ligne de commande, les poids peuvent être téléchargés en utilisant
```
wget https://www.dropbox.com/s/5ar6j8u9v9a0rmh/model.zip?dl=0 -O model.zip
unzip model.zip
```

- **entrée** : `shape = (N, 4096, 12)`. Le tenseur d'entrée doit contenir les `4096` points des tracés ECG échantillonnés à `400Hz` (c'est-à-dire un signal d'environ 10 secondes). À la fois dans l'entraînement et dans l'ensemble de test, lorsque le signal n'était pas assez long, nous avons rempli le signal de zéros, donc 4096 points ont été atteints. La dernière dimension du tenseur contient les points des 12 dérivations différentes. Les dérivations sont ordonnées dans l'ordre suivant : `{DI, DII, DIII, AVR, AVL, AVF, V1, V2, V3, V4, V5, V6}`. Tous les signaux sont représentés sous forme de nombres à virgule flottante de 32 bits à l'échelle 1e-4V : donc si le signal est en V, il doit être multiplié par 1000 avant de l'alimenter au modèle de réseau de neurones.

- **sortie** : `shape = (N, 6)`. Chaque entrée contient une probabilité comprise entre 0 et 1, et peut être comprise comme la probabilité qu'une anomalie donnée soit présente. Les anomalies qu'il prédit sont **(dans cet ordre)** : bloc AV du 1er degré (1dAVb), bloc de branche droit (RBBB), bloc de branche gauche (BGB), bradycardie sinusale (SB), fibrillation auriculaire (FA), tachycardie sinusale ( ST). Les anomalies ne sont pas mutuellement exclusives, de sorte que les probabilités ne s'additionnent pas nécessairement à un.

![abnormalities](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41467-020-15432-4/MediaObjects/41467_2020_15432_Fig1_HTML.png?as=webp)

## Données de test

L'ensemble de données de test décrit dans le document peut être téléchargé à partir de : https://doi.org/10.5281/zenodo.3625006. Ou dans le lien de la boîte de dépôt miroir [here](https://www.dropbox.com/s/p3vd3plcbu9sf1o/data.zip?dl=0). En utilisant la ligne de commande :
The testing dataset described in the paper can be downloaded from:
```
wget https://www.dropbox.com/s/p3vd3plcbu9sf1o/data.zip?dl=0 -O data.zip
unzip data.zip
```


## Données d'entraînement

Une partie des données d'entraînement est désormais disponible en libre accès sur https://doi.org/10.5281/zenodo.4916206.


## Scripts

- ``train.py``: script pour l'entraînement du réseau de neurones. Pour entraîner le réseau de neurones, exécutez :
```bash
$ python train.py PATH_TO_HDF5 PATH_TO_CSV
```
Les modèles pré-entraînés obtenus à l'aide d'un tel script peuvent être téléchargés [here](https://doi.org/10.5281/zenodo.3625017)


- ``predict.py``: Script pour générer les prédictions du réseau de neurones sur un jeu de données donné.
```bash
$ python predict.py PATH_TO_HDF5_ECG_TRACINGS PATH_TO_MODEL  --ouput_file PATH_TO_OUTPUT_FILE 
```
Le dossier `./dnn_predicts` contient la sortie obtenue en appliquant ce script aux modèles disponibles [here](https://doi.org/10.5281/zenodo.3625017) pour faire les prédictions sur les tracés à partir de [cet ensemble de données de test](https://doi.org/10.5281/zenodo.3625006).


- ``generate_figures_and_tables.py``:  générer des figures et des tableaux à partir de l'article "Automatic Diagnosis o the Short-Duration12-Lead ECG using a Deep Neural Network". Assurez-vous d'exécuter le script à partir du dossier racine afin que tous les chemins relatifs soient corrects. Donc première course :
```
$ cd /path/to/automatic-ecg-diagnosis
```
Puis le script
 ```bash
$ python generate_figures_and_tables.py
```
Il doit générer les tableaux et figurer dans le dossier `outputs/`

- ``model.py``: module auxiliaire qui définit l'architecture du réseau de neurones profonds. Pour imprimer un résumé des couches du modèle exécutées :
```bash
$ python model.py
```
