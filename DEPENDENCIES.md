# Dépendances du projet TataConnect

## Frontend (React + Vite)

### Runtime
- `react@^19.2.4` - Bibliothèque UI
- `react-dom@^19.2.4` - DOM renderer
- `react-router-dom@^7.14.1` - Routing

### Dev Tools
- `vite@^8.0.4` - Build tool
- `typescript@~6.0.2` - TypeScript compiler
- `eslint@^9.39.4` - Linting
- `@vitejs/plugin-react@^6.0.1` - React plugin pour Vite

## Backend (Node.js + Express)

### Runtime
- `express@^5.2.1` - Web framework
- `@prisma/client@^7.7.0` - ORM
- `cors@^2.8.6` - CORS middleware
- `dotenv@^17.4.2` - Environment variables
- `zod@^4.3.6` - Validation de schéma

### Dev Tools
- `prisma@^7.7.0` - ORM CLI
- `tsx@^4.21.0` - TypeScript execution
- `typescript@^6.0.2` - TypeScript compiler

## Versions minimales requises

- Node.js: 18.x ou supérieur
- npm: 9.x ou supérieur

## Installation

```bash
# Global npm
npm install -g npm@latest

# Frontend
npm install

# Backend
cd backend && npm install
```

## Scripts disponibles

### Frontend
- `npm run dev` - Démarrage dev
- `npm run build` - Build production
- `npm run lint` - ESLint

### Backend
- `npm run dev` - Dev avec hot reload
- `npm run build` - Compiler TypeScript
- `npm run start` - Lancer serveur compilé
- `npm run prisma:generate` - Générer Prisma Client
- `npm run db:push` - Sync BD
- `npm run seed` - Ajouter données test
