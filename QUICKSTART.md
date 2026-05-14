# Guide de démarrage rapide - TataConnect

## ⚡ Démarrer en 5 minutes

### 1️⃣ Installer les dépendances
```bash
# Terminal principal
npm install

# Backend (nouveau terminal)
cd backend && npm install && cd ..
```

### 2️⃣ Configurer la base de données
```bash
cd backend

# Générer Prisma Client
npm run prisma:generate

# Créer la BD SQLite
npm run db:push

cd ..
```

### 3️⃣ Lancer l'application

**Terminal 1 - Frontend**:
```bash
npm run dev
```
👉 Ouvre http://localhost:5173

**Terminal 2 - Backend** (nouveau):
```bash
cd backend && npm run dev
```
👉 Tourne sur http://localhost:4000

---

## 🧪 Tester l'application

### Créer un compte
1. Allez sur http://localhost:5173
2. Cliquez "Sign Up"
3. Choisissez **"Je cherche une nounou"** ou **"Je suis nounou/aide ménagère"**
4. Remplissez les champs

### Tester en tant que Nounou
1. Inscrivez-vous comme Caregiver avec:
   - Email: `nounou@test.com`
   - Ville: `Douala`
   - Tarif: `3000`
   - Documents: URL de fichier (mime-URL)

2. Accédez au dashboard
3. Allez à "Vérification" pour soumettre documents

### Tester en tant que Famille
1. Inscrivez-vous comme Family
2. Allez à "Chercher une nounou"
3. Filtrez par ville
4. Cliquez "Me contacter"
5. Accédez à "Mes messages"

### Tester Admin (manuellement)
1. Inscrivez-vous comme Admin via direct DB modification OU
2. Changez `role` manuellement dans `backend/prisma/dev.db`
3. Allez à `/admin`
4. Approbation/Refus de caregivers

---

## 📊 Comptes de test

### Famille
- **Email**: family@test.com
- **Mot de passe**: Test@123

### Caregiver
- **Email**: caregiver@test.com
- **Mot de passe**: Test@123
- **Ville**: Douala
- **Tarif**: 3000 FCFA/h

### Admin
- **Email**: admin@test.com
- **Mot de passe**: Test@123

---

## 🎯 Fonctionnalités à tester

| Fonctionnalité | URL/Route | Statut |
|--------|-----------|--------|
| Inscription | `/signup` | ✅ |
| Connexion | `/login` | ✅ |
| Dashboard | `/dashboard` | ✅ |
| Recherche | `/search` | ✅ |
| Admin | `/admin` | ✅ |
| Messagerie | `/messages/:id` | ✅ |
| Profil Nounou | (depuis dashboard) | ✅ |

---

## 🔧 Commandes utiles

```bash
# Frontend
npm run dev          # Développement
npm run build        # Production build
npm run lint         # ESLint

# Backend
cd backend
npm run dev          # Dev avec hot-reload (tsx watch)
npm run build        # Compiler TypeScript
npm run start        # Lancer serveur compilé
npm run db:push      # Sync BD
npm run seed         # Ajouter données test

# Prisma
npm run prisma:generate   # Générer client
npm run prisma:studio     # Interface graphique
```

---

## 📝 Notes

- 🗄️ BD SQLite stockée en `backend/prisma/dev.db`
- 🔑 Pas de JWT encore (MVP simplifié, auth d'état localStorage)
- 📱 Responsive design inclus
- 🎨 Thème bleu/teal/vert
- 🌐 Français par défaut

---

## ❓ Problèmes?

### Backend ne démarre
```bash
cd backend
npm install
npm run prisma:generate
npm run db:push
npm run dev
```

### Port 5173 ou 4000 occupé?
```bash
# Changer dans vite.config.ts (frontend)
# Changer PORT= variable (backend)
```

### BD corrompue
```bash
cd backend
rm prisma/dev.db
npm run db:push
npm run dev
```

---

Enjoy! 🎉
