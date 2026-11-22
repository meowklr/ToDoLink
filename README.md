# ToDoLink

Application web de gestion de tâches réalisée avec Laravel. Elle permet de suivre des tâches sur un tableau de bord, d'en créer avec une date limite, une priorité, des collaborateurs et une pièce jointe, et d'envoyer les échéances vers un scénario Make.com. Projet réalisé dans le cadre d'un devoir de groupe en 1ère année d'études.

## Fonctionnalités

L'application repose sur trois vues principales :
- une page d'accueil (`/`) avec accès à l'espace utilisateur
- un tableau de bord (`/dashboard`) affichant les tâches triées par date limite, avec téléchargement des pièces jointes (`/tasks/{task}/download`)
- un formulaire d'ajout de tâche (`/task-add` ou `/tasks/create`) gérant le titre, la description, la date limite, la priorité (`Haute`, `Moyenne`, `Basse`), la sélection de collaborateurs et l'envoi d'un fichier (10 Mo maximum, stocké dans `storage/app/tasks`)

L'authentification (inscription, connexion, gestion du profil) est assurée par Laravel Breeze. Lors de la création d'une tâche, `App\Services\CalendarService` peut également envoyer un appel HTTP POST vers un webhook Make.com pour créer un événement de calendrier.

## Stack technique

Backend : PHP 8.3+, Laravel 13, Laravel Breeze.
Frontend : vues Blade, CSS (`public/style.css`) et JavaScript (`public/task_add.js`, `public/account_menu.js`), avec Vite 8, Tailwind CSS et Alpine.js configurés dans `package.json`.
Base de données : SQLite par défaut (configurable dans `.env`).
Tests et formatage : Pest 4, PHPUnit, Laravel Pint.

## Prérequis

- PHP 8.3 ou supérieur (avec les extensions requises par Laravel, dont `pdo_sqlite` pour SQLite)
- Composer
- Node.js et npm

## Installation et lancement

1. Cloner le dépôt et installer les dépendances :

```bash
composer install
npm install
```

2. Créer le fichier d'environnement et générer la clé d'application :

```bash
cp .env.example .env
php artisan key:generate
```

Sous Windows (Invite de commandes), utiliser `copy .env.example .env`.

3. Préparer la base de données SQLite et exécuter les migrations :

```bash
touch database/database.sqlite
php artisan migrate
```

4. Compiler les assets et démarrer le serveur local :

```bash
npm run build
php artisan serve
```

Le script `composer run setup` automatise ces étapes. Pour le développement, `composer run dev` lance en parallèle le serveur Laravel, le worker de file d'attente, les logs Pail et Vite.

### Webhook Make.com (optionnel)

Pour activer la création d'événements calendrier à l'ajout d'une tâche, ajouter la variable suivante dans `.env` :

```env
MAKE_COM_CALENDAR_WEBHOOK=https://hook.eu2.make.com/votre-webhook
```

Sans cette variable, l'application fonctionne normalement et enregistre un avertissement dans les logs.

### Tests

```bash
composer run test
```

## Structure du projet

```text
app/
  Http/Controllers/   Contrôleurs (TaskController, ProfileController, Auth)
  Models/             Modèles Eloquent (Task, User)
  Services/           CalendarService (intégration Make.com)
database/
  migrations/         Migrations des tables users, tasks, task_user, cache, jobs
public/
  images/             Logos et ressources graphiques
  style.css           Feuille de style principale
  task_add.js         Logique du formulaire d'ajout de tâche
  account_menu.js     Menu du compte utilisateur
resources/views/      Vues Blade (welcome, dashboard, task_add, auth, profile)
routes/web.php        Définition des routes principales
tests/                Tests Pest (Feature et Unit)
```
