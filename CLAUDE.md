# 🧠 CLAUDE.md — Master Context File
## Copilot BTP — SaaS IA pour artisans & TPE du bâtiment

> Projet en pause depuis le bilan du 02/09/2026.

> **Ce fichier est ton brief complet. Lis-le en ENTIER avant d'écrire la moindre ligne de code.**
> Il connecte tous les documents du projet et te donne le contexte, les règles, et les décisions déjà prises.

---

## 🎯 Mission Produit

**Copilot BTP** = Le premier copilote IA tout-en-un pour artisans et TPE du bâtiment français.

### Problème résolu
Les artisans BTP (maçons, électriciens, plombiers, menuisiers) passent 30% de leur temps sur de l'administratif : devis, PPSPS, situations de travaux, CERFA DC4, PV réception. Aucun outil du marché ne combine IA + mobile terrain + conformité e-facture 2026.

### Solution
6 modules intégrés dans une seule app :

| Module | Valeur clé |
|--------|------------|
| 📁 **GED Chantier** | Upload + extraction IA (Gemini) de tous les docs BTP |
| 🧾 **Facturation IA** | Auto-détection autoliquidation TVA, situations numérotées, PPF 2026 |
| 🏗️ **Suivi Chantier** | Planning lots, réserves, PV réception, avancement % |
| 🧠 **Brain BTP** | Copilot IA : lecture CCTP, risques chantier, devis auto |
| 📊 **Analytics** | KPIs chantier/comptable/opérationnel avec Recharts |
| 👥 **Équipe** | Rôles : chef de chantier, conducteur travaux, artisan |

### Cible
- **600 000 TPE/artisans** BTP en France
- Prix : **29 €/mois** (Essentiel) · **79 €/mois** (Pro)
- Beta : **Mai 2026** · Prod : **Septembre 2026** (conformité e-facture)

---

## 🏛️ Architecture — Règles Absolues

> **Base : Odoc v1.0.7 — 70% copié, 30% adapté BTP. Ne réinvente rien qui existe dans Odoc.**

### Stack (NE PAS CHANGER)
React 18 · TypeScript strict · Vite · Tailwind CSS · Shadcn/UI · Lucide Icons · Framer Motion · Recharts · React Query · React Router DOM v6 · Supabase · Gemini Flash · Resend · Stripe · Lovable Cloud / Vercel

### Design System (OBLIGATOIRE)
- **Thème** : Sombre par défaut (dark mode forcé)
- **Primaire** : indigo (#4f46e5)
- **Accent** : ambre/jaune (#f59e0b) — couleur identitaire BTP
- **Mobile** : Mobile-first, breakpoint 375px en priorité
- **Icônes** : Lucide uniquement (pas de heroicons)

Patterns détaillés (hooks, Edge Functions, RLS) : [ARCHITECTURE.md](./ARCHITECTURE.md). Tables, structure fichiers, types TypeScript, checklist de copie Odoc : [docs/agents/architecture.md](docs/agents/architecture.md).

---

## 🛠️ Commandes

```bash
npm install                  # Install dependencies
npm run dev                  # Vite dev server → http://localhost:5173
npm run type-check          # TypeScript strict mode
npm run build               # Build production
supabase functions deploy   # Deploy edge functions to Supabase
```

Pré-requis avant Jour 1 et pistes de debug : [docs/agents/setup-debug.md](docs/agents/setup-debug.md).

---

## ⚠️ Règles de Développement — Non Négociables

### TypeScript
- ✅ `strict: true` — zéro tolérance
- ✅ Aucun `any` type
- ✅ Toutes les fonctions async ont un `try/catch`
- ✅ Interfaces nommées (pas de types inline complexes)

### Architecture
- ✅ Copier le pattern Odoc — ne pas réinventer
- ✅ React Query pour TOUS les appels Supabase (pas de `useEffect` pour fetch)
- ✅ RLS activé sur toutes les tables AVANT de connecter le frontend
- ✅ Edge Functions pour toute logique IA (jamais côté client)
- ✅ Variables d'env dans `.env.local` (jamais hardcodé)

### UX/Mobile
- ✅ Tester sur 375px en priorité
- ✅ Tous les boutons ont `:hover` + `:disabled` states
- ✅ Loading spinners visibles sur toute action async
- ✅ Formulaires : tous les champs ont un `label`

### Commits & Pull Requests
Pattern : `feat(module): description courte` · `fix(bug): …` · `db(schema): …` · `docs(section): …`
- Toujours merge sur `main` (trunk-based)
- 1 commit par feature logique (squash si besoin)
- Pas de `git push --force` sans approbation
- CI/CD check : TypeScript strict + tests

Logging/error handling et exemples de commits : [docs/agents/conventions-detail.md](docs/agents/conventions-detail.md).

---

## 📚 Documentation détaillée

| Fichier | Rôle | Priorité |
|---------|------|----------|
| **[`ARCHITECTURE.md`](./ARCHITECTURE.md)** | Stack, patterns, types, RLS, variables d'env | 🔴 CRITIQUE |
| **[`ROADMAP.md`](./ROADMAP.md)** | Roadmap 10 jours, prompts détaillés par jour, livrables | 🔴 CRITIQUE |
| **[`PLAN STRATÉGIQUE COPILOT BTP`](./PLAN%20STRATÉGIQUE%20COPILOT%20BTP)** | Décisions arch, risques, sprints, qualité gates | 🟠 IMPORTANT |
| **[`CHECKLIST PRÉ-LANCEMENT COPILOT BTP`](./CHECKLIST%20PRÉ-LANCEMENT%20COPILOT%20BTP)** | Setup infra, secrets, outils à préparer | 🟠 IMPORTANT |
| **[`README.md`](./README.md)** | Vue produit, modules, concurrents, quick start | 🟢 RÉFÉRENCE |
| **[`docs/agents/architecture.md`](docs/agents/architecture.md)** | Tables & RLS détaillées, structure fichiers, types, checklist copie Odoc | 🟢 RÉFÉRENCE |
| **[`docs/agents/setup-debug.md`](docs/agents/setup-debug.md)** | Pré-requis avant Jour 1, debug | 🟢 RÉFÉRENCE |
| **[`docs/agents/conventions-detail.md`](docs/agents/conventions-detail.md)** | Logging & error handling, exemples de commits | 🟢 RÉFÉRENCE |
| **[`docs/agents/roadmap-risques.md`](docs/agents/roadmap-risques.md)** | Sprint J1-J10, risques, prompt complet Jour 1 | 🟢 RÉFÉRENCE |
| **[`docs/agents/qualite.md`](docs/agents/qualite.md)** | Checklist MVP beta, classification bugs, quality gates, ressources | 🟢 RÉFÉRENCE |

---

*Dernière mise à jour : 11/09/2026 (trim documentation + pause projet)*
*Statut : 🟡 Specs 100% — Code 0% — en pause depuis le 02/09/2026*
*Base : Odoc v1.0.7 — 70% copie, 30% adaptation BTP — documenté pour réutilisation*
