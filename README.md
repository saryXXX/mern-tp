# Code Source – Cours MERN Décalé (Séance 2)

Projet complet prêt à l'emploi correspondant au support de cours [`S2_Seance_Interactive_Decale.pdf`](../S2_Seance_Interactive_Decale.pdf).

---

## 🏗️ Structure du Projet

```text
s2-mongoose-atlas/
├── src/
│   ├── config/
│   │   └── db.js                 # Module de connexion asynchrone à MongoDB Atlas
│   ├── controllers/
│   │   ├── articleController.js  # CRUD des articles avec persistance & .populate()
│   │   └── userController.js     # CRUD des utilisateurs avec intégrité relationnelle
│   ├── models/
│   │   ├── articleModel.js       # Schéma Article avec référence ObjectId vers User
│   │   └── userModel.js          # Schéma User avec Regex email, age min 4 et enum role
│   ├── routes/
│   │   ├── articleRoutes.js      # Routeur Express pour /api/articles
│   │   └── userRoutes.js         # Routeur Express pour /api/users
│   └── server.js                 # Point d'entrée : Express, middlewares, routes, connexion DB & app.listen
├── .env.example                  # Modèle de variables d'environnement
├── .gitignore                    # Exclusion de node_modules/ et .env
└── package.json                  # Dépendances (express, mongoose, dotenv)
```

---

## 🚀 Démarrage Rapide

### 1. Installation des dépendances
```bash
npm install
```

### 2. Configuration du fichier `.env`
Créez un fichier `.env` à la racine :
```env
PORT=3000
MONGODB_URI=mongodb+srv://admin_mern:<password>@cluster0.xxxxx.mongodb.net/blogDB?retryWrites=true&w=majority
```
*(Remplacez `<password>` et le nom de cluster par vos identifiants réels créés sur MongoDB Atlas).*

### 3. Lancer en mode développement (auto-reload)
```bash
npm run dev
```

---

## 📡 Endpoints de l'API

### Domaine Utilisateurs (`/api/users`)

| Méthode | URL | Corps JSON / Paramètres | Description |
|:---:|:---|:---|:---|
| **POST** | `/api/users` | `{ "name": "...", "email": "...", "age": 22, "role": "admin" }` | Crée un utilisateur validé (Regex, age $\ge 4$, enum) |
| **GET** | `/api/users` | Aucun | Liste tous les utilisateurs |
| **GET** | `/api/users/:id` | Paramètre `:id` | Récupère un utilisateur par son `_id` |
| **DELETE** | `/api/users/:id` | Paramètre `:id` | Supprime un utilisateur (refusé si des articles lui sont liés) |

### Domaine Articles (`/api/articles`)

| Méthode | URL | Corps JSON / Paramètres | Description |
|:---:|:---|:---|:---|
| **GET** | `/api/articles` | Aucun | Liste tous les articles avec l'auteur peuplé (`.populate()`) |
| **GET** | `/api/articles/:id` | Paramètre `:id` | Détail d'un article avec l'auteur peuplé |
| **POST** | `/api/articles` | `{ "title": "...", "content": "...", "authorId": "..." }` | Crée un article lié à un utilisateur existant |
| **PUT** | `/api/articles/:id` | `{ "title": "...", "content": "..." }` | Met à jour un article |
| **DELETE** | `/api/articles/:id` | Paramètre `:id` | Supprime un article |

---

## 🧪 Scénario de Test Postman

1. **Création d'un utilisateur valide (POST `http://localhost:3000/api/users`) :**
   ```json
   {
     "name": "Aya Ben Salah",
     "email": "aya@polytechnique.tn",
     "age": 22,
     "role": "admin"
   }
   ```
   $\rightarrow$ Statut `201 Created`. Copiez l'\`_id` retourné.

2. **Test de rejet par validation (POST `http://localhost:3000/api/users`) :**
   ```json
   {
     "name": "Test Invalide",
     "email": "invalide_sans_arobase",
     "age": 2
   }
   ```
   $\rightarrow$ Statut `400 Bad Request` avec détail de l'erreur retourné par Mongoose.

3. **Création d'un article lié (POST `http://localhost:3000/api/articles`) :**
   ```json
   {
     "title": "Persistance NoSQL avec Mongoose",
     "content": "Découverte des modèles et des relations par référence.",
     "authorId": "COLLEZ_ICI_L_ID_UTILISATEUR"
   }
   ```
   $\rightarrow$ Statut `201 Created` avec l'objet `author` entièrement peuplé.

4. **Test d'intégrité relationnelle (DELETE `http://localhost:3000/api/users/:id`) :**
   Tentez de supprimer l'utilisateur ayant écrit l'article.
   $\rightarrow$ Statut `400 Bad Request` : *"Impossible de supprimer un auteur ayant des articles publies"*.
