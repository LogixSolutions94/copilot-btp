# Architecture détaillée — Copilot BTP

> Détail référencé depuis [`../../CLAUDE.md`](../../CLAUDE.md) (section « Architecture — Règles Absolues »). Projet en pause depuis le bilan du 02/09/2026.
> Voir aussi [`ARCHITECTURE.md`](../../ARCHITECTURE.md) à la racine pour les patterns de code (hooks, edge functions, types complets).

---

## Patterns (copier depuis ARCHITECTURE.md)
- **Hooks** : React Query pattern (voir [ARCHITECTURE.md](../../ARCHITECTURE.md))
- **Edge Functions** : 5-step pattern Supabase (recevoir → fetch → Gemini → update → retourner)
- **RLS** : Ownership simple + shared tables (voir [ARCHITECTURE.md](../../ARCHITECTURE.md) cas 1 & 2)

---

## Base de Données — Tables & RLS

### 11 Tables (créées Jour 2)
Voir [ROADMAP.md](../../ROADMAP.md) Jour 2 pour le SQL complet.
- **NEW BTP** : chantiers, documents_btp, factures_btp, reserves_chantier, vw_dashboard_kpis
- **Copié Odoc** : profiles, team_members, notifications, brain_sessions, brain_messages, connectors

### RLS — Règles Universelles
Voir [ARCHITECTURE.md](../../ARCHITECTURE.md) pour les patterns RLS.
- **Cas 1** : Ownership simple (`auth.uid() = user_id`)
- **Cas 2** : Shared tables via `team_id` (team_members)
- **Important** : Service role (`SUPABASE_SERVICE_ROLE_KEY`) bypass RLS — uniquement en Edge Functions

### Variables d'Environnement
Voir `.env.example` et [ARCHITECTURE.md](../../ARCHITECTURE.md) pour la liste complète.
- **Secrets** (`.env.local`, jamais commitées) : `SUPABASE_SERVICE_ROLE_KEY`, `GEMINI_API_KEY`, `RESEND_API_KEY`, `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`
- **Publiques** (OK en `.env`) : `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`, `VITE_STRIPE_PUBLISHABLE_KEY`

---

## Structure Fichiers

Voir [ROADMAP.md](../../ROADMAP.md) pour la liste complète des fichiers et composants.

**Clés :**
- `pages/` : 10 routes (Index, Auth, Chantiers, Documents, Factures, SuiviChantier, Analytics, Brain, Team, Settings, Notifications, admin)
- `components/` : Sous-dossiers par domaine (chantiers/, documents/, factures/, brain/, analytics/, suivi/, ui/)
- `hooks/` : React Query pattern (useChantiers, useDocuments, useFactures, useBrainSessions, etc.)
- `lib/` : btpAggregation (KPIs), btpDocTypes (types), supabase client
- `supabase/functions/` : Edge Functions Gemini (analyze-btp-document, brain-btp-query, extract-facture-btp)

**Répertoires supplémentaires :**
```
.env.example              # Template variables (copier en .env.local + remplir secrets)
supabase/migrations/      # SQL migrations (1 par table créée)
supabase/seed.sql        # Données test (DEV only)
public/                  # Assets statiques (favicon, images)
```

---

## Types TypeScript Critiques

Voir [ARCHITECTURE.md](../../ARCHITECTURE.md) pour les interfaces complètes : `ExtractedDataBTP`, `Chantier`, `FactureBTP`, `GeminiExtraction`.

**Essentiels :**
- `ExtractedDataBTP` : pour toute extraction Gemini
- `ChantierStatus` : 'planifie' | 'en_cours' | 'reception' | 'termine' | 'litige'
- `FactureBTP` : extends ExtractedDataBTP + statut_paiement
- `GeminiExtraction<T>` : réponse standardisée { success, extracted_data, confidence, error }

---

## Copier depuis Odoc v1.0.7

**Où trouver Odoc ?** (À remplir selon accès)
- [ ] Demander le snapshot Odoc à [owner/lien repo privé]

**Checklist Copie :**
- [ ] Dossier `components/ui/` — Shadcn/UI components (exact copie)
- [ ] Hook `useAuth.ts` — Auth pattern Supabase
- [ ] Hook `useTeamMembers.ts`, `useProfile.ts`, `useNotifications.ts`
- [ ] Pages : `Auth.tsx`, `Team.tsx`, `Settings.tsx`, `Notifications.tsx`, `admin/`
- [ ] Adapter `types/` pour types BTP (ExtractedDataBTP, Chantier, FactureBTP)

**À adapter (ne pas copier brut) :**
- `hooks/useDocuments.ts` → ajouter champs BTP (document_type, extracted_data, lot, numero_situation)
- `hooks/useFactures.ts` → ajouter autoliquidation_tva, retenue_garantie
- `lib/exportFEC.ts` → vérifier conformité PPF 2026 e-facture
