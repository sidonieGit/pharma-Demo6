# Projet Pharma6 - Application de Gestion de Médicaments (Spring XML, HikariCP, JDBC Réel)

Ce dépôt contient la sixième itération du projet "Pharma". Cette version établit une **connexion réelle à une base de données MySQL** en utilisant **HikariCP** (un pool de connexions performant) et revient à la **configuration XML de Spring** pour la définition des beans. Elle intègre également **Log4j** pour une gestion plus fine des logs.

## Contexte

- **Pharma1-4** : Progression de l'injection de dépendances et de la configuration Spring (Java pur -> DI manuelle -> Spring XML -> Spring Annotations).
- **Pharma5** : Introduction du code JDBC, mais avec un DAO toujours mocké pour la persistance réelle.
- **Pharma6** : Passage à une **persistance réelle et performante** :
  - Utilisation de **Spring XML** pour la configuration des beans.
  - Implémentation d'un **pool de connexions HikariCP** pour gérer les connexions à la base de données.
  - Le `MedicamentDao` utilise la `DataSource` fournie par HikariCP pour interagir avec une base de données MySQL réelle.
  - Les informations de connexion à la base de données sont **codées en dur dans le fichier XML de configuration Spring**.
  - Intégration de **Log4j** pour la journalisation.

## Fonctionnalités (Démo 6)

- **Gestion des Médicaments** : Opérations CRUD (Création, Lecture, Mise à jour, Suppression) sur des médicaments, persistées dans une base de données MySQL réelle.
- **Persistance Réelle via JDBC/HikariCP** :
  - Le `MedicamentDao` est configuré pour recevoir une `DataSource` (fournie par HikariCP) via injection de dépendances.
  - HikariCP gère le pool de connexions à la base de données, améliorant les performances et la robustesse.
- **Conteneur Spring avec Configuration XML** :
  - Le fichier de configuration des beans Spring (ex: `applicationContext.xml` ou `demo-beans.xml`) est utilisé pour définir le `HikariDataSource`, le `MedicamentDao` et le `ServiceMedicament`.
  - **⚠️ Avertissement :** Ce fichier XML contient des informations sensibles (URL de la DB, utilisateur, mot de passe). Pour des raisons de sécurité, **ce fichier XML n'est PAS committé sur le dépôt Git distant**. Un exemple de fichier `applicationContext.xml.example` (ou similaire) sans les informations sensibles peut être fourni à titre indicatif.
- **Gestion des Logs avec Log4j** : L'application utilise Log4j pour générer des messages de log, ce qui est essentiel pour le débogage et le monitoring.

## Technologies Utilisées

- Java (JDK 8 ou supérieur recommandé)
- **Maven** (pour la gestion des dépendances et du build)
- **Spring Framework** (en particulier `spring-context` pour l'IoC et l'intégration JDBC)
- **HikariCP** (pool de connexions JDBC)
- **MySQL Database** (le projet attend une base de données MySQL locale).
- **MySQL Connector/J** (dépendance Maven pour le driver JDBC)
- **Log4j 2** (pour la journalisation)

## Préparation de l'Environnement et Exécution

Pour tester cette application, vous devez configurer votre environnement :

1.  **Installer et Démarrer MySQL :** Assurez-vous d'avoir un serveur de base de données MySQL opérationnel et accessible sur `localhost:3306`.

2.  **Créer la Base de Données :** Connectez-vous à MySQL et créez une base de données nommée `syspharma`.

    ```sql
    CREATE DATABASE syspharma;
    USE syspharma;
    ```

3.  **Créer la Table `Medicament` :** Exécutez le script SQL suivant dans votre base de données `syspharma` :

    ```sql
    CREATE TABLE Medicament (
        id INT AUTO_INCREMENT PRIMARY KEY,
        designation VARCHAR(255) NOT NULL,
        prix DOUBLE NOT NULL,
        description TEXT,
        image VARCHAR(255),
        categorie VARCHAR(255)
    );
    ```

4.  **Créer l'Utilisateur et lui donner les droits :** L'application utilise l'utilisateur `YOUR_DB_USERNAME` avec le mot de passe `YOUR_DB_PASSWORD` (selon votre XML). Assurez-vous que cet utilisateur existe et a les privilèges nécessaires sur la base `syspharma`. **Il est fortement recommandé de créer un utilisateur dédié avec des privilèges minimaux pour une application réelle, plutôt que d'utiliser `root`.**
    _(Exemple pour un utilisateur dédié, si vous ne voulez pas utiliser root:)_

    ```sql
    CREATE USER 'springuser'@'localhost' IDENTIFIED BY 'springpassword';
    GRANT ALL PRIVILEGES ON syspharma.* TO 'springuser'@'localhost';
    FLUSH PRIVILEGES;
    ```

    \*Si vous créez un utilisateur dédié, **vous devrez modifier les valeurs `username` et `password` dans votre `applicationContext.xml` localement.\***

5.  **Prérequis Java/Maven :**

    - Assurez-vous d'avoir le [JDK (Java Development Kit)](https://www.oracle.com/java/technologies/downloads/) installé (version 8 ou supérieure).
    - Assurez-vous d'avoir [Maven](https://maven.apache.org/download.cgi) installé et configuré.

6.  **Cloner le dépôt :**

    ```bash
    git clone https://github.com/sidonieGit/pharma-Demo6.git
    cd Pharma6
    ```

7.  **Dans votre fichier de configuration Spring localement :**
    `demo-beans.xml` dans `src/main/resources` , vous devez manuellement renseigner les informations de connexion à votre base de données MySQL locale.

        ```

8.  **Construire le projet et télécharger les dépendances (via Maven) :**

    ```bash
    mvn clean install
    ```

    Cela compilera le code et téléchargera les dépendances Spring, HikariCP, MySQL Connector/J et Log4j.

9.  **Exécuter l'application (depuis l'IDE) :**
    - Importez le projet `Pharma6` dans votre IDE (IntelliJ IDEA, Eclipse, VS Code) comme un projet Maven existant.
    - Exécutez la classe `com.sidoCop.sysPharma.launcher.Laucher` en tant qu'application Java.
    - Vous devriez voir les messages de console indiquant le chargement du contexte Spring, l'utilisation du pool de connexions, et les interactions réelles avec la base de données. Les logs de Log4j devraient également apparaître.

## Prochaines Étapes Possibles

- **Externalisation des propriétés de connexion DB** : La prochaine étape cruciale est de :
- déplacer les informations sensibles (`jdbcUrl`, `username`, `password`) de `applicationContext.xml` vers un fichier de propriétés externe (ex: `datasource.properties`), qui sera également ignoré par Git. Ces propriétés seront ensuite injectées dans le bean `dataSourceSk` via Spring.
- Spring charge ces propriétés et les utilise pour configurer le pool de connexions HikariCP. La configuration des beans reste en XML et l'application interagit avec une base de données MySQL réelle.

---

**Auteur :** Sidonie sidoniedjuissifohouo@gmail.com---

www.linkedin.com/in/sidonie-djuissi-fohouo

**Date :** 12 juin 2025
