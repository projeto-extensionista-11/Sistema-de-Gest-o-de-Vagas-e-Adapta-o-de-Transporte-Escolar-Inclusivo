# 🎨 Regras do Design System e Guia de Integração Figma-to-Code

**Projeto 16:** Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo  
**Referência:** Especificação gerada via `/mcp:figma_figma:create_design_system_rules`  
**Responsáveis Frontend & UI/UX:** Eduardo Guerra e Erick

Este documento estabelece as diretrizes oficiais de Design System, tokens, arquitetura de componentes, acessibilidade (**LBI / WCAG 2.1 AA e AAA**) e organização de código para a integração contínua entre os protótipos no **Figma (via MCP)** e o código **Frontend (`Next.js + TypeScript + Tailwind CSS + shadcn/ui`)**.

---

## 1. Token Definitions (Definição de Tokens de Design)

### 1.1 Onde os tokens são definidos
- **Arquivo principal de tokens CSS:** `frontend/src/app/globals.css` (utilizando variáveis CSS nativas no padrão HSL/OKLCH consumidas pelo Tailwind CSS e pelo `shadcn/ui`).
- **Mapeamento para o Tailwind:** `frontend/tailwind.config.ts`.

### 1.2 Estrutura e Paleta Semântica Inclusiva (Alto Contraste)
Como o sistema é operado em **ambientes externos com reflexo solar** (pela monitora Marisa ao lado da rampa elevatória e pelo motorista Seu Valdir no painel da van), a paleta prioriza **contraste mínimo de 7:1 (WCAG AAA) em ações críticas de campo** e **4.5:1 (WCAG AA) nos painéis administrativos**.

```css
/* frontend/src/app/globals.css */
@layer base {
  :root {
    /* Superfícies e Texto Base */
    --background: 210 40% 98%;        /* #F8FAFC - Slate 50 (Fundo limpo sem ofuscamento) */
    --foreground: 222.2 84% 4.9%;     /* #0F172A - Slate 900 (Texto principal de alto contraste) */
    --card: 0 0% 100%;                /* #FFFFFF - Cards e painéis */
    --card-foreground: 222.2 84% 4.9%;

    /* Identidade Institucional & Inclusão (Azul Acessibilidade) */
    --primary: 221.2 83.2% 53.3%;     /* #2563EB - Blue 600 (Ações primárias e cabeçalhos) */
    --primary-foreground: 210 40% 98%;

    /* Estados Operacionais de Viagem e Check-in (Críticos para US01) */
    --success: 142.1 76.2% 36.3%;     /* #16A34A - Green 600 (Aluno Embarcado / Check-in confirmado) */
    --success-foreground: 355.7 100% 97.3%;
    --warning: 32.1 94.6% 43.7%;      /* #D97706 - Amber 600 (Aguardando Rampa / Van a 5 min do portão) */
    --warning-foreground: 0 0% 100%;
    --destructive: 0 84.2% 60.2%;     /* #DC2626 - Red 600 (Alerta de Segurança / Ausência / Pendência) */
    --destructive-foreground: 210 40% 98%;

    /* Destaque de Acessibilidade (Elevador Hidráulico / Cadeira de Rodas) */
    --accessible-accent: 262.1 83.3% 57.8%; /* #7C3AED - Violet 600 (Badge de Vaga Adaptada / Cadeirante) */

    /* Bordas, Inputs e Raio */
    --border: 214.3 31.8% 91.4%;      /* #E2E8F0 - Slate 200 */
    --input: 214.3 31.8% 91.4%;
    --ring: 221.2 83.2% 53.3%;        /* Anel de foco visível para navegação por teclado */
    --radius: 0.75rem;                /* 12px - Bordas amigáveis e áreas de toque claras */
  }
}
```

### 1.3 Tipografia e Escala de Espaçamento
- **Fonte Principal (UI):** `Inter` ou `Plus Jakarta Sans` (`next/font/google`), escolhida pela alta legibilidade de números (horários de chegada, placas e contadores de vagas).
- **Fonte Monoespaçada (Placas, Protocolos e Coordenadas):** `JetBrains Mono`.
- **Escala Tipográfica por Perfil:**
  - *Painel Web (Gestor Fernando / Diretora Tássia):* Base `14px` (`text-sm`) a `16px` (`text-base`), títulos `20px–28px`.
  - *App de Campo (Monitora Mari / Motorista Valdir):* Base mínima `18px` (`text-lg`), nomes de alunos e endereços de parada em `20px–24px` (`text-xl`/`text-2xl` em `font-bold`).
- **Alvos de Toque (Touch Targets):**
  - Mínimo absoluto: `48px x 48px` (`min-h-12`).
  - Botões operacionais de Check-in/Check-out (`US01`) e Condução (`US09`): `56px` a `64px` de altura (`h-14` / `h-16`).

---

## 2. Component Library (Biblioteca e Arquitetura de Componentes)

### 2.1 Onde os componentes são definidos
Os componentes seguem uma separação em duas camadas dentro de `frontend/src/components/`:

1. **`frontend/src/components/ui/` (Primitivos Base — `shadcn/ui` + Radix UI):**
   - `button.tsx`, `card.tsx`, `badge.tsx`, `dialog.tsx`, `form.tsx`, `input.tsx`, `select.tsx`, `table.tsx`, `tabs.tsx`, `toast.tsx`, `avatar.tsx`, `alert.tsx`.
2. **`frontend/src/components/domain/` (Componentes de Domínio do Transporte Inclusivo):**
   - `StudentCheckInCard.tsx`: Card da monitora Marisa com foto/iniciais do aluno, badge de cadeira de rodas (`wheelchair_type`), indicador de acompanhante e botão de 1 toque (`Embarcar na Rampa` / `Desembarcar na Escola`).
   - `DriverNextStopBanner.tsx`: Painel simplificado do motorista Seu Valdir com a próxima parada em fonte gigante, indicador de uso do elevador hidráulico e botão de comando de voz/GPS.
   - `FleetLiveMap.tsx`: Mapa interativo (`react-leaflet`) com marcadores customizados de vans adaptadas, paradas ordenadas (`RouteStop.sequence`) e escolas.
   - `SchoolArrivalBoard.tsx`: Painel da Diretora Tássia com contagem regressiva (ETA) das vans chegando ao portão e número de alunos cadeirantes a bordo.
   - `GuardianTripTimeline.tsx`: Linha do tempo em tempo real para a mãe Solange (`Van a caminho` → `Embarcado às 07:12` → `Na Escola às 07:35`).

### 2.2 Padrão de Arquitetura de Componente (Exemplo)

```tsx
// frontend/src/components/domain/StudentCheckInCard.tsx
"use client";

import { Accessibility, CheckCircle2, QrCode, UserCheck } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Card, CardContent } from "@/components/ui/card";

interface StudentCheckInCardProps {
  studentName: string;
  stopAddress: string;
  wheelchairType?: "MANUAL" | "MOTORIZED" | "NONE";
  requiresCompanion: boolean;
  status: "WAITING" | "BOARDED" | "ALIGHTED";
  onOneTapConfirm: () => void;
}

export function StudentCheckInCard({
  studentName,
  stopAddress,
  wheelchairType = "NONE",
  requiresCompanion,
  status,
  onOneTapConfirm,
}: StudentCheckInCardProps) {
  return (
    <Card className="border-2 shadow-sm">
      <CardContent className="flex flex-col gap-4 p-4 sm:flex-row sm:items-center sm:justify-between">
        <div className="space-y-1">
          <div className="flex flex-wrap items-center gap-2">
            <h3 className="text-xl font-bold text-slate-900">{studentName}</h3>
            {wheelchairType !== "NONE" && (
              <Badge className="bg-violet-600 text-white hover:bg-violet-700">
                <Accessibility className="mr-1 h-4 w-4" aria-hidden="true" />
                Cadeira ({wheelchairType === "MOTORIZED" ? "Motorizada" : "Manual"})
              </Badge>
            )}
            {requiresCompanion && (
              <Badge variant="secondary">
                <UserCheck className="mr-1 h-4 w-4" aria-hidden="true" />
                Com Acompanhante
              </Badge>
            )}
          </div>
          <p className="text-base text-slate-600">{stopAddress}</p>
        </div>

        <Button
          size="lg"
          onClick={onOneTapConfirm}
          aria-label={`Confirmar ${status === "WAITING" ? "embarque" : "desembarque"} de ${studentName}`}
          className="h-14 min-w-[220px] text-lg font-bold bg-green-600 hover:bg-green-700 text-white"
        >
          <CheckCircle2 className="mr-2 h-6 w-6" aria-hidden="true" />
          {status === "WAITING" ? "Confirmar Embarque" : "Confirmar Desembarque"}
        </Button>
      </CardContent>
    </Card>
  );
}
```

---

## 3. Frameworks & Libraries (Stack de Frontend)

| Categoria | Tecnologia | Papel no Projeto |
| :--- | :--- | :--- |
| **Framework Principal** | **Next.js 15 (App Router) + React 19** | Roteamento por perfil, SSR/CSR e otimização de bundle |
| **Linguagem** | **TypeScript (Strict Mode)** | Contratos tipados alinhados aos serializers do Django DRF |
| **Estilização** | **Tailwind CSS + `clsx` + `tailwind-merge`** | Utilitários responsivos e composição segura de classes via helper `cn()` |
| **Componentes Acessíveis** | **shadcn/ui (Radix UI Primitives)** | Modais, selects, tabelas e formulários com WAI-ARIA nativo |
| **Estado de Servidor / API** | **TanStack Query (React Query v5)** | Cache, invalidação automática e sincronização de viagens e vagas |
| **Formulários e Validação** | **React Hook Form + Zod** | Validação tipada de CPF, CNH, capacidade de vagas e coordenadas |
| **Mapas e Geodados** | **Leaflet + `react-leaflet`** | Renderização de rotas otimizadas, paradas e GPS das vans |
| **Qualidade e Padronização** | **ESLint + Prettier** | Padronização automática de código entre Eduardo e Erick |

---

## 4. Asset Management (Gestão de Arquivos e Mídia)

- **Ativos Estáticos Públicos:** Armazenados em `frontend/public/` (ex: `public/icons/`, `public/manifest.json` para PWA, `public/markers/van-accessible.svg`, `public/markers/school-pin.svg`).
- **Otimização de Imagens:** Uso obrigatório do componente `<Image />` de `next/image` para logos institucionais e ilustrações.
- **Documentos Sensíveis e Laudos (`Supabase Storage`):**
  - Nunca são armazenados em pastas públicas do frontend.
  - São acessados exclusivamente via **URLs assinadas temporárias** geradas pelo Backend Django após verificação de permissão e vínculo organizacional (conformidade LGPD).

---

## 5. Icon System (Sistema de Ícones)

- **Biblioteca Oficial:** `lucide-react`.
- **Convenção de Importação:** Importação nomeada direta (`import { Bus, Accessibility, MapPin, Bell, ShieldCheck } from "lucide-react"`).
- **Mapeamento Semântico de Ícones do Domínio:**
  - `Bus` / `Truck`: Veículo da frota escolar.
  - `Accessibility`: Estudante cadeirante / Vaga adaptada / Elevador hidráulico.
  - `MapPin` / `Navigation`: Parada da rota (`RouteStop`) e navegação GPS.
  - `CheckCircle2` / `QrCode`: Confirmação de embarque (`BOARDING`) e desembarque (`ALIGHTING`).
  - `Bell` / `BellRing`: Notificação em tempo real para a mãe/responsável.
  - `Clock` / `AlertTriangle`: ETA de chegada na escola e alertas de atraso.
- **Regra de Acessibilidade:** Ícones puramente decorativos devem conter `aria-hidden="true"`. Botões que contenham apenas ícone devem obrigatoriamente definir `aria-label` descritivo em português.

---

## 6. Styling Approach (Abordagem de Estilização e Responsividade)

- **Metodologia:** Utility-first com **Tailwind CSS** combinado a variantes de componentes via `class-variance-authority` (`cva`).
- **Helper de Classes:** Uso padronizado de `cn(...inputs)` em `frontend/src/lib/utils.ts`.
- **Estratégia Responsiva por Persona:**
  - **Mobile-First (`360px` – `768px`):** Foco prioritário nas rotas `/monitor` (Marisa), `/motorista` (Seu Valdir) e `/familia` (Solange). Layout em coluna única, barra de navegação inferior fixa (`bottom-nav`), botões largos (`w-full h-14`) e zero tabelas horizontais complexas.
  - **Desktop-First (`1024px` – `1920px`):** Foco nas rotas `/gestor` (Eng. Fernando) e `/escola` (Profa. Tássia). Layout com Sidebar lateral recolhível, KPIs no topo, divisão em 2 painéis (Lista de Rotas/Vans à esquerda + Mapa Leaflet interativo à direita).

---

## 7. Project Structure (Organização Completa do Código)

```text
.
├── backend/                          # Monólito Modular Django + DRF (Rita, Erick, Edmilson, Karina)
│   ├── config/                       # settings.py, urls.py, asgi.py, wsgi.py
│   ├── apps/
│   │   ├── accounts/                 # User customizado (UUID), Auth JWT
│   │   ├── organizations/            # Organization, OrganizationMembership
│   │   ├── students/                 # Student, StudentNeed (acesso restrito LGPD)
│   │   ├── guardians/                # Guardian, StudentGuardian
│   │   ├── schools/                  # School (coordenadas PostGIS)
│   │   ├── vacancies/                # Vacancy, TransportRequest, Enrollment
│   │   ├── fleet/                    # Vehicle (elevador hidráulico), Driver, Monitor
│   │   ├── routes/                   # Route, RouteStop, otimizador OR-Tools
│   │   ├── trips/                    # Trip, TripMonitor, TripStudent, AttendanceEvent
│   │   ├── tracking/                 # VehicleLocation (histórico GPS)
│   │   ├── notifications/            # Notification + integração FCM
│   │   └── audit/                    # AuditLog
│   └── manage.py
│
├── frontend/                         # Next.js + TypeScript + Tailwind + shadcn/ui (Eduardo, Erick)
│   ├── public/
│   │   ├── manifest.json             # Configuração PWA para celular do monitor/família
│   │   └── markers/                  # Pins SVG acessíveis para o mapa Leaflet
│   ├── src/
│   │   ├── app/
│   │   │   ├── globals.css           # Design tokens CSS (HSL)
│   │   │   ├── layout.tsx            # Providers (QueryClient, Auth, Toast)
│   │   │   ├── (auth)/login/         # Autenticação unificada
│   │   │   ├── (campo)/              # Layout Mobile-First (botões grandes, alto contraste)
│   │   │   │   ├── monitor/          # Tela da Monitora Mari (Check-in/Out 1 toque - US01)
│   │   │   │   ├── motorista/        # Tela do Motorista Valdir (Próxima parada + GPS - US09)
│   │   │   │   └── familia/          # Tela da Mãe Solange (Mapa ao vivo + Avisos - US10)
│   │   │   └── (painel)/             # Layout Desktop Sidebar (Gestão e Escola)
│   │   │       ├── gestor/           # Dashboard do Eng. Fernando (Frota, Vagas, Rotas - US02)
│   │   │       └── escola/           # Portal da Diretora Tássia (ETA das vans no portão - US11)
│   │   ├── components/
│   │   │   ├── ui/                   # Componentes base shadcn/ui
│   │   │   └── domain/               # Componentes de negócio do Transporte Inclusivo
│   │   ├── hooks/                    # Custom hooks (useTrips, useCheckIn, useVehicleTracking)
│   │   ├── lib/
│   │   │   ├── api.ts                # Cliente HTTP configurado para a API Django
│   │   │   ├── utils.ts              # Helper cn() e formatadores
│   │   │   └── validations/          # Schemas Zod compartilhados
│   │   └── types/                    # Interfaces TypeScript espelhadas no dicionário de dados
│   ├── tailwind.config.ts
│   └── tsconfig.json
│
├── docs/                             # Documentação de Engenharia (Marco M1..M4)
│   ├── ADR-arquitetura.md            # Decisões Arquiteturais + Diagramas C4
│   ├── backlog-sprint-planning.md    # Backlog Ágil, Sprints e Story Points
│   ├── design-system-rules.md        # Regras do Design System e Integração Figma
│   ├── modelagem-arquitetura-v0.1.md # Dicionário de Dados completo (23 entidades)
│   ├── premissa_projeto.md           # BRD, PRD e Personas originais
│   └── wireframes-fluxo-telas.md     # Protótipos de Baixa/Média Fidelidade e Fluxos
├── CLAUDE.md                         # Regras rápidas de contexto para agentes de IA e Figma MCP
├── CONTRIBUTING.md                   # Regras de GitFlow, Commits e Code Review
├── LICENSE                           # Licença MIT
└── README.md                         # Visão executiva do projeto
```
