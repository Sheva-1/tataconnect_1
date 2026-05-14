# Guide de test - TataConnect

## 🧪 Tests manuels

### Test 1: Inscription & Authentification

#### Créer compte Famille
1. Allez sur http://localhost:5173/signup
2. Sélectionnez "Je cherche une nounou"
3. Remplissez:
   - Email: `family1@test.com`
   - Password: `Test1234`
   - NIC Number: `ID123456`
   - NIC Document: `https://via.placeholder.com/id.pdf`
   - Video: `https://via.placeholder.com/video.mp4`
4. Click "S'inscrire"
5. Devrait rediriger vers `/dashboard`

#### Créer compte Caregiver
1. Allez sur http://localhost:5173/signup
2. Sélectionnez "Je suis nounou/aide ménagère"
3. Remplissez:
   - Email: `caregiver1@test.com`
   - Password: `Test1234`
   - Full Name: `Marie Dupont`
   - City: `Douala`
   - Rate: `3000`
   - Bio: `Expérience 10 ans en garde d'enfants`
   - NIC: `ID654321`
   - NIC Doc: `https://via.placeholder.com/id.pdf`
   - Video: `https://via.placeholder.com/video.mp4`
4. Click "S'inscrire"
5. Devrait créer profil en statut `DRAFT`

#### Test Login
1. Déconnectez (si connecté)
2. Allez `/login`
3. Email: `family1@test.com`, Password: `Test1234`
4. Devrait connecter et afficher dashboard

---

### Test 2: Profil Caregiver

#### Éditer profil
1. Loggez-vous comme Caregiver
2. Accédez `/profile` (depuis dashboard)
3. Modifiez:
   - Bio: `Spécialisée en garde d'enfants`
   - Services: `["CHILDCARE", "BABYSITTING"]`
   - Languages: `["French", "English"]`
4. Cliquez "Sauvegarder"
5. Devrait voir changements

#### Uploader documents
1. Toujours connecté comme Caregiver
2. Accédez page vérification
3. Upload (tests avec URLs):
   - Type: `ID` → URL: `https://via.placeholder.com/400x300?text=ID`
   - Type: `CERTIFICATE` → URL: `https://via.placeholder.com/400x300?text=CERT`
4. Success → Documents visibles

#### Soumettre pour vérification
1. Après avoir uploadé documents
2. Cliquez "Soumettre pour vérification"
3. Profil passe à statut `PENDING`
4. Message de confirmation

---

### Test 3: Admin Dashboard

#### Voir profils en attente
1. Créer 2-3 comptes Caregiver différents
2. Soumettre chacun pour vérification
3. Accédez `/admin` (besoin compte ADMIN)
   - **Note**: Créer manuellement admin via:
   ```bash
   # Option 1: Via Prisma Studio
   cd backend
   npm run prisma:studio
   # Créer user avec role=ADMIN
   
   # Option 2: Via SQLite
   sqlite3 backend/prisma/dev.db
   # UPDATE User SET role = 'ADMIN' WHERE id = '...';
   ```
4. Page `/admin` affiche liste PENDING

#### Approver caregiver
1. Cliquez sur caregiver PENDING
2. Voyez les documents
3. Lisez info et documents
4. Cliquez "✓ Approuver"
5. **Résultat**: 
   - Statut → VERIFIED
   - verifiedAt = maintenant
   - Disparaît de PENDING

#### Refuser caregiver
1. Cliquez sur autre caregiver PENDING
2. Remplissez raison refus: `Documents insuffisants`
3. Cliquez "✗ Refuser"
4. **Résultat**:
   - Statut → REJECTED
   - rejectionReason sauvegardée
   - Disparaît de PENDING

#### Historique vérifications
1. POST `/api/admin/verification/:caregiverId/verification-history`
2. Devrait voir trace de toutes actions

---

### Test 4: Recherche & Découverte

#### Voir caregivers
1. Loggez-vous comme Famille
2. Allez `/search`
3. Devrait voir:
   - Liste caregivers avec statut VERIFIED
   - Badge "✓ Vérifiée"
   - Tarif, ville, bio visible

#### Filtrer par ville
1. Entrez ville filter
2. Lista filtrée
3. Devrait voir que caregivers de cette ville

#### Important
- Caregivers NON-VERIFIED ne doivent PAS apparaître
- Vérifier que seuls VERIFIED s'affichent

---

### Test 5: Messagerie

#### Créer conversation
1. Loggez-vous comme Famille
2. Allez `/search`
3. Cliquez "Me contacter" sur caregiver
4. Devrait créer conversation
5. Rediriger vers `/messages/:conversationId`

#### Envoyer message
1. Dans page messagerie
2. Écrivez message: `Bonjour, je suis intéressée`
3. Cliquez "Envoyer"
4. Message apparat dans liste
5. Timestamp correct

#### Voir conversations
1. Allez `/messages` (sans ID)
2. Devrait lister conversations précédentes
3. Click conversation → affiche messages

#### Tester bidirectional
1. Loggez-vous comme Caregiver (autre navigateur/incognito)
2. Accédez `/messages`
3. Voyez conversation avec Famille
4. Envoyez réponse: `Merci pour votre intérêt`
5. Famille reçoit message

---

### Test 6: Réservations

#### Créer réservation
1. Loggez-vous comme Famille
2. Accédez `/bookings`
3. "Nouvelle réservation" → Remplissez:
   - Service: `Garde enfants`
   - Date: `2026-05-01`
   - Lieu: `Douala, Bastos`
   - Tarif: `3000 FCFA/h`
4. Soumettre
5. Status: PENDING

#### Accepter réservation
1. Famille sees booking: PENDING
2. Loggez-vous comme Caregiver
3. Allez `/bookings`
4. Voyez booking de Famille
5. Cliquez "Accepter"
6. Status → ACCEPTED (visible pour les deux)

#### Réserver comme Famille
1. Depuis dashboard, cliquez "Réserver nounou"
2. Cherchez caregiver
3. Cliquez "Réserver"
4. Formulaire de réservation
5. Submit → booking créé

---

### Test 7: Sécurité & Confiance

#### Profils non-vérifiés masqués
1. Créez caregiver, NE soumettez PAS pour vérification
2. Loggez-vous comme Famille
3. Allez `/search`
4. Caregiver ne doit PAS apparaître ✓

#### Messages sécurisés
1. Deux utilisateurs communiquent via messagerie
2. Aucun contact n'est partagé directement ✓
3. Tous les messages traçés ✓

#### Historique admin
1. Admin approuve/refuse plusieurs caregivers
2. `GET /api/admin/verification/:id/verification-history`
3. Devrait lister toutes actions avec:
   - Admin ID
   - Statut
   - Timestamp
   - Raison (si refus) ✓

---

## 🔄 Tests d'API (avec cURL ou Postman)

### Auth

```bash
# Sign Up
curl -X POST http://localhost:4000/api/auth/sign-up \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@test.com",
    "password": "Test1234",
    "role": "CAREGIVER",
    "nicNumber": "ID123",
    "nicDocumentUrl": "https://...",
    "videoUrl": "https://...",
    "fullName": "Test User",
    "city": "Douala",
    "rateFcfa": "3000"
  }'

# Login
curl -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@test.com", "password": "Test1234"}'
```

### Caregivers

```bash
# Get all verified
curl http://localhost:4000/api/caregivers

# Get one
curl http://localhost:4000/api/caregivers/:userId

# Update
curl -X PUT http://localhost:4000/api/caregivers/:userId \
  -d '{"bio": "New bio"}'

# Documents
curl -X POST http://localhost:4000/api/caregivers/:userId/documents \
  -d '{"type": "ID", "fileUrl": "https://...", "fileName": "id.pdf"}'

# Submit for verification
curl -X POST http://localhost:4000/api/caregivers/:userId/submit-verification
```

### Admin

```bash
# Pending
curl http://localhost:4000/api/admin/verification/pending

# Approve
curl -X POST http://localhost:4000/api/admin/verification/:caregiverId/approve \
  -d '{"adminId": "...", "notes": "Approved"}'

# Reject
curl -X POST http://localhost:4000/api/admin/verification/:caregiverId/reject \
  -d '{"adminId": "...", "rejectionReason": "Documents missing"}'
```

---

## ✅ Checklist de test complet

### Auth
- [ ] Sign up Famille
- [ ] Sign up Caregiver
- [ ] Sign up Admin
- [ ] Login tous rôles
- [ ] Logout
- [ ] Error handling (email already exists, etc)

### Profil
- [ ] Edit caregiver profile
- [ ] Upload documents
- [ ] Submit for verification
- [ ] See pending status
- [ ] Email validation

### Admin
- [ ] See pending list
- [ ] Approve caregiver
- [ ] Reject caregiver
- [ ] See verification history
- [ ] Only ADMIN can access /admin

### Search
- [ ] See verified caregivers only
- [ ] Filter by city
- [ ] See badge "✓ Vérifiée"
- [ ] See tarif, bio, rating

### Messaging
- [ ] Create conversation
- [ ] Send message
- [ ] Receive message
- [ ] See timestamp
- [ ] List conversations
- [ ] Bidirectional messages

### Bookings
- [ ] Create booking
- [ ] See pending
- [ ] Accept/Reject booking
- [ ] Change status
- [ ] Dashboard shows bookings

### Security
- [ ] Non-verified not visible
- [ ] Contacts not shared
- [ ] Admin history traced
- [ ] Messages stored

---

## 🐛 Problèmes courants & solutions

| Problème | Solution |
|----------|----------|
| Login fails | Vérifier email exact, password matched |
| Caregivers not showing | Vérifier status = VERIFIED |
| Messages not sending | Vérifier conversation ID valide |
| Admin page blank | Vérifier user role = ADMIN |
| Documents not uploading | Vérifier URLs accessibles |
| BD pas syncée | `cd backend && npm run db:push` |

---

## 📊 Métriques de succès

- ✅ Tous les endpoints répondent
- ✅ Workflows complets exécutables
- ✅ Données persistées en BD
- ✅ Frontend-backend communicent
- ✅ Sécurité appliquée (VERIFIED filter)
- ✅ Historique tracé (admin)

**Tester tout!** 🚀
