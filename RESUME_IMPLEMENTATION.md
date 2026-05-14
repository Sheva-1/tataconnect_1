# Implémentation du résumé TataConnect

Ce document mappe le résumé de votre projet avec l'implémentation actuelle.

---

## 1️⃣ Objectif
> Créer une plateforme web qui met en relation des familles et des nounous / aides ménagères via un système de profils, de recherche et de contact, avec un point central : des profils "Verified" (vérifiés) pour renforcer la confiance et la sécurité.

### ✅ Implémentation
- **Système de profils**: Modèles `User` et `Caregiver` avec statut de vérification
- **Recherche**: Page `/search` avec filtres par ville, statut, services
- **Contact**: Messagerie intégrée dans `/messages`
- **Verified**: Enum `VerificationStatus` (DRAFT → PENDING → VERIFIED/REJECTED)
- **Confiance**: Seuls les profils VERIFIED apparaissent aux familles

---

## 2️⃣ Principe (Marketplace à deux côtés)

### Côté Familles
> Cherchent des caregivers (par ville, tarif, services, langues, disponibilité)

**✅ Implémenté:**
- **Page `/search`**: Liste caregivers vérifiés
- **Filtres**: Ville, tarif (dans caregiver-card), services (JSON)
- **Vue complète**: Bio, tarif, ratings, langues, disponibilités

> Consultent les profils, l'historique, et éventuellement les avis

**✅ Implémenté:**
- **Profils détaillés**: `GET /api/caregivers/:userId`
- **Reviews model**: Préparé pour avis futurs

> Contactent via une messagerie intégrée

**✅ Implémenté:**
- **Messagerie**: `/messages/:conversationId`
- **API**: POST conversations, GET/POST messages
- **Confidentialité**: Pas de partage direct contacts

> Font une demande de réservation (et plus tard, paient dans l'app)

**✅ Implémenté:**
- **Bookings model**: PENDING/ACCEPTED/REJECTED/COMPLETED/CANCELLED
- **API**: `POST /api/bookings` pour créer demande
- **Gestion**: `PUT /api/bookings/:bookingId` pour accepter/refuser

---

### Côté Caregivers (nounous/housekeepers)
> Créent un profil type CV (bio, expérience, services, tarifs, zones, disponibilités)

**✅ Implémenté:**
- **Model Caregiver**: fullName, city, town, bio, rateFcfa
- **Champs étendus**: languages (JSON), services (JSON), availability (JSON)
- **Édition**: `PUT /api/caregivers/:userId`

> Soumettent des documents pour être vérifiés

**✅ Implémenté:**
- **Model Document**: Stocke type, fileUrl, fileName
- **API**: `POST /api/caregivers/:userId/documents`
- **Récupération**: `GET /api/caregivers/:userId/documents`

> Reçoivent et gèrent les demandes (accepter/refuser)

**✅ Implémenté:**
- **Dashboard caregiver**: Voir bookings avec statut
- **API**: `PUT /api/bookings/:bookingId` pour changer statut

---

## 3️⃣ Le cœur: "Verified"

> Le caregiver soumet des documents → Profil = "pending_verification" → Admin vérifie → verified OU rejected

**✅ Implémenté:**

```
1. Signup Caregiver
   ↓
   verificationStatus = DRAFT

2. Upload Documents
   ↓
   Bouton "Soumettre pour vérification"
   
3. Submit
   ↓
   verificationStatus = PENDING
   submittedAt = now()

4. Admin Dashboard
   ↓
   voir caregivers PENDING
   
5. Admin Approuve
   ↓
   verificationStatus = VERIFIED
   verifiedAt = now()
   
   OU Admin Refuse
   ↓
   verificationStatus = REJECTED
   rejectionReason = "..."

6. Visibility
   ↓
   Frontend filtres sur VERIFIED uniquement pour familles
```

**API Endpoints:**
- `POST /api/caregivers/:userId/submit-verification` - Soumettre
- `GET /api/admin/verification/pending` - File d'attente
- `POST /api/admin/verification/:caregiverId/approve` - Approuver
- `POST /api/admin/verification/:caregiverId/reject` - Refuser

---

## 4️⃣ Fonctionnalités principales (MVP)

### 1. Inscription / Connexion ✅
- **Routes**: `/login`, `/signup`
- **API**: POST `/api/auth/sign-up`, POST `/api/auth/login`
- **Rôles implémentés**: FAMILY, CAREGIVER, ADMIN

### 2. Gestion de profil caregiver ✅
- **Création/Édition**: PUT `/api/caregivers/:userId`
- **Services**: JSON array (préparé pour types dynamiques)
- **Tarif**: rateFcfa
- **Ville/Zone**: city, town
- **Langues**: languages JSON
- **Disponibilités**: availability JSON
- **Statut vérification**: DRAFT → PENDING → VERIFIED/REJECTED

### 3. Recherche & découverte ✅
- **Page**: `/search`
- **Endpoint**: GET `/api/caregivers`
- **Filtres**: ville, services, langues
- **Badge Vérifiée**: ✓ Vérifiée (visible que si VERIFIED)
- **Priorité**: Les vérifiés s'affichent en premier

### 4. Messagerie interne ✅
- **Page**: `/messages/:conversationId`
- **Endpoints**:
  - `POST /api/messages/conversations` - Créer
  - `GET /api/messages/user/:userId` - Lister
  - `GET /api/messages/conversation/:id/messages` - Récupérer
  - `POST /api/messages/conversation/:id/messages` - Envoyer
- **Avantage**: Confidentiel, traçable, pas de partage perso

### 5. Espace Admin (Trust & Safety) ✅
- **Page**: `/admin`
- **Fonction**: File d'attente PENDING
- **Actions**: Voir documents, approuver, refuser
- **Historique**: Model `Verification` trace tout

---

## 5️⃣ Sécurité & confiance (principes)

| Principe | Implémentation |
|----------|----------------|
| Messagerie dans plateforme | ✅ ConversationMessage model |
| Profils non vérifiés limités | ✅ Frontend filtre VERIFIED only |
| Avis post-mission | ✅ Review model (prêt pour intégration) |
| Historique actions admin | ✅ Verification model avec admin trace |

---

## 6️⃣ Paiement / Réservation (Phase 2)

### Niveau MVP (actuellement):
- ✅ Demande de réservation sans paiement
- ✅ Statuts: PENDING, ACCEPTED, REJECTED, COMPLETED, CANCELLED
- ✅ Dashboard affiche réservations

### Niveau avancé (futur):
- ❌ Paiement Stripe
- ❌ Reversement au caregiver
- ❌ Facturation in-app

---

## 7️⃣ Livrables

### Site React ✅
```
Pages:
- /login → Login
- /signup → Signup
- /dashboard → Dashboard
- /search → Search Caregivers
- /admin → Admin Dashboard
- /messages/:id → Messaging
```

### API + BD ✅
```
Users: email, password, role, documents
Caregivers: profil enrichi + services/langues/dispo
Documents: support vérification
Conversations: messages internes
Bookings: demandes + statuts
Verification: historique admin
```

### Architecture ✅
```
Frontend: React Router + Context Auth
Backend: Express + Prisma
BD: SQLite dev.db
```

---

## 📊 Matrice d'implémentation

| Feature | MVP | Phase 2 | Statut |
|---------|-----|---------|--------|
| Auth | ✅ | - | ✅ Complet |
| Profils Caregivers | ✅ | JWT | ✅ Complet |
| Vérification | ✅ | ID Checker externe | ✅ Complet |
| Recherche | ✅ | Filtres avancés | ✅ Complet |
| Messagerie | ✅ | Notifications | ✅ Complet |
| Réservations | ✅ | Paiement Stripe | ✅ Complet |
| Avis/Ratings | Skeleton | ❌ À faire | 🟡 Modèle prêt |
| Admin Panel | ✅ | Analytics | ✅ Complet |

---

## 🎬 Cas d'usage complet

### Pour Famille

1. **Inscription**
   ```
   Sign Up → Email + Password → Role: FAMILY
   ```

2. **Chercher nounou**
   ```
   Dashboard → Click "Chercher une nounou" → /search
   Voir liste: [Caregivers avec VERIFIED status]
   ```

3. **Contacter**
   ```
   Click "Me contacter" → Create Conversation
   Envoyer message → Conversation Page (/messages/:id)
   ```

4. **Réserver**
   ```
   POST /api/bookings → Status: PENDING
   Attendre acceptation
   ```

### Pour Caregiver

1. **Inscription**
   ```
   Sign Up → Email + Password + Nom + Ville + Tarif → Role: CAREGIVER
   Profile créé: verificationStatus = DRAFT
   ```

2. **Compléter profil**
   ```
   Dashboard → Edit Profile
   Ajouter: Services, Langues, Bio, Disponibilités
   ```

3. **Soumettre documents**
   ```
   Upload ID + Certificats → Status: PENDING
   Submit for Verification
   ```

4. **Attendre vérification**
   ```
   Admin approuve → VERIFIED
   Profil visible aux familles
   ```

5. **Recevoir messages**
   ```
   /messages → Voir conversations
   Répondre à familles
   ```

6. **Accepter réservation**
   ```
   Dashboard → See Bookings
   Put /api/bookings/:id → Status: ACCEPTED
   ```

### Pour Admin

1. **Accéder panel**
   ```
   /admin → Liste PENDING caregivers
   ```

2. **Vérifier dossier**
   ```
   Click caregiver → Voir documents + infos
   ```

3. **Approuver**
   ```
   Click "Approuve" → VERIFIED
   Caregiver visible aux familles
   ```

   **OU Refuser**
   ```
   Click "Refuse" → REJECTED
   Spécifier raison
   Historique sauvegardé
   ```

---

## 🚀 Prêt à déployer

Le projet est prêt pour:
- ✅ Présentation démo
- ✅ Tests utilisateur
- ✅ Phase 2: Payment integration
- ✅ Scaling: Mobile app, Analytics, Multi-langue

Utilisez `QUICKSTART.md` pour lancer!
