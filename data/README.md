# Ensemble de données ECG 12 dérivations annoté

Contient 827 tracés ECG de différents patients, annotés par plusieurs cardiologues, résidents et étudiants en médecine.
Il est utilisé comme ensemble de test sur le papier:
"Diagnostic automatique de l'ECG 12 dérivations à l'aide d'un réseau de neurones profonds". 
https://www.nature.com/articles/s41467-020-15432-4.

Il contient des annotations sur 6 anomalies ECG différentes :
- Bloc AV du 1er degré (1dAVb) ;
- bloc de branche droit (RBBB);
- bloc de branche gauche (LBBB) ;
- bradycardie sinusale (SB);
- fibrillation auriculaire (FA); et,
- tachycardie sinusale (TS).

Les scripts python compagnons sont disponibles sur : 
https://github.com/antonior92/automatic-ecg-diagnosis


--------

Citation
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


## Contenu du dossier :

- `ecg_tracings.hdf5`:  ce fichier n'est pas disponible sur le dépôt github en raison de sa taille. Mais il peut être télécharger
[here](https://doi.org/10.5281/zenodo.3625006). Le fichier HDF5 contenant un seul jeu de données nommé `tracings`. Cet ensemble de données est un tenseur `(827, 4096, 12)`. La première dimension correspond aux 827 examens différents de différents patients ; la deuxième dimension correspond aux 4096 échantillons de signal ; la troisième dimension aux 12 dérivations différentes des examens ECG dans l'ordre suivant : `{DI, DII, DIII, AVL, AVF, AVR, V1, V2, V3, V4, V5, V6}`.

Les signaux sont échantillonnés à 400 Hz. Certains signaux ont à l'origine une durée de 10 secondes (10 * 400 = 4000 échantillons) et d'autres de 7 secondes (7 * 400 = 2800 échantillons). Afin qu'ils aient tous la même taille (4096 échantillons), nous les remplissons de zéros sur les deux tailles. Par exemple, pour un signal ECG de 7 secondes avec 2800 échantillons, nous incluons 648 échantillons au début et 648 échantillons à la fin, ce qui donne 4096 échantillons qui sont eux enregistrés dans l'ensemble de données hdf5. Tous les signaux sont représentés sous forme de nombres à virgule flottante à l'échelle 1e-4V : il faut donc le multiplier par 1000 afin d'obtenir les signaux en V.

En python, on peut lire ce fichier en suivant la séquence suivante :
```python
import h5py
with h5py.File(args.tracings, "r") as f:
    x = np.array(f['tracings'])
```

- Le fichier `attributes.csv` contient les attributs de base du patient : sexe (M ou F) et âge. Il contient 827 lignes (plus l'en-tête). Le i-ème tracé dans `ecg_tracings.hdf5` correspond à la i-ème ligne.
- `annotations/`: dossier contenant les annotations au format csv. Chaque fichier csv contient 827 lignes (plus l'en-tête). La i-ème ligne correspond au i-ème tracé dans `ecg_tracings.hdf5` correspond au dans tous les fichiers csv. Les fichiers csv ont tous 6 colonnes `1dAVb, RBBB, LBBB, SB, AF, ST` correspondant au temps que l'annotateur a détecté l'anomalie dans l'ECG (`=1`) ou non (`=0`).
  1. `cardiologist[1,2].csv` contient des annotations de deux cardiologues différents.
  2. `gold_standard.csv` annotation d'étalon-or pour cet ensemble de données de test. Lorsque le cardiologue 1 et le cardiologue 2 étaient d'accord, le diagnostic commun était considéré comme le gold standard. En cas de désaccord, un troisième spécialiste principal, au courant des annotations des deux autres, décidait du diagnostic.
  3. `dnn.csv` prédiction dnn.csv à partir du réseau de neurones profonds décrit dans "Diagnostic automatique de l'ECG de courte durée à 12 dérivations à l'aide d'un réseau de neurones profonds". Le seuil est fixé de manière à maximiser le score F1.
  4. `cardiology_residents.csv` annotations de deux résidents en cardiologie de 4e année (chacun annoté la moitié de l'ensemble de données).
  5. `emergency_residents.csv` annotations de deux résidents d'urgence de 3e année (chacun annoté la moitié de l'ensemble de données).
  6. `medical_students.csv` annotations de deux étudiants en médecine de 5e année (chacun annoté la moitié de l'ensemble de données).
