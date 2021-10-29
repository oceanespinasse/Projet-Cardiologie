# Prédictions du modèle sur l'ensemble de test
Ce dossier contient les prédictions du réseau de neurones profonds sur l'ensemble de test. Tous les fichiers sont au format `.npy` et peuvent être lus à l'aide de `numpy.load()`. Chacun doit contenir un

Tout le contenu de ce dossier peut être généré à l'aide de la séquence de commandes suivante :

(sans GPU, cela devrait prendre environ 25 minutes. Avec l'accélération GPU, cela devrait prendre moins d'une minute)

 ```bash
cd /path/to/automatic-ecg-diagnosis
PFOLDER="./dnn_predicts"
MFOLDER="./model"
DFOLDER="./data"

# Pour générer les prédictions sur le jeu de test correspondant au modèle principal utilisé tout au long de l'utilisation du papier :

python predict.py $DFOLDER/ecg_tracings.hdf5 $MFOLDER/model.hdf5 --output_file $PFOLDER/model.npy


# Nous formons également plusieurs réseaux avec la même architecture et la même configuration
# mais avec des graines initiales différentes. Afin de générer le réseau de neurones
# prédiction sur l'ensemble de test pour chacun de ces modèles :

mkdir $FNAME/other_seeds
for n in 1 2 3 4 5 6 7 8 9 10
do
python predict.py $DFOLDER/ecg_tracings.hdf5 $MFOLDER/other_seeds/model_$n.hdf5 --output_file $PFOLDER/other_seeds/model_$n.npy
done


# Enfin, pour évaluer l'effet de la façon dont nous structurons notre problème, nous avons envisagé des solutions alternatives
# cenarios où nous utilisons des fractionnements 90\%-5\%-5\%, stratifiés aléatoirement,
# par patient ou par ordre chronologique. Les prédictions de ces modèles dans l'ensemble de test
# peut être obtenu en utilisant :

mkdir $PFOLDER/other_splits
for n in date_order individual_patients normal_order
do
python predict.py $DFOLDER/ecg_tracings.hdf5 $MFOLDER/other_splits/model_$n.hdf5 --output_file $PFOLDER/other_splits/model_$n.npy
done
```

Où `DFOLDER` doit donner le chemin d'accès au dossier contenant l'ensemble de données de test et `MFOLDER` doit pointer vers le dossier contenant les modèles pré-entraînés. L'ensemble de données de test peut être téléchargé à partir d'[here](https://doi.org/10.5281/zenodo.3625006) et les modèles pré-entraînés peuvent être téléchargés à partir d'[here](https://doi.org/10.5281/zenodo.3625017)
