# 🏛️ Architecture Copilot BTP

> Ce document est destiné à **Claude Code** pour reproduire rapidement l'architecture complète.  
> Base : Odoc v1.0.7 — réutilisation 70% — adaptation BTP 30%

---

## Commande d'initialisation Claude

```
Crée un projet React + TypeScript + Vite nommé "copilot-btp".
Même architecture exacte qu'Odoc v1.0.7.
Stack : React 18, TypeScript strict, Vite, Tailwind CSS, Shadcn/UI, Supabase, Lucide, Recharts, Framer Motion, React Query, React Router DOM.
Couleurs : indigo (primaire) + ambre/jaune (accent BTP).
Thème sombre par défaut.
Mobile-first.
See ROADMAP.md for full spec.
```

---

## Pattern Hooks (identique Odoc)

```typescript
// Chaque hook suit ce pattern React Query :
const useChantiers = () => {
  const { data, isLoading, error } = useQuery(['chantiers'], fetchChantiers)
  const createMutation = useMutation(createChantier, { onSuccess: () => queryClient.invalidateQueries(['chantiers']) })
  return { chantiers: data, isLoading, error, create: createMutation.mutate }
}
```

## Pattern Edge Functions (identique Odoc)

```typescript
// supabase/functions/analyze-btp-document/index.ts
// 1. Recevoir documentId
// 2. Télécharger fichier depuis Storage
// 3. Appeler Gemini Flash avec prompt BTP spécialisé
// 4. UPDATE documents_btp SET extracted_data = {...}, statut = 'analyzed'
// 5. Retourner extracted_data
```

## extractedData BTP — Structure JSON

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
  autoliquidation_tva?: boolean  // sous-traitance BTP
  retenue_garantie?: number
  fournisseur?: { nom: string; siret: string; adresse: string }
  date_document?: string
  date_echeance?: string
  references?: string[]
  summary: string
}
```

---

## RLS Policies (identique Odoc — appliquer sur toutes les tables)

```sql
-- Exemple pour chantiers
ALTER TABLE chantiers ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users CRUD own chantiers" ON chantiers
  FOR ALL USING (auth.uid() = user_id);
```

---

## Variables d'environnement

```env
VITE_SUPABASE_URL=
VITE_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
GEMINI_API_KEY=
RESEND_API_KEY=
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
```

---

*Voir ROADMAP.md pour la roadmap complète 10 jours.*
