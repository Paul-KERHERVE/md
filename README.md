# Présentation du Projet : Outil de synchronisation inter-logiciels

## Résumé exécutif

Ce document présente l'outil développé pour harmoniser les données entre deux systèmes d'information utilisés au sein de l'entité IT Risk de PF. L'objectif est d'éliminer les doubles saisies et de maintenir une cohérence entre les données exportées par deux logiciels différents. 

## Problématique

### Contexte initial
L'organisation utilise deux logiciels distincts, ServiceNow et MyAPM, qui contiennent des données partiellement redondantes. Les mises à jour sont effectuées uniquement dans le premier système sur les données qui nous intéressent ici, mais ces modifications doivent être reportées dans le second système. 
Ce n'est pas le cas actuellement car la solution de la communication "directe" entre ServiceNow et MyAPM à travers leurs API ne peut être développée en raison de l'absence d'un collaborateur. Il a donc été décidé de développer un script permettant de temporairement pallier à problème.

### Défis identifiés
1. **Formats d'exportation différents** : Le premier logiciel (ServiceNow) exporte en CSV (choix à effectuer à l'export), tandis que second (MyAPM) n'offre que l'exportation XLSX (format Excel)
2. **Nomenclature non standardisée** : 
   - Les noms des colonnes varient entre les deux systèmes (ex: "var" vs "variable")
   - Les valeurs d'attributs peuvent être formatées différemment (ex: "A4" vs "4A")
3. **Synchronisation partielle** : Seuls certains champs doivent être mis à jour, les autres données spécifiques du second système devant être préservées
4. **Contraintes techniques** : Impossibilité d'utiliser des bibliothèques externes
5. **Collaboration inter-services** : Besoin de partager facilement le fichier mis à jour avec l'équipe responsable du second système

### Impact opérationnel actuel
Sans solution automatisée, le processus actuel est :
- Propice aux erreurs : manipulations manuelles complexes entre fichiers
- Non standardisé : chaque utilisateur développe sa propre méthode
- Risqué : possibilité d'écraser des données importantes spécifiques au second système

## Solution développée

### Vue d'ensemble
Un script Python a été développé pour automatiser le processus de synchronisation. Il permet de :
- Convertir automatiquement les fichiers XLSX en CSV en utilisant Excel via VBScript
- Identifier les enregistrements communs entre les fichiers source et cible via un identifiant unique
- Mettre à jour les données du fichier cible avec celles du fichier source en respectant les correspondances de colonnes
- Préserver toutes les données spécifiques au fichier cible
- Exporter directement le fichier mis à jour vers un espace SharePoint partagé via l'accès réseau Windows

### Caractéristiques clés

#### 1. Conversion XLSX vers CSV sans dépendances externes
Le script utilise Excel via VBScript pour convertir les fichiers XLSX en CSV de manière fidèle sans nécessiter l'installation de bibliothèques externes. Cette méthode:
- Utilise Excel déjà installé sur la machine
- Ne requiert aucune installation de module Python
- Assure une conversion parfaitement fidèle à l'original
- Fonctionne même avec des fichiers volumineux

#### 2. Système de correspondance avancé
Un système de correspondance configurable pour gérer :
- Les différences de noms de colonnes
- Les différences de formatage des valeurs d'attributs

#### 3. Optimisation des performances
Le script utilise des structures de données efficaces (dictionnaires imbriqués) et minimise les lectures/écritures pour garantir de bonnes performances même avec des milliers d'enregistrements.

#### 4. Intégration SharePoint simplifiée
Utilisation des lecteurs réseau Windows déjà configurés pour accéder directement à SharePoint, permettant:
- Un accès direct aux espaces partagés
- Aucune configuration d'authentification supplémentaire
- Un partage immédiat des fichiers mis à jour avec les autres services

### Architecture technique

Le script s'articule autour de ces composants principaux :
1. **Conversion de formats** : Utilisation d'Excel via VBScript pour convertir XLSX en CSV
2. **Lecture optimisée** : Chargement des données en mémoire sous forme de dictionnaires pour un accès rapide
3. **Système de correspondance** : Mapping configurable entre les noms de colonnes et les formats de valeurs
4. **Moteur de mise à jour** : Identification et mise à jour sélective des données sans altérer la structure cible
5. **Export direct** : Sauvegarde du fichier mis à jour directement sur le lecteur réseau SharePoint

## Mode d'emploi

### Prérequis
- Python >= 3.6 (version 3.12 téléchargeable depuis le centre logiciel)
- Excel installé sur la machine
- Accès au lecteur réseau SharePoint mappé

### Configuration initiale
Les correspondances entre colonnes sont définies directement dans le script, permettant une adaptation facile aux spécificités des systèmes source et cible.

### Utilisation
1. Exporter les données du premier logiciel (source) en CSV ou XLSX
2. Exporter les données du second logiciel (cible) en CSV ou XLSX
3. Nommer les fichiers selon la convention définie ou les placer dans les répertoires surveillés
4. Lancer le script
5. Le fichier résultant est automatiquement déposé sur l'espace SharePoint partagé

## Conclusion

Cette solution répond efficacement aux défis identifiés sans utiliser de bibliothèques externes. Elle permet une synchronisation fiable des données entre deux systèmes distincts, tout en s'adaptant aux différences de nomenclature et de formats. L'intégration avec SharePoint via les lecteurs réseau Windows facilite le partage entre services, complétant ainsi l'automatisation du processus de bout en bout.

La méthode de conversion XLSX vers CSV utilisant Excel via VBScript est particulièrement adaptée à notre environnement restrictif, garantissant une conversion fidèle sans nécessiter d'installations supplémentaires.
