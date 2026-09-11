# Qualité, métriques & ressources — Copilot BTP

> Détail référencé depuis [`../../CLAUDE.md`](../../CLAUDE.md) (section « Documentation détaillée »). Projet en pause depuis le bilan du 02/09/2026.

---

## Métriques de Succès — Checklist Finale

### MVP Beta (Jour 10)
- [ ] 5 artisans beta onboardés
- [ ] 3/5 ont créé ≥ 1 chantier avec documents
- [ ] Extraction IA moyenne > 80% accuracy (vs 5 documents test annotés)
- [ ] **0 bug P1 (critique)**
- [ ] Response time API < 3s (hors Gemini)
- [ ] Lighthouse mobile (375px) > 80
- [ ] Bundle < 500KB gzip (`npm run build`)

### Bug Classification
- **P1 (Critique)** : Perte de données, auth bypass, crash app, RLS broken
- **P2 (Urgent)** : Feature cassée, slow query (>5s), UI glitch majeur
- **P3 (Normal)** : Typo, optimisation, style mineur

### Quality Gates Code
- [ ] TypeScript strict = 0 erreurs (`npm run type-check`)
- [ ] No `any` type
- [ ] RLS 100% coverage (toutes tables testées)
- [ ] Tous les async ont error handling (try/catch)
- [ ] Mobile 375px OK sur toutes les pages
- [ ] Aucune clé API hardcodée (secrets en `.env.local`)

---

## Ressources Utiles

- **Supabase console** : https://supabase.com/dashboard
- **Gemini API docs** : https://ai.google.dev/docs
- **Shadcn/UI** : https://ui.shadcn.com
- **Lovable** : https://lovable.dev
- **Stripe** : https://dashboard.stripe.com/test
- **Resend** : https://resend.com/docs
