# 🏗️ COPILOT BTP — Roadmap 10 Jours

> **Chef de projet** : Claude Code + Claude Max  
> **Stack** : React + TypeScript + Vite + Tailwind + Supabase + Gemini Flash  
> **Objectif** : MVP Beta artisans BTP — prêt à être complété, zéro bug, base saine  
> **Repo parent inspirant** : `odoc` (même architecture, fork adapté BTP)  
> **Date cible beta** : Mai 2026

---

## 🎯 Vision Produit

**Copilot BTP** est le premier copilote IA dédié aux artisans et TPE du bâtiment français et européen.  
Il combine en un seul outil :
- 📁 **GED Chantier** : gestion documentaire IA (devis, PPSPS, situations, DOE, DC4)
- 🧾 **Facturation BTP** : extraction IA, autoliquidation TVA, conformité PPF 2026
- 🏗️ **Suivi Chantier** : planning, réserves, PV réception, avancement %
- 🧠 **Brain BTP** : copilote IA lecture CCTP, risques, suggestions variantes

**Cible** : 600 000 TPE/artisans BTP en France (maçons, électriciens, plombiers, menuisiers)  
**Prix** : 29-79 €/mois  
**Différenciateur** : IA first + mobile terrain + conformité e-facture sept. 2026

---

## 🏛️ Architecture (Identique à Odoc — Claude sait déjà faire)

```
src/
├── pages/
│   ├── Index.tsx              # Dashboard BTP (chantiers en cours, alertes, KPIs)
│   ├── Auth.tsx               # Login/Signup Supabase (copy Odoc)
│   ├── Chantiers.tsx          # Liste chantiers + filtres + drawer
│   ├── Documents.tsx          # GED docs BTP (devis, PPSPS, DOE, DC4…)
│   ├── Factures.tsx           # Facturation IA BTP + autoliquidation TVA
│   ├── SuiviChantier.tsx      # Planning, réserves, PV, avancement %
│   ├── Analytics.tsx          # Analytics multi-vues (chantier, comptable, RH)
│   ├── Brain.tsx              # Copilot IA BTP (lecture CCTP, risques, devis auto)
│   ├── Team.tsx               # Équipe chantier (copy Odoc)
│   ├── Settings.tsx           # Paramètres + intégrations (copy Odoc)
│   ├── Notifications.tsx      # Notifications (copy Odoc)
│   └── admin/                 # Admin Dashboard (copy Odoc + KPIs BTP)
│
├── components/
│   ├── chantiers/
│   │   ├── ChantierCard.tsx
│   │   ├── ChantierDrawer.tsx
│   │   ├── ChantierStatusBadge.tsx
│   │   └── SituationTravaux.tsx
│   ├── documents/             # Copy Odoc + types BTP
│   ├── factures/              # Copy Odoc + autoliquidation + DC4
│   ├── brain/                 # Copy Odoc + prompts BTP spécifiques
│   ├── analytics/             # Copy Odoc + vues chantier/marge
│   └── ui/                    # Shadcn/UI (copy Odoc)
│
├── hooks/
│   ├── useAuth.ts             # Copy Odoc
│   ├── useChantiers.ts        # NEW — CRUD chantiers
│   ├── useDocuments.ts        # Copy Odoc (types BTP)
│   ├── useFactures.ts         # Copy Odoc + champs BTP
│   ├── useBrainSessions.ts    # Copy Odoc + contexte BTP
│   ├── useTeamMembers.ts      # Copy Odoc
│   ├── useProfile.ts          # Copy Odoc
│   └── useNotifications.ts   # Copy Odoc
│
├── lib/
│   ├── btpAggregation.ts      # NEW — KPIs BTP (marge chantier, retard, avancement)
│   ├── btpDocTypes.ts         # NEW — types docs BTP
│   └── exportFEC.ts           # Copy Odoc
│
supabase/
├── functions/
│   ├── analyze-btp-document/  # NEW — Gemini extraction BTP
│   ├── brain-btp-query/       # NEW — Brain BTP (CCTP, risques…)
│   ├── extract-facture-btp/   # NEW — extraction facture BTP + autoliquidation
│   ├── check-overdue-btp/     # Copy Odoc — alertes impayés
│   └── connector-sync/        # Copy Odoc — Google Drive / Dropbox
```

---

## 🗄️ Base de Données Supabase

### Tables nouvelles (BTP-specific)

```sql
-- Chantiers
CREATE TABLE chantiers (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id uuid REFERENCES auth.users NOT NULL,
  nom text NOT NULL,
  adresse text,
  maitre_ouvrage text,
  numero_marche text,
  statut text DEFAULT 'en_cours', -- planifie | en_cours | reception | termine | litige
  date_debut date,
  date_fin_prevue date,
  date_reception date,
  montant_marche numeric,
  avancement_percent int DEFAULT 0,
  retenue_garantie numeric DEFAULT 0,
  notes text,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);

-- Documents BTP (étend la logique Odoc)
CREATE TABLE documents_btp (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id uuid REFERENCES auth.users NOT NULL,
  chantier_id uuid REFERENCES chantiers(id),
  type text NOT NULL, -- devis | situation | ppsps | doe | dc4 | pv_reception | cctp | bon_commande | contrat
  titre text NOT NULL,
  file_url text,
  statut text DEFAULT 'pending',
  extracted_data jsonb,
  summary text,
  lot text,           -- lot électricité, maçonnerie, etc.
  numero_situation int,
  montant_ht numeric,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);

-- Factures BTP
CREATE TABLE factures_btp (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id uuid REFERENCES auth.users NOT NULL,
  chantier_id uuid REFERENCES chantiers(id),
  document_id uuid REFERENCES documents_btp(id),
  type text NOT NULL,       -- facture | situation | avoir | acompte
  statut text DEFAULT 'draft',
  numero_facture text,
  fournisseur_nom text,
  fournisseur_siret text,
  montant_ht numeric,
  montant_tva numeric,
  taux_tva numeric,
  montant_ttc numeric,
  autoliquidation_tva boolean DEFAULT false, -- sous-traitance BTP
  retenue_garantie numeric DEFAULT 0,
  date_facture date,
  date_echeance date,
  statut_approbation text DEFAULT 'pending',
  archive_hash text,
  fraud_score int,
  ai_confidence_score numeric,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);

-- Réserves chantier (PV réception)
CREATE TABLE reserves_chantier (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  chantier_id uuid REFERENCES chantiers(id),
  user_id uuid REFERENCES auth.users NOT NULL,
  description text NOT NULL,
  lot text,
  statut text DEFAULT 'ouverte', -- ouverte | en_cours | levee
  date_constat date,
  date_levee date,
  priorite text DEFAULT 'normale',
  created_at timestamptz DEFAULT now()
);
```

### Tables réutilisées d'Odoc (copy-paste)
- `profiles` — identique
- `team_members` — identique
- `notifications` — identique
- `brain_sessions` + `brain_messages` — identique
- `connectors` — identique

---

## 🚀 ROADMAP 10 JOURS — Sprints Claude Code

---

### 📅 JOUR 1 — Setup & Architecture Core
**Durée** : 6-8h Claude Code  
**Objectif** : Repo clean, auth fonctionnel, navigation BTP en place

**Prompt Claude :**
> *"Crée un SaaS React+TypeScript+Vite+Tailwind+Supabase nommé Copilot BTP. Même architecture qu'Odoc (v1.0.7). Auth Supabase email/password. Layout sidebar avec routes : Dashboard, Chantiers, Documents, Factures, Suivi Chantier, Analytics, Brain BTP, Équipe, Paramètres. Design sombre indigo/jaune BTP. Shadcn/UI. Icônes Lucide. Mobile-first."*

**Livrables :**
- [ ] Repo GitHub initialisé `copilot-btp`
- [ ] Auth Login/Signup Supabase fonctionnel
- [ ] Sidebar + routing 10 routes
- [ ] Layout + design system BTP (couleurs : indigo + ambre/jaune)
- [ ] Page 404 + Loading states
- [ ] Hook `useAuth.ts` opérationnel
- [ ] Deploy Lovable/Vercel preview

---

### 📅 JOUR 2 — Tables Supabase + Dashboard BTP
**Durée** : 6-8h Claude Code  
**Objectif** : Toutes les tables créées, dashboard avec KPIs réels

**Prompt Claude :**
> *"Crée les tables Supabase : chantiers, documents_btp, factures_btp, reserves_chantier, profiles, team_members, notifications, brain_sessions, brain_messages, connectors. RLS activé sur toutes (user_id). Dashboard BTP avec 6 KPIs : chantiers actifs, CA en cours, factures en attente, réserves ouvertes, avancement moyen %, alertes échéances."*

**Livrables :**
- [ ] 9 tables créées avec RLS
- [ ] Migrations SQL propres
- [ ] Hook `useChantiers.ts` (CRUD)
- [ ] Dashboard avec KPIs connectés (données réelles)
- [ ] Alertes Dashboard (impayés, réserves, échéances)

---

### 📅 JOUR 3 — Module Chantiers
**Durée** : 6-8h Claude Code  
**Objectif** : CRUD chantiers complet, fiches chantier

**Prompt Claude :**
> *"Module Chantiers complet. Liste chantiers avec filtres (statut, date, client). ChantierCard avec statut badge (planifié/en cours/réception/terminé/litige). ChantierDrawer : infos générales, avancement %, lots, montant marché, retenue garantie, documents liés, factures liées, réserves. Création/édition chantier. Couleurs statut : vert/orange/bleu/gris/rouge."*

**Livrables :**
- [ ] Liste chantiers paginée + filtres
- [ ] ChantierDrawer avec toutes les infos
- [ ] CRUD chantier (créer/éditer/archiver)
- [ ] ChantierStatusBadge
- [ ] Lien chantier → documents → factures

---

### 📅 JOUR 4 — GED Documents BTP + IA Extraction
**Durée** : 8-10h Claude Code  
**Objectif** : Upload docs BTP, extraction IA Gemini spécialisée BTP

**Prompt Claude :**
> *"GED Documents BTP. Upload PDF/image. Edge Function analyze-btp-document avec Gemini Flash. Extraire : type de doc (devis/situation/PPSPS/DOE/DC4/PV), numéro chantier, maître d'ouvrage, lot, montant HT, avancement %, retenue garantie, SIRET, dates. DocumentsDataTable avec filtres type/chantier/statut. Drawer détail avec données extraites. Même pattern que Odoc."*

**Livrables :**
- [ ] Upload docs multi-types BTP
- [ ] Edge Function `analyze-btp-document` (Gemini)
- [ ] Extraction : type, lot, montant, avancement, SIRET, dates
- [ ] Interface DocumentsDataTable BTP
- [ ] Lien document ↔ chantier
- [ ] `extractedData` JSON BTP enrichi

---

### 📅 JOUR 5 — Facturation BTP IA
**Durée** : 8-10h Claude Code  
**Objectif** : Module factures BTP complet avec spécificités métier

**Prompt Claude :**
> *"Module Facturation BTP. Edge Function extract-facture-btp : extraction Gemini + détection autoliquidation TVA (sous-traitance BTP art. 283-2 CGI). Situations de travaux avec n° situation + avancement %. Retenue de garantie (5%). Circuit approbation. Statuts (brouillon/validé/envoyé/payé/litigieux). Export FEC. Alertes échéances J-7/J-3. KPIs : CA HT, TVA due, situations en attente, retenues totales."*

**Livrables :**
- [ ] Edge Function `extract-facture-btp`
- [ ] Détection autoliquidation TVA automatique
- [ ] Situations de travaux numérotées
- [ ] Retenue garantie 5% calculée
- [ ] Circuit d'approbation BTP
- [ ] Export FEC BTP
- [ ] KPIs factures BTP
- [ ] Alertes impayés (cron + notifications)

---

### 📅 JOUR 6 — Suivi Chantier (killer feature)
**Durée** : 8-10h Claude Code  
**Objectif** : Module différenciateur — planification, réserves, PV

**Prompt Claude :**
> *"Module Suivi Chantier. Planning Gantt simplifié (lots × dates). Réserves chantier : liste, statut (ouverte/en cours/levée), lot, priorité, date levée. PV réception avec signature. Avancement par lot (%). Vue mobile-first pour utilisation terrain. Alertes réserves non levées après 30 jours. KPIs : % avancement global, nb réserves ouvertes, retard jours, marge estimée."*

**Livrables :**
- [ ] Planning lots × dates (Gantt simplifié)
- [ ] CRUD Réserves chantier
- [ ] PV réception avec export PDF
- [ ] Avancement par lot en %
- [ ] Vue mobile terrain optimisée
- [ ] Alertes réserves non levées

---

### 📅 JOUR 7 — Brain BTP (Copilot IA Métier)
**Durée** : 8-10h Claude Code  
**Objectif** : Copilot IA spécialisé BTP — différenciateur IA fort

**Prompt Claude :**
> *"Brain BTP : page fullscreen chat IA. Edge Function brain-btp-query avec Gemini. Contexte : documents chantier sélectionnés. Capabilities : lire CCTP et extraire obligations, détecter risques chantier, suggérer variantes devis, calculer devis à partir de métrés, expliquer clauses marché, analyser situations de travaux. Sessions persistées brain_sessions/brain_messages. Split view docs + chat. Suggestions rapides BTP."*

**Livrables :**
- [ ] Edge Function `brain-btp-query`
- [ ] Contexte documents BTP injecté
- [ ] Suggestions rapides : Analyse CCTP | Risques chantier | Aide devis | Explique clause
- [ ] Sessions persistées
- [ ] Split view docs/chat
- [ ] Réponses IA avec sources citées

---

### 📅 JOUR 8 — Analytics BTP Multi-Vues
**Durée** : 6-8h Claude Code  
**Objectif** : Analytics métier BTP (chantier, comptable, opérationnel)

**Prompt Claude :**
> *"Analytics BTP 3 vues : (1) Vue Chantier — avancement tous chantiers, retards, marges, CA par chantier, top clients. (2) Vue Comptable — CA HT/TTC, TVA, balance âgée, situations en attente, retenues. (3) Vue Opérationnelle — taux automatisation, temps traitement doc, alertes actives, KPIs IA. Même architecture qu'Odoc analytics. Recharts. Export CSV/PDF."*

**Livrables :**
- [ ] `btpAggregation.ts` avec 10+ fonctions KPIs
- [ ] VueChantierAnalytics.tsx
- [ ] VueComptableAnalytics.tsx
- [ ] VueOperationnelleAnalytics.tsx
- [ ] Export CSV + PDF
- [ ] Filtres date/chantier

---

### 📅 JOUR 9 — Admin + Team + Settings + Notifications
**Durée** : 6-8h Claude Code  
**Objectif** : Tous les modules support copy-paste Odoc + adaptés BTP

**Prompt Claude :**
> *"Copy exact de Odoc v1.0.7 pour : Team.tsx (membres équipe chantier + rôles : chef de chantier, conducteur travaux, artisan, admin), Settings.tsx (5 tabs + intégrations Google Drive), Notifications.tsx, OnboardingWizard BTP (4 étapes : profil artisan, type métier, 1er chantier, prêt). Super Admin Dashboard mock (même pattern Odoc) avec KPIs BTP : chantiers actifs total, MRR, users, alertes. Mettre à jour tous les textes en contexte BTP."*

**Livrables :**
- [ ] Team.tsx BTP (rôles chantier)
- [ ] Settings.tsx 5 tabs
- [ ] OnboardingWizard BTP (4 étapes)
- [ ] Notifications système
- [ ] Admin Dashboard mock KPIs BTP
- [ ] Connecteurs Google Drive (plans, docs chantier)

---

### 📅 JOUR 10 — Site Marketing + Polish + Stripe + Deploy
**Durée** : 6-8h Claude Code  
**Objectif** : Prod-ready, bêta 10 artisans, destroy les concurrents

**Prompt Claude :**
> *"Site marketing Copilot BTP : hero 'Votre copilot IA de chantier', sections Problème (paperasse BTP), Solution (10 modules), Pour qui (maçon/électricien/plombier), Pricing 3 plans (Starter 0€ / Essentiel 29€ / Pro 79€), Blog, CGU/CGV BTP, Mentions légales. Stripe abonnements 3 plans. MFA obligatoire. PWA manifest pour usage terrain. Logo BTP indigo+ambre. SIRET obligatoire onboarding. RGPD conforme."*

**Livrables :**
- [ ] Site marketing BTP complet
- [ ] Pricing 0/29/79 € + Stripe
- [ ] CGU/CGV/Mentions légales
- [ ] PWA manifest (mobile terrain)
- [ ] MFA Auth
- [ ] RGPD + cookies
- [ ] Deploy prod app.copilot-btp.fr
- [ ] Beta 10 artisans onboardés

---

## 📊 Récap Budget & Timeline

| Indicateur | Valeur |
|---|---|
| Durée totale | **10 jours** |
| Heures Claude Code | **~70-90h** |
| Réutilisation Odoc | **~70%** |
| Coût infra/mois (2 projets) | **~100 €** (Supabase ×2 + Lovable) |
| Équivalent agence | **40 000-60 000 €** |
| Coût réel avec Claude Max | **< 500 €** |

---

## 🧨 Pourquoi On Détruit Les Concurrents

| Concurrent | Leur Faiblesse | Notre Avantage |
|---|---|---|
| Tolteck / Obat | Zéro IA, interface 2015 | IA extraction + Brain BTP |
| Batappli | Lourd, desktop only | Mobile-first terrain |
| Batigest (Sage) | 150 €/mois, complexe | 29 €/mois, simple |
| Procore | PME/ETI seulement | TPE artisan first |
| Alobees | Pas de facturation | GED + Factures + Brain = tout-en-un |

**Notre moat** : combinaison GED × Facturation IA × Suivi Chantier × Brain IA dans un seul outil à prix artisan, avec conformité e-facture sept. 2026 intégrée.

---

## 🌍 Roadmap Post-Beta (Mois 2-6)

- **Mois 2** : Intégration PPF (facturation électronique DGFiP)
- **Mois 3** : Export PPSPS automatique IA
- **Mois 4** : Module sous-traitants (DC4 auto, attestation fiscale 3406)
- **Mois 5** : Open Banking (paiements fournisseurs depuis l'app)
- **Mois 6** : Version Belgique 🇧🇪 + Espagne 🇪🇸 (même stack, i18n)

---

## 🔧 Stack Technique Complète

```
Frontend  : React 18 + TypeScript + Vite + Tailwind CSS
UI        : Shadcn/UI + Lucide Icons + Recharts + Framer Motion
Backend   : Supabase (DB PostgreSQL + Auth + Storage + RLS)
IA        : Gemini 1.5 Flash (docs) + Gemini 2.5 Flash (Brain)
Edge      : Supabase Edge Functions (Deno)
Emails    : Resend
Paiements : Stripe
Deploy    : Lovable Cloud / Vercel
SEO       : react-helmet-async + sitemap dynamique
Outils    : Claude Code + Claude Max + Continue.dev
```

---

*Document généré le 04/04/2026 — Copilot BTP v0.1 Roadmap*  
*Architecture inspirée de Odoc v1.0.7 — 70% réutilisable*
