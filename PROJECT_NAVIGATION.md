# 🗺️ Navigation du projet TataConnect

Ce guide vous aide à naviguer et comprendre la structure du projet.

---

## 📂 Structure des fichiers

```
tataconnect/
│
├── 📄 Documentation (LISEZ D'ABORD!)
│   ├── QUICKSTART.md ⭐ START HERE
│   ├── PROJECT_SUMMARY.md
│   ├── DOCUMENTATION.md
│   ├── RESUME_IMPLEMENTATION.md
│   ├── TESTING_GUIDE.md
│   └── DEPENDENCIES.md
│
├── 📦 Frontend (React)
│   ├── src/
│   │   ├── App.tsx ← Point d'entrée
│   │   ├── main.tsx ← Bootstrap
│   │   ├── index.css ← Styles complets
│   │   │
│   │   ├── pages/ ← Pages principales
│   │   │   ├── Login.tsx
│   │   │   ├── Signup.tsx
│   │   │   ├── Dashboard.tsx
│   │   │   ├── SearchCaregivers.tsx
│   │   │   ├── AdminDashboard.tsx
│   │   │   └── Messaging.tsx
│   │   │
│   │   ├── context/ ← État global
│   │   │   └── AuthContext.tsx ← Auth state
│   │   │
│   │   ├── services/ ← Appels API
│   │   │   └── api.ts ← Endpoints
│   │   │
│   │   ├── components/ ← Composants réutilisables
│   │   └── hooks/ ← Hooks personnalisés
│   │
│   ├── index.html → Landing page
│   ├── vite.config.ts → Config Vite
│   ├── tsconfig.json → Config TypeScript
│   └── package.json → Dépendances
│
├── 🔌 Backend (Express)
│   ├── backend/
│   │   ├── src/
│   │   │   ├── server.ts ← Point d'entrée
│   │   │   │
│   │   │   └── routes/ ← Endpoints API
│   │   │       ├── auth.ts (sign-up, login)
│   │   │       ├── caregivers.ts (profils)
│   │   │       ├── admin.ts (vérification)
│   │   │       ├── messaging.ts (chat)
│   │   │       └── bookings.ts (réservations)
│   │   │
│   │   ├── prisma/
│   │   │   ├── schema.prisma ← BD schema
│   │   │   └── dev.db ← SQLite file
│   │   │
│   │   ├── .env ← Variables environnement
│   │   ├── .env.example
│   │   ├── tsconfig.json
│   │   └── package.json
│   │
│   └── dist/ ← Build output (après npm run build)
│
└── 📋 Configuration générale
    ├── package.json ← Root dependencies
    ├── eslint.config.js
    └── README.md
```

---

## 🎯 Où aller selon votre besoin?

### ❓ Je veux démarrer rapidement
→ Lisez: **QUICKSTART.md**
→ Commandes: `npm install` + `npm run dev`

### ❓ Je veux comprendre l'architecture
→ Lisez: **DOCUMENTATION.md**
→ Comprendre: BD, API, frontend stack

### ❓ Je veux voir comment ça marche
→ Lisez: **TESTING_GUIDE.md**
→ Tester: Tous les workflows utilisateur

### ❓ Je veux voir comment votre résumé est implémenté
→ Lisez: **RESUME_IMPLEMENTATION.md**
→ Mapper: Résumé → Code

### ❓ Je veux modifier le code
→ Allez à: `src/pages/` (frontend) ou `backend/src/routes/` (backend)

---

## 🧭 Workflows principaux

### Workflow 1: Ajouter une nouvelle page (Frontend)

1. Créer fichier dans `src/pages/MyPage.tsx`
2. Créer composant React:
   ```tsx
   export function MyPage() {
     return <div>...</div>
   }
   ```
3. Router dans `src/App.tsx`:
   ```tsx
   <Route path="/my-page" element={<MyPage />} />
   ```
4. Ajouter lienknavigation si besoin
5. Styles dans `src/index.css`

### Workflow 2: Ajouter un nouvel endpoint (Backend)

1. Créer/modifier fichier dans `backend/src/routes/`
2. Ajouter route Express:
   ```ts
   router.post('/my-endpoint', async (req, res) => {
     // Logic
     res.json(data)
   })
   ```
3. Export/Import dans `backend/src/server.ts`
4. Register route:
   ```ts
   app.use('/api/prefix', routerImport)
   ```

### Workflow 3: Ajouter un nouveau modèle (BD)

1. Éditer `backend/prisma/schema.prisma`
2. Ajouter model:
   ```prisma
   model MyModel {
     id    String @id @default(cuid())
     // fields...
   }
   ```
3. Sync BD:
   ```bash
   cd backend
   npm run db:push
   npm run prisma:generate
   ```

---

## 📍 Fichiers clés & leurs rôles

### Frontend
| Fichier | Rôle |
|---------|------|
| `src/App.tsx` | Routing principal |
| `src/context/AuthContext.tsx` | État utilisateur |
| `src/services/api.ts` | Appels API backend |
| `src/pages/*.tsx` | Pages screens |
| `src/index.css` | Tous les styles |
| `vite.config.ts` | Config dev/build |

### Backend
| Fichier | Rôle |
|---------|------|
| `server.ts` | Serveur Express + middleware |
| `routes/*.ts` | Endpoints API (groupés par domaine) |
| `prisma/schema.prisma` | Définition modèles BD |
| `prisma/dev.db` | Données SQLite |
| `.env` | Configuration runtime |
| `package.json` | Dépendances + scripts |

---

## 🔍 Comment explorer le code

### Pour comprendre un endpoint

**Exemple: Login**

1. Frontend: `src/pages/Login.tsx` → voir appel `login(email, password)`
2. Services: `src/services/api.ts` → voir `api.login()`
3. Backend: `backend/src/routes/auth.ts` → voir `router.post('/login', ...)`
4. BD: `schema.prisma` → voir `model User` et utilisations

### Pour comprendre un workflow complet

**Exemple: Vérification caregiver**

1. **Frontend páginas**:
   - `src/pages/Signup.tsx` → Caregiver signup
   - `src/pages/AdminDashboard.tsx` → Admin view

2. **Backend routes**:
   - `backend/src/routes/caregivers.ts` → `submit-verification`
   - `backend/src/routes/admin.ts` → `approve`/`reject`

3. **Database models**:
   - `Caregiver` (verificationStatus field)
   - `Verification` (historique)
   - `Document` (fichiers)

---

## 💻 Commandes utiles

### Frontend
```bash
npm run dev      # Démarrage dev (localhost:5173)
npm run build    # Build production
npm run lint     # ESLint
npm run preview  # Préview du build
```

### Backend
```bash
cd backend
npm run dev              # Dev avec hot-reload
npm run build
npm run start            # Serveur production
npm run prisma:generate  # Régénérer client
npm run db:push          # Sync BD
npm run prisma:studio    # GUI Prisma
```

### BD
```bash
# Ouvrir SQLite CLI
sqlite3 backend/prisma/dev.db

# Voir tables
.tables

# Voir schéma user
.schema User

# Query
SELECT * FROM User LIMIT 5;
```

---

## 🔗 Connections entre couches

```
User (Frontend)
    ↓
    interagit avec
    ↓
Page React (src/pages/*.tsx)
    ↓
    appelle
    ↓
API Service (src/services/api.ts)
    ↓
    requête HTTP
    ↓
Backend Route (backend/src/routes/*.ts)
    ↓
    utilise
    ↓
Prisma Client
    ↓
    interagit avec
    ↓
SQLite DB (backend/prisma/dev.db)
```

---

## 🧪 Tester une modification

### Modification Frontend
1. Éditez `src/pages/Something.tsx`
2. Save → Hot reload automatique (Vite)
3. Testez dans navigateur

### Modification Backend
1. Éditez `backend/src/routes/something.ts`
2. Save → Hot reload automatique (tsx watch)
3. Testez avec curl ou Postman

### Modification BD
1. Éditez `backend/prisma/schema.prisma`
2. `npm run db:push`
3. `npm run prisma:generate`
4. Redémarrez backend
5. Testez

---

## 🐛 Débogage

### Frontend
- Ouvrez DevTools (F12)
- Console: Erreurs, logs (console.log)
- Network: Requêtes API
- Application: localStorage (auth state)

### Backend
- Terminal: Logs (console.log)
- Vérifiez `http://localhost:4000/health`
- Prisma Studio: `npm run prisma:studio`

### BD
- Ouvrez SQLite Browser (GUI)
- Ou CLI: `sqlite3 dev.db`

---

## 📚 Ressources

| Ressource | Lien |
|-----------|------|
| React Docs | https://react.dev |
| React Router | https://reactrouter.com |
| Prisma Docs | https://www.prisma.io/docs |
| Express Docs | https://expressjs.com |
| Vite Docs | https://vitejs.dev |
| TypeScript | https://www.typescriptlang.org |

---

## ✨ Tips & Tricks

### Frontend
- Utilisez `useAuth()` pour accéder user partout
- Importez `api` depuis `services/api.ts` pour appels
- Styles: regardez `index.css` pour classes disponibles

### Backend
- Toutes les routes retournent JSON
- Utilisez `z` (Zod) pour valider inputs
- Utilisez `prisma` pour DB queries

### General
- Les types TypeScript vous aident: lecture build errors
-omments dans code:  regardez explications
- Test d'abord, déployez après

---

## 🚀 Prochaines étapes

1. **Lire QUICKSTART.md**
2. **Lancer `npm run dev` + `cd backend && npm run dev`**
3. **Ouvrir http://localhost:5173**
4. **Créer compte test**
5. **Tester les workflows (voir TESTING_GUIDE.md)**
6. **Modifier le code comme vous voulez!**

Bon coding! 💻
