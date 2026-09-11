# Setup local & debug — Copilot BTP

> Détail référencé depuis [`../../CLAUDE.md`](../../CLAUDE.md) (section « Commandes »). Projet en pause depuis le bilan du 02/09/2026.

---

## Pré-requis (avant Jour 1)
- **Node** ≥ 20.0
- **npm** ou **pnpm** (recommandé)
- `npm install -g @supabase/cli`
- Compte Supabase (create project)
- Clés API : Gemini, Stripe, Resend
- IDE : VS Code + extensions TypeScript/Tailwind

## Debug
- **Browser DevTools** : React Query DevTools, Network tab, Console logs
- **Supabase Studio** : https://supabase.com/dashboard → view DB/RLS live, browse tables
- **Console logs** : visible dans `npm run dev` terminal
- **Gemini API** : test via `/supabase/functions/` localement
