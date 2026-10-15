# 🚗 MarocodeMove — Système de Gestion des Frais de Déplacement (SGFD)

Application web de gestion des frais de déplacement professionnels : les employés déclarent leurs déplacements et soumettent leurs notes de frais avec justificatifs, les managers valident ou rejettent, et l'administrateur approuve définitivement et supervise l'ensemble.

> Projet de développement web, 2ème année ISIC, **ENSA El Jadida** (Université Chouaib Doukkali), année universitaire 2025/2026.
> Encadrante : Prof. Hajar LAZAR.

---

## 📌 Contexte

La gestion des frais de déplacement repose encore souvent sur des documents papier, des emails et des fichiers Excel, ce qui entraîne retards, erreurs et manque de suivi. SGFD digitalise et centralise ce processus, avec une validation hiérarchique et une traçabilité complète.

## 🎯 Objectifs

- Centraliser la gestion des déplacements et des notes de frais
- Automatiser le circuit de validation (employé → manager → administrateur)
- Conserver l'historique de chaque changement de statut
- Réduire les erreurs et les délais de remboursement
- Générer automatiquement des documents PDF

## 👥 Rôles

| Rôle | Actions principales |
|------|---------------------|
| **Employé** | Déclarer un déplacement, créer des notes de frais avec justificatifs, suivre l'état de ses demandes |
| **Manager** | Consulter les demandes de son équipe, valider ou rejeter avec commentaire, attribuer des déplacements |
| **Administrateur** | Gérer les utilisateurs et les catégories de frais, approuver ou rejeter définitivement, consulter statistiques et historiques |

## 🔄 Circuit de validation

```
brouillon → soumis → valide_manager → en_cours_admin → approuve
                  ↘ rejetee_manager              ↘ rejetee_admin
```

Chaque transition est enregistrée dans l'historique des statuts.

## ✨ Fonctionnalités

- Authentification, inscription avec **vérification par email**, gestion du profil (avatar, couverture)
- Gestion des déplacements, avec **carte** et sélection de lieux (pays et villes)
- Notes de frais, détails de frais par catégorie et **upload de justificatifs** (PDF, images)
- **Calendrier** des déplacements
- **Messagerie interne** (conversations) et **notifications en temps réel** (WebSocket)
- Tableaux de bord distincts : employé, manager, administrateur
- Historique des remboursements et des statuts
- Génération de **PDF** (mPDF)
- **Assistant IA** intégré (Gemini)
- Support : création et suivi de tickets

## 🧱 Architecture

Architecture **MVC** avec couche **DAO** pour l'accès aux données, sans framework.

```
sgfd/
├── app/
│   ├── Config/        # Connexion PDO (Singleton)
│   ├── Controllers/   # Logique des requêtes
│   ├── Core/          # Router, BaseController, Middleware, View
│   ├── Helpers/       # Auth
│   ├── Models/
│   │   ├── DAO/       # Accès aux données
│   │   └── Entities/  # Entités (User, NoteFrais, Deplacement...)
│   ├── Services/      # Email, PDF, Gemini, contexte de l'assistant
│   ├── routes/        # Définition des routes
│   ├── views/         # Vues PHP (Bootstrap 5)
│   └── WebSocket/     # Clients WebSocket
├── public/            # Point d'entrée (index.php), assets, uploads
├── storage/
├── websocket-server.php
└── composer.json
```

## 🛠️ Technologies

**Backend** : PHP 8, MySQL (PDO), Composer
**Dépendances PHP** : `cboden/ratchet` (WebSocket), `mpdf/mpdf` (PDF), `phpmailer/phpmailer` (emails)
**Frontend** : HTML5, CSS3, JavaScript, Bootstrap 5, Bootstrap Icons
**Services externes** : GeoNames (pays et villes), Photon (géolocalisation), ImgBB (hébergement d'images), Google Gemini (assistant IA), SMTP Gmail (emails)

## 🚀 Installation

### Prérequis

- PHP ≥ 8.0 (extensions `pdo_mysql`, `curl`, `mbstring`, `gd`)
- MySQL / MariaDB
- Apache avec `mod_rewrite` (XAMPP ou WAMP)
- Composer

### Étapes

```bash
# 1. Cloner le dépôt
git clone https://github.com/korachia1KA/Travel-expense-management.git
cd Travel-expense-management

# 2. Installer les dépendances
composer install
```

**3. Base de données**

Créez une base `sgfd` (utf8mb4) et importez le script SQL du projet.

> ⚠️ Le script SQL n'est pas inclus dans l'archive fournie : ajoutez-le au dépôt (par exemple `database/sgfd.sql`, export phpMyAdmin) pour que le projet soit installable.

Tables utilisées : `users`, `deplacements`, `notes_frais`, `details_frais`, `categories_frais`, `historique_statuts`, `notifications`, `conversations`, `conversation_participants`, `messages`, `message_reads`, `email_verifications`, `support_tickets`, `support_replies`, `assistant_conversations`, `assistant_messages`.

**4. Configuration**

La connexion lit les variables d'environnement `DB_HOST`, `DB_NAME`, `DB_USER`, `DB_PASS` (valeurs par défaut : `127.0.0.1`, `sgfd`, `root`, vide).

**5. Lancer l'application**

Placez le projet dans `htdocs` (XAMPP) ou `www` (WAMP), démarrez Apache et MySQL, puis ouvrez :

```
http://localhost/sgfd/public
```

**6. Notifications temps réel (optionnel)**

Dans un second terminal :

```bash
php websocket-server.php
```

Le serveur écoute sur `ws://localhost:8080` (clients) et `tcp://127.0.0.1:8081` (commandes internes PHP).

## 🔐 Sécurité

Avant toute publication du dépôt :

- **Ne versionnez pas** `vendor/`, `.env`, `websocket.pid` ni `public/uploads/` (voir `.gitignore`).
- Le code contient actuellement des **identifiants en dur** (clé API Gemini dans `GeminiService.php`, compte SMTP dans `EmailService.php`, clé ImgBB dans `UserController.php`). Déplacez-les dans des variables d'environnement, **révoquez les clés déjà exposées** et régénérez-en de nouvelles.
- Si ces clés ont déjà été poussées sur GitHub, elles restent visibles dans l'historique : changez-les.

## 📊 Gestion de projet

- Méthodologie : Agile Scrum, en plusieurs sprints
- Suivi des tâches : Trello
- Versionnement : Git et GitHub

## 📈 Perspectives

- Application mobile (Android / iOS)
- Analyse intelligente des dépenses par IA
- Gestion budgétaire avancée
- Notifications push
- Tableau de bord décisionnel pour l'administration

## 👨‍💻 Auteurs

- **Ali Dali**
- **Abdelghafour Korachi**

Projet académique, ENSA El Jadida, 2025/2026.
