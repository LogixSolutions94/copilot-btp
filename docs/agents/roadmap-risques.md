# Roadmap, risques & quick start — Copilot BTP

> Détail référencé depuis [`../../CLAUDE.md`](../../CLAUDE.md) (section « Documentation détaillée »). Projet en pause depuis le bilan du 02/09/2026 — roadmap et statuts ci-dessous figés à cette date.
> Voir aussi [`ROADMAP.md`](../../ROADMAP.md) pour les prompts détaillés par jour et [`PLAN STRATÉGIQUE COPILOT BTP`](../../PLAN%20STRAT%C3%89GIQUE%20COPILOT%20BTP) pour les décisions/sprints complets.

---

## Roadmap Sprint — État Actuel

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

## Risques — Mitigation Active

| Risque | Priorité | Solution |
|--------|----------|----------|
| Gemini API timeout | 🔴 CRITIQUE | Fallback Groq + timeout 30s max |
| Supabase RLS cassée | 🔴 CRITIQUE | Tester J2 matin avant tout |
| Scope creep | 🟠 HAUTE | Couper toute feature > 6h estimée |
| Mobile responsive | 🟠 MOYENNE | Tester sur device réel chaque jour |
| Stripe webhook | 🟢 BASSE | Mock jusqu'à J10, puis vrai webhook |

---

## Quick Start — Lancer Jour 1

**Vérifications avant Jour 1 :**
- [ ] Lire CLAUDE.md en entier (ce fichier)
- [ ] Lire [ARCHITECTURE.md](../../ARCHITECTURE.md)
- [ ] Lire [ROADMAP.md](../../ROADMAP.md) Jour 1 section
- [ ] Vérifier [CHECKLIST PRÉ-LANCEMENT](../../CHECKLIST%20PR%C3%89-LANCEMENT%20COPILOT%20BTP) : secrets `.env.local` prêts, Supabase projet créé

**Commande exacte pour Jour 1 :**
```
Lis CLAUDE.md en entier.
Lis ARCHITECTURE.md (patterns, types, RLS).
Lis ROADMAP.md section "JOUR 1" pour le prompt exact.

Stack confirmée : React 18 + TS strict + Vite + Tailwind + Shadcn/UI + Supabase + Gemini.
Secrets .env.local : VITE_SUPABASE_URL, VITE_SUPABASE_ANON_KEY, SUPABASE_SERVICE_ROLE_KEY, GEMINI_API_KEY.
DB Schema SQL : Jour 2 — ne pas lancer avant.

Jour 1 : Setup Vite project + Auth Supabase + Sidebar BTP (10 routes) + Dashboard mock 6 KPIs.
Design : thème sombre forcé, indigo primaire, ambre accent.
Mobile-first breakpoint 375px. TypeScript strict (zéro `any`, zéro errors).
Lovable deploy CI/CD inclus si préparé.

Prompt complet dans ROADMAP.md > JOUR 1.
Go !
```
