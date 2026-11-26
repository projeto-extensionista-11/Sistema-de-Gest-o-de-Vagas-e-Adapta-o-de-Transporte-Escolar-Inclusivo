# CLAUDE.md — Guia de Contexto e Regras do Design System (Figma MCP)

Este arquivo fornece contexto imediato para assistentes de IA e para o servidor **Figma MCP** ao traduzir protótipos em código ou inspecionar a arquitetura do **Projeto 16: Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo**.

## 1. Resumo da Stack
- **Frontend:** Next.js (App Router) + React + TypeScript + Tailwind CSS + `shadcn/ui` (Radix UI) + `TanStack Query` + `React Hook Form` + `Zod` + `react-leaflet` + `lucide-react`.
- **Backend:** Python + Django + Django REST Framework (Monólito Modular em `backend/apps/`).
- **Banco de Dados:** PostgreSQL + PostGIS (gerenciado via Supabase, `SRID 4326`).

## 2. Regras Obrigatórias de Design System e Acessibilidade (LBI)
1. **Tokens Semânticos:** Utilizar sempre as variáveis CSS/Tailwind (`bg-background`, `text-foreground`, `bg-primary`, `bg-green-600` para check-in confirmado, `bg-violet-600` para badges de acessibilidade/cadeira de rodas).
2. **Contraste e Alvos de Toque por Persona:**
   - **Rotas Mobile de Campo (`/monitor`, `/motorista`, `/familia`):** Layout mobile-first (`360px+`), fonte mínima `18px` (`text-lg`), botões de ação principal com altura mínima de `56px` (`h-14`) e contraste WCAG AAA (`>= 7:1`) para uso sob luz solar.
   - **Rotas Web Desktop (`/gestor`, `/escola`):** Layout em painel com sidebar, KPIs, tabelas acessíveis e mapa `react-leaflet`.
3. **Ícones:** Usar exclusivamente `lucide-react` com `aria-hidden="true"` em ícones decorativos e `aria-label` em português para botões de ação rápida.
4. **Segurança e LGPD:** O Frontend **nunca** acessa o banco Supabase diretamente com chaves privilegiadas; toda leitura/escrita de `Student`, `StudentNeed`, `Trip` e `AttendanceEvent` passa pela API REST do Django.

Consulte [`docs/design-system-rules.md`](docs/design-system-rules.md) para a especificação completa dos 7 pilares do Design System e [`docs/wireframes-fluxo-telas.md`](docs/wireframes-fluxo-telas.md) para o mapeamento das telas das 5 personas.
