# 🧠 CLAUDE.md — Master Context File
## Copilot BTP — SaaS IA pour artisans & TPE du bâtiment

> **Ce fichier est ton brief complet. Lis-le en ENTIER avant d'écrire la moindre ligne de code.**  
> Il connecte tous les documents du projet et te donne le contexte, les règles, et les décisions déjà prises.

---

## 📂 Carte des Documents (Lis dans cet ordre)

| Fichier | Rôle | Priorité |
|---------|------|----------|
| **`CLAUDE.md`** (ce fichier) | Master context — à lire en premier | 🔴 CRITIQUE |
| **[`ARCHITECTURE.md`](./ARCHITECTURE.md)** | Stack, patterns hooks/edge functions, schémas JSON | 🔴 CRITIQUE |
| **[`ROADMAP.md`](./ROADMAP.md)** | Roadmap 10 jours, prompts par jour, livrables | 🔴 CRITIQUE |
| **[`PLAN STRATÉGIQUE COPILOT BTP`](./PLAN%20STRATÉGIQUE%20COPILOT%20BTP)** | Décisions arch, risques, sprints, qualité gates | 🟠 IMPORTANT |
| **[`CHECKLIST PRÉ-LANCEMENT COPILOT BTP`](./CHECKLIST%20PRÉ-LANCEMENT%20COPILOT%20BTP)** | Setup infra, secrets, outils à préparer | 🟠 IMPORTANT |
| **[`README.md`](./README.md)** | Vue produit, modules, concurrents, quick start | 🟢 RÉFÉRENCE |

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
```
Frontend  : React 18 + TypeScript strict + Vite
UI        : Tailwind CSS + Shadcn/UI + Lucide Icons
Animations: Framer Motion
Data viz  : Recharts
State     : React Query (caching + mutations)
Routing   : React Router DOM v6
Backend   : Supabase (PostgreSQL + Auth + Storage + RLS + Edge Functions)
IA docs   : Gemini 1.5 Flash (extraction documents)
IA Brain  : Gemini 2.5 Flash (copilot conversationnel)
Emails    : Resend
Paiements : Stripe
Deploy    : Lovable Cloud / Vercel
```

### Design System (OBLIGATOIRE)
```
Thème     : Sombre par défaut (dark mode forcé)
Primaire  : indigo (#4f46e5)
Accent    : ambre/jaune (#f59e0b) — couleur identitaire BTP
Mobile    : Mobile-first (breakpoint 375px en priorité)
Icônes    : Lucide uniquement (pas de heroicons, pas d'autres libs)
```

### Pattern Hooks (COPIER CE PATTERN EXACTEMENT)
```typescript
// Pattern React Query pour tous les hooks de données
const useChantiers = () => {
  const { data, isLoading, error } = useQuery(['chantiers'], fetchChantiers)
  const createMutation = useMutation(createChantier, {
    onSuccess: () => queryClient.invalidateQueries(['chantiers'])
  })
  return { chantiers: data, isLoading, error, create: createMutation.mutate }
}
```

### Pattern Edge Functions Supabase (COPIER CE PATTERN)
```typescript
// Toutes les edge functions suivent ce pattern :
// 1. Recevoir ID(s) en body
// 2. Récupérer la ressource depuis Supabase
// 3. Appeler Gemini avec prompt métier spécialisé
// 4. UPDATE la table avec les données extraites
// 5. Retourner les données
```

---

## 🗄️ Base de Données — Tables & RLS

### 11 Tables (créées Jour 2)
```
chantiers              — table principale NEW BTP
documents_btp          — GED documents NEW BTP
factures_btp           — facturation NEW BTP
reserves_chantier      — réserves/PV NEW BTP
profiles               — copié Odoc
team_members           — copié Odoc
notifications          — copié Odoc
brain_sessions         — copié Odoc
brain_messages         — copié Odoc
connectors             — copié Odoc
vw_dashboard_kpis      — VIEW (pas table) NEW BTP
```

### RLS — Règle universelle
```sql
-- Appliquer sur CHAQUE table sans exception :
ALTER TABLE {table} ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users CRUD own {table}" ON {table}
  FOR ALL USING (auth.uid() = user_id);
```

### Variables d'environnement requises
```env
VITE_SUPABASE_URL=
VITE_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
GEMINI_API_KEY=
RESEND_API_KEY=
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
STRIPE_PUBLISHABLE_KEY=
```

---

## 🗺️ Structure Fichiers (src/)

```
src/
├── pages/
│   ├── Index.tsx              # Dashboard BTP — 6 KPIs
│   ├── Auth.tsx               # Login/Signup (copier Odoc)
│   ├── Chantiers.tsx          # Liste + filtres + drawer
│   ├── Documents.tsx          # GED BTP
│   ├── Factures.tsx           # Facturation IA
│   ├── SuiviChantier.tsx      # Planning + réserves + PV
│   ├── Analytics.tsx          # Analytics 3 vues
│   ├── Brain.tsx              # Copilot IA chat
│   ├── Team.tsx               # Équipe (copier Odoc)
│   ├── Settings.tsx           # Paramètres (copier Odoc)
│   ├── Notifications.tsx      # Notifications (copier Odoc)
│   └── admin/                 # Admin (copier Odoc)
│
├── components/
│   ├── chantiers/             # ChantierCard, ChantierDrawer, StatusBadge, SituationTravaux
│   ├── documents/             # DocumentsDataTable, DocumentDrawer (copier Odoc + types BTP)
│   ├── factures/              # FacturesTable, ApprobationFlow (copier Odoc + autoliquidation)
│   ├── brain/                 # BrainChat, Suggestions (copier Odoc + prompts BTP)
│   ├── analytics/             # VueChantier, VueComptable, VueOperationnelle
│   ├── suivi/                 # ReservesTable, PVReception, PlanningLots
│   └── ui/                    # Shadcn/UI (copier Odoc exactement)
│
├── hooks/
│   ├── useAuth.ts             # Copier Odoc
│   ├── useChantiers.ts        # NEW — CRUD chantiers
│   ├── useDocuments.ts        # Copier Odoc (types BTP)
│   ├── useFactures.ts         # Copier Odoc + champs BTP
│   ├── useBrainSessions.ts    # Copier Odoc + contexte BTP
│   ├── useTeamMembers.ts      # Copier Odoc
│   ├── useProfile.ts          # Copier Odoc
│   └── useNotifications.ts   # Copier Odoc
│
├── lib/
│   ├── btpAggregation.ts      # NEW — 10+ fonctions KPIs BTP
│   ├── btpDocTypes.ts         # NEW — types docs BTP (PPSPS, DC4, CCTP…)
│   ├── exportFEC.ts           # Copier Odoc
│   └── supabase.ts            # Client Supabase (copier Odoc)
│
└── types/
    └── btp.ts                 # Types TypeScript BTP (ExtractedDataBTP, etc.)

supabase/
└── functions/
    ├── analyze-btp-document/  # Gemini extraction documents BTP
    ├── brain-btp-query/       # Brain IA (CCTP, risques, devis)
    ├── extract-facture-btp/   # Facturation + autoliquidation TVA
    ├── check-overdue-btp/     # Cron alertes impayés (copier Odoc)
    └── connector-sync/        # Google Drive sync (copier Odoc)
```

---

## 🔑 Types TypeScript Critiques

### ExtractedDataBTP (à utiliser pour toute extraction Gemini)
```typescript
interface ExtractedDataBTP {
  document_type: 'devis' | 'situation' | 'ppsps' | 'doe' | 'dc4' | 'pv_reception' | 'cctp' | 'bon_commande' | 'contrat' | 'autre'
  chantier_nom?: string
  maitre_ouvrage?: string
  numero_marche?: string
  lot?: string
  numero_situation?: number
  avancement_percent?: number
  montant_ht?: number
  montant_ttc?: number
  tva_rate?: number
  autoliquidation_tva?: boolean  // CRITIQUE : sous-traitance BTP art. 283-2 CGI
  retenue_garantie?: number       // Standard BTP = 5%
  fournisseur?: { nom: string; siret: string; adresse: string }
  date_document?: string
  date_echeance?: string
  references?: string[]
  summary: string
}
```

### ChantierStatus
```typescript
type ChantierStatus = 'planifie' | 'en_cours' | 'reception' | 'termine' | 'litige'
// Couleurs : vert/orange/bleu/gris/rouge
```

---

## 📅 Roadmap Sprint — État Actuel

> Voir **[`ROADMAP.md`](./ROADMAP.md)** pour les prompts détaillés par jour.

| Jour | Module | Statut |
|------|--------|--------|
| J1 | Setup + Auth + Navigation | ⏳ À faire |
| J2 | Tables Supabase + Dashboard KPIs | ⏳ À faire |
| J3 | Module Chantiers CRUD | ⏳ À faire |
| J4 | GED Documents + Extraction IA | ⏳ À faire |
| J5 | Facturation BTP + Autoliquidation TVA | ⏳ À faire |
| J6 | Suivi Chantier + Réserves + PV | ⏳ À faire |
| J7 | Brain BTP (Copilot IA) | ⏳ À faire |
| J8 | Analytics 3 vues + Team + Settings | ⏳ À faire |
| J9 | Notifications + Stripe mock + PWA | ⏳ À faire |
| J10 | Deploy prod + Beta artisans | ⏳ À faire |

**Mettre à jour ce tableau à chaque sprint complété.**

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

### Commits
```
Pattern : feat(module): description courte
Exemples :
  feat(day1): auth + sidebar routing
  feat(chantiers): CRUD + ChantierDrawer
  feat(edge): analyze-btp-document Gemini extraction
  fix(rls): correct policy on documents_btp
  db(schema): add reserves_chantier table
```

---

## 🚩 Risques — Mitigation Active

| Risque | Priorité | Solution |
|--------|----------|----------|
| Gemini API timeout | 🔴 CRITIQUE | Fallback Groq + timeout 30s max |
| Supabase RLS cassée | 🔴 CRITIQUE | Tester J2 matin avant tout |
| Scope creep | 🟠 HAUTE | Couper toute feature > 6h estimée |
| Mobile responsive | 🟠 MOYENNE | Tester sur device réel chaque jour |
| Stripe webhook | 🟢 BASSE | Mock jusqu'à J10, puis vrai webhook |

---

## 🎯 Métriques de Succès — Checklist Finale

### MVP Beta (Jour 10)
- [ ] 5 artisans beta onboardés
- [ ] 3/5 ont créé ≥ 1 chantier avec documents
- [ ] Extraction IA moyenne > 80% accuracy
- [ ] 0 bug critique (P1)
- [ ] Response time API < 3s (hors IA)
- [ ] Lighthouse mobile > 80
- [ ] Bundle < 500KB gzip

### Quality Gates Code
- [ ] TypeScript strict = 0 erreurs
- [ ] No `any` type
- [ ] RLS 100% coverage
- [ ] Tous les async ont error handling
- [ ] Mobile 375px OK sur toutes les pages

---

## 🔗 Ressources Utiles

- **Supabase console** : https://supabase.com/dashboard
- **Gemini API docs** : https://ai.google.dev/docs
- **Shadcn/UI** : https://ui.shadcn.com
- **Lovable** : https://lovable.dev
- **Stripe** : https://dashboard.stripe.com/test
- **Resend** : https://resend.com/docs

---

## ⚡ Commande Fast Lane — Copier-Coller pour démarrer

Si tout est préparé (voir [`CHECKLIST PRÉ-LANCEMENT`](./CHECKLIST%20PRÉ-LANCEMENT%20COPILOT%20BTP)), dire à Claude Code :

```
Lis CLAUDE.md en entier.
Lis ARCHITECTURE.md.
Lis ROADMAP.md section "JOUR 1".

Stack: React 18 + TypeScript strict + Vite + Tailwind + Shadcn/UI + Supabase.
Secrets .env.local prêts (Supabase + Gemini + Stripe + Resend).
DB Schema SQL exécutée (11 tables + RLS).

Lance Jour 1 : Setup + Auth Supabase + Sidebar BTP (10 routes) + Dashboard mock 6 KPIs.
Design sombre, couleurs indigo + ambre.
Mobile-first. TypeScript strict.
Lovable deploy inclus.

Prompt exact dans ROADMAP.md > JOUR 1.
Go !
```

---

*Dernière mise à jour : 04/04/2026*  
*Statut projet : 🟡 Specs 100% — Code 0% — Prêt à lancer Jour 1*  
*Architecture : basée sur Odoc v1.0.7 — 70% réutilisable*
