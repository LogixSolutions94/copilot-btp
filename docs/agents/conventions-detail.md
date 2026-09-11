# Conventions détaillées — Copilot BTP

> Détail référencé depuis [`../../CLAUDE.md`](../../CLAUDE.md) (section « Règles de Développement »). Projet en pause depuis le bilan du 02/09/2026.

---

## Logging & Error Handling

### Frontend (Client)
```typescript
// User-facing errors
if (isError) return <div className="text-red-500">{error.message}</div>

// Technical logs (DEV only)
if (import.meta.env.DEV) console.log("[CHANTIERS]", data)
```

### Edge Functions
```typescript
console.error("[analyze-btp-document]", error.message)  // Logs visibles Supabase
return new Response(JSON.stringify({ error: "Invalid document" }), { status: 400 })
```

### Observability (Post-Jour 10)
- Consider Sentry, Datadog, ou logs Supabase Studio
- Never log API keys, SIRET, or sensitive user data

---

## Commits — pattern et exemples

**Pattern :**
```
feat(module): description courte
fix(bug): description courte
db(schema): description courte
docs(section): description courte
```

**Exemples :**
```
feat(day1): setup auth + sidebar routing (10 routes)
feat(chantiers): CRUD chantiers + ChantierDrawer
feat(edge): analyze-btp-document Gemini extraction
fix(rls): correct SELECT policy on documents_btp
db(schema): add reserves_chantier table
```

**PR Rules :**
- Toujours merge sur `main` (trunk-based)
- 1 commit par feature logique (squash si besoin)
- Pas de `git push --force` sans approbation
- CI/CD check : TypeScript strict + tests
