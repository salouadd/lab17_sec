# LabSec17 – Reverse Engineering Android : Analyse Statique et Native



---

## Contexte

Ce laboratoire est consacré à l'analyse de l'application pédagogique **OWASP MSTG UnCrackable Level 3**.

L'objectif est d'étudier les différentes couches de protection présentes dans l'application en combinant plusieurs approches :

* Analyse du code Java décompilé ;
* Inspection des fichiers Smali ;
* Étude des bibliothèques natives ;
* Reconstruction d'une application modifiée dans un environnement de laboratoire.

Le challenge UnCrackable Level 3 constitue un excellent support pédagogique car il combine protections Java, logique native et mécanismes de vérification d'intégrité.

---

## Objectifs du Laboratoire

* Analyser l'application avec Jadx.
* Identifier les mécanismes de protection présents dans le code Java.
* Décompiler l'APK avec Apktool.
* Examiner la structure Smali générée.
* Étudier les bibliothèques natives embarquées.
* Comprendre le rôle de la bibliothèque `libfoo.so`.
* Reconstruire et signer l'application dans l'environnement de test.
* Vérifier les résultats obtenus pendant l'analyse.

---

## Environnement

| Élément            | Valeur                         |
| ------------------ | ------------------------------ |
| Application        | OWASP MSTG UnCrackable Level 3 |
| Plateforme         | Android Emulator               |
| Décompilateur Java | Jadx-GUI                       |
| Outil APK          | Apktool                        |
| Analyse native     | Outil de reverse engineering   |
| Signature APK      | apksigner                      |
| Validation         | Script Python                  |

---

## Architecture du Laboratoire

```text
+----------------------+
| APK UnCrackable L3   |
+----------+-----------+
           |
           |
    +------v------+
    |    Jadx     |
    +-------------+
           |
           |
    +------v------+
    |   Apktool   |
    +-------------+
           |
           |
    +------v------+
    |   Smali     |
    +-------------+
           |
           |
    +------v------+
    |  libfoo.so  |
    +-------------+
           |
           |
    +------v------+
    | Validation  |
    |   Python    |
    +-------------+
```

Cette approche permet d'étudier successivement les couches Java, Smali et natives de l'application.

---

## Analyse Java avec Jadx

L'application est d'abord ouverte dans Jadx-GUI afin d'obtenir une vue lisible du code Java décompilé.

### MainActivity

![MainActivity](screenshots/Capture d'écran 2026-05-31 205157.png)

La classe `MainActivity` constitue le point d'entrée principal de l'application.

---

## Identification des Mécanismes de Protection

L'analyse du code révèle plusieurs vérifications exécutées lors du démarrage de l'application.

### Vérifications Observées

![Protections](screenshots/Capture d'écran 2026-05-31 205231.png)

Les contrôles identifiés concernent notamment :

* l'intégrité de l'application ;
* certaines vérifications d'environnement ;
* la variable `tampered` ;
* différents mécanismes de protection utilisés par le challenge.

---

## Chargement de la Bibliothèque Native

Le code Java met également en évidence le chargement d'une bibliothèque native.

### Bibliothèque Native

![Libfoo](screenshots/Capture d'écran 2026-05-31 205333.png)

La présence de :

```text
libfoo.so
```

indique qu'une partie importante de la logique applicative est exécutée dans le code natif.

---

## Suivi du Flux d'Exécution

L'inspection du code Java permet de suivre le chemin emprunté avant l'appel vers les fonctions natives.

### Inspection Java

![Inspection Java](screenshots/Capture d'écran 2026-05-31 205352.png)

Cette étape permet de comprendre la transition entre la logique Java et la logique native.

---

## Décompilation avec Apktool

L'application est ensuite décompilée avec Apktool afin d'obtenir une structure modifiable.

### Décompilation APK

![Apktool](screenshots/Capture d'écran 2026-05-31 205457.png)

Le résultat contient notamment :

* les fichiers Smali ;
* les ressources Android ;
* les bibliothèques natives ;
* le manifeste Android.

---

## Analyse des Fichiers Smali

L'analyse des fichiers Smali permet de localiser certaines parties du flux applicatif.

### Localisation des Contrôles

![Message Protection](screenshots/Capture d'écran 2026-05-31 205756.png)

Cette étape facilite l'identification des sections du programme impliquées dans les mécanismes de protection.

---

## Modification du Code Smali

Les fichiers Smali sont ensuite étudiés afin de comprendre leur rôle dans l'exécution de l'application.

### Inspection Smali

![Patch Smali](screenshots/Capture d'écran 2026-05-31 205930.png)

L'analyse permet de suivre plus précisément le comportement de l'application au niveau du bytecode Android.

---

## Analyse Native

L'étape suivante consiste à examiner la bibliothèque native embarquée dans l'APK.

### Inspection de libfoo.so

![Analyse Native](screenshots/Capture d'écran 2026-05-31 211227.png)

L'étude de cette bibliothèque permet de localiser les fonctions impliquées dans les mécanismes de validation implémentés par l'application.

---

## Vérification des Fonctions Natives

Une inspection complémentaire est réalisée afin d'identifier les fonctions importantes du programme.

### Vérification du Flux Natif

![Vérification Native](screenshots/Capture d'écran 2026-05-31 211408.png)

Cette étape confirme la relation entre le code Java observé précédemment et les fonctions exécutées dans la bibliothèque native.

---

## Validation des Résultats

Un script Python est utilisé pour reproduire et vérifier la logique identifiée pendant l'analyse.

### Calcul et Vérification

![Calcul Secret](screenshots/Capture d'écran 2026-05-31 212248.png)

Cette validation permet de confirmer la compréhension du fonctionnement observé durant l'étude du binaire.

---

## Fonction Native Principale

L'analyse met en évidence la fonction native utilisée dans le mécanisme de validation.

### Fonction de Vérification

![Fonction Native](screenshots/Capture d'écran 2026-05-31 212327.png)

Cette fonction constitue l'un des éléments centraux du challenge pédagogique.

---

## Reconstruction de l'Application

Après l'analyse, l'application est reconstruite dans l'environnement de laboratoire.

### Commandes Utilisées

```bash
apktool b uncrackable3 -o UnCrackable-Level3-patched.apk

apksigner sign \
--ks "%USERPROFILE%\.android\debug.keystore" \
UnCrackable-Level3-patched.apk

adb uninstall owasp.mstg.uncrackable3

adb install -r UnCrackable-Level3-patched.apk
```

Ces opérations permettent de générer une nouvelle version de l'application pour les tests réalisés dans le laboratoire.

---

## Analyse des Résultats

| Élément analysé             | Résultat |
| --------------------------- | -------- |
| Décompilation Java          | Réussie  |
| Analyse Jadx                | Réussie  |
| Décompilation Apktool       | Réussie  |
| Inspection Smali            | Réussie  |
| Analyse native              | Réussie  |
| Identification de libfoo.so | Réussie  |
| Validation Python           | Réussie  |
| Reconstruction APK          | Réussie  |
| Réinstallation Android      | Réussie  |

---

## Résultats

Les objectifs du laboratoire ont été atteints :

* ✅ Analyse complète de l'application.
* ✅ Étude des mécanismes de protection Java.
* ✅ Inspection du code Smali.
* ✅ Analyse de la bibliothèque native.
* ✅ Compréhension du flux d'exécution.
* ✅ Validation des observations par script Python.
* ✅ Reconstruction et signature de l'application.
* ✅ Réinstallation dans l'environnement Android de test.

---

## Conclusion

Ce laboratoire a permis d'explorer les principales techniques utilisées lors d'une analyse reverse engineering Android.

La combinaison de Jadx, Apktool, de l'analyse Smali et de l'étude des bibliothèques natives offre une vision complète du fonctionnement interne d'une application Android. L'exercice met également en évidence l'importance des composants natifs dans les mécanismes de protection modernes.

Cette démarche complète les travaux réalisés dans les laboratoires précédents consacrés à l'analyse statique, l'instrumentation dynamique avec Frida, l'observation réseau avec Burp Suite ainsi que l'étude des composants Android avec Drozer.

---


