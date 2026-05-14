# TataConnect - Plateforme de marketplace pour nounous et aides ménagères

## 📋 Aperçu du projet

TataConnect est une plateforme web qui met en relation des **familles** avec des **caregivers** (nounous/aides ménagères) via un système de profils vérifiés, de recherche et de messagerie intégrée.

### Objectifs principaux
- ✅ Connexion sécurisée entre familles et caregivers
- ✅ Système de vérification des profils pour renforcer la confiance
- ✅ Recherche et découverte simple et efficace
- ✅ Messagerie interne pour confidentialité
- ✅ Gestion des réservations/demandes
- ✅ Espace admin pour modération

---

## 🏗️ Architecture

### Stack technique
- **Frontend**: React 19 + TypeScript + Vite + React Router
- **Backend**: Express.js + Node.js
- **Base de données**: SQLite (Prisma ORM)
- **Validation**: Zod

### Structure du projet

```
tataconnect/
├── src/                              # Frontend React
│   ├── pages/
│   │   ├── Login.tsx                 # Page de connexion
│   │   ├── Signup.tsx                # Page d'inscription
│   │   ├── Dashboard.tsx             # Tableau de bord utilisateur
│   │   ├── SearchCaregivers.tsx      # Recherche de caregivers
│   │   ├── AdminDashboard.tsx        # Panel admin
│   │   └── Messaging.tsx             # Messagerie interne
│   ├── context/
│   │   └── AuthContext.tsx           # Contexte d'authentification
│   ├── services/
│   │   └── api.ts                    # Appels API
│   ├── components/                   # Composants réutilisables
│   ├── App.tsx                       # Routing principal
│   └── index.css                     # Styles
│
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   │   ├── auth.ts               # Authentification
│   │   │   ├── caregivers.ts         # Gestion profils caregivers
│   │   │   ├── admin.ts              # Vérification (admin)
│   │   │   ├── messaging.ts          # Messagerie
│   │   │   └── bookings.ts           # Réservations
│   │   └── server.ts                 # Serveur Express
│   ├── prisma/
│   │   ├── schema.prisma             # Schéma BD
│   │   └── dev.db                    # BD SQLite
│   └── package.json
│
├── package.json                      # Frontend dependencies
└── README.md                         # Ce fichier
```

---

## 🗄️ Modèle de données

### Rôles utilisateur
- **FAMILY**: Cherche une nounou/aide ménagère
- **CAREGIVER**: Propose ses services
- **ADMIN**: Vérifie les profils

### Statuts de vérification
- **DRAFT**: Profil en création
- **PENDING**: Documents soumis, en attente d'approbation
- **VERIFIED**: Approuvé et visible publiquement
- **REJECTED**: Refusé avec explications

### Entités principales
- **User**: Utilisateur du système (email, mot de passe, rôle)
- **Caregiver**: Profil enrichi avec expérience, tarif, services, langues
- **Document**: Fichiers supportant la vérification (ID, certificats, références)
- **Verification**: Historique des actions de vérification
- **Conversation**: Discussions entre utilisateurs
- **ConversationMessage**: Messages dans une conversation
- **Booking**: Demandes de réservation
- **Review**: Avis après mission

---

## 🚀 Installation et démarrage

### Prérequis
- Node.js >= 18.x
- npm

### 1. Installation des dépendances

```bash
# Frontend & racine
npm install

# Backend
cd backend
npm install
cd ..
```

### 2. Configuration BD (Prisma)

```bash
cd backend

# Générer le client Prisma
npm run prisma:generate

# Pousser le schéma à la BD
npm run db:push

# (Optionnel) Ajouter des données de test
npm run seed

cd ..
```

### 3. Lancer l'application

#### En développement (2 terminaux)

**Terminal 1 - Frontend (Vite)**:
```bash
npm run dev
```
➜ http://localhost:5173

**Terminal 2 - Backend (Express)**:
```bash
cd backend
npm run dev
```
➜ http://localhost:4000

#### En production

```bash
# Build frontend
npm run build

# Build backend
cd backend
npm run build
npm run start
```

---

## 📋 Fonctionnalités implementées

### 1. Authentification (✅ Complète)
- POST `/api/auth/sign-up` - Inscription (Famille/Caregiver)
- POST `/api/auth/login` - Connexion
- Contexte React pour gestion d'état utilisateur

### 2. Profils Caregivers (✅ Complète)
- GET `/api/caregivers` - Liste des caregivers vérifiés
- GET `/api/caregivers/:userId` - Détail caregiver
- PUT `/api/caregivers/:userId` - Modification profil
- POST `/api/caregivers/:userId/documents` - Upload documents
- POST `/api/caregivers/:userId/submit-verification` - Soumission

### 3. Vérification Admin (✅ Complète)
- GET `/api/admin/verification/pending` - File d'attente
- POST `/api/admin/verification/:caregiverId/approve` - Approbation
- POST `/api/admin/verification/:caregiverId/reject` - Refus
- GET `/api/admin/verification/:caregiverId/verification-history` - Historique

### 4. Messagerie (✅ Complète)
- POST `/api/messages/conversations` - Créer/Get conversation
- GET `/api/messages/user/:userId` - Conversations d'un utilisateur
- GET `/api/messages/conversation/:conversationId/messages` - Messages
- POST `/api/messages/conversation/:conversationId/messages` - Envoyer message

### 5. Réservations (✅ Complète)
- POST `/api/bookings` - Créer demande
- GET `/api/bookings/user/:userId` - Voir ses réservations
- PUT `/api/bookings/:bookingId` - Accepter/Refuser/Marquer complet
- GET `/api/bookings/:bookingId` - Détails

### 6. Pages Frontend (✅ Complète)
- ✅ Login - Connexion
- ✅ Signup - Inscription avec rôle (Famille/Caregiver)
- ✅ Dashboard - Tableau de bord personnalisé
- ✅ SearchCaregivers - Recherche avec filtres
- ✅ AdminDashboard - Gestion vérifications
- ✅ Messaging - Conversations et messages
- ✅ CSS complet et responsive

---

## 📂 Flux utilisateur

### Pour une Famille
1. S'inscrire → Login
2. Tableau de bord
3. Chercher nounou → SearchCaregivers (filtrés par vérification)
4. Cliquer "Me contacter" → Créer conversation
5. Envoyer message → Messagerie
6. Faire demande de réservation

### Pour un Caregiver
1. S'inscrire → Profil créé en DRAFT
2. Ajouter documents (ID, certificats)
3. Soumettre pour vérification (statut = PENDING)
4. Admin approuve → Profil VERIFIED et visible
5. Recevoir messages de familles
6. Accepter/Refuser réservations

### Pour un Admin
1. Aller `/admin`
2. Voir file d'attente PENDING
3. Vérifier documents
4. Approuver ou refuser
5. Historique sauvegardé

---

## 🔐 Sécurité

- ✅ Messagerie interne (pas d'échange direct contacts)
- ✅ Profils non-vérifiés masqués aux familles
- ✅ Statut de vérification pour filtrage
- ✅ Documents obligatoires
- ✅ Historique des actions admin
- ❌ Pas encore: JWT tokens, hachage mot de passe (MVP simplifié)

---

## 🎯 Roadmap futures

### Phase 2 - Paiement & Sécurité avancée
- Intégration Stripe pour paiements in-app
- Hachage sécurisé des mots de passe
- JWT tokens
- 2FA pour admins
- Vérification externe (ID checker)

### Phase 3 - Fonctionnalités avancées
- Système d'avis/notation
- Notifications push
- Planification d'horaires
- Factures/reçus
- Système d'étoiles/favoris
- Sous-contrats pour caregivers

### Phase 4 - Scaling
- Application mobile (React Native)
- Internationalization (FR/EN)
- Support multilingue pour caregivers
- Analytics & dashboard propriétaire

---

## 🐛 Débogage

### Erreurs courantes

**Backend ne démarre pas**
```bash
cd backend
npm run prisma:generate
npm run db:push
npm run dev
```

**Frontend ne se connecte au backend**
- Vérifier que backend tourne sur http://localhost:4000
- Check CORS dans `backend/src/server.ts`
- Vérifier port dans `.env` ou hardcoded en API

**Prisma erreurs**
```bash
cd backend
rm prisma/dev.db
npm run prisma:generate
npm run db:push
```

---

## 📞 Support & Contact

TataConnect v1.0 - MVP Marketplace
Développé avec ❤️ en React + Express
